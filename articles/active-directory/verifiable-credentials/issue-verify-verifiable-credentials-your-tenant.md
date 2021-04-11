---
title: 'Esercitazione: emettere e verificare le credenziali verificabili usando il tenant di Azure (anteprima)'
description: Modificare l'esempio di codice delle credenziali verificabile per lavorare con il tenant di Azure
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: e4772b6701065a44416d849faa9a501bd7895f27
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553380"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Esercitazione: emettere e verificare le credenziali verificabili usando il tenant (anteprima)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ora che il tenant di Azure è configurato con il servizio credenziali verificabile, verranno illustrati i passaggi necessari per consentire al Azure Active Directory (Azure AD) di emettere credenziali verificabili usando l'app di esempio.

In questo articolo sono state illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Registrare l'app di esempio nell'Azure AD
> * Creazione di regole e file di visualizzazione
> * Caricare le regole e i file di visualizzazione
> * Configurare il servizio emittente di credenziali verificabili per l'uso di Azure Key Vault
> * Aggiornare il codice di esempio con le informazioni del tenant.

Il codice di esempio richiede agli utenti di eseguire l'autenticazione a un provider di identità, in particolare Azure AD B2C, prima che sia possibile emettere il VC esperto di credenziali verificato. Non tutte le autorità emittenti verificabili richiedono l'autenticazione prima di emettere le credenziali.

L'autenticazione dei token ID consente agli utenti di dimostrare chi si trovano prima di ricevere le proprie credenziali. Quando gli utenti accedono correttamente, il provider di identità restituisce un token di sicurezza che contiene le attestazioni relative all'utente. Il servizio emittente trasforma quindi questi token di sicurezza e le relative attestazioni in una credenziale verificabile. La credenziale verificabile è firmata con l'autorità emittente.

È supportato qualsiasi provider di identità che supporta il protocollo OpenID Connect. Esempi di provider di identità supportati sono [Azure Active Directory](../fundamentals/active-directory-whatis.md)e [Azure ad B2C](../../active-directory-b2c/overview.md). In questa esercitazione si usa AAD.

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che siano già stati completati i passaggi dell' [esercitazione precedente](enable-your-tenant-verifiable-credentials.md) e che sia possibile accedere all'ambiente in uso.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Registrare un'app per abilitare i portafogli per l'accesso degli utenti

Per emettere una credenziale verificabile, è necessario registrare un'app in modo che l'autenticatore, o qualsiasi altro portafoglio di credenziali verificabile, sia autorizzato ad accedere agli utenti.  

Registrare un'applicazione denominata "app Wallet VC" in Azure AD e ottenere un ID client.

1. Seguire le istruzioni per la registrazione di un'applicazione con [Azure ad](../develop/quickstart-register-app.md) durante la registrazione, usare i valori riportati di seguito.

   - Nome: "app portafogli VC"
   - Tipi di account supportati: solo account in questa directory organizzativa
   - URI di reindirizzamento: vcclient://openid/

   ![registrare un'applicazione](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Dopo aver registrato l'applicazione, annotare l'ID dell'applicazione (client). Questo valore sarà necessario in un secondo momento.

   ![ID client applicazione](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Selezionare il pulsante **endpoint** e copiare l'URI del documento di metadati OpenID Connect. Queste informazioni sono necessarie per la sezione successiva. 

   ![endpoint dell'emittente](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Configurare l'app node con accesso a Azure Key Vault

Per autenticare la richiesta di rilascio delle credenziali di un utente, il sito Web dell'autorità emittente USA le chiavi crittografiche in Azure Key Vault. Per accedere a Azure Key Vault, il sito Web necessita di un ID client e di un segreto client che possono essere usati per eseguire l'autenticazione a Azure Key Vault.

1. Quando si visualizza la pagina Panoramica di VC Wallet app, selezionare **certificati & segreti**.
    ![certificati e segreti](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Nella sezione **segreti client** scegliere **nuovo segreto client**
    1. Aggiungere una descrizione come "segreto client del nodo VC"
    1. Scade: in un anno.
  ![Segreto dell'applicazione con scadenza di un anno](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Copiare il segreto. Queste informazioni sono necessarie per aggiornare l'app del nodo di esempio.

>[!WARNING]
> Si ha la possibilità di copiare il segreto. Il segreto è un metodo con hash dopo questa operazione. Non copiare l'ID. 

Dopo aver creato l'applicazione e il segreto client in Azure AD, è necessario concedere all'applicazione le autorizzazioni necessarie per eseguire operazioni nel Key Vault. Per consentire al sito Web di accedere e usare le chiavi private archiviate, è necessario apportare queste modifiche alle autorizzazioni.

1. Passare a Key Vault.
2. Selezionare l'insieme di credenziali delle chiavi usato per queste esercitazioni.
3. Scegliere i **criteri di accesso** nel NAV a sinistra
4. Scegliere **+ Aggiungi criteri di accesso**.
5. Nella sezione **autorizzazioni chiave** scegliere **Get** e **Sign**.
6. Selezionare **principale** e usare l'ID applicazione per cercare l'applicazione registrata in precedenza. Selezionarla.
7. Selezionare **Aggiungi**.
8. Scegliere **Salva**.

Per altre informazioni sulle autorizzazioni Key Vault e sul controllo di accesso, vedere la [Guida RBAC Key Vault](../../key-vault/general/rbac-guide.md)

![assegnare le autorizzazioni di Key Vault](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Apportare modifiche in base all'ambiente

Fino ad ora abbiamo lavorato con l'app di esempio. L'app usa [Azure Active Directory B2C](../../active-directory-b2c/overview.md) e ora si passa a usare Azure ad, quindi è necessario apportare alcune modifiche non solo per la corrispondenza con l'ambiente, ma anche per supportare attestazioni aggiuntive non usate in precedenza.

1. Copiare il file di regole seguente e salvarlo in **modified-expertRules.js**. 

    > [!NOTE]
    > **"scope": "OpenID profile"** è incluso in questo file di regole e non è stato incluso nel file delle regole dell'app di esempio. Nella sezione successiva verrà illustrato come abilitare le attestazioni facoltative nel tenant del Azure Active Directory. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Aprire il file e sostituire i valori di **client_id** e di **configurazione** con i due valori copiati nella sezione precedente.

    ![evidenziazione dei due valori che devono essere modificati nell'ambito di questo passaggio](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  La **configurazione** del valore è l'URI del documento di metadati OpenID Connect.

  Poiché il codice di esempio USA Azure Active Directory B2C e si usa Azure Active Directory, è necessario aggiungere attestazioni facoltative tramite gli ambiti affinché queste attestazioni vengano incluse nel token ID da scrivere nella credenziale verificabile. 

3. Tornare alla portale di Azure, aprire Azure Active Directory.
4. Scegliere **registrazioni app**.
5. Aprire l'app del portafogli VC creata in precedenza.
6. Scegliere la **configurazione del token**.
7. Scegliere **+ Aggiungi attestazione facoltativa**

     ![in configurazione token aggiungere un'attestazione facoltativa](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Dal **tipo di token** scegliere **ID** e nell'elenco delle attestazioni disponibili scegliere **given_name** e **family_name**

     ![aggiungere attestazioni facoltative](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Premere **Aggiungi**.
10. Se si riceve un avviso di autorizzazione come illustrato di seguito, selezionare la casella e selezionare **Aggiungi**.

     ![aggiungere le autorizzazioni per le attestazioni facoltative](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

A questo punto, quando un utente viene presentato con l'"accesso" per ottenere la credenziale verificabile, l'app del portafogli VC sa di includere le attestazioni specifiche tramite il parametro di ambito da scrivere nella credenziale verificabile.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Crea nuovo VC con questo file di regole e il vecchio file di visualizzazione

1. Caricare il nuovo file di regole nel contenitore
1. Dalla pagina credenziali verificabili creare una nuova credenziale denominata **modifiedCredentialExpert** usando il file di visualizzazione precedente e il nuovo file delle regole (**modified-credentialExpert.json**).
1. Al termine del processo di creazione delle credenziali dalla pagina **Panoramica** , copiare l' **URL delle credenziali del problema** e salvarlo perché è necessario nella sezione successiva.

## <a name="before-we-continue"></a>Prima di continuare

Prima di poter apportare le modifiche necessarie al codice, è necessario inserire alcuni valori. Questi valori vengono usati nella sezione successiva per fare in modo che il codice di esempio usi le proprie chiavi archiviate nell'insieme di credenziali. Fino ad ora è necessario che i valori seguenti siano pronti.

- **URI del contratto** dalle credenziali appena create (emissione dell'URL delle credenziali)
- **ID client applicazione** Questa operazione è stata eseguita quando è stata registrata l'app node.
- **Segreto client** Questa operazione è stata creata in precedenza quando è stato concesso all'app l'accesso a Key Vault.

Ci sono alcuni altri valori che è necessario ottenere prima di poter apportare le modifiche una volta nell'app di esempio. Ora è possibile ottenerli.

### <a name="verifiable-credentials-settings"></a>Impostazioni delle credenziali verificabili

1. Passare alla pagina credenziali verificabili e scegliere **Impostazioni**.  
1. Copiare i valori seguenti:

    - Identificatore del tenant 
    - Identificatore dell'autorità emittente (DID)
    - Key Vault (URI)

1. Nell'identificatore della chiave di firma è presente un URI, ma ne è necessario solo una parte. Copiare dalla parte che indica **issuerSigningKeyION** come evidenziato dal rettangolo rosso nell'immagine seguente.

   ![identificatore di chiave di accesso](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Documento DID

1. Aprire [dif Ion Network Explorer](https://identity.foundation/ion/explorer/)

2. Incollare il fatto nella barra di ricerca.

4. Dalla risposta formattata trovare la sezione denominata **verificationMethod**
5. In "verificationMethod" copiare l'ID ed etichettarlo come kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

A questo punto sono disponibili tutti gli elementi necessari per apportare le modifiche nel codice di esempio.

- **Emittente:** app.js aggiornare le credenziali const con il nuovo URI del contratto
- **Verifier:** app.js aggiornare issuerDid con l'identificatore dell'autorità emittente
- L' **emittente e il verificatore** aggiornano il didconfig.jscon i valori seguenti:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Si tratta di un'applicazione demo che in genere non si deve mai fornire direttamente all'applicazione il segreto.


A questo punto sono disponibili tutti gli elementi necessari per emettere e verificare le proprie credenziali verificabili dal tenant di Azure Active Directory con le proprie chiavi. 

## <a name="issue-and-verify-the-vc"></a>Rilasciare e verificare il VC

Seguire la stessa procedura seguita nell'esercitazione precedente per emettere le credenziali verificabili e convalidarla con l'app. Una volta completato correttamente il processo di verifica, è ora possibile continuare a conoscere le credenziali verificabili.

1. Aprire un prompt dei comandi e aprire la cartella Issuer.
2. Eseguire l'app node aggiornata.

    ```terminal
    node app.js
    ```

3. Utilizzando un prompt dei comandi diverso, eseguire ngrok per impostare un URL su 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > È anche possibile che venga visualizzato un avviso che indica che l'app o il sito Web potrebbe essere rischioso. Il messaggio è previsto in questo momento perché non è ancora stata collegata al dominio. Per configurare questo, seguire le istruzioni di [Binding DNS](how-to-dnsbind.md) .

    
4. Aprire l'URL HTTPS generato da ngrok.

    ![Endpoint di NGROK di inoltri](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Scegliere **Get Credential**
6. In Authenticator eseguire la scansione del codice QR.
7. In **questa app o sito Web potrebbe essere** presente un messaggio di avviso rischioso scegliere **Avanzate**.

  ![Avviso iniziale](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Nell'avviso del sito Web rischioso scegliere **continua comunque (non sicuro)**

  ![Secondo avviso relativo all'autorità emittente](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Alla schermata **Aggiungi credenziali** si notino alcuni aspetti: 
    1. Nella parte superiore della schermata è possibile visualizzare un messaggio rosso **non verificato**
    1. La credenziale viene personalizzata in base alle modifiche apportate al file di visualizzazione.
    1. L'opzione **accedi al tuo account** fa riferimento alla pagina di accesso Azure ad.
    
   ![Aggiungi schermata credenziali con avviso](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Scegliere **accedi al proprio account** ed eseguire l'autenticazione usando un utente nel tenant del Azure ad.
11. Una volta eseguita l'autenticazione, il pulsante **Aggiungi** non è più disattivato. Scegliere **Aggiungi**.

  ![Aggiungi schermata credenziali dopo l'autenticazione](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Sono state rilasciate credenziali verificabili usando il tenant per generare il VC mentre si usa ancora il tenant B2C per l'autenticazione.

  ![VC rilasciato da Azure AD e autenticato dall'istanza di Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testare la verifica del VC usando l'app di esempio

Ora che sono state rilasciate le credenziali verificabili dal nostro tenant con attestazioni dalla Azure AD, è possibile verificarla usando l'app di esempio.

1. Arrestare l'esecuzione del servizio ngrok dell'autorità emittente.

    ```cmd
    control-c
    ```

2. Eseguire ora ngrok con la porta Verifier 8082.

    ```cmd
    ngrok http 8082
    ```

3. In un'altra finestra del terminale passare all'app Verifier ed eseguirla in modo analogo al modo in cui è stata eseguita l'app emittente.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Aprire l'URL ngrok nel browser ed eseguire la scansione del codice a matrice usando Authenticator nel dispositivo mobile.
5. Nel dispositivo mobile scegliere **Consenti** nella schermata **nuova richiesta di autorizzazione** .

   >[!IMPORTANT]
    > Poiché l'app di esempio usa anche il fatto di firmare la richiesta di presentazione, si noterà un avviso che indica che l'app o il sito Web potrebbe essere rischioso. Il messaggio è previsto in questo momento perché non è ancora stata collegata al dominio. Per configurare questo, seguire le istruzioni di [Binding DNS](how-to-dnsbind.md) .
    
   ![nuova richiesta di autorizzazione](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Le credenziali sono state verificate correttamente e il sito Web deve visualizzare il nome e il cognome dall'account utente del Azure AD. 

A questo punto è stata completata l'esercitazione e si è ufficialmente un esperto di credenziali verificato. L'app di esempio usa il fatto per il rilascio e la verifica, durante la scrittura delle attestazioni in una credenziale verificabile dal Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare [credenziali personalizzate](credential-design.md)
- [Esempi](issuer-openid.md) di comunicazione del servizio emittente
