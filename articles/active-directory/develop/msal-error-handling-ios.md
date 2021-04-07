---
title: Gestire errori ed eccezioni in MSAL per iOS/macOS
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, le richieste di attestazioni di accesso condizionale e i tentativi in MSAL per le applicazioni iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761097"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>Gestire errori ed eccezioni in MSAL per iOS/macOS

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>Gestione degli errori in MSAL per iOS/macOS

L'elenco completo degli errori di MSAL per iOS e macOS è elencato nell'[enumerazione MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Tutti gli errori generati da MSAL vengono restituiti con il dominio `MSALErrorDomain`.

Per gli errori di sistema, MSAL restituisce l'errore `NSError` originale dall'API di sistema. Se, ad esempio, l'acquisizione di token ha esito negativo a causa della mancanza di connettività di rete, MSAL restituisce un errore con il dominio `NSURLErrorDomain` e il codice `NSURLErrorNotConnectedToInternet`.

È consigliabile gestire almeno i due errori MSAL seguenti sul lato client:

- `MSALErrorInteractionRequired`: l'utente deve eseguire una richiesta interattiva. Esistono molte condizioni che possono causare questo errore, ad esempio una sessione di autenticazione scaduta o la necessità di requisiti di autenticazione aggiuntivi. Chiamare l'API di acquisizione token interattiva MSAL per correggere l'errore. 

- `MSALErrorServerDeclinedScopes`: alcuni o tutti gli ambiti sono stati rifiutati. Decidere se continuare solo con gli ambiti concessi oppure arrestare il processo di accesso.

> [!NOTE]
> L'enumerazione `MSALInternalError` deve essere usata solo a scopi di riferimento e debug. Non tentare di gestire automaticamente questi errori in fase di esecuzione. Se l'app riscontra uno degli errori che rientrano in `MSALInternalError`, è possibile che venga visualizzato un messaggio utente generico che spiega ciò che è accaduto.

Ad esempio, `MSALInternalErrorBrokerResponseNotReceived` significa che l'utente non ha completato l'autenticazione e che è tornato manualmente all'app. In questo caso, l'app dovrebbe mostrare un messaggio di errore generico che informa che l'autenticazione non è stata completata e suggerisce di provare a eseguire di nuovo l'autenticazione.

Il codice di esempio Objective-C seguente illustra le procedure consigliate per la gestione di alcune condizioni di errore comuni.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL per iOS e macOS consente di richiedere attestazioni specifiche negli scenari di acquisizione di token interattiva e invisibile all'utente.

Per richiedere attestazioni personalizzate, specificare `claimsRequest` in `MSALSilentTokenParameters` o `MSALInteractiveTokenParameters`.

Per altre informazioni, vedere [Richiedere attestazioni personalizzate usando MSAL per iOS e macOS](request-custom-claims.md).

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare [la registrazione in MSAL per iOS/MacOS](msal-logging-ios.md) per facilitare la diagnosi e il debug dei problemi.
