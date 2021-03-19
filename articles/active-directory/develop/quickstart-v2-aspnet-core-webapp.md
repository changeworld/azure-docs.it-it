---
title: "Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET Core | Azure"
titleSuffix: Microsoft identity platform
description: Questa Guida introduttiva illustra come un'app implementa l'accesso Microsoft in un'app Web di ASP.NET Core usando OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578703"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET Core

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'app Web ASP.NET Core che consente agli utenti di accedere da qualsiasi organizzazione Azure Active Directory (Azure AD).  

> [!div renderon="docs"]
> Il diagramma seguente illustra il funzionamento dell'app di esempio:
>
> ![Diagramma dell'interazione tra il Web browser, l'app Web e la piattaforma di identità Microsoft nell'app di esempio.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Prerequisiti
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) o [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Registrare e scaricare l'app
> Sono disponibili due opzioni per iniziare a compilare l'applicazione: configurazione automatica o manuale.
>
> ### <a name="automatic-configuration"></a>Configurazione automatica
> Se si vuole configurare automaticamente l'app e quindi scaricare l'esempio di codice, seguire questa procedura:
>
> 1. Passare alla <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">pagina portale di Azure per la registrazione dell'app</a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un solo clic.
>
> ### <a name="manual-configuration"></a>Configurazione manuale
> Se si desidera configurare manualmente l'applicazione e l'esempio di codice, utilizzare le procedure riportate di seguito.
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>.
> 1. Se si ha accesso a più tenant, usare il filtro **directory + sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto per selezionare il tenant in cui si vuole registrare l'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Per **nome** immettere un nome per l'applicazione. Ad esempio, immettere **AspNetCore-QuickStart**. Gli utenti dell'app visualizzeranno questo nome ed è possibile modificarlo in un secondo momento.
> 1. Per **URI di reindirizzamento**, immettere **https://localhost:44321/signin-oidc** .
> 1. Selezionare **Registra**.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Per **URL di disconnessione front-Channel**, immettere **https://localhost:44321/signout-oidc** .
> 1. In **concessione implicita e flussi ibridi** selezionare **token ID**.
> 1. Selezionare **Salva**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice in questa Guida introduttiva:
> - Per **URI di reindirizzamento**, immettere **https://localhost:44321/** e **https://localhost:44321/signin-oidc** .
> - Per **URL di disconnessione front-Channel**, immettere **https://localhost:44321/signout-oidc** . 
>
> L'endpoint di autorizzazione emetterà token ID richiesta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-aspnet-core-project"></a>Passaggio 2: Scaricare il progetto ASP.NET Core

> [!div renderon="docs"]
> [Scaricare la soluzione ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Eseguire il progetto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passaggio 3: L'app è configurata e pronta per l'esecuzione
> Il progetto è stato configurato con i valori delle proprietà dell'app ed è pronto per l'esecuzione.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Passaggio 3: Configurare il progetto ASP.NET Core
> 1. Estrarre l'archivio ZIP in una cartella locale vicina alla radice dell'unità. Ad esempio, Estrai in *C:\Azure-Samples*.
> 
>    Si consiglia di estrarre l'archivio in una directory accanto alla radice dell'unità per evitare errori causati da limitazioni di lunghezza del percorso in Windows.
> 1. Aprire la soluzione in Visual Studio 2019.
> 1. Aprire il *appsettings.jsnel* file e modificare il codice seguente:
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Sostituire `Enter_the_Application_Id_here` con l'ID applicazione (client) dell'applicazione registrata nell'portale di Azure. È possibile trovare il valore dell' **ID dell'applicazione (client)** nella pagina **Panoramica** dell'app.
>    - Sostituire `common` con uno dei seguenti elementi:
>       - Se l'applicazione supporta **solo gli account in questa directory aziendale**, sostituire questo valore con l'ID (tenant) della directory (un GUID) o il nome del tenant (ad esempio, `contoso.onmicrosoft.com` ). È possibile trovare il valore di **ID directory (tenant)** nella pagina **Panoramica** dell'app.
>       - Se l'applicazione supporta gli **account in qualsiasi directory organizzativa**, sostituire questo valore con `organizations` .
>       - Se l'applicazione supporta **tutti gli utenti account Microsoft**, lasciare questo valore come `common` .
>
> Per questa guida di avvio rapido, non modificare altri valori nel file *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Passaggio 4: Compilare ed eseguire l'applicazione
>
> Compilare ed eseguire l'app in Visual Studio selezionando il menu **debug** > **avviare il debug** o premendo il tasto F5.
>
> Verranno richieste le credenziali e quindi verrà richiesto di fornire il consenso alle autorizzazioni richieste dall'app. Selezionare **Accetta** nella richiesta di consenso.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Screenshot della finestra di dialogo di consenso, che mostra le autorizzazioni richieste dall'app dall'utente.":::
>
> Dopo aver acconsentito alle autorizzazioni richieste, l'app Visualizza che è stato eseguito l'accesso con le credenziali Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Screenshot di un Web browser che mostra l'app Web in esecuzione e l'utente che ha eseguito l'accesso.":::

## <a name="more-information"></a>Ulteriori informazioni

Questa sezione include una panoramica del codice necessario per consentire l'accesso degli utenti. Questa panoramica può essere utile per comprendere il funzionamento del codice, gli argomenti principali e come aggiungere l'accesso a un'applicazione ASP.NET Core esistente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
>
> ![Diagramma dell'interazione tra il Web browser, l'app Web e la piattaforma di identità Microsoft nell'app di esempio.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe di avvio

Il middleware *Microsoft. AspNetCore. Authentication* utilizza una `Startup` classe che viene eseguita all'avvio del processo di hosting:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Il `AddAuthentication()` metodo configura il servizio per aggiungere l'autenticazione basata su cookie. Questa autenticazione viene usata negli scenari del browser e per impostare la richiesta di verifica di OpenID Connect.

La riga che contiene `.AddMicrosoftIdentityWebApp` aggiunge l'autenticazione della piattaforma di identità Microsoft all'applicazione. L'applicazione viene quindi configurata per l'accesso degli utenti in base alle informazioni seguenti nella `AzureAD` sezione del *appsettings.js* nel file di configurazione:

| Chiave *appsettings.json* | Descrizione                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID applicazione (client) dell'applicazione registrata nel portale di Azure.                                                                                       |
| `Instance`             | Endpoint del servizio token di sicurezza (STS) per l'autenticazione dell'utente. Questo valore è in genere `https://login.microsoftonline.com/`, che indica il cloud pubblico di Azure. |
| `TenantId`             | Nome del tenant o dell'ID tenant (GUID) o `common` per l'accesso degli utenti con account aziendali o dell'Istituto di istruzione o account personali Microsoft.                             |

Il metodo `Configure()` contiene due metodi importanti, `app.UseAuthentication()` e `app.UseAuthorization()`, che abilitano la funzionalità denominata. Inoltre, nel `Configure()` metodo, è necessario registrare le route Web di Microsoft Identity con almeno una chiamata a `endpoints.MapControllerRoute()` o una chiamata a `endpoints.MapControllers()` :

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Attributo per la protezione di un controller o di metodi

È possibile proteggere un controller o i metodi controller usando l' `[Authorize]` attributo. Questo attributo limita l'accesso al controller o ai metodi consentendo solo agli utenti autenticati. È quindi possibile avviare una richiesta di autenticazione per accedere al controller se l'utente non è autenticato.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Il repository GitHub che contiene questa esercitazione su ASP.NET Core include istruzioni e altri esempi di codice che illustrano come:

- Aggiungere l'autenticazione a una nuova applicazione Web ASP.NET Core.
- Chiamare Microsoft Graph, altre API Microsoft o API Web personalizzate.
- Aggiungere l'autorizzazione.
- Consentire agli utenti di accedere a cloud nazionali o a identità di social networking.

> [!div class="nextstepaction"]
> [Esercitazioni sulle API Web ASP.NET Core in GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
