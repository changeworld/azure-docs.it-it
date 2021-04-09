---
title: Registrazione di errori ed eccezioni in MSAL.NET
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare gli errori e le eccezioni in MSAL.NET
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
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954835"
---
# <a name="logging-in-msalnet"></a>Registrazione in MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Configurare la registrazione in MSAL.NET

In MSAL 3.x la registrazione viene impostata per le singole applicazioni durante la creazione dell'app usando il modificatore del generatore `.WithLogging`. Questo metodo accetta parametri facoltativi:

- `Level` consente di scegliere il livello di registrazione desiderato. Se lo si imposta su Error, verranno restituiti solo gli errori.
- `PiiLoggingEnabled` consente di registrare dati personali e aziendali se impostati su true. Per impostazione predefinita, questo parametro è impostato su false, in modo che l'applicazione non registri dati personali.
- `LogCallback` viene impostato su un delegato che esegue la registrazione. Se `PiiLoggingEnabled` è true, questo metodo riceverà i messaggi due volte: una volta con il `containsPii` parametro uguale a false e il messaggio senza dati personali e la seconda volta con il `containsPii` parametro è uguale a true e il messaggio potrebbe contenere dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.
- `DefaultLoggingEnabled` Abilita la registrazione predefinita per la piattaforma. Per impostazione predefinita, questo parametro è impostato su false. Se viene impostato su true, usa Event Tracing nelle applicazioni desktop/UWP, NSLog in iOS e logcat in Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > Vedere il [wiki di MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) per esempi di registrazione di MSAL.NET e altro ancora.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).