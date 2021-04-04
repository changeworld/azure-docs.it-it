---
title: Cancellare la cache dei token (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come cancellare la cache dei token utilizzando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064760"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Cancellare la cache dei token usando MSAL.NET

Quando si [acquisisce un token di accesso](msal-acquire-cache-tokens.md) tramite Microsoft Authentication Library per .net (MSAL.NET), il token viene memorizzato nella cache. Quando l'applicazione richiede un token, deve prima chiamare il `AcquireTokenSilent` metodo per verificare se un token accettabile si trova nella cache. 

La cancellazione della cache viene eseguita rimuovendo gli account dalla cache. Questa operazione, tuttavia, non rimuove il cookie di sessione, che si trova nel browser.  Nell'esempio seguente viene creata un'istanza di un'applicazione client pubblica, vengono ottenuti gli account per l'applicazione e vengono rimossi gli account.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Per altre informazioni su come acquisire e memorizzare nella cache i token, leggere [acquisire un token di accesso](msal-acquire-cache-tokens.md).
