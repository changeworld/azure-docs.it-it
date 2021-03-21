---
title: Gestire errori ed eccezioni in MSAL.js
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, le richieste di attestazioni di accesso condizionale e i tentativi nelle applicazioni MSAL.js.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761338"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Gestire errori ed eccezioni in MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Gestione degli errori in MSAL.js

MSAL.js fornisce oggetti errore che riepilogano e classificano i diversi tipi di errori comuni. Fornisce inoltre un'interfaccia per accedere a dettagli specifici degli errori, ad esempio i messaggi di errore per gestirli in modo appropriato.

### <a name="error-object"></a>Oggetto errore

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Estendendo la classe Error, è possibile accedere alle proprietà seguenti:
- `AuthError.message`:  uguale a `errorMessage`.
- `AuthError.stack`: analisi dello stack per gli errori generati.

### <a name="error-types"></a>Tipi di errore

Sono disponibili i tipi di errore seguenti:

- `AuthError`: classe Error di base per la libreria MSAL.js, usata anche per gli errori imprevisti.

- `ClientAuthError`: classe di errore che indica un problema con l'autenticazione client. La maggior parte degli errori che provengono dalla libreria saranno ClientAuthError. Questi errori possono essere causati da chiamate a un metodo di accesso quando l'accesso è già in corso, utenti che annullano l'accesso e così via.

- `ClientConfigurationError`: classe di errore che estende l'errore `ClientAuthError` generato prima che le richieste vengano effettuate quando i parametri di configurazione dell'utente specificato non sono corretti o sono mancanti.

- `ServerError`: classe di errore che rappresenta le stringhe di errore inviate dal server di autenticazione. Può trattarsi di errori come parametri o formati della richiesta non validi oppure di eventuali altri errori che impediscono al server di eseguire l'autenticazione o l'autorizzazione dell'utente.

- `InteractionRequiredAuthError`: classe di errore che estende `ServerError` per rappresentare gli errori del server che richiedono una chiamata interattiva. Questo errore viene generato da `acquireTokenSilent` se è necessario che l'utente interagisca con il server per fornire le credenziali o il consenso per l'autenticazione/autorizzazione. I codici errore includono `"interaction_required"`, `"login_required"` e `"consent_required"`.

Per la gestione degli errori nei flussi di autenticazione con metodi di reindirizzamento (`loginRedirect`, `acquireTokenRedirect`), sarà necessario registrare il callback che viene chiamato con esito positivo o negativo dopo il reindirizzamento tramite il metodo `handleRedirectCallback()` come indicato di seguito:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

I metodi per l'esperienza popup (`loginPopup`, `acquireTokenPopup`) restituiscono promesse, pertanto è possibile usare il modello per le promesse (.then e .catch) per gestirli, come illustrato di seguito:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Errori che richiedono l'interazione

Quando si tenta di usare un metodo non interattivo per acquisire un token, ad esempio `acquireTokenSilent`, viene restituito un errore, ma per MSAL non è stato possibile eseguire l'operazione in modo invisibile all'utente.

Le possibili cause sono:

- è necessario eseguire l'accesso
- è necessario fornire il consenso
- è necessario usare un'esperienza con autenticazione a più fattori.

La correzione consiste nel chiamare un metodo interattivo, ad esempio `acquireTokenPopup` o `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Quando i token vengono recuperati in modalità invisibile all'utente (tramite `acquireTokenSilent`) usando MSAL.js, l'applicazione potrebbe riscontrare errori quando una [richiesta di verifica delle attestazioni di accesso condizionale](../azuread-dev/conditional-access-dev-guide.md), ad esempio criteri di autenticazione a più fattori, è necessaria per un'API a cui si sta tentando di accedere.

Il modello per gestire questo errore consiste nell'effettuare una chiamata interattiva per acquisire il token in MSAL.js, ad esempio `acquireTokenPopup` o `acquireTokenRedirect`, come nell'esempio seguente:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

L'acquisizione del token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, è possibile ricevere una richiesta di attestazioni nell'errore dall'API. In questo caso, è possibile passare le attestazioni restituite nell'errore al campo `claimsRequest` della classe `AuthenticationParameters.ts` per soddisfare i criteri appropriati. 

Per informazioni dettagliate, vedere [Richiesta di attestazioni aggiuntive](active-directory-optional-claims.md).


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passaggi successivi

Si consiglia di abilitare la [registrazione in MSAL.js](msal-logging-js.md) per facilitare la diagnosi e il debug dei problemi.
