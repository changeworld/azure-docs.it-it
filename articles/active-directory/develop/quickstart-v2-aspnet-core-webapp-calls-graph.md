---
title: "Avvio rapido: App Web ASP.NET Core che concede l'accesso agli utenti e chiama Microsoft Graph | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra un'app che usa Microsoft.Identity.Web per implementare l'accesso Microsoft in un'app Web ASP.NET Core tramite OpenID Connect e quindi chiama Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: efa9465adc13b50e6ae12628d21347152c3fc2c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104578720"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Avvio rapido: App Web ASP.NET Core che concede l'accesso agli utenti e chiama Microsoft Graph per conto degli utenti

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'app Web ASP.NET Core che consente agli utenti di accedere da qualsiasi organizzazione Azure Active Directory (Azure AD) e chiama Microsoft Graph.  

Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Prerequisiti
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) o [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrare e scaricare l'app di avvio rapido
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni:
> * [Rapida] [Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuale] [Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app</a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Immettere un **Nome** per l'applicazione, ad esempio `AspNetCoreWebAppCallsGraph-Quickstart`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. Immettere un **URI** di Reindirizzamento `https://localhost:44321/signin-oidc` .
> 1. Selezionare **Registra**.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Immettere un **URL di disconnessione front-Channel** di `https://localhost:44321/signout-oidc` .
> 1. Selezionare **Salva**.
> 1. In **Gestisci** selezionare **Certificati e segreti** > **Nuovo segreto client**.
> 1. In **Descrizione** immettere una descrizione, ad esempio `clientsecret1`.
> 1. Selezionare **Tra 1 anno** per la scadenza del segreto.
> 1. Selezionare **Aggiungi** e registrare immediatamente il valore del segreto in **Valore** per poterlo usare in un passaggio successivo. Il valore del segreto *non verrà visualizzato di nuovo* e non è recuperabile con altri mezzi. Registrarlo in una posizione sicura come si farebbe con qualsiasi password.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice in questa Guida introduttiva, aggiungere un **URI di reindirizzamento** di `https://localhost:44321/signin-oidc` e l' **URL di disconnessione front-Channel** di `https://localhost:44321/signout-oidc` nella registrazione dell'app.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-aspnet-core-project"></a>Passaggio 2: Scaricare il progetto ASP.NET Core

> [!div renderon="docs"]
> [Scaricare la soluzione ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Eseguire il progetto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app ed è pronto per essere eseguito.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Passaggio 3: Configurare il progetto ASP.NET Core
> 1. Estrarre l'archivio ZIP in una cartella locale vicina alla radice dell'unità. Ad esempio, in *C:\Azure-Samples*.
> 1. Aprire la soluzione in Visual Studio 2019.
> 1. Aprire il file *appsettings.json* e modificare quanto segue:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Sostituire `Enter_the_Application_Id_here` con il valore di **ID applicazione (client)** dell'applicazione registrata nel portale di Azure. **ID applicazione (client)** si trova nella pagina **Panoramica** dell'app.
>    - Sostituire `common` con uno dei seguenti elementi:
>       - Se l'applicazione supporta **Account solo in questa directory dell'organizzazione** sostituire questo valore con l'**ID della directory (tenant)** (un GUID) o il **nome del tenant** (ad esempio, `contoso.onmicrosoft.com`). È possibile trovare l'**ID della directory (tenant)** nella pagina **Panoramica** dell'app.
>       - Se l'applicazione supporta **Account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations`
>       - Se l'applicazione supporta **Tutti gli utenti di account Microsoft**, lasciare il valore `common`
>    - Sostituire `Enter_the_Client_Secret_Here` con il valore di **Segreto client** creato e registrato in un passaggio precedente.
> 
> Per questa guida di avvio rapido, non modificare altri valori nel file *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Passaggio 4: Compilare ed eseguire l'applicazione
>
> Compilare ed eseguire l'app in Visual Studio selezionando il menu **Debug** > **Avvia debug** oppure premendo `F5`.
>
> Viene chiesto di immettere le credenziali e quindi di concedere le autorizzazioni richieste dall'app. Selezionare **Accetta** nella richiesta di consenso.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Finestra di dialogo di consenso che mostra le autorizzazioni richieste dall'app all'utente >":::
>
> Dopo aver acconsentito alle autorizzazioni richieste, l'app conferma che l'accesso è stato eseguito correttamente con le credenziali di Azure Active Directory e nella sezione del risultato dell'API della pagina viene visualizzato l'indirizzo di posta elettronica dell'utente. Questo indirizzo è stato estratto tramite Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Web browser che visualizza l'app Web in esecuzione e l'utente connesso":::

## <a name="about-the-code"></a>Informazioni sul codice

Questa sezione offre una panoramica del codice necessario per consentire l'accesso agli utenti e chiamare l'API Microsoft Graph per loro conto. Questa panoramica è utile per comprendere il funzionamento del codice, conoscere gli argomenti principali e anche se si intende aggiungere l'accesso a un'applicazione ASP.NET Core esistente e chiamare Microsoft Graph. Viene usato [Microsoft.Identity.Web](microsoft-identity-web.md), che è un wrapper per [MSAL.NET](msal-overview.md).

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe di avvio

Il middleware *Microsoft.AspNetCore.Authentication* usa una classe `Startup` che viene eseguita quando viene inizializzato il processo di hosting:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Il metodo `AddAuthentication()` configura il servizio in modo da aggiungere l'autenticazione basata su cookie (usata in scenari con browser) e impostare la richiesta su OpenID Connect.

La riga contenente `.AddMicrosoftIdentityWebApp` aggiunge l'autenticazione di Microsoft Identity Platform all'applicazione. Viene fornita da [Microsoft.Identity.Web](microsoft-identity-web.md). Viene quindi configurato per l'accesso tramite la piattaforma Microsoft Identity in base alle informazioni contenute nella `AzureAD` sezione del *appsettings.jsnel* file di configurazione:

| Chiave *appsettings.json* | Descrizione                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID applicazione (client)** dell'applicazione registrata nel portale di Azure.                                                                                       |
| `Instance`             | Endpoint del servizio token di sicurezza (STS) per l'autenticazione dell'utente. Questo valore è in genere `https://login.microsoftonline.com/`, che indica il cloud pubblico di Azure. |
| `TenantId`             | Nome del tenant o dell'ID tenant (GUID) oppure *common* per l'accesso agli utenti con account aziendali o dell'istituto di istruzione o account personali Microsoft.                             |

Il metodo `EnableTokenAcquisitionToCallDownstreamApi` consente all'applicazione di acquisire un token per chiamare API Web protette. `AddMicrosoftGraph` consente a controller o Razor Pages di beneficiare direttamente di `GraphServiceClient` (tramite inserimento delle dipendenze) e il metodo `AddInMemoryTokenCaches` consente all'app di trarre vantaggio da una cache di token.

Il metodo `Configure()` contiene due metodi importanti, `app.UseAuthentication()` e `app.UseAuthorization()`, che abilitano la funzionalità denominata. Inoltre, nel metodo `Configure()` è necessario registrare le route Web di Microsoft Identity con almeno una chiamata a `endpoints.MapControllerRoute()` o una chiamata a `endpoints.MapControllers()`.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteggere un controller o un metodo del controller

È possibile proteggere un controller o i relativi metodi applicando l'attributo `[Authorize]` alla classe del controller o a uno o più dei relativi metodi. L'attributo `[Authorize]` limita l'accesso ai soli utenti autenticati. Se l'utente non è già autenticato, è possibile avviare una richiesta di autenticazione per accedere al controller. In questa guida di avvio rapido gli ambiti vengono letti dal file di configurazione:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Il repository GitHub che contiene l'esempio di codice ASP.NET Core a cui si fa riferimento in questa guida di avvio rapido include istruzioni e altri esempi di codice che illustrano come:

- Aggiungere l'autenticazione per una nuova API Web ASP.NET Core
- Chiamare Microsoft Graph, altre API Microsoft o API Web personalizzate
- Aggiungere l'autorizzazione
- Consentire agli utenti di accedere a cloud nazionali o con identità di social networking

> [!div class="nextstepaction"]
> [Esercitazioni sulle API Web ASP.NET Core in GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
