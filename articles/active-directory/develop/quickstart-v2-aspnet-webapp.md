---
title: "Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET | Azure"
titleSuffix: Microsoft identity platform
description: Questa Guida introduttiva illustra come implementare l'accesso Microsoft in un'app Web ASP.NET usando OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104578686"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Avvio rapido: Aggiungere l'accesso Microsoft Identity Platform a un'app Web ASP.NET

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'app Web ASP.NET che consente agli utenti di accedere da qualsiasi organizzazione Azure Active Directory (Azure AD). 

> [!div renderon="docs"]
> Il diagramma seguente illustra il funzionamento dell'app di esempio:
>
> ![Diagramma dell'interazione tra il Web browser, l'app Web e la piattaforma di identità Microsoft nell'app di esempio.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Prerequisiti
>
> * Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 o versione successiva](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Registrare e scaricare l'app
> Sono disponibili due opzioni per iniziare a compilare l'applicazione: configurazione automatica o manuale.
>
> ### <a name="automatic-configuration"></a>Configurazione automatica
> Se si vuole configurare automaticamente l'app e quindi scaricare l'esempio di codice, seguire questa procedura:
>
> 1. Passare alla <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">pagina portale di Azure per la registrazione dell'app</a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un solo clic.
>
> ### <a name="manual-configuration"></a>Configurazione manuale
> Se si desidera configurare manualmente l'applicazione e l'esempio di codice, utilizzare le procedure riportate di seguito.
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si ha accesso a più tenant, usare il filtro **directory + sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto per selezionare il tenant in cui si vuole registrare l'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Per **nome** immettere un nome per l'applicazione. Ad esempio, immettere **ASPNET-QuickStart**. Gli utenti dell'app visualizzeranno questo nome ed è possibile modificarlo in un secondo momento.
> 1. Aggiungere **https://localhost:44368/** l' **URI di reindirizzamento** e selezionare **registra**.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Nella sezione **concessione implicita e flussi ibridi** selezionare **token ID**.
> 1. Selezionare **Salva**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice in questa Guida introduttiva, immettere **https://localhost:44368/** per l' **URI di reindirizzamento**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![L'applicazione è già stata configurata ](media/quickstart-v2-aspnet-webapp/green-check.png) con questo attributo.

#### <a name="step-2-download-the-project"></a>Passaggio 2: Scaricare il progetto

> [!div renderon="docs"]
> [Scaricare la soluzione di Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Eseguire il progetto usando Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Passaggio 3: Eseguire il progetto di Visual Studio

1. Estrarre il file zip in una cartella locale vicina alla cartella radice. Ad esempio, Estrai in *C:\Azure-Samples*.
   
   Si consiglia di estrarre l'archivio in una directory accanto alla radice dell'unità per evitare errori causati da limitazioni di lunghezza del percorso in Windows.
2. Aprire la soluzione in Visual Studio (*AppModelv2-webapp-OpenIDConnect-dotnet. sln*).
3. A seconda della versione di Visual Studio, potrebbe essere necessario fare clic con il pulsante destro del mouse sul progetto **AppModelv2-webapp-OpenIDConnect-DotNet** , quindi scegliere **Ripristina pacchetti NuGet**.
4. Aprire la console di gestione pacchetti selezionando **Visualizza**  >  **altre**  >  **console di gestione pacchetti** di Windows. Quindi eseguire `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Modificare *Web.config* e sostituire i parametri `ClientId` , `Tenant` e `redirectUri` con:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    Nel codice:
>
>    - `Enter_the_Application_Id_here` è l'ID dell'applicazione (client) della registrazione dell'app creata in precedenza. Trovare l'ID applicazione (client) nella pagina **Panoramica** dell'app in **registrazioni app** nel portale di Azure.
>    - `Enter_the_Tenant_Info_Here` è una delle opzioni seguenti:
>      - Se l'applicazione supporta **solo l'organizzazione**, sostituire questo valore con l'ID di directory (tenant) o il nome del tenant (ad esempio, `contoso.onmicrosoft.com` ). Trovare l'ID di directory (tenant) nella pagina **Panoramica** dell'app in **registrazioni app** nel portale di Azure.
>      - Se l'applicazione supporta gli **account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations` .
>      - Se l'applicazione supporta **tutti gli utenti account Microsoft**, sostituire questo valore con `common` .
>    - `redirectUri` URI di **Reindirizzamento** immesso in precedenza in **registrazioni app** nel portale di Azure.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Ulteriori informazioni

Questa sezione include una panoramica del codice necessario per consentire l'accesso degli utenti. Questa panoramica può essere utile per comprendere il funzionamento del codice, gli argomenti principali e come aggiungere l'accesso a un'applicazione ASP.NET esistente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
>
> ![Diagramma dell'interazione tra il Web browser, l'app Web e la piattaforma di identità Microsoft nell'app di esempio.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Pacchetti NuGet middleware OWIN

È possibile configurare la pipeline di autenticazione con l'autenticazione basata su cookie usando OpenID Connect in ASP.NET con i pacchetti middleware OWIN. È possibile installare questi pacchetti eseguendo i comandi seguenti nella console di gestione pacchetti in Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Classe Startup OWIN

Il middleware OWIN usa una *classe di avvio* che viene eseguita all'avvio del processo di hosting. In questa Guida introduttiva il file *Startup. cs* si trova nella cartella radice. Il codice seguente illustra i parametri usati da questa Guida introduttiva:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | Descrizione |
> |---------|---------|
> | `ClientId`     | ID applicazione dell'applicazione registrata nel portale di Azure. |
> | `Authority`    | Endpoint del servizio token di sicurezza (STS) per l'autenticazione dell'utente. Si tratta in genere di `https://login.microsoftonline.com/{tenant}/v2.0` un cloud pubblico. In tale URL, *{tenant}* è il nome del tenant, l'ID tenant o `common` per un riferimento all'endpoint comune. L'endpoint comune viene usato per le applicazioni multi-tenant. |
> | `RedirectUri`  | URL in cui gli utenti vengono inviati dopo l'autenticazione con la piattaforma di identità Microsoft. |
> | `PostLogoutRedirectUri`     | URL in cui gli utenti vengono inviati dopo la disconnessione. |
> | `Scope`     | Elenco degli ambiti richiesti, separati da spazi. |
> | `ResponseType`     | La richiesta di autenticazione della risposta contiene un codice di autorizzazione e un token ID. |
> | `TokenValidationParameters`     | Elenco di parametri per la convalida del token. In questo caso, `ValidateIssuer` è impostato su `false` per indicare che può accettare accessi da qualsiasi tipo di account personale, aziendale o dell'Istituto di istruzione. |
> | `Notifications`     | Elenco di delegati che possono essere eseguiti sui `OpenIdConnect` messaggi. |


> [!NOTE]
> L'impostazione di `ValidateIssuer = false` è una semplificazione per questo avvio rapido. In applicazioni reali, convalidare l'autorità emittente. Vedere gli esempi per comprendere come eseguire questa operazione.

### <a name="authentication-challenge"></a>Richiesta di autenticazione

È possibile indurre un utente a eseguire l'accesso inserendo una richiesta di autenticazione nel controller in uso:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> La richiesta di un problema di autenticazione tramite questo metodo è facoltativa. Normalmente viene usato quando si vuole che una visualizzazione sia accessibile da parte di utenti autenticati e non autenticati. È possibile in alternativa proteggere i controller usando il metodo descritto nella sezione successiva.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Attributo per la protezione di un controller o di azioni del controller

È possibile proteggere le azioni del controller o del controller usando l' `[Authorize]` attributo. Questo attributo limita l'accesso al controller o alle azioni consentendo solo agli utenti autenticati di accedere alle azioni nel controller. Una richiesta di autenticazione viene quindi eseguita automaticamente quando un utente non autenticato tenta di accedere a una delle azioni o dei controller decorati dall' `[Authorize]` attributo.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per una guida dettagliata completa sulla creazione di applicazioni e nuove funzionalità, inclusa una spiegazione completa di questa Guida introduttiva, provare l'esercitazione su ASP.NET.

> [!div class="nextstepaction"]
> [Aggiungere l'accesso a un'app Web ASP.NET](tutorial-v2-asp-webapp.md)
