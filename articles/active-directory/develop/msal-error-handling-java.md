---
title: Gestire errori ed eccezioni in MSAL4J
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, le richieste di attestazioni di accesso condizionale e i tentativi nelle applicazioni MSAL4J.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760640"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Gestire errori ed eccezioni in MSAL per Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Gestione degli errori in MSAL per Java

In MSAL per Java esistono tre tipi di eccezioni: `MsalClientException`, `MsalServiceException` e `MsalInteractionRequiredException`, che ereditano tutte da `MsalException`.

- `MsalClientException`: viene generata quando si verifica un errore locale nella libreria o nel dispositivo.
- `MsalServiceException`: viene generata quando il servizio token di sicurezza restituisce una risposta di errore o si verifica un altro errore di rete.
- `MsalInteractionRequiredException`: viene generata quando è necessaria l'interazione dell'interfaccia utente affinché l'autenticazione abbia esito positivo.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` espone le intestazioni HTTP restituite nelle richieste al servizio token di sicurezza. Accedervi tramite `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Uno dei codici di stato comuni restituiti da MSAL per Java quando si chiama `AcquireTokenSilently()` è `InvalidGrantError`. Questo significa che è necessaria un'interazione utente aggiuntiva prima di poter rilasciare il token di autenticazione. L'applicazione deve chiamare nuovamente la libreria di autenticazione, ma in modalità interattiva inviando `AuthorizationCodeParameters` o `DeviceCodeParameters` per le applicazioni client pubbliche.

Nella maggior parte dei casi, quando `AcquireTokenSilently` ha esito negativo, è perché la cache dei token non contiene token corrispondenti alla richiesta. I token di accesso scadono entro un'ora e `AcquireTokenSilently` tenterà di ottenerne uno nuovo in base a un token di aggiornamento. In termini di OAuth2, si tratta del flusso Token di aggiornamento. Questo flusso può anche avere esito negativo per diversi motivi, ad esempio quando un amministratore del tenant configura criteri di accesso più rigorosi.

Alcune condizioni che generano questo errore sono facili da risolvere per gli utenti. Ad esempio, potrebbe essere necessario accettare le condizioni per l'utilizzo o la richiesta non può essere soddisfatta con la configurazione corrente perché il computer deve connettersi a una rete aziendale specifica.

MSAL espone un campo `reason`, che può essere usato per offrire un'esperienza utente migliore. Ad esempio, il campo `reason` può indicare all'utente che la password è scaduta o che è necessario fornire il consenso per l'uso di alcune risorse. I valori supportati sono parte dell'enumerazione `InteractionRequiredExceptionReason`:

| Motivo | Significato | Gestione consigliata |
|---------|-----------|-----------------------------|
| `BasicAction` | La condizione può essere risolta tramite l'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare `acquireToken` con parametri interattivi. |
| `AdditionalAction` | La condizione può essere risolta tramite un'interazione correttiva aggiuntiva con il sistema, all'esterno del flusso di autenticazione interattiva. | Chiamare `acquireToken` con i parametri interattivi per visualizzare un messaggio in cui viene illustrata l'azione correttiva da adottare. L'app chiamante può scegliere di nascondere i flussi che richiedono un'azione aggiuntiva se è improbabile che l'utente completi l'azione correttiva. |
| `MessageOnly` | Non è possibile risolvere la condizione in questo momento. Avviare il flusso di autenticazione interattiva per visualizzare un messaggio che spiega la condizione. | Chiamare `acquireToken` con i parametri interattivi per visualizzare un messaggio che spiega la condizione. `acquireToken` restituirà l'errore `UserCanceled` dopo che l'utente legge il messaggio e chiude la finestra. L'app può scegliere di nascondere i flussi che generano un messaggio se l'utente non può trarre vantaggio dal messaggio. |
| `ConsentRequired`| Il consenso dell'utente è mancante o è stato revocato. |Chiamare `acquireToken` con parametri interattivi in modo che l'utente possa fornire il consenso. |
| `UserPasswordExpired` | La password dell'utente è scaduta. | Chiamare `acquireToken` con il parametro Interactive in modo che l'utente possa reimpostare la password. |
| `None` |  Sono fornite ulteriori informazioni dettagliate. La condizione può essere risolta tramite l'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare `acquireToken` con parametri interattivi. |

### <a name="code-example"></a>Esempio di codice

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passaggi successivi

Si consiglia di abilitare [la registrazione in MSAL per Java](msal-logging-java.md) per facilitare la diagnosi e il debug dei problemi.
