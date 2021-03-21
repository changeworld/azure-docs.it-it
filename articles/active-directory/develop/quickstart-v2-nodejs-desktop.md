---
title: "Guida introduttiva: chiamare Microsoft Graph da un'app desktop Node.js | Azure"
titleSuffix: Microsoft identity platform
description: Questa Guida introduttiva illustra come un'applicazione desktop Node.js Electron può accedere agli utenti e ottenere un token di accesso per chiamare un'API protetta da un endpoint della piattaforma di identità Microsoft
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653270"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Guida introduttiva: acquisire un token di accesso e chiamare l'API Microsoft Graph da un'app desktop Electron

In questa Guida introduttiva viene scaricato ed eseguito un esempio di codice che illustra come un'applicazione desktop Electron può accedere agli utenti e acquisire i token di accesso per chiamare l'API Microsoft Graph.

Questa Guida introduttiva usa [Microsoft Authentication Library per Node.js (nodo MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) con il [flusso del codice di autorizzazione con PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrare e scaricare l'applicazione di esempio
>
> Per iniziare, seguire questa procedura.
>
> #### <a name="step-1-register-the-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `msal-node-desktop`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Selezionare **Registra** per creare l'applicazione.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Selezionare **Aggiungi una piattaforma** > **Applicazioni per dispositivi mobili e desktop**.
> 1. Nella sezione **URI di reindirizzamento** immettere `msal://redirect`.
> 1. Selezionare **Configura**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passaggio 1: configurare l'applicazione in portale di Azure
> Per il funzionamento dell'esempio di codice per questa Guida introduttiva, è necessario aggiungere un URL di risposta come **MSAL://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-windows-desktop/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-electron-sample-project"></a>Passaggio 2: scaricare il progetto di esempio Electron

> [!div renderon="docs"]
> [Scaricare il codice di esempio](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Passaggio 3: configurare il progetto di esempio Electron
>
> 1. Estrarre il file zip in una cartella locale vicina alla radice del disco, ad esempio *C:/Azure-Samples*.
> 1. Modificare *. env* e sostituire i valori dei campi `TENANT_ID` e `CLIENT_ID` con il frammento di codice seguente:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Dove:
>    - `Enter_the_Application_Id_Here` è l'**ID applicazione (client)** per l'applicazione registrata.
>    - `Enter_the_Tenant_Id_Here`: sostituire questo valore con l'**ID tenant** o il **nome del tenant** (ad esempio, contoso.microsoft.com)
>
> > [!TIP]
> > Per trovare i valori di **ID applicazione (client)** e **ID della directory (tenant)** , passare alla pagina **Panoramica** dell'app nel portale di Azure.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passaggio 4: Eseguire l'applicazione

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Passaggio 4: Eseguire l'applicazione

È necessario installare le dipendenze di questo esempio una volta:

```console
npm install
```

Eseguire quindi l'applicazione tramite il prompt dei comandi o la console:

```console
npm start
```

Verrà visualizzata l'interfaccia utente dell'applicazione con un pulsante **Accedi** .

## <a name="about-the-code"></a>Informazioni sul codice

Di seguito sono illustrati alcuni aspetti importanti dell'applicazione di esempio.

### <a name="msal-node"></a>MSAL Node

Il [nodo MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) è la libreria usata per l'accesso degli utenti e la richiesta di token usati per accedere a un'API protetta dalla piattaforma di identità Microsoft. Per altre informazioni su come usare il nodo MSAL con le app desktop, vedere [questo articolo](scenario-desktop-overview.md).

È possibile installare il nodo MSAL eseguendo il comando NPM seguente.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per il nodo MSAL aggiungendo il codice seguente:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Dove: |Descrizione |
> |---------|---------|
> | `clientId` | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. Questo valore è riportato nella pagina **Panoramica** dell'app nel portale di Azure. |
> | `authority`    | Endpoint del servizio token di sicurezza per l'utente da autenticare. in genere `https://login.microsoftonline.com/{tenant}` per il cloud pubblico, dove {tenant} è il nome del tenant o l'ID tenant.|

### <a name="requesting-tokens"></a>Richiesta di token

Nel primo tratto del codice di autorizzazione flusso con PKCE, preparare e inviare una richiesta di codice di autorizzazione con i parametri appropriati. Quindi, nella seconda parte del flusso, attendere la risposta del codice di autorizzazione. Una volta ottenuto il codice, scambiarlo per ottenere un token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Dove:| Descrizione |
> |---------|---------|
> | `authWindow` | Finestra degli elettroni corrente in corso. |
> | `tokenRequest` | Contiene gli ambiti richiesti, ad esempio `"User.Read"` per Microsoft Graph o `"api://<Application ID>/access_as_user"` per le API Web personalizzate. |

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo sviluppo di app desktop Electron con il nodo MSAL, vedere l'esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: accedere agli utenti e chiamare l'API Microsoft Graph in un'app desktop Electron](tutorial-v2-nodejs-desktop.md)