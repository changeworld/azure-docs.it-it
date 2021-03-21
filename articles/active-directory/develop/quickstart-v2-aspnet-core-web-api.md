---
title: "Avvio rapido: Proteggere un'API Web ASP.NET Core con Microsoft Identity Platform | Azure"
titleSuffix: Microsoft identity platform
description: In questa guida di avvio rapido verrà scaricato e modificato un esempio di codice che illustra come proteggere un'API Web ASP.NET Core usando Microsoft Identity Platform per l'autorizzazione.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720832"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Guida introduttiva: proteggere un'API Web ASP.NET Core con la piattaforma di identità Microsoft

In questa Guida introduttiva viene scaricato un esempio di codice dell'API Web di ASP.NET Core e viene esaminato il modo in cui viene limitato l'accesso alle risorse solo agli account autorizzati. L'esempio supporta l'autorizzazione di account Microsoft personali e di account presenti in qualsiasi organizzazione Azure Active Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Prerequisiti
>
> - Account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Tenant di Azure Active Directory](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) o [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Passaggio 1: Registrare l'applicazione
>
> Registrare prima di tutto l'API Web nel tenant di Azure AD e aggiungere un ambito seguendo questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si ha accesso a più tenant, usare il filtro **directory + sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto per selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Per **nome** immettere un nome per l'applicazione. Ad esempio, immettere **AspNetCoreWebApi-QuickStart**. Gli utenti dell'app visualizzeranno questo nome ed è possibile modificarlo in un secondo momento.
> 1. Selezionare **Registra**.
> 1. In **Gestisci** selezionare **Esporre un'API** > **Aggiungi un ambito**. Per **URI ID applicazione** accettare l'impostazione predefinita selezionando **Salva e continua**, quindi immettere i dettagli seguenti:
>    - **Nome ambito**: `access_as_user`
>    - **Utenti che possono fornire il consenso**: **Amministratori e utenti**
>    - **Nome visualizzato per il consenso amministratore**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descrizione del consenso amministratore**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Nome visualizzato per il consenso utente**: `Access AspNetCoreWebApi-Quickstart`
>    - **Descrizione del consenso utente**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Stato**: **Enabled**
> 1. Selezionare **Aggiungi ambito** per completare l'aggiunta dell'ambito.

## <a name="step-2-download-the-aspnet-core-project"></a>Passaggio 2: Scaricare il progetto ASP.NET Core

> [!div renderon="docs"]
> [Scaricare la soluzione ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) da GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Passaggio 3: Configurare il progetto ASP.NET Core
>
> In questo passaggio, configurare il codice di esempio in modo che funzioni con la registrazione dell'app creata in precedenza.
>
> 1. Estrarre l'archivio ZIP in una cartella vicina alla radice dell'unità. Ad esempio, Estrai in *C:\Azure-Samples*.
>
>    Si consiglia di estrarre l'archivio in una directory accanto alla radice dell'unità per evitare errori causati da limitazioni di lunghezza del percorso in Windows.
>
> 1. Aprire la soluzione nella cartella *webapi* nell'editor del codice.
> 1. Aprire il *appsettings.jsnel* file e modificare il codice seguente:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Sostituire `Enter_the_Application_Id_here` con l'ID applicazione (client) dell'applicazione registrata nell'portale di Azure. È possibile trovare l'ID dell'applicazione (client) nella pagina **Panoramica** dell'app.
>    - Sostituire `Enter_the_Tenant_Info_Here` con uno dei seguenti elementi:
>       - Se l'applicazione supporta **solo gli account in questa directory aziendale**, sostituire questo valore con l'ID di directory (tenant) (Guid) o il nome del tenant (ad esempio, `contoso.onmicrosoft.com` ). È possibile trovare l'ID della directory (tenant) nella pagina **Panoramica** dell'app.
>       - Se l'applicazione supporta gli **account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations` .
>       - Se l'applicazione supporta **tutti gli utenti account Microsoft**, lasciare questo valore come `common` .
>
> Per questa guida di avvio rapido, non modificare altri valori nel file *appsettings.json*.

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

L'API Web riceve un token da un'applicazione client e il codice nell'API Web convalida il token. Questo scenario viene illustrato più dettagliatamente in [Scenario: API Web protetta](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Classe di avvio

Il middleware *Microsoft. AspNetCore. Authentication* utilizza una `Startup` classe che viene eseguita all'avvio del processo di hosting. Nel metodo `ConfigureServices` viene chiamato il metodo di estensione `AddMicrosoftIdentityWebApi` fornito da *Microsoft.Identity.Web*.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

Il metodo `AddAuthentication()` configura il servizio per l'aggiunta dell'autenticazione basata su JwtBearer.

La riga che contiene `.AddMicrosoftIdentityWebApi` aggiunge l'autorizzazione della piattaforma di identità Microsoft all'API Web. Viene quindi configurata in modo da convalidare i token di accesso emessi dalla piattaforma Microsoft Identity in base alle informazioni contenute nella `AzureAD` sezione del *appsettings.jsnel* file di configurazione:

| Chiave *appsettings.json* | Descrizione                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID applicazione (client) dell'applicazione registrata nel portale di Azure.                                                                                       |
| `Instance`             | Endpoint del servizio token di sicurezza (STS) per l'autenticazione dell'utente. Questo valore è in genere `https://login.microsoftonline.com/`, che indica il cloud pubblico di Azure. |
| `TenantId`             | Nome del tenant o dell'ID tenant (GUID) o `common` per l'accesso degli utenti con account aziendali o dell'Istituto di istruzione o account personali Microsoft.                             |

Il metodo `Configure()` contiene due metodi importanti, `app.UseAuthentication()` e `app.UseAuthorization()`, che abilitano la funzionalità denominata:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Protezione di un controller, metodo di un controller o pagina Razor

È possibile proteggere un controller o i metodi controller usando l' `[Authorize]` attributo. Questo attributo limita l'accesso al controller o ai metodi consentendo solo agli utenti autenticati. È possibile avviare una richiesta di autenticazione per accedere al controller se l'utente non è autenticato.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Convalida dell'ambito nel controller

Il codice nell'API verifica che gli ambiti necessari siano presenti nel token usando `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` :

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Il repository GitHub che contiene questo esempio di codice dell'API Web ASP.NET Core include istruzioni e altri esempi di codice che illustrano come:

- Aggiungere l'autenticazione a una nuova API Web ASP.NET Core.
- Chiamare l'API Web da un'applicazione desktop.
- Chiama API downstream come Microsoft Graph e altre API Microsoft.

> [!div class="nextstepaction"]
> [Esercitazioni sulle API Web ASP.NET Core su GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
