---
title: "Esercitazione: chiamare Microsoft Graph in un'app console Node.js | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione viene compilata un'app console per la chiamata di Microsoft Graph a un'app console Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645790"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Esercitazione: chiamare l'API Microsoft Graph in un'app console Node.js

In questa esercitazione viene compilata un'app console che chiama Microsoft Graph API usando la propria identità. L'app console compilata USA [Microsoft Authentication Library (MSAL) per Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Attenersi alla procedura descritta in questa esercitazione per:

> [!div class="checklist"]
> - Registrare l'applicazione nel portale di Azure
> - Creare un progetto di app console Node.js
> - Aggiungere la logica di autenticazione all'app
> - Aggiungere i dettagli di registrazione dell'app
> - Aggiungere un metodo per chiamare un'API Web
> - Testare l'app

## <a name="prerequisites"></a>Prerequisiti

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

## <a name="register-the-application"></a>Registrare l'applicazione

Prima di tutto, completare la procedura descritta in [registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md) per registrare l'app.

Usare le impostazioni seguenti per la registrazione dell'app:

- Nome: `NodeConsoleApp` (consigliato)
- Tipi di account supportati: **solo account in questa directory organizzativa**
- Autorizzazioni API: **API Microsoft**  >  **Microsoft Graph** le  >  **autorizzazioni dell'applicazione** > `User.Read.All`
- Segreto client: `*********` (registrare questo valore per l'uso in un passaggio successivo. viene visualizzato solo una volta)

## <a name="create-the-project"></a>Creare il progetto

Creare una cartella per ospitare l'applicazione, ad esempio *NodeConsoleApp*.

1. Per prima cosa, passare alla directory del progetto nel terminale e quindi eseguire i comandi NPM seguenti:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Successivamente, creare una cartella denominata *bin*. Quindi, all'interno di questa cartella, creare un file denominato *index.js* e aggiungere il codice seguente:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Questo file fa riferimento a altri due moduli del nodo: *auth.js* che contiene un'implementazione del nodo MSAL per l'acquisizione dei token di accesso e *fetch.js* che contiene un metodo per eseguire una richiesta HTTP per Microsoft Graph API con un token di accesso. Dopo aver completato il resto dell'esercitazione, la struttura di file e cartelle del progetto sarà simile alla seguente:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Aggiungi logica di autenticazione

All'interno della cartella *bin* creare un altro file denominato *auth.js* e aggiungere il codice seguente per acquisire un token di accesso da presentare quando si chiama l'API Microsoft Graph.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

Nel frammento di codice precedente si crea prima un oggetto di configurazione (*msalConfig*) e lo si passa per inizializzare un [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)MSAL. Si crea quindi un metodo per acquisire i token tramite le **credenziali client** e infine esporre questo modulo per poter accedere a *main.js*. I parametri di configurazione in questo modulo vengono creati da un file di ambiente, che verrà creato nel passaggio successivo.

## <a name="add-app-registration-details"></a>Aggiungere i dettagli di registrazione dell'app

Creare un file di ambiente per archiviare i dettagli di registrazione dell'app che verranno usati durante l'acquisizione dei token. A tale scopo, creare un file denominato *. env* nella cartella radice dell'esempio (*NodeConsoleApp*) e aggiungere il codice seguente:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Immettere questi dettagli con i valori ottenuti dal portale di registrazione delle app di Azure:

- `Enter_the_Tenant_Id_here` deve essere uno dei seguenti:
  - Se l'applicazione supporta *account nella directory organizzativa corrente*, sostituire questo valore con l'**ID tenant** o il **nome del tenant**. Ad esempio: `contoso.microsoft.com`.
  - Se l'applicazione supporta *account in qualsiasi directory organizzativa*, sostituire questo valore con `organizations`.
  - Se l'applicazione supporta *account in qualsiasi directory organizzativa e account Microsoft personali*, sostituire questo valore con `common`.
  - Per limitare il supporto ai *soli account Microsoft personali*, sostituire questo valore con `consumers`.
- `Enter_the_Application_Id_Here`: l'**ID applicazione (client)** per l'applicazione registrata.
- `Enter_the_Cloud_Instance_Id_Here`: l'istanza del cloud di Azure in cui è registrata l'applicazione.
  - Per il cloud principale (o *globale*) di Azure, immettere `https://login.microsoftonline.com`.
  - Peri cloud **nazionali**, ad esempio Cina, è possibile trovare i valori appropriati nella pagina [Cloud nazionali](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` è l'istanza dell'API Microsoft Graph con cui l'applicazione dovrà comunicare.
  - Per l'endpoint API Microsoft Graph **globale**, sostituire entrambe le istanze di questa stringa con `https://graph.microsoft.com`.
  - Per gli endpoint delle distribuzioni di cloud **nazionali**, vedere [Distribuzioni di cloud nazionali](/graph/deployments) nella documentazione di Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Aggiungere un metodo per chiamare un'API Web

All'interno della cartella *bin* creare un altro file denominato *fetch.js* e aggiungere il codice seguente per eseguire chiamate REST all'API Microsoft Graph:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Qui `callApi` viene usato il metodo per eseguire una richiesta HTTP `GET` su una risorsa protetta che richiede un token di accesso. La richiesta restituisce quindi il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. La risorsa protetta è la Microsoft Graph [endpoint Users](/graph/api/user-list) API che visualizza gli utenti nel tenant in cui è registrata l'app.

## <a name="test-the-app"></a>Testare l'app

La creazione dell'applicazione è stata completata e ora è possibile testare la funzionalità dell'app.

Avviare l'app console Node.js eseguendo il comando seguente dalla radice della cartella del progetto:

```console
node . --op getUsers
```

Questa operazione dovrebbe generare una risposta JSON dall'API Microsoft Graph e dovrebbe essere visualizzata una matrice di oggetti utente nella console:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Interfaccia della riga di comando che visualizza la risposta Graph":::

## <a name="how-the-application-works"></a>Funzionamento dell'applicazione

Questa applicazione usa la [concessione di credenziali client OAuth 2,0](./v2-oauth2-client-creds-grant-flow.md). Questo tipo di concessione viene comunemente usato per le interazioni da server a server che devono essere eseguite in background, senza l'interazione immediata con un utente. Il flusso di concessione delle credenziali consente a un servizio Web (client riservato) di usare le proprie credenziali, anziché rappresentare un utente, per eseguire l'autenticazione quando si chiama un altro servizio Web. Il tipo di applicazioni supportate con questo modello di autenticazione è in genere **daemon** o **account di servizio**.

L'ambito da richiedere per un flusso di credenziali client è il nome della risorsa seguita da `/.default` . Questa notazione indica Azure Active Directory (Azure AD) di utilizzare le autorizzazioni a livello di applicazione dichiarate in modo statico durante la registrazione dell'applicazione. Inoltre, queste autorizzazioni API devono essere concesse da un **amministratore tenant**.

## <a name="next-steps"></a>Passaggi successivi

Per approfondire la Node.js lo sviluppo di applicazioni console sulla piattaforma Microsoft Identity, vedere la serie di scenari multiparte:

> [!div class="nextstepaction"]
> [Scenario: applicazione daemon](scenario-daemon-overview.md)