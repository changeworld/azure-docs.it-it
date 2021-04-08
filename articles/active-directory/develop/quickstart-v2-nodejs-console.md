---
title: "Guida introduttiva: chiamare Microsoft Graph da un'app console Node.js | Azure"
titleSuffix: Microsoft identity platform
description: In questa Guida introduttiva viene scaricato ed eseguito un esempio di codice che illustra come un'applicazione console Node.js può ottenere un token di accesso e chiamare un'API protetta da un endpoint della piattaforma di identità Microsoft, usando l'identità dell'app.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653746"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app console Node.js usando l'identità dell'app

In questa Guida introduttiva viene scaricato ed eseguito un esempio di codice che illustra come un'applicazione console Node.js può ottenere un token di accesso usando l'identità dell'app per chiamare l'API Microsoft Graph e visualizzare un [elenco di utenti](/graph/api/user-list) nella directory. L'esempio di codice dimostra come è possibile eseguire un processo o un servizio di Windows automatico con un'identità dell'applicazione invece che con un'identità dell'utente.

Questa Guida introduttiva usa [Microsoft Authentication Library per Node.js (nodo MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) con la [concessione delle credenziali client](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrare e scaricare l'applicazione di esempio
>
> Per iniziare, seguire questa procedura.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `msal-node-cli`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Selezionare **Registra**.
> 1. In **Gestisci**, selezionare **Certificati e segreti**.
> 1. In **Segreti client** selezionare **Nuovo segreto client**, immettere un nome e quindi selezionare **Aggiungi**. Registrare il valore del segreto in una posizione sicura per usarlo in un passaggio successivo.
> 1. In **Gestisci** selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**.
> 1. Selezionare **Autorizzazioni applicazione**.
> 1. Nel nodo **Utente** selezionare **User.Read.All**, quindi selezionare **Aggiungi autorizzazioni**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Scaricare e configurare l'app di esempio
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passaggio 1: configurare l'applicazione in portale di Azure
> Per fare in modo che l'esempio di codice per questo avvio rapido funzioni, è necessario creare un segreto client e aggiungere l'autorizzazione dell'applicazione **User.Read.All** dell'API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-netcore-daemon/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Passaggio 2: scaricare il progetto di esempio Node.js

> [!div renderon="docs"]
> [Scaricare il codice di esempio](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Passaggio 3: configurare il progetto di esempio Node.js
>
> 1. Estrarre il file zip in una cartella locale vicina alla radice del disco, ad esempio *C:/Azure-Samples*.
> 1. Modificare *. env* e sostituire i valori dei campi `TENANT_ID` , `CLIENT_ID` e `CLIENT_SECRET` con il frammento di codice seguente:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Dove:
>    - `Enter_the_Application_Id_Here` -ID dell'applicazione **(client)** dell'applicazione registrata in precedenza. Trovare questo ID nel riquadro **Panoramica** della registrazione dell'app nel portale di Azure.
>    - `Enter_the_Tenant_Id_Here` -sostituire questo valore con l' **ID tenant** o il **nome del tenant** (ad esempio, contoso.Microsoft.com).  Trovare questi valori nel riquadro **Panoramica** della registrazione dell'app nel portale di Azure.
>    - `Enter_the_Client_Secret_Here` -sostituire questo valore con il segreto client creato in precedenza. Per generare una nuova chiave, usare i **certificati & segreti** nelle impostazioni di registrazione dell'app nel portale di Azure.
>
> > [!WARNING]
> > Qualsiasi segreto di testo non crittografato nel codice sorgente costituisce un rischio maggiore per la sicurezza. Questo articolo usa un segreto client non crittografato solo per semplicità. Usare le [credenziali del certificato](active-directory-certificate-credentials.md) invece dei segreti client nelle applicazioni client riservate, in particolare le app che si intende distribuire nell'ambiente di produzione.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passaggio 3: Consenso dell'amministratore

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passaggio 4: Consenso dell'amministratore

Se si prova a eseguire l'applicazione a questo punto, si riceverà l'errore *HTTP 403 - Accesso negato*: `Insufficient privileges to complete the operation`. Questo errore si verifica perché qualsiasi *autorizzazione solo app* richiede il **consenso dell'amministratore**: un amministratore globale della directory deve concedere il consenso all'applicazione. Selezionare una delle opzioni seguenti in base al ruolo:

##### <a name="global-tenant-administrator"></a>Amministratore del tenant globale

> [!div renderon="docs"]
> Se si è un amministratore tenant globale, passare alla pagina **autorizzazioni API** nella registrazione dell'applicazione del portale di Azure e selezionare **concedi il consenso dell'amministratore per {nome tenant}** (dove {nome tenant} è il nome della directory).

> [!div renderon="portal" class="sxs-lookup"]
> Gli amministratori globali devono passare alla pagina **Autorizzazioni API** e selezionare **Concedi consenso amministratore per Immettere_il_nome_tenant_qui**
> > [!div id="apipermissionspage"]
> > [Passare alla pagina Autorizzazioni API]()

##### <a name="standard-user"></a>Utente standard

Se si è un utente standard del tenant, è necessario chiedere a un amministratore globale di concedere il **consenso dell'amministratore** per l'applicazione. A tale scopo, assegnare l'URL seguente all'amministratore:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Dove:
>> * `Enter_the_Tenant_Id_Here`: sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passaggio 4: Eseguire l'applicazione

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passaggio 5: Eseguire l'applicazione

Individuare la cartella radice dell'esempio (dove `package.json` risiede) in un prompt dei comandi o in una console. È necessario installare le dipendenze di questo esempio una volta:

```console
npm install
```

Eseguire quindi l'applicazione tramite il prompt dei comandi o la console:

```console
node . --op getUsers
```

Nell'output della console viene visualizzato un frammento JSON che rappresenta un elenco di utenti nella directory Azure AD.

## <a name="about-the-code"></a>Informazioni sul codice

Di seguito sono illustrati alcuni aspetti importanti dell'applicazione di esempio.

### <a name="msal-node"></a>MSAL Node

Il [nodo MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) è la libreria usata per l'accesso degli utenti e la richiesta di token usati per accedere a un'API protetta dalla piattaforma di identità Microsoft. Come descritto, questa Guida introduttiva richiede i token in base alle autorizzazioni dell'applicazione (usando l'identità dell'applicazione) anziché le autorizzazioni delegate. Il flusso di autenticazione usato in questo caso è noto come [flusso di credenziali client OAuth 2,0](v2-oauth2-client-creds-grant-flow.md). Per altre informazioni su come usare il nodo MSAL con le app daemon, vedere [scenario: applicazione daemon](scenario-daemon-overview.md).

 È possibile installare il nodo MSAL eseguendo il comando NPM seguente.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```javascript
const msal = require('@azure/msal-node');
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Dove: |Descrizione |
> |---------|---------|
> | `clientId` | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. Questo valore è riportato nella pagina **Panoramica** dell'app nel portale di Azure. |
> | `authority`    | Endpoint del servizio token di sicurezza per l'utente da autenticare. in genere `https://login.microsoftonline.com/{tenant}` per il cloud pubblico, dove {tenant} è il nome del tenant o l'ID tenant.|
> | `clientSecret` | È il segreto client creato per l'applicazione nel portale di Azure. |

Per altre informazioni, vedere la [documentazione di riferimento`ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Richiesta di token

Per richiedere un token con l'identità dell'app, usare il metodo `acquireTokenByClientCredential`:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Dove:| Descrizione |
> |---------|---------|
> | `tokenRequest` | Contiene gli ambiti richiesti. Per i client riservati, dovrebbe essere usato un formato simile a `{Application ID URI}/.default` per indicare che gli ambiti che vengono richiesti sono quelli definiti in modo statico nell'oggetto app impostato nel portale di Azure. Per Microsoft Graph, `{Application ID URI}` punta a `https://graph.microsoft.com`. Per le API Web personalizzate, `{Application ID URI}` è definito nella sezione **esporre un'API** nella registrazione dell'applicazione del portale di Azure. |
> | `tokenResponse` | La risposta contiene un token di accesso per gli ambiti richiesti. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo sviluppo di app daemon/console con il nodo MSAL, vedere l'esercitazione:

> [!div class="nextstepaction"]
> [Applicazione daemon che chiama le API Web](tutorial-v2-nodejs-console.md)