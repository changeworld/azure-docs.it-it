---
title: Registrazione di errori ed eccezioni in MSAL per iOS/macOS
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare gli errori e le eccezioni in MSAL per iOS/macOS
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763492"
---
# <a name="logging-in-msal-for-iosmacos"></a>Registrazione in MSAL per iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL per la registrazione di iOS e macOS-ObjC

Impostare un callback per acquisire la registrazione MSAL e incorporarla nella registrazione dell'applicazione. La firma per il callback ha un aspetto simile al seguente:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Ad esempio:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Dati personali

Per impostazione predefinita, MSAL non acquisisce né registra i dati personali. La libreria consente agli sviluppatori di app di attivare questa operazione tramite una proprietà nella classe MSALLogger. Attivando `pii.Enabled` , l'app si assume la responsabilità di gestire in modo sicuro i dati altamente sensibili e i requisiti normativi.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Livelli di registrazione

Per impostare il livello di registrazione quando si registra usando MSAL per iOS e macOS, usare uno dei valori seguenti:

|Level  |Descrizione |
|---------|---------|
| `MSALLogLevelNothing`| Disabilitare tutte le registrazioni |
| `MSALLogLevelError` | Livello predefinito, stampa le informazioni solo quando si verificano errori |
| `MSALLogLevelWarning` | Avvisi |
| `MSALLogLevelInfo` |  Punti di ingresso della libreria, con parametri e varie operazioni Keychain |
|`MSALLogLevelVerbose`     |  Tenere traccia delle API |

Ad esempio:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formato messaggi registro

La parte del messaggio dei messaggi di log di MSAL è nel formato `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ad esempio:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornire ID di correlazione e timestamp è utile per tenere traccia dei problemi. Le informazioni sul timestamp e sull'ID di correlazione sono disponibili nel messaggio del log. L'unica posizione affidabile per recuperarli è MSAL i messaggi di registrazione.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL per la registrazione di iOS e macOS-Swift

Impostare un callback per acquisire la registrazione MSAL e incorporarla nella registrazione dell'applicazione. La firma (rappresentata in Objective-C) per il callback ha un aspetto simile al seguente:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Ad esempio:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Dati personali

Per impostazione predefinita, MSAL non acquisisce né registra i dati personali. La libreria consente agli sviluppatori di app di attivare questa operazione tramite una proprietà nella classe MSALLogger. Attivando `pii.Enabled` , l'app si assume la responsabilità di gestire in modo sicuro i dati altamente sensibili e i requisiti normativi.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Livelli di registrazione

Per impostare il livello di registrazione quando si registra usando MSAL per iOS e macOS, usare uno dei valori seguenti:

|Level  |Descrizione |
|---------|---------|
| `MSALLogLevelNothing`| Disabilitare tutte le registrazioni |
| `MSALLogLevelError` | Livello predefinito, stampa le informazioni solo quando si verificano errori |
| `MSALLogLevelWarning` | Avvisi |
| `MSALLogLevelInfo` |  Punti di ingresso della libreria, con parametri e varie operazioni Keychain |
|`MSALLogLevelVerbose`     |  Tenere traccia delle API |

Ad esempio:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato messaggi registro

La parte del messaggio dei messaggi di log di MSAL è nel formato `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ad esempio:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornire ID di correlazione e timestamp è utile per tenere traccia dei problemi. Le informazioni sul timestamp e sull'ID di correlazione sono disponibili nel messaggio del log. L'unica posizione affidabile per recuperarli è MSAL i messaggi di registrazione.

---

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).