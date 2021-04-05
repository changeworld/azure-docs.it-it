---
title: "Esercitazione: accedere agli utenti e chiamare l'API Microsoft Graph in un'app desktop Electron | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione viene compilata un'app desktop Electron che può accedere agli utenti e usare il flusso del codice di autenticazione per ottenere un token di accesso dalla piattaforma di identità Microsoft e chiamare l'API Microsoft Graph.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: e9e36a3ed2cab05eb63168452bc8472656ab5efb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644290"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Esercitazione: accedere agli utenti e chiamare l'API Microsoft Graph in un'app desktop Electron

In questa esercitazione viene compilata un'applicazione desktop elettronico che esegue l'accesso degli utenti e chiama Microsoft Graph usando il flusso del codice di autorizzazione con PKCE. L'app desktop compilata USA [Microsoft Authentication Library (MSAL) per Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Attenersi alla procedura descritta in questa esercitazione per:

> [!div class="checklist"]
> - Registrare l'applicazione nel portale di Azure
> - Creare un progetto di app desktop Electron
> - Aggiungere la logica di autenticazione all'app
> - Aggiungere un metodo per chiamare un'API Web
> - Aggiungere i dettagli di registrazione dell'app
> - Testare l'app

## <a name="prerequisites"></a>Prerequisiti

- [Node.js](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

## <a name="register-the-application"></a>Registrare l'applicazione

Prima di tutto, completare la procedura descritta in [registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md) per registrare l'app.

Usare le impostazioni seguenti per la registrazione dell'app:

- Nome: `ElectronDesktopApp` (consigliato)
- Tipi di account supportati: **account in qualsiasi directory organizzativa (qualsiasi Azure ad directory-multi-tenant) e account Microsoft personali (ad esempio Skype, Xbox)**
- Tipo di piattaforma: **applicazioni per dispositivi mobili e desktop**
- URI di reindirizzamento: `msal://redirect`

## <a name="create-the-project"></a>Creare il progetto

Creare una cartella per ospitare l'applicazione, ad esempio *ElectronDesktopApp*.

1. Per prima cosa, passare alla cartella del progetto nel terminale e quindi eseguire i comandi `npm` seguenti:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Quindi, creare una cartella denominata *app*. All'interno di questa cartella, creare un file denominato *index.html* che verrà utilizzato come interfaccia utente. Aggiungere il codice seguente:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Successivamente, creare il file denominato *main.js* e aggiungere il codice seguente:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

Nel frammento di codice precedente viene inizializzato un oggetto finestra principale elettroni e vengono creati alcuni gestori di eventi per le interazioni con la finestra dell'elettrone. Si importano anche i parametri di configurazione, si crea un'istanza della classe *authProvider* per la gestione dell'accesso, la disconnessione e l'acquisizione dei token e si chiama l'API Microsoft Graph.

4. Nella stessa cartella (*app*) creare un altro file denominato *renderer.js* e aggiungere il codice seguente:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Infine, creare un file denominato *constants.js* in cui archiviare le costanti delle stringhe per la descrizione degli **eventi** dell'applicazione:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

È ora disponibile una semplice interfaccia utente grafica e interazioni per l'app Electron. Dopo aver completato il resto dell'esercitazione, la struttura di file e cartelle del progetto sarà simile alla seguente:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Aggiungere la logica di autenticazione all'app

Nella cartella *app* creare un file denominato *AuthProvider.js*. Questo conterrà una classe del provider di autenticazione che gestirà l'accesso, la disconnessione, l'acquisizione di token, la selezione dell'account e le attività di autenticazione correlate usando il nodo MSAL Aggiungere il codice seguente:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

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

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

Nel frammento di codice precedente è stato inizializzato per la prima volta il nodo MSAL `PublicClientApplication` passando un oggetto di configurazione ( `msalConfig` ). Si espostano quindi i `login` `logout` `getToken` metodi e che devono essere chiamati dal modulo principale (*main.js*). In `login` e `getToken` , i token di accesso e ID vengono acquisiti, rispettivamente, richiedendo prima un codice di autorizzazione e quindi scambiando questo con un token usando l'API del nodo MSAL `acquireTokenByCode` .

## <a name="add-a-method-to-call-a-web-api"></a>Aggiungere un metodo per chiamare un'API Web

Creare un altro file denominato *fetch.js*. Questo file conterrà un client HTTP Axios per eseguire chiamate REST all'API Microsoft Graph.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Aggiungere i dettagli di registrazione dell'app

Infine, creare un file di ambiente per archiviare i dettagli di registrazione dell'app che verranno usati durante l'acquisizione dei token. A tale scopo, creare un file denominato *. env* nella cartella radice dell'esempio (*ElectronDesktopApp*) e aggiungere il codice seguente:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Immettere questi dettagli con i valori ottenuti dal portale di registrazione delle app di Azure:

- `Enter_the_Tenant_Id_here` deve essere uno dei seguenti:
  - Se l'applicazione supporta *account nella directory organizzativa corrente*, sostituire questo valore con l'**ID tenant** o il **nome del tenant**. Ad esempio: `contoso.microsoft.com`.
  - Se l'applicazione supporta *account in qualsiasi directory organizzativa*, sostituire questo valore con `organizations`.
  - Se l'applicazione supporta *account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con `common`.
  - Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con `consumers`.
- `Enter_the_Application_Id_Here`: l'**ID applicazione (client)** per l'applicazione registrata.
- `Enter_the_Cloud_Instance_Id_Here`: l'istanza del cloud di Azure in cui è registrata l'applicazione.
  - Per il cloud principale (o *globale*) di Azure, immettere `https://login.microsoftonline.com/`.
  - Peri cloud **nazionali**, ad esempio Cina, è possibile trovare i valori appropriati nella pagina [Cloud nazionali](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` è l'istanza dell'API Microsoft Graph con cui l'applicazione dovrà comunicare.
  - Per l'endpoint API Microsoft Graph **globale**, sostituire entrambe le istanze di questa stringa con `https://graph.microsoft.com/`.
  - Per gli endpoint delle distribuzioni di cloud **nazionali**, vedere [Distribuzioni di cloud nazionali](/graph/deployments) nella documentazione di Microsoft Graph.

## <a name="test-the-app"></a>Testare l'app

La creazione dell'applicazione è stata completata e ora è possibile avviare l'app desktop Electron e testare la funzionalità dell'app.

1. Avviare l'app eseguendo il comando seguente dall'interno della directory radice della cartella del progetto:

```console
electron App/main.js
```

2. Nella finestra principale dell'applicazione dovrebbe essere visualizzato il contenuto del file *index.html* e il pulsante **Accedi** .

## <a name="test-sign-in-and-sign-out"></a>Testare l'accesso e la disconnessione

Al termine del caricamento del file *index.html* , selezionare **Accedi**. Viene richiesto di accedere con la piattaforma di identità Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="richiesta di accesso":::

Se si acconsente alle autorizzazioni richieste, nelle applicazioni Web viene visualizzato il nome utente, a indicare che l'accesso è riuscito:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="accesso riuscito":::

## <a name="test-web-api-call"></a>Testare la chiamata all'API Web

Dopo aver eseguito l'accesso, selezionare **See Profile** (Vedi profilo) per visualizzare le informazioni del profilo utente restituite in una risposta dalla chiamata all'API Microsoft Graph:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="informazioni sul profilo da Microsoft Graph":::

Selezionare **Leggi messaggi** per visualizzare i messaggi nell'account dell'utente. Verrà visualizzata una schermata di consenso:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="schermata di consenso per l'autorizzazione Read. mail":::

Dopo il consenso, si visualizzano i messaggi restituiti nella risposta dalla chiamata all'API Microsoft Graph:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="informazioni di posta elettronica da Microsoft Graph":::

## <a name="how-the-application-works"></a>Funzionamento dell'applicazione

Quando un utente seleziona il pulsante **Accedi** per la prima volta, `getTokenInteractive` viene chiamato il metodo Get di *AuthProvider.js* . Questo metodo reindirizza l'utente all'accesso con l'endpoint della piattaforma di *identità Microsoft* e convalida le credenziali dell'utente e quindi ottiene un **codice di autorizzazione**. Questo codice viene quindi scambiato per un token di accesso tramite l' `acquireTokenByCode` API pubblica del nodo MSAL.

A questo punto, un codice di autorizzazione protetto da PKCE viene inviato all'endpoint token protetto da CORS e viene sostituito con i token. Un token ID, un token di accesso e un token di aggiornamento vengono ricevuti dall'applicazione ed elaborati dal nodo MSAL e le informazioni contenute nei token vengono memorizzate nella cache.

Il token ID contiene informazioni di base sull'utente, ad esempio il nome visualizzato. Il token di accesso ha una durata limitata e scade dopo 24 ore. Se si prevede di usare questi token per accedere alla risorsa protetta, il server back-end *deve* convalidarlo per garantire che il token sia stato rilasciato a un utente valido per l'applicazione.

L'app desktop creata in questa esercitazione esegue una chiamata REST all'API Microsoft Graph usando un token di accesso come bearer token nell'intestazione della richiesta ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di Azure. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. Ad esempio, l'API Microsoft Graph richiede l'ambito *Mail.Read* per visualizzare la posta elettronica dell'utente.

Quando si aggiungono ambiti, agli utenti potrebbe essere richiesto di fornire un consenso aggiuntivo.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per approfondire la Node.js e lo sviluppo di applicazioni desktop Electron sulla piattaforma di identità Microsoft, vedere la serie di scenari multiparte:

> [!div class="nextstepaction"]
> [Scenario: App desktop che chiama le API Web](scenario-desktop-overview.md)
