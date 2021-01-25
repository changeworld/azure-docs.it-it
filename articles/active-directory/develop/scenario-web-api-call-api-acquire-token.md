---
title: Ottenere un token per un'API Web che chiama le API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web che richiedono l'acquisizione di un token per l'app.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756394"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>API Web che chiama le API Web: acquisire un token per l'app

Dopo aver compilato un oggetto applicazione client, usarlo per acquisire un token che è possibile usare per chiamare un'API Web.

## <a name="code-in-the-controller"></a>Codice nel controller

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* aggiunge metodi di estensione che forniscono servizi pratici per chiamare Microsoft Graph o un'API Web downstream. Questi metodi sono descritti in dettaglio in [un'API Web che chiama API Web: chiamare un'API](scenario-web-api-call-api-call-api.md). Con questi metodi helper non è necessario acquisire manualmente un token.

Se tuttavia si desidera acquisire manualmente un token, il codice seguente illustra un esempio di utilizzo di *Microsoft. Identity. Web* a tale scopo in un controller API. Chiama un'API downstream denominata *todo*.
Per ottenere un token per chiamare l'API downstream, inserire il `ITokenAcquisition` servizio tramite l'inserimento di dipendenze nel costruttore del controller (o nel costruttore della pagina se si usa Blazer) e usarlo nelle azioni del controller, ottenendo un token per l'utente ( `GetAccessTokenForUserAsync` ) o per l'applicazione stessa ( `GetAccessTokenForAppAsync` ) nel caso di uno scenario daemon.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Per informazioni dettagliate sul `callTodoListService` metodo, vedere  [un'API Web che chiama API Web: chiamare un'API](scenario-web-api-call-api-call-api.md).

### <a name="java"></a>[Java](#tab/java)

Di seguito è riportato un esempio di codice chiamato nelle azioni dei controller API. Chiama il Microsoft Graph API downstream.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
Un'API Web Python richiede l'uso del middleware per convalidare la bearer token ricevuta dal client. L'API Web può quindi ottenere il token di accesso per un'API downstream usando la libreria MSAL Python chiamando il [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metodo.
 
Di seguito è riportato un esempio di codice che acquisisce un token di accesso usando il `acquire_token_on_behalf_of` metodo e il Framework Flask. Chiama l'API downstream, ovvero l'endpoint delle sottoscrizioni di gestione di Azure.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Avanzate Accesso alla cache dei token dell'utente connesso da app in background, API e servizi

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [chiamare un'API](scenario-web-api-call-api-call-api.md).
