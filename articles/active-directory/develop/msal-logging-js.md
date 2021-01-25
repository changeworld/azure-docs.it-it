---
title: Registrazione di errori ed eccezioni in MSAL.js
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare gli errori e le eccezioni in MSAL.js
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
ms.openlocfilehash: 8604a38bc310cc884c2b5e99efe7a47ae5e787d7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763493"
---
# <a name="logging-in-msaljs"></a>Registrazione in MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Configurare la registrazione in MSAL.js

Abilitare la registrazione in MSAL.js (JavaScript) passando un oggetto logger durante la configurazione per la creazione di un' `UserAgentApplication` istanza. Questo oggetto logger ha le proprietà seguenti:

- `localCallback`: istanza di callback che può essere fornita dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Implementare il metodo localCallback a seconda del modo in cui si intende reindirizzare i log.
- `level` (facoltativo): livello di registrazione configurabile. I livelli di registrazione supportati sono: `Error` ,, `Warning` `Info` e `Verbose` . Il valore predefinito è `Info`.
- `piiLoggingEnabled` (facoltativo): se impostato su true, registra i dati personali e aziendali. Per impostazione predefinita, questo valore è false in modo che l'applicazione non registri i dati personali. I log dei dati personali non vengono mai scritti negli output predefiniti come Console, Logcat o NSLog.
- `correlationId` (facoltativo): identificatore univoco, usato per eseguire il mapping della richiesta con la risposta a scopo di debug. Per impostazione predefinita, viene usato il GUID RFC 4122 versione 4 (128 bit).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).