---
title: 'Esercitazione: configurare il Azure Active Directory per emettere credenziali verificabili (anteprima)'
description: In questa esercitazione si compila l'ambiente necessario per distribuire le credenziali verificabili nel tenant
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 03/31/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 08aaa49f73ed437e041ffb93dc9ef5be41e316ec
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106172025"
---
# <a name="tutorial-configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Esercitazione: configurare il Azure Active Directory per emettere credenziali verificabili (anteprima)

In questa esercitazione viene compilato il lavoro svolto come parte dell'articolo [introduttivo e viene](get-started-verifiable-credentials.md) configurato il Azure Active Directory (Azure ad) con il proprio [identificatore decentralizzato](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (did). Viene usato l'identificatore decentralizzato per emettere una credenziale verificabile usando l'app di esempio e l'emittente. Tuttavia, in questa esercitazione viene comunque usato il tenant di Azure B2C di esempio per l'autenticazione.  Nell'esercitazione successiva verranno eseguiti altri passaggi per configurare l'app in modo che funzioni con la Azure AD.

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Contenuto dell'articolo:

> [!div class="checklist"]
> * È possibile creare i servizi necessari per caricare i Azure AD per le credenziali verificabili 
> * Creazione del
> * Personalizzazione delle regole e dei file di visualizzazione
> * Configurare le credenziali verificabili in Azure AD.


## <a name="prerequisites"></a>Prerequisiti

Per completare correttamente questa esercitazione, è innanzitutto necessario:

- Per iniziare [, completare](get-started-verifiable-credentials.md)l'operazione.
- Avere a disposizione un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD con una [licenza](https://azure.microsoft.com/pricing/details/active-directory/)P2. Se non si dispone [di un account, seguire la procedura per creare un account gratuito per sviluppatori](how-to-create-a-free-developer-account.md) .
- Istanza di [Azure Key Vault](../../key-vault/general/overview.md) cui si dispone dei diritti per la creazione di chiavi e segreti.

## <a name="azure-active-directory"></a>Azure Active Directory

Prima di iniziare, è necessario un tenant Azure AD. Quando il tenant è abilitato per le credenziali verificabili, viene assegnato un identificatore decentralizzato (DID) ed è abilitato con un servizio emittente per l'emissione di credenziali verificabili. Tutte le credenziali verificabili emesse vengono rilasciate dal tenant e dalla relativa impostazione. Viene inoltre utilizzato per la verifica delle credenziali verificabili.
Se è stata appena creata una sottoscrizione di Azure di test, non è necessario che il tenant venga popolato con gli account utente, ma sarà necessario avere almeno un account di test utente per completare le esercitazioni successive.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Quando si usano le credenziali verificabili, si ha il controllo completo e la gestione delle chiavi crittografiche usate dal tenant per firmare digitalmente le credenziali verificabili. Per emettere e verificare le credenziali, è necessario fornire Azure AD con accesso alla propria istanza di Azure Key Vault.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Nella casella di ricerca immettere **Key Vault**.
3. Nell'elenco dei risultati scegliere **Key Vault**.
4. Nella sezione Key Vault scegliere **Crea**.
5. Nella pagina **Crea insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    - **Sottoscrizione** Scegliere una sottoscrizione.
    - In **gruppo di risorse** scegliere **Crea nuovo** e immettere un nome per il gruppo di risorse, ad esempio **VC-Resource-Group**. Viene usato lo stesso nome del gruppo di risorse in più articoli.
    - **Name**: è necessario un nome univoco. Si usa **woodgroup-VC-KV,** quindi sostituire questo valore con il proprio nome univoco.
    - Scegliere un percorso nel menu a discesa **Percorso**.
    - Lasciare invariati i valori predefiniti delle altre opzioni.
6. Dopo aver fornito le informazioni, selezionare **criteri di accesso**

    ![creare una pagina di Key Vault](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Nella schermata Criteri di accesso scegliere **Aggiungi criteri di accesso**

    >[!NOTE]
    > Per impostazione predefinita, l'account che crea l'insieme di credenziali delle chiavi è l'unico con accesso. Il servizio credenziali verificabile richiede l'accesso a Key Vault. L'insieme di credenziali delle chiavi deve disporre di un criterio di accesso che consenta all'amministratore di **creare le chiavi**, di **eliminare le chiavi** se si sceglie di rifiutare e **firmare** per creare l'associazione di dominio per le credenziali verificabili. Se si usa lo stesso account durante i test, assicurarsi di modificare i criteri predefiniti per concedere l' **accesso** all'account oltre alle autorizzazioni predefinite concesse agli autori dell'insieme di credenziali.

8. Per l'amministratore utente, assicurarsi che la sezione autorizzazioni chiave includa **creazione**, **eliminazione** e **firma** abilitata. Per impostazione predefinita, creazione ed eliminazione sono già abilitate e la firma deve essere l'unica autorizzazione chiave che deve essere aggiornata. 

    ![Autorizzazioni Key Vault](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Selezionare **Rivedi e crea**.
10. Selezionare **Crea**.
11. Passare all'insieme di credenziali e prendere nota del nome dell'insieme di credenziali e dell'URI

Prendere nota delle due proprietà elencate di seguito:

- **Nome** dell'insieme di credenziali: nell'esempio, il nome del valore è **Woodgrove-VC-KV**. Usare questo nome per altri passaggi.
- **URI** dell'insieme di credenziali: nell'esempio, questo valore è https://woodgrove-vc-kv.vault.azure.net/ . Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

>[!NOTE]
> Ogni transazione dell'insieme di credenziali delle chiavi comporta costi aggiuntivi per la sottoscrizione di Azure. Per altri dettagli, vedere la pagina relativa ai [prezzi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

>[!IMPORTANT]
> Durante la Azure Active Directory anteprima delle credenziali verificabili, le chiavi e i segreti creati nell'insieme di credenziali non devono essere modificati dopo la creazione. L'eliminazione, la disabilitazione o l'aggiornamento delle chiavi e dei segreti invalida tutte le credenziali rilasciate. Non modificare le chiavi o i segreti durante l'anteprima.

## <a name="create-a-modified-rules-and-display-file"></a>Creare regole modificate e file di visualizzazione

In questa sezione vengono usate le regole e i file visualizzati dall'app emittente di esempio e vengono modificati leggermente per creare le prime credenziali verificabili del tenant.

1. Copiare le regole e visualizzare i file JSON in una cartella temporanea e rinominarli **MyFirstVC-display.jsrispettivamente in** e **MyFirstVC-rules.js** . È possibile trovare entrambi i file in **Issuer \ issuer_config**

   ![visualizzare i file e le regole come parte della directory dell'app di esempio](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![visualizzare i file e le regole in una cartella temporanea](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Aprire il MyFirstVC-rules.jsnel file nell'editor di codice. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

A questo punto, modificare il campo tipo in "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Salvare questa modifica.

 >[!NOTE]
   > A questo punto dell'esercitazione non si sta modificando " **Configuration"** o **"client_id"** . Viene comunque usato il tenant di Microsoft B2C usato nella pagina [iniziale](get-started-verifiable-credentials.md). Nell'esercitazione successiva verrà usato il Azure AD.

3. Aprire il MyFirstVC-display.jsnel file nell'editor di codice.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

È possibile apportare alcune modifiche in modo che questa credenziale verificabile risulti visibilmente diversa dalla versione del codice di esempio. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Salvare le modifiche.
## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Prima di creare le prime credenziali verificabili, è necessario creare un contenitore di archiviazione BLOB in grado di memorizzare i file di configurazione e di regole.

1. Creare un account di archiviazione usando le opzioni illustrate di seguito. Per i passaggi dettagliati, vedere l'articolo [creare un account di archiviazione](../../storage/common/storage-account-create.md?tabs=azure-portal) .

   - **Sottoscrizione:** Scegliere la sottoscrizione usata per queste esercitazioni.
   - **Gruppo di risorse:** Scegliere lo stesso gruppo di risorse usato nelle esercitazioni precedenti (**VC-Resource-Group**).
   - **Nome:**  Nome univoco.
   - **Località:** (Stati Uniti) Stati Uniti orientali.
   - **Prestazioni:** Standard.
   - **Tipo di account:** Archiviazione V2.
   - **Replica:** Con ridondanza locale.
 
   ![Creare un nuovo account di archiviazione.](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Dopo aver creato l'account di archiviazione, è necessario creare un contenitore. Selezionare i **contenitori** nell' **Archivio BLOB** e creare un contenitore usando i valori indicati di seguito:

    - **Nome:** VC-container
    - **Livello di accesso pubblico:** Privato (nessun accesso anonimo)

   ![Creare un contenitore](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. A questo punto, selezionare il nuovo contenitore e caricare sia le nuove regole che i file di visualizzazione **MyFirstVC-display.jsin** e **MyFirstVC-rules.jscreati in** precedenza.

   ![Carica file di regole](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Assegnare il ruolo BLOB

Prima di creare le credenziali, è necessario innanzitutto assegnare all'utente connesso l'assegnazione di ruolo corretta, in modo che possano accedere ai file nel BLOB di archiviazione.

1. Passare al   >  **contenitore** di archiviazione.
2. Scegliere **controllo di accesso (IAM)** dal menu a sinistra.
3. Scegliere **assegnazioni di ruolo**.
4. Selezionare **Aggiungi**.
5. Nella sezione **Role** scegliere **storage BLOB data reader**.
6. In **assegnare l'accesso per** scegliere un **utente, un gruppo o un principio di servizio**.
7. In **Seleziona**: scegliere l'account usato per eseguire questi passaggi.
8. Selezionare **Save (Salva** ) per completare l'assegnazione di ruolo.


   ![Aggiungi un'assegnazione di ruolo](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Per impostazione predefinita, i creatori di contenitori ottengono il ruolo **proprietario** assegnato. Il ruolo di **proprietario** non è sufficiente. Per l'account è necessario il ruolo **lettore dati BLOB di archiviazione** . Per altre informazioni, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Configurare le credenziali verificabili (anteprima)

A questo punto è necessario eseguire l'ultimo passaggio per configurare il tenant per le credenziali verificabili.

1. Dalla portale di Azure cercare le **credenziali verificabili**.
2. Scegliere **credenziali verificabili (anteprima)**.
3. Scegli **inizia**
4. È necessario configurare l'organizzazione e fornire il nome, il dominio e l'insieme di credenziali delle chiavi dell'organizzazione. Esaminiamo ognuno dei valori.

      - **nome organizzazione**: questo nome è il modo in cui si fa riferimento all'attività all'interno del servizio credenziali verificabile. Questo valore non è rivolte al cliente.
      - **Dominio:** Il dominio immesso viene aggiunto a un endpoint di servizio nel documento DID. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) e altri portafogli usano queste informazioni per verificare che l'operazione sia stata [collegata al dominio](how-to-dnsbind.md). Se il portafogli è in grado di verificare il fatto, viene visualizzato un simbolo verificato. Se il portafoglio non è in grado di verificare il fatto, informa l'utente che le credenziali sono state emesse da un'organizzazione che non è stato possibile convalidare. Il dominio è quello che associa il fatto a qualcosa di tangibile che l'utente può conoscere sulla propria azienda.
      - **Key Vault:** Specificare il nome del Key Vault creato in precedenza.
 
   >[!IMPORTANT]
   > Il dominio non può essere un reindirizzamento; in caso contrario, il dominio DID e non può essere collegato. Assicurarsi di usare il https://www.domain.com formato.
    
5. Scegliere **Salva e crea credenziali**

    ![configurare l'identità organizzativa](media/enable-your-tenant-verifiable-credentials/save-create.png)

Il tenant è ora abilitato per l'anteprima della credenziale verificabile.

## <a name="create-your-vc-in-the-portal"></a>Creare il VC nel portale

Il passaggio precedente rimane nella pagina **Crea una nuova credenziale** . 

   ![Introduzione alle credenziali verificabili](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. In nome credenziale aggiungere il nome **MyFirstVC**. Questo nome viene usato nel portale per identificare le credenziali verificabili ed è incluso come parte del contratto di credenziali verificabili.
2. Nella sezione file di visualizzazione scegliere **Configura file di visualizzazione** .
3. Nella sezione **account di archiviazione** selezionare **woodgrovevcstorage**.
4. Dall'elenco dei contenitori disponibili scegliere **VC-container**.
5. Scegliere il **MyFirstVC-display.jsnel** file creato in precedenza.
6. Dalla sezione **creare una nuova credenziale** nel **file delle regole** scegliere **Configura file di regole**
7. Nella sezione **account di archiviazione** selezionare **woodgrovecstorage**
8. Scegliere **VC-container**.
9. Seleziona **MyFirstVC-rules.js**
10. Dalla schermata **Crea una nuova credenziale** scegliere **Crea**.

   ![Crea una nuova credenziale](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>URL credenziali

Ora che si dispone di una nuova credenziale, copiare l'URL delle credenziali.

   ![URL delle credenziali del problema](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>L'URL delle credenziali è la combinazione di regole e file di visualizzazione. Si tratta dell'URL che l'autenticatore valuta prima di visualizzare ai requisiti di rilascio delle credenziali verificabili dall'utente.  

## <a name="update-the-sample-app"></a>Aggiornare l'app di esempio

A questo punto si apportano modifiche al codice dell'autorità di certificazione dell'app di esempio per aggiornarlo con l'URL di credenziali verificabile. In questo modo è possibile emettere credenziali verificabili usando il proprio tenant.

1. Passare alla cartella in cui sono stati inseriti i file dell'app di esempio.
2. Aprire la cartella Issuer e quindi aprire app.js in Visual Studio Code.
3. Aggiornare la costante ' Credential ' con il nuovo URL delle credenziali e impostare la costante credentialType su' MyFirstVC ' e salvare il file.

    ![immagine di Visual Studio Code che mostra le aree rilevanti evidenziate](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Aprire un prompt dei comandi e aprire la cartella Issuer.
5. Eseguire l'app node aggiornata.

    ```terminal
    node app.js
    ```

6. Utilizzando un prompt dei comandi diverso, eseguire ngrok per impostare un URL su 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > È anche possibile che venga visualizzato un avviso che indica che l'app o il sito Web potrebbe essere rischioso. Il messaggio è previsto in questo momento perché non è ancora stata collegata al dominio. Per configurare questo, seguire le istruzioni di [Binding DNS](how-to-dnsbind.md) .

    
7. Aprire l'URL HTTPS generato da ngrok.

    ![Endpoint di NGROK di inoltri](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Scegliere **Get Credential**
9. In Authenticator eseguire la scansione del codice QR.
10. In **questa app o sito Web potrebbe essere** presente un messaggio di avviso rischioso scegliere **Avanzate**.

  ![Avviso iniziale](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. Nell'avviso del sito Web rischioso scegliere **continua comunque (non sicuro)**

  ![Secondo avviso relativo all'autorità emittente](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. Alla schermata **Aggiungi credenziali** si notino alcuni aspetti: 
    1. Nella parte superiore della schermata è possibile visualizzare un messaggio rosso **non verificato**
    1. La credenziale viene personalizzata in base alle modifiche apportate al file di visualizzazione.
    1. L'opzione **accedi al tuo account** fa riferimento a **didplayground.b2clogin.com**.
    
   ![Aggiungi schermata credenziali con avviso](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Scegliere **accedi al proprio account** ed eseguire l'autenticazione usando le informazioni sulle credenziali fornite nell' [esercitazione introduttiva](get-started-verifiable-credentials.md).
14. Una volta eseguita l'autenticazione, il pulsante **Aggiungi** non è più disattivato. Scegliere **Aggiungi**.

  ![Aggiungi schermata credenziali dopo l'autenticazione](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Sono state rilasciate credenziali verificabili usando il tenant per generare il VC mentre si usa ancora il tenant B2C per l'autenticazione.

  ![VC rilasciato da Azure AD e autenticato dall'istanza di Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testare la verifica del VC usando l'app di esempio

Ora che sono state rilasciate le credenziali verificabili dal tenant, è possibile verificarla usando l'app di esempio.

>[!IMPORTANT]
> Durante i test, usare lo stesso indirizzo di posta elettronica e la stessa password usati durante l'articolo [introduttivo](get-started-verifiable-credentials.md) . Mentre il tenant emette il VC, l'input è proveniente da un token ID emesso dal tenant di Microsoft B2C. Nell'esercitazione due si passa il rilascio di token al tenant

1. Aprire le **Impostazioni** nel pannello credenziali verificabili della portale di Azure. Copiare l'identificatore decentralizzato (DID).

   ![copia](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. A questo punto aprire la cartella Verifier dei file dell'app di esempio. È necessario aggiornare il file di app.js nel codice di esempio di Verifier e apportare le modifiche seguenti:

    - **credenziale**: modificare l'URL delle credenziali
    - **CredentialType**:' MyFirstVC '
    - **issuerDid**: copiare questo valore da portale di Azure>le credenziali verificabili (anteprima) >le impostazioni>identificatore decentralizzato (did)
    
   ![aggiornare la costante issuerDid in modo che corrisponda all'identificatore dell'autorità emittente](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Arrestare l'esecuzione del servizio ngrok dell'autorità emittente.

    ```cmd
    control-c
    ```

4. Eseguire ora ngrok con la porta Verifier 8082.

    ```cmd
    ngrok http 8082
    ```

5. In un'altra finestra del terminale passare all'app Verifier ed eseguirla in modo analogo al modo in cui è stata eseguita l'app emittente.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Aprire l'URL ngrok nel browser ed eseguire la scansione del codice a matrice usando Authenticator nel dispositivo mobile.
7. Nel dispositivo mobile scegliere **Consenti** nella schermata **nuova richiesta di autorizzazione** .

    >[!NOTE]
    > La firma di questo VC è ancora Microsoft B2C. Il verificatore ha ancora fatto parte del tenant dell'app di esempio Microsoft. Poiché Microsoft è stato collegato a un dominio di cui si è proprietari, l'avviso non viene visualizzato come si è verificato durante il flusso di rilascio. Questa operazione verrà aggiornata nella sezione successiva.
    
   ![nuova richiesta di autorizzazione](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Le credenziali non sono state verificate correttamente.

## <a name="next-steps"></a>Passaggi successivi

Ora che è disponibile il codice di esempio che emette un VC dall'emittente, è possibile continuare con la sezione successiva in cui si usa il proprio provider di identità per autenticare gli utenti che tentano di ottenere credenziali verificabili e usare l'utente per firmare le richieste di presentazione.

> [!div class="nextstepaction"]
> [Esercitazione: emettere e verificare le credenziali verificabili usando il tenant](issue-verify-verifiable-credentials-your-tenant.md)


