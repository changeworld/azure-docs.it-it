---
title: "Esercitazione: utenti di accesso in un'app Web Node.js & Express | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione si aggiunge il supporto per gli utenti che accedono a un'app Web.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 01/12/2021
ms.author: v-doeris
ms.openlocfilehash: 1c11a5ecc2d1a9c2e83e9ebd7cc8aa85caa72b70
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562083"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Esercitazione: utenti di accesso in un'app Web Node.js & Express

In questa esercitazione viene compilata un'app Web per l'accesso degli utenti. L'app Web compilata USA [Microsoft Authentication Library (MSAL) per node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Attenersi alla procedura descritta in questa esercitazione per:

> [!div class="checklist"]
> - Registrare l'applicazione nel portale di Azure
> - Creare un progetto di app Web Express
> - Installare i pacchetti della libreria di autenticazione
> - Aggiungere i dettagli di registrazione dell'app
> - Aggiungere il codice per l'accesso utente
> - Testare l'app

## <a name="prerequisites"></a>Prerequisiti

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) o un altro editor di codice

## <a name="register-the-application"></a>Registrare l'applicazione

Prima di tutto, completare la procedura descritta in [registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md) per registrare l'app.

Usare le impostazioni seguenti per la registrazione dell'app:

- Nome: `ExpressWebApp` (consigliato)
- Tipi di account supportati: **account in qualsiasi directory organizzativa (qualsiasi Azure ad directory-multi-tenant) e account Microsoft personali (ad esempio Skype, Xbox)**
- Tipo piattaforma: **Web**
- URI di reindirizzamento: `http://localhost:3000/redirect`
- Segreto client: `*********` (registrare questo valore per l'uso in un passaggio successivo. viene visualizzato solo una volta)

## <a name="create-the-project"></a>Creare il progetto

Creare una cartella per ospitare l'applicazione, ad esempio *ExpressWebApp*.

1. Per prima cosa, passare alla cartella del progetto nel terminale e quindi eseguire i comandi `npm` seguenti:

```console
    npm init -y
    npm install --save express
```

2. Successivamente, creare il file denominato *index.js* e aggiungere il codice seguente:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');
    
    const SERVER_PORT = process.env.PORT || 3000;
    
    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

A questo punto è disponibile un semplice server Web in esecuzione sulla porta 3000. La struttura di file e cartelle del progetto dovrebbe essere simile alla seguente:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Installare la libreria di autenticazione

Individuare la radice della directory del progetto in un terminale e installare il pacchetto del nodo MSAL tramite NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Aggiungere i dettagli di registrazione dell'app

Nel file *index.js* creato in precedenza, aggiungere il codice seguente:

```JavaScript
    // Before running the sample, you will need to replace the values in the config, 
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
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
- `Enter_the_Client_secret`: Sostituire questo valore con il segreto client creato in precedenza. Per generare una nuova chiave, usare i **certificati & segreti** nelle impostazioni di registrazione dell'app nel portale di Azure.

> [!WARNING]
> Qualsiasi segreto di testo non crittografato nel codice sorgente costituisce un rischio maggiore per la sicurezza. Questo articolo usa un segreto client non crittografato solo per semplicità. Usare le [credenziali del certificato](active-directory-certificate-credentials.md) invece dei segreti client nelle applicazioni client riservate, in particolare le app che si intende distribuire nell'ambiente di produzione.

## <a name="add-code-for-user-login"></a>Aggiungere il codice per l'accesso utente

Nel file *index.js* creato in precedenza, aggiungere il codice seguente:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);
    
    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };
    
        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });
    
    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };
    
        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Test di accesso

La creazione dell'applicazione è stata completata e ora è possibile testare la funzionalità dell'app.

1. Avviare l'app console Node.js eseguendo il comando seguente dalla radice della cartella del progetto:

```console
   node index.js
```

2. Aprire una finestra del browser e passare a `http://localhost:3000`. Verrà visualizzata una schermata di accesso:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Visualizzazione della schermata di accesso Azure AD":::

3. Dopo aver immesso le credenziali, viene visualizzata una schermata di consenso in cui viene chiesto di approvare le autorizzazioni per l'app.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Visualizzazione della schermata di consenso Azure AD":::

## <a name="how-the-application-works"></a>Funzionamento dell'applicazione

In questa esercitazione è stato inizializzato un oggetto [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) del nodo MSAL passando un oggetto di configurazione (*msalConfig*) che contiene i parametri ottenuti dalla registrazione dell'app Azure ad in portale di Azure. L'app Web creata usa il [flusso di concessione del codice di autorizzazione OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) per gli utenti di accesso e ottenere i token di accesso e ID.

## <a name="next-steps"></a>Passaggi successivi

Per approfondire la Node.js & lo sviluppo di applicazioni Web Express nella piattaforma di identità Microsoft, vedere la serie di scenari multiparte:

> [!div class="nextstepaction"]
> [Scenario: App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)
