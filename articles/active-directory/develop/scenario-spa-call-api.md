---
title: Creare un'app a singola pagina che chiama un'API Web
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'applicazione a singola pagina che chiama un'API Web
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4dd4a40233fc1c030581fd1ae2827061435a0ab3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753546"
---
# <a name="single-page-application-call-a-web-api"></a>Applicazione a pagina singola: chiamare un'API Web

È consigliabile chiamare il `acquireTokenSilent` metodo per acquisire o rinnovare un token di accesso prima di chiamare un'API Web. Una volta che si dispone di un token, è possibile chiamare un'API Web protetta.

## <a name="call-a-web-api"></a>Chiamare un'API Web

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Usare il token di accesso acquisito come portatore in una richiesta HTTP per chiamare qualsiasi API Web, ad esempio Microsoft Graph API. Ad esempio:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

Il wrapper angolare MSAL sfrutta l'intercettore HTTP per acquisire automaticamente i token di accesso in modo invisibile all'utente e associarli alle richieste HTTP alle API. Per altre informazioni, vedere [acquisire un token per chiamare un'API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [passare alla produzione](scenario-spa-production.md).