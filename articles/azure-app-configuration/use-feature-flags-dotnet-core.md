---
title: Esercitazione per l'uso dei flag di funzionalità in un'app .NET Core | Microsoft Docs
description: In questa esercitazione si apprenderà come implementare i flag di funzionalità nelle app .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729508"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Esercitazione: Usare i flag di funzionalità in un'app ASP.NET Core

Le librerie di gestione delle funzionalità di .NET Core offrono supporto idiomatico per implementare i flag di funzionalità in un'applicazione .NET o ASP.NET Core. Queste librerie consentono di aggiungere in modo dichiarativo i flag funzionalità al codice in modo da non dover scrivere manualmente il codice per abilitare o disabilitare le funzionalità con le `if` istruzioni.

Le librerie di gestione delle funzionalità gestiscono anche i cicli di vita dei flag di funzionalità in background. Ad esempio, le librerie aggiornano e memorizzano nella cache gli stati del flag o garantiscono lo stato immutabile di un flag durante una chiamata di richiesta. In aggiunta, la libreria ASP.NET Core offre integrazioni predefinite, tra cui le azioni del controller MVC, viste, route e middleware.

La [Guida introduttiva Aggiunta di flag funzionalità a una ASP.NET Core app](./quickstart-feature-flag-aspnet-core.md) Mostra un semplice esempio di come usare i flag di funzionalità in un'applicazione ASP.NET Core. Questa esercitazione illustra le opzioni di installazione e le funzionalità aggiuntive delle librerie di gestione delle funzionalità. Per provare il codice di esempio illustrato in questa esercitazione, è possibile usare l'app di esempio creata nella Guida introduttiva. 

Per la documentazione di riferimento dell'API di gestione delle funzionalità ASP.NET Core, vedere [spazio dei nomi Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere i flag di funzionalità nelle parti chiave dell'applicazione per controllare la disponibilità delle funzionalità.
> * Eseguire l'integrazione con Configurazione app quando viene usato per gestire i flag di funzionalità.

## <a name="set-up-feature-management"></a>Configurare la gestione delle funzionalità

Per accedere a gestione funzionalità di .NET Core, l'app deve avere riferimenti al `Microsoft.FeatureManagement.AspNetCore` pacchetto NuGet.

.NET Core feature Manager è configurato dal sistema di configurazione nativo del Framework. Di conseguenza, è possibile definire le impostazioni dei flag delle funzionalità dell'applicazione usando qualsiasi origine di configurazione supportata da .NET Core, inclusa la *appsettings.jslocale su* file o variabili di ambiente.

Per impostazione predefinita, gestione funzionalità Recupera la configurazione del flag funzionalità dalla `"FeatureManagement"` sezione dei dati di configurazione di .NET Core. Per usare il percorso di configurazione predefinito, chiamare il metodo [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) di **IServiceCollection** passato nel metodo **ConfigureServices** della classe **Startup** .


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

È possibile specificare che la configurazione di gestione delle funzionalità deve essere recuperata da una sezione di configurazione diversa chiamando [Configuration. GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) e passando il nome della sezione desiderata. L'esempio seguente indica invece la lettura da un'altra sezione chiamata `"MyFeatureFlags"`:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Se si usano i filtri nei flag di funzionalità, è necessario includere lo spazio dei nomi [Microsoft. FeatureManagement. FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) e aggiungere una chiamata a [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) specificando il nome del tipo di filtro che si vuole usare come tipo generico del metodo. Per ulteriori informazioni sull'utilizzo di filtri funzionalità per abilitare e disabilitare in modo dinamico le funzionalità, vedere [abilitare la distribuzione temporanea delle funzionalità per destinatari specifici](./howto-targetingfilter-aspnet-core.md).

L'esempio seguente illustra come usare un filtro di funzionalità incorporato denominato `PercentageFilter`:



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Anziché codificare i flag delle funzionalità nell'applicazione, è consigliabile usare i flag delle funzionalità all'esterno dell'applicazione e gestirli separatamente. In questo modo è possibile modificare gli stati dei flag in qualsiasi momento e applicare immediatamente tali modifiche nell'applicazione. Il servizio di configurazione app Azure fornisce un'interfaccia utente del portale dedicata per la gestione di tutti i flag di funzionalità. Il servizio di configurazione app Azure recapita anche i flag di funzionalità all'applicazione direttamente tramite le librerie client di .NET Core.

Il modo più semplice per connettere l'applicazione ASP.NET Core alla configurazione dell'app è tramite il provider di configurazione incluso nel `Microsoft.Azure.AppConfiguration.AspNetCore` pacchetto NuGet. Dopo aver incluso un riferimento al pacchetto, seguire questa procedura per usare il pacchetto NuGet.

1. Aprire il file *Program.cs* e aggiungere il codice seguente.
    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.x. Selezionare la sintassi corretta in base all'ambiente.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Aprire *Startup. cs* e aggiornare il `Configure` `ConfigureServices` metodo e per aggiungere il middleware incorporato denominato `UseAzureAppConfiguration` . Questo middleware consente di aggiornare i valori dei flag delle funzionalità a intervalli ricorrenti mentre l'app Web ASP.NET Core continua a ricevere richieste.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
In uno scenario tipico, i valori dei flag di funzionalità vengono aggiornati periodicamente quando si distribuiscono e si abilitano le diverse funzionalità dell'applicazione. Per impostazione predefinita, i valori dei flag di funzionalità vengono memorizzati nella cache per un periodo di 30 secondi, in modo che un'operazione di aggiornamento attivata quando il middleware riceve la richiesta non aggiorni il valore fino alla scadenza del valore memorizzato nella cache. Il codice seguente illustra come modificare la scadenza della cache o l'intervallo di polling in 5 minuti impostando [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) nella chiamata a **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Dichiarazione dei flag di funzionalità

Ogni dichiarazione di flag di funzionalità è costituita da due parti: un nome e un elenco di uno o più filtri utilizzati per valutare se lo stato di una funzionalità è *on* (ovvero, quando il valore è `True` ). Un filtro definisce un criterio per l'accensione di una funzionalità.

Se un flag di funzionalità ha più filtri, l'elenco dei filtri viene attraversato fino a quando uno dei filtri non determina che la funzionalità deve essere abilitata. A questo punto, il flag di funzionalità è *attivo* e i risultati del filtro rimanenti vengono ignorati. Se nessun filtro indica che deve essere abilitata la funzionalità, il flag di funzionalità viene *disattivato*.

La gestione funzionalità supporta *appSettings.json* come origine di configurazione per i flag di funzionalità. L'esempio seguente mostra come configurare i flag di funzionalità in un file JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Per convenzione, la sezione `FeatureManagement` di questo documento JSON viene usata per le impostazioni dei flag di funzionalità. L'esempio precedente mostra tre flag di funzionalità con i relativi filtri definiti nella proprietà `EnabledFor`:

* `FeatureA` è *attivo*.
* `FeatureB` è *disattivato*.
* `FeatureC` specifica un filtro denominato `Percentage` con una proprietà `Parameters`. `Percentage` è un filtro configurabile. In questo esempio `Percentage` specifica una probabilità del 50% che il flag `FeatureC` sia *attivo*. Per istruzioni su come usare i filtri delle funzionalità, vedere usare i filtri delle funzionalità [per abilitare i flag delle funzionalità condizionali](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Usare l'inserimento di dipendenze per accedere a IFeatureManager 

Per alcune operazioni, ad esempio il controllo manuale dei valori dei flag di funzionalità, è necessario ottenere un'istanza di [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview). In ASP.NET Core MVC è possibile accedere a gestione funzionalità `IFeatureManager` tramite l'inserimento di dipendenze. Nell'esempio seguente viene aggiunto un argomento di tipo `IFeatureManager` alla firma del costruttore per un controller. Il runtime risolve automaticamente il riferimento e fornisce un dell'interfaccia quando viene chiamato il costruttore. Se si usa un modello di applicazione in cui il controller dispone già di uno o più argomenti di inserimento delle dipendenze nel costruttore, ad esempio `ILogger` , è possibile aggiungere solo `IFeatureManager` come argomento aggiuntivo:

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Riferimenti di flag funzionalità

Definire i flag di funzionalità come variabili stringa per farvi riferimento dal codice:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Controlli dei flag di funzionalità

Un modello comune di gestione delle funzionalità consiste nel verificare se un flag funzionalità è impostato *su on* e, in caso affermativo, eseguire una sezione di codice. Ad esempio:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Azioni del controller

Con i controller MVC, è possibile usare l' `FeatureGate` attributo per controllare se è abilitata un'intera classe controller o un'azione specifica. Il controller `HomeController` seguente richiede che il flag `FeatureA` sia *attivo* prima che venga eseguita qualsiasi azione contenuta nella classe controller:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Per poter eseguire l'azione `Index` seguente, il flag`FeatureA` deve essere *attivo*:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Quando un controller o un'azione MVC è bloccata perché il flag della funzionalità di controllo è *disattivato*, viene chiamata un'interfaccia [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) registrata. L'interfaccia `IDisabledFeaturesHandler` predefinita restituisce un codice di stato 404 al client senza alcun corpo della risposta.

## <a name="mvc-views"></a>Viste MVC

Aprire *_ViewImports.cshtml* nella directory *Views* e aggiungere l'helper per i tag di gestione funzionalità:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

Nelle viste MVC è possibile usare un tag `<feature>` per eseguire il rendering del contenuto in base allo stato di attivazione del flag di funzionalità:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Per visualizzare il contenuto alternativo quando non vengono soddisfatti i requisiti, può essere utilizzato l'attributo `negate`.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Il tag di funzionalità `<feature>` può anche essere usato per mostrare il contenuto se una o tutte le funzionalità di un elenco sono abilitate.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtri MVC

È possibile configurare i filtri MVC in modo da attivarli in base allo stato di un flag di funzionalità. Questa funzionalità è limitata ai filtri che implementano [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). Il codice seguente aggiunge un filtro MVC denominato `ThirdPartyActionFilter`. Questo filtro viene attivato all'interno della pipeline MVC solo se il flag `FeatureA` è attivato.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middleware

È possibile usare i flag di funzionalità per aggiungere rami di applicazioni e middleware in modo condizionale. Il codice seguente inserisce un componente middleware nella pipeline di richiesta solo quando il flag `FeatureA` è attivato:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Questo codice produce la capacità più generica di creare rami nell'intera applicazione in base al flag di funzionalità:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come implementare i flag di funzionalità nell'applicazione ASP.NET Core usando le librerie `Microsoft.FeatureManagement`. Per altre informazioni sul supporto della gestione funzionalità in ASP.NET Core e Configurazione app, vedere le risorse seguenti:

* [Codice di esempio del flag di funzionalità di ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Documentazione di Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Gestire i flag di funzionalità](./manage-feature-flags.md)