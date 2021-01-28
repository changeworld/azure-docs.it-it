---
title: Registrazione di errori ed eccezioni in MSAL per Android.
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare gli errori e le eccezioni in MSAL per Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954885"
---
# <a name="logging-in-msal-for-android"></a>Registrazione in MSAL per Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Registrazione in MSAL per Android con Java

Attivare la registrazione durante la creazione dell'app creando un callback di registrazione. Il callback accetta i parametri seguenti:

- `tag` stringa passata al callback dalla libreria. È associato alla voce di log e può essere usato per ordinare i messaggi di registrazione.
- `logLevel` consente di scegliere il livello di registrazione desiderato. I livelli di registrazione supportati sono: `Error` ,, `Warning` `Info` e `Verbose` .
- `message` contenuto della voce di log.
- `containsPII` Specifica se i messaggi contenenti dati personali o i dati aziendali vengono registrati. Per impostazione predefinita, questo valore è impostato su false, in modo che l'applicazione non registri i dati personali. Se `containsPII` è `true` , questo metodo riceverà i messaggi due volte: una volta con il `containsPII` parametro impostato su `false` e `message` senza dati personali e una seconda volta con il `containsPii` parametro impostato su `true` e il messaggio potrebbe contenere dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Per impostazione predefinita, il logger MSAL non acquisisce informazioni personali o informazioni identificabili dall'organizzazione.
Per abilitare la registrazione di informazioni personali o informazioni identificabili dall'organizzazione:

```java
Logger.getInstance().setEnablePII(true);
```

Per disabilitare la registrazione dei dati personali e delle organizzazioni:

```java
Logger.getInstance().setEnablePII(false);
```

Per impostazione predefinita, la registrazione in logcat è disabilitata. Per abilitare:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).