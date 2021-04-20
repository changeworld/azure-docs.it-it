---
title: 'Esercitazione: Rilasciare e verificare le credenziali verificabili usando il tenant di Azure (anteprima)'
description: Modificare l'esempio di codice Delle credenziali verificabili per usare il tenant di Azure
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739225"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Esercitazione: Rilasciare e verificare le credenziali verificabili usando il tenant (anteprima)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dopo aver configurato il tenant di Azure con il servizio Credenziali verificabili, vengono descritti i passaggi necessari per consentire all'Azure Active Directory (Azure AD) di rilasciare credenziali verificabili usando l'app di esempio.

In questo articolo:

> [!div class="checklist"]
> * Registrare l'app di esempio nel Azure AD
> * Creare regole e visualizzare un file
> * Caricare regole e visualizzare i file
> * Configurare il servizio Autorità emittente delle credenziali verificabili per l'uso Azure Key Vault
> * Aggiornare il codice di esempio con le informazioni del tenant.

Il codice di esempio richiede agli utenti di eseguire l'autenticazione a un provider di identità, in particolare Azure AD B2C, prima di poter emissione di Verified Credential Expert VC. Non tutte le autorità emittente di credenziali verificabili richiedono l'autenticazione prima di rilasciare le credenziali.

L'autenticazione dei token ID consente agli utenti di dimostrare chi sono prima di ricevere le credenziali. Quando l'accesso degli utenti ha esito positivo, il provider di identità restituisce un token di sicurezza contenente le attestazioni relative all'utente. Il servizio autorità emittente trasforma quindi questi token di sicurezza e le relative attestazioni in credenziali verificabili. Le credenziali verificabili vengono firmate con il did dell'autorità emittente.

È supportato qualsiasi provider di identità che OpenID Connect il protocollo. Esempi di provider di identità supportati [includono Azure Active Directory](../fundamentals/active-directory-whatis.md), [e Azure AD B2C](../../active-directory-b2c/overview.md). In questa esercitazione si usa AAD.

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che siano già stati completati i passaggi [dell'esercitazione precedente](enable-your-tenant-verifiable-credentials.md) e che sia possibile accedere all'ambiente usato.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Registrare un'app per consentire ai portafoglio DID di accedere agli utenti

Per rilasciare credenziali verificabili, è necessario registrare un'app in modo che Authenticator o qualsiasi altro portafoglio di credenziali verificabile sia autorizzato ad accedere agli utenti.  

Registrare un'applicazione denominata "VC Wallet App" Azure AD e ottenere un ID client.

1. Seguire le istruzioni per la registrazione di un'applicazione [con](../develop/quickstart-register-app.md) Azure AD durante la registrazione, usare i valori seguenti.

   - Nome: "VC Wallet App"
   - Tipi di account supportati: solo gli account in questa directory organizzativa
   - URI di reindirizzamento: vcclient://openid/

   ![registrare un'applicazione](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Dopo aver registrato l'applicazione, annota l'ID applicazione (client). Questo valore sarà necessario in un secondo momento.

   ![ID client dell'applicazione](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Selezionare il **pulsante Endpoint e** copiare l'URI del documento OpenID Connect metadati. Queste informazioni sono necessarie per la sezione successiva. 

   ![endpoint dell'autorità di emittente](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Configurare l'app node con accesso Azure Key Vault

Per autenticare la richiesta di rilascio delle credenziali di un utente, il sito Web dell'autorità emittente usa le chiavi di crittografia in Azure Key Vault. Per accedere Azure Key Vault, il sito Web richiede un ID client e un segreto client che possono essere usati per l'autenticazione Azure Key Vault.

1. Durante la visualizzazione della pagina di panoramica dell'app portafoglio vc selezionare **Certificati & segreti**.
    ![certificati e segreti](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Nella sezione **Segreti client** scegliere Nuovo **segreto client**
    1. Aggiungere una descrizione come "Segreto client Vc Node"
    1. Scadenza: in un anno.
  ![Segreto dell'applicazione con scadenza di un anno](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Copiare il segreto. Queste informazioni sono necessarie per aggiornare l'app Node di esempio.

>[!WARNING]
> È possibile copiare il segreto. Dopo questa operazione viene eseguito l'hashing unidiresto del segreto. Non copiare l'ID. 

Dopo aver creato l'applicazione e il segreto client in Azure AD, è necessario concedere all'applicazione le autorizzazioni necessarie per eseguire operazioni sul Key Vault. È necessario apportare queste modifiche alle autorizzazioni per consentire al sito Web di accedere e usare le chiavi private archiviate.

1. Passare a Key Vault.
2. Selezionare l'insieme di credenziali delle chiavi in uso per queste esercitazioni.
3. Scegliere **Criteri di accesso nel** riquadro di spostamento a sinistra
4. Scegliere **+Aggiungi criteri di accesso.**
5. Nella sezione **Autorizzazioni chiave** scegliere **Ottieni** e **Firma.**
6. Selezionare **Entità e** usare l'ID applicazione per cercare l'applicazione registrata in precedenza. Selezionarla.
7. Selezionare **Aggiungi**.
8. Scegliere **SALVA.**

Per altre informazioni sulle autorizzazioni di Key Vault e sul controllo di accesso, vedere la guida al controllo degli accessi in base al ruolo [dell'insieme di credenziali delle chiavi](../../key-vault/general/rbac-guide.md)

![assegnare le autorizzazioni dell'insieme di credenziali delle chiavi](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Apportare modifiche in modo che corrispondano all'ambiente

Finora si è lavorato con l'app di esempio. L'app usa [Azure Active Directory B2C](../../active-directory-b2c/overview.md) e si sta ora passando all'uso di Azure AD, quindi è necessario apportare alcune modifiche non solo in base all'ambiente, ma anche per supportare attestazioni aggiuntive che non sono state usate in precedenza.

1. Copiare il file delle regole seguente e salvarlo **inmodified-expertRules.jsin**. 

    > [!NOTE]
    > **"scope": "openid profile"** è incluso in questo file Rules e non è stato incluso nel file Rules dell'app di esempio. La sezione successiva illustra come abilitare le attestazioni facoltative nel tenant Azure Active Directory tenant. 
    
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

2. Aprire il file e sostituire i **client_id** **di** configurazione con i due valori copiati nella sezione precedente.

    ![evidenziazione dei due valori che devono essere modificati come parte di questo passaggio](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Il valore **Configuration** è l'URI OpenID Connect del documento di metadati.

  Poiché il codice di esempio usa Azure Active Directory B2C e si usa Azure Active Directory, è necessario aggiungere attestazioni facoltative tramite ambiti perché queste attestazioni siano incluse nel token ID da scrivere in Credenziali verificabili. 

3. Nel portale di Azure aprire Azure Active Directory.
4. Scegliere **Registrazioni app**.
5. Aprire l'app VC Wallet creata in precedenza.
6. Scegliere **Configurazione del token**.
7. Scegliere **+ Aggiungi attestazione facoltativa**

     ![nella configurazione del token aggiungere un'attestazione facoltativa](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. In **Tipo di token** scegliere **ID** e nell'elenco delle attestazioni disponibili **scegliere** given_name e **family_name**

     ![aggiungere attestazioni facoltative](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Premere **Aggiungi**.
10. Se viene visualizzato un avviso relativo alle autorizzazioni, come illustrato di seguito, selezionare la casella e selezionare **Aggiungi**.

     ![aggiungere le autorizzazioni per le attestazioni facoltative](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

A questo punto, quando un utente viene presentato con il "accedi" per ottenere le credenziali verificabili, l'app VC Portafoglio sa di includere le attestazioni specifiche tramite il parametro di ambito da scrivere in Credenziali verificabili.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Creare una nuova vc con questo file di regole e il file di visualizzazione precedente

1. Caricare il nuovo file delle regole nel contenitore
1. Dalla pagina delle credenziali verificabili creare una nuova credenziale denominata **modifiedCredentialExpert** usando il file di visualizzazione precedente e il nuovo file delle regole (**modified-credentialExpert.jsin**).
1. Al termine del processo di  creazione delle credenziali dalla pagina Panoramica, copiare **l'URL** delle credenziali del problema e salvarlo perché sarà necessario nella sezione successiva.

## <a name="before-we-continue"></a>Prima di continuare

Prima di poter apportare le modifiche necessarie al codice, è necessario riunire alcuni valori. Questi valori vengono utilizzati nella sezione successiva per fare in modo che il codice di esempio usi le proprie chiavi archiviate nell'insieme di credenziali. Fino a questo punto dovrebbero essere pronti i valori seguenti.

- **URI del** contratto dalla credenziale appena creata (emettere l'URL delle credenziali)
- **ID client applicazione** Questa operazione è stata ottenuto quando è stata registrata l'app Node.
- **Segreto client** Questa operazione è stata creata in precedenza quando all'app è stato concesso l'accesso all'insieme di credenziali delle chiavi.

Prima di poter apportare le modifiche una volta nell'app di esempio, è necessario ottenere alcuni altri valori. A questo punto è possibile ottenete queste informazioni.

### <a name="verifiable-credentials-settings"></a>Impostazioni delle credenziali verificabili

1. Passare alla pagina Credenziali verificabili e scegliere **Impostazioni.**  
1. Copiare i valori seguenti:

    - Identificatore del tenant 
    - Identificatore dell'autorità emittente (DID)
    - Insieme di credenziali delle chiavi (URI)

1. Nell'identificatore della chiave di firma è presente un URI, ma è necessaria solo una parte di esso. Copiare dalla parte che indica **issuerSigningKeyION** come evidenziato dal rettangolo rosso nell'immagine seguente.

   ![identificatore di chiave di accesso](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Documento DID

1. Aprire [il file DIF ION Esplora rete](https://identity.foundation/ion/explorer/)

2. Incollare did nella barra di ricerca.

4. Dalla risposta formattata trovare la sezione denominata **verificationMethod**
5. In "verificationMethod" copiare l'ID ed etichettarlo come kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

A questo punto è disponibile tutto il necessario per apportare le modifiche nel codice di esempio.

- **Autorità emittente: app.js** aggiornare le credenziali const con il nuovo URI del contratto
- **Verifica: aggiornare app.js** issuerDid con l'identificatore dell'autorità emittente
- **Issuer e Verifier** aggiornano il didconfig.jssu con i valori seguenti:


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
>Si tratta di un'applicazione demo e in genere è consigliabile non assegnare mai direttamente il segreto all'applicazione.


A questo punto sono presenti tutti gli elementi da rilasciare e verificare le proprie credenziali verificabili dal tenant Azure Active Directory con le proprie chiavi. 

## <a name="issue-and-verify-the-vc"></a>Eseguire il problema e verificare vc

Seguire la stessa procedura seguita nell'esercitazione precedente per rilasciare le credenziali verificabili e convalidarla con l'app. Dopo aver completato correttamente il processo di verifica, è possibile continuare a ottenere informazioni sulle credenziali verificabili.

1. Aprire un prompt dei comandi e aprire la cartella issuer.
2. Eseguire l'app Node aggiornata.

    ```terminal
    node app.js
    ```

3. Usando un prompt dei comandi diverso, eseguire ngrok per configurare un URL in 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > È anche possibile che venga visualizzato un avviso che indica che l'app o il sito Web potrebbe essere rischioso. Il messaggio è previsto in questo momento perché l'DID non è ancora stato collegato al dominio. Seguire le [istruzioni per l'associazione DNS](how-to-dnsbind.md) per configurarlo.

    
4. Aprire l'URL HTTPS generato da ngrok.

    ![Endpoint di inoltro NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Scegliere **GET CREDENTIAL**
6. In Authenticator eseguire la scansione del codice a codici.
7. In **Questa app o sito Web potrebbe essere un messaggio di avviso rischioso** scegliere **Avanzate.**

  ![Avviso iniziale](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Nell'avviso del sito Web rischioso **scegliere Continua comunque (non sicuro)**

  ![Secondo avviso relativo all'autorità emittente](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Nella schermata **Aggiungi credenziali** osservare alcuni aspetti: 
    1. Nella parte superiore della schermata viene visualizzato un messaggio rosso **Non verificato**
    1. Le credenziali vengono personalizzate in base alle modifiche apportate al file di visualizzazione.
    1. **L'opzione Accedi all'account** punta alla Azure AD di accesso.
    
   ![Schermata aggiungi credenziali con avviso](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Scegliere **Accedi al proprio account ed** eseguire l'autenticazione usando un utente nel tenant Azure AD personale.
11. Dopo l'autenticazione, **il pulsante** Aggiungi non è più visualizzato in grigio. Scegliere **Aggiungi**.

  ![Schermata aggiungi credenziali dopo l'autenticazione](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

A questo punto è stata rilasciata una credenziale verificabile usando il tenant per generare vc mentre si usa ancora il tenant B2C originale per l'autenticazione.

  ![vc rilasciato da Azure AD e autenticato dall'istanza di Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testare la verifica della vc usando l'app di esempio

Ora che la credenziale verificabile è stata rilasciata dal proprio tenant con attestazioni del Azure AD, è possibile verificarla usando l'app di esempio.

1. Arrestare l'esecuzione del servizio ngrok dell'autorità di emittente.

    ```cmd
    control-c
    ```

2. Eseguire ora ngrok con la porta di verifica 8082.

    ```cmd
    ngrok http 8082
    ```

3. In un'altra finestra del terminale passare all'app di verifica ed eseguirla in modo analogo a come è stata eseguita l'app autorità di emittente.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Aprire l'URL ngrok nel browser ed eseguire la scansione del codice AR usando Authenticator nel dispositivo mobile.
5. Nel dispositivo mobile scegliere Consenti **nella** schermata **Nuova richiesta di** autorizzazione.

   >[!IMPORTANT]
    > Poiché l'app di esempio usa anche did per firmare la richiesta di presentazione, si noterà un avviso che l'app o il sito Web potrebbe essere rischioso. Il messaggio è previsto in questo momento perché il DID non è ancora stato collegato al dominio. Per configurare [questa operazione,](how-to-dnsbind.md) seguire le istruzioni di associazione DNS.
    
   ![nuova richiesta di autorizzazione](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. La verifica delle credenziali è stata completata e il sito Web dovrebbe visualizzare il nome e il cognome dell'account Azure AD'account utente dell'utente. 

A questo punto, l'esercitazione è stata completata e ufficialmente è stato verificato un esperto di credenziali. L'app di esempio usa did sia per l'emissione che per la verifica, mentre scrive attestazioni in credenziali verificabili dal Azure AD. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare [credenziali personalizzate](credential-design.md)
- Esempi di comunicazione del servizio autorità di [emittente](issuer-openid.md)
