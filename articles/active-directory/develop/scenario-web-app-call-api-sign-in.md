---
title: Rimuovere gli account dalla cache dei token durante la disconnessione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come rimuovere un account dalla cache dei token durante la disconnessione
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fc271dfa9edbedac8527009dd2b2180b7c5e7cd
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756260"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Un'app Web che chiama le API Web: rimuovere gli account dalla cache dei token per la disconnessione globale

Si è appreso come aggiungere l'accesso all'app Web nell'app Web per l'accesso [degli utenti: accesso e disconnessione](scenario-web-app-sign-user-sign-in.md).

La disconnessione è diversa per un'app Web che chiama le API Web. Quando l'utente si disconnette dall'applicazione o da qualsiasi applicazione, è necessario rimuovere i token associati a tale utente dalla cache dei token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercettare il callback dopo Single Sign-out

Per cancellare la voce della cache dei token associata all'account che ha effettuato la disconnessione, l'applicazione può intercettare l' `logout` evento After. Le app Web archiviano i token di accesso per ogni utente in una cache dei token. Intercettando il callback after `logout` , l'applicazione Web può rimuovere l'utente dalla cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web si occupa dell'implementazione di disconnessione. Per informazioni dettagliate [, vedere codice sorgente Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L'esempio ASP.NET non rimuove gli account dalla cache nella disconnessione globale.

# <a name="java"></a>[Java](#tab/java)

L'esempio Java non rimuove gli account dalla cache nella disconnessione globale.

# <a name="python"></a>[Python](#tab/python)

L'esempio Python non rimuove gli account dalla cache nella disconnessione globale.

---

## <a name="next-steps"></a>Passaggi successivi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Passare all'articolo successivo in questo scenario, [acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Passare all'articolo successivo in questo scenario, [acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Passare all'articolo successivo in questo scenario, [acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Passare all'articolo successivo in questo scenario, [acquisire un token per l'app Web](./scenario-web-app-call-api-acquire-token.md?tabs=python).

---