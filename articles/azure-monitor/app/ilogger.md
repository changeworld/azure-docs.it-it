---
title: Esplorare i log di traccia .NET con ILogger - applicazione Azure Insights
description: Esempi di uso del provider ILogger applicazione Azure Insights con ASP.NET core e console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568591"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider per Microsoft.Extension.Logging

Questo articolo illustra come usare per `ApplicationInsightsLoggerProvider` acquisire i log nella console e nelle ASP.NET `ILogger` Core.
Per altre informazioni sulla registrazione, [vedere Registrazione in ASP.NET Core.](/aspnet/core/fundamentals/logging)

## <a name="aspnet-core-applications"></a>ASP.NET Core

`ApplicationInsightsLoggerProvider`è abilitato per impostazione predefinita per ASP.NET core quando ApplicationInsights è configurato [usando](./asp-net-core.md) l'approccio [code-less.](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring)

Solo *i* log di avviso e di livello superiore `ILogger` (di tutte [le](/aspnet/core/fundamentals/logging/#log-category)categorie) vengono inviati Application Insights per impostazione predefinita. È tuttavia possibile [personalizzare questo comportamento.](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection) Sono necessari passaggi aggiuntivi per acquisire i log ILogger **da Program.cs** **o Startup.cs.** Vedere [Capturing ILogger logs from Startup.cs and Program.cs (Acquisizione](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)di log ILogger da Startup.cs e Program.cs in ASP.NET Core).

Se si vuole usare solo `ApplicationInsightsLoggerProvider` senza altri Application Insights monitoraggio, seguire questa procedura.

1. Installare il pacchetto NuGet:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Modificare `Program.cs` come illustrato di seguito:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Il codice nel passaggio 2 configura `ApplicationInsightsLoggerProvider` . Il codice seguente illustra una classe Controller di esempio, che usa `ILogger` per inviare i log. I log vengono acquisiti da Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Acquisire i log di ILogger da Startup.cs e Program.cs nelle ASP.NET Core

> [!NOTE]
> In ASP.NET Core 3.0 e versioni successive non è più possibile inserire `ILogger` in Startup.cs e Program.cs. Per informazioni dettagliate, vedere https://github.com/aspnet/Announcements/issues/353.

`ApplicationInsightsLoggerProvider` può acquisire i log dall'inizio dell'avvio dell'applicazione. Sebbene ApplicationInsightsLoggerProvider sia abilitato automaticamente in Application Insights (a partire dalla versione 2.7.1), non ha una chiave di strumentazione impostata fino a un secondo momento nella pipeline. Verranno quindi acquisiti solo i **log di controller**/altre classi. Per acquisire ogni log a partire **da Program.cs** e **Startup.cs,** è necessario abilitare in modo esplicito una chiave di strumentazione per ApplicationInsightsLoggerProvider. Inoltre, *TelemetryConfiguration* non è completamente configurato quando si esegue la registrazione da **Program.cs** o **Startup.cs** stesso. Tali log avranno quindi una configurazione minima che usa [](./sampling.md) [InMemoryChannel](./telemetry-channels.md), nessun campionamento e nessun inizializzatore di telemetria standard [o processori](./api-filtering-sampling.md).

Gli esempi seguenti illustrano questa funzionalità **con Program.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Esempio di Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Esempio di Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Eseguire la migrazione dal vecchio ApplicationInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK versioni precedenti alla 2.7.1 supporta un provider di registrazione obsoleto. Questo provider è stato abilitato tramite **il metodo di estensione AddApplicationInsights()** di ILoggerFactory. È consigliabile eseguire la migrazione al nuovo provider, che prevede due passaggi:

1. Rimuovere la *chiamata ILoggerFactory.AddApplicationInsights()* dal **metodoStartup.Configure()** per evitare la registrazione doppia.
2. Riapplicare le regole di filtro nel codice, perché non verranno rispettate dal nuovo provider. Gli overload di *ILoggerFactory.AddApplicationInsights()* accettavano le funzioni logLevel o filter minime. Con il nuovo provider, il filtro fa parte del framework di registrazione stesso. Non viene eseguita dal provider Application Insights. Pertanto, tutti i filtri forniti tramite gli overload *ILoggerFactory.AddApplicationInsights()* devono essere rimossi. E le regole di filtro devono essere fornite seguendo le istruzioni [sul livello di registrazione del](#control-logging-level) controllo. Se si usa *appsettings.jsper* filtrare la registrazione, continuerà a funzionare con il nuovo provider, perché entrambi usano lo stesso alias del provider, *ApplicationInsights*.

È comunque possibile usare il provider precedente. Verrà rimossa solo in una modifica della versione principale a 3. *xx*.) È tuttavia consigliabile eseguire la migrazione al nuovo provider per i motivi seguenti:

- Il provider precedente non supporta gli [ambiti del log](/aspnet/core/fundamentals/logging#log-scopes). Nel nuovo provider le proprietà dell'ambito vengono aggiunte automaticamente come proprietà personalizzate ai dati di telemetria raccolti.
- I log possono ora essere acquisiti molto prima nella pipeline di avvio dell'applicazione. È ora possibile **acquisire i** log delle classi Program e **Startup.**
- Con il nuovo provider, il filtro viene eseguito a livello di framework. È possibile filtrare i log in base al provider Application Insights come per altri provider, inclusi i provider predefiniti come Console, Debug e così via. È anche possibile applicare gli stessi filtri a più provider.
- In ASP.NET Core (2.0 e versioni successive), [](https://github.com/aspnet/Announcements/issues/255) il modo consigliato per abilitare i provider di registrazione è l'uso di metodi di estensione in ILoggingBuilder in **Program.cs** stesso.

> [!Note]
> Il nuovo provider è disponibile per le applicazioni che hanno come destinazione NETSTANDARD2.0 o versione successiva. A [partire da Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.14.0, è disponibile un nuovo provider anche per le applicazioni che hanno come destinazione .NET Framework NET461 o versioni successive. Se l'applicazione è destinata a versioni precedenti di .NET Core, ad esempio .NET Core 1.1, o se è destinata a .NET Framework minore di NET46, continuare a usare il provider precedente.

## <a name="console-application"></a>Applicazione console

> [!NOTE]
> È disponibile un nuovo SDK Application Insights denominato [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) che può essere usato per abilitare Application Insights (ILogger e altri dati di telemetria Application Insights) per qualsiasi applicazione console. È consigliabile usare questo pacchetto e le istruzioni associate da [qui](./worker-service.md).

Se si vuole usare semplicemente ApplicationInsightsLoggerProvider senza altri Application Insights monitoraggio, seguire questa procedura.

Pacchetti installati:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

In questo esempio viene utilizzato il pacchetto autonomo `Microsoft.Extensions.Logging.ApplicationInsights` . Per impostazione predefinita, questa configurazione usa la configurazione di telemetria "minima" per l'invio di dati Application Insights. Minimo indica che InMemoryChannel è il canale usato. Non è disponibile alcun campionamento e non sono presenti inizializzatori di telemetria standard. Questo comportamento può essere sottoposto a override per un'applicazione console, come illustrato nell'esempio seguente.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La sezione seguente illustra come eseguire l'override di TelemetryConfiguration predefinito usando il **metodoservices.Configure \<TelemetryConfiguration>** (). Questo esempio configura e `ServerTelemetryChannel` campionamento. Aggiunge un ITelemetryInitializer personalizzato a TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Controllare il livello di registrazione

`ILogger`dispone di un meccanismo predefinito per applicare il [filtro dei log.](/aspnet/core/fundamentals/logging#log-filtering) In questo modo è possibile controllare i log inviati a ogni provider registrato, incluso Application Insights provider. Il filtro può essere eseguito nella configurazione (in genere usando unappsettings.js *nel* file) o nel codice.

Negli esempi seguenti viene illustrato come applicare regole di filtro a `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Creare regole di filtro nella configurazione con appsettings.jsattivata

Per ApplicationInsightsLoggerProvider, l'alias del provider è `ApplicationInsights` . La sezione seguente diappsettings.js *in* indica ai provider di registrazione in genere di accedere al livello *Avviso* e superiore. Esegue quindi l'override di per registrare le categorie `ApplicationInsightsLoggerProvider` che iniziano con "Microsoft" a livello *di errore* e successive.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Creare regole di filtro nel codice

Il frammento di codice seguente configura i log per *Avviso* e superiori da tutte le categorie e per *Errore* e successive dalle categorie che iniziano con "Microsoft" per l'invio a `ApplicationInsightsLoggerProvider` . Questa configurazione è la stessa della sezione precedente in *appsettings.jsin*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Ambiti di registrazione

`ApplicationInsightsLoggingProvider` supporta ambiti [di log e](/aspnet/core/fundamentals/logging#log-scopes) gli ambiti sono abilitati per impostazione predefinita.

Se l'ambito è di tipo , ogni coppia chiave-valore nella raccolta viene aggiunta ai dati di telemetria di `IReadOnlyCollection<KeyValuePair<string,object>>` Application Insights come proprietà personalizzate. Nell'esempio seguente i log verranno acquisiti come `TraceTelemetry` e avranno ("MyKey", "MyValue") nelle proprietà.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Se si usa un altro tipo come Ambito, questi verranno archiviati nella proprietà "Scope" nei dati di telemetria di Application Insights. Nell'esempio seguente, `TraceTelemetry` avrà una proprietà denominata "Scope" contenente l'ambito.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quali sono le versioni precedenti e nuove di ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) includeva un ApplicationInsightsLoggerProvider predefinito (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), che è stato abilitato tramite i metodi di **estensione ILoggerFactory.** Questo provider è contrassegnato come obsoleto dalla versione 2.7.1. Verrà rimossa completamente nella successiva modifica della versione principale. Il [pacchetto Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) non è obsoleto. È necessario per abilitare il monitoraggio di richieste, dipendenze e così via.

L'alternativa suggerita è il nuovo pacchetto autonomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), che contiene un ApplicationInsightsLoggerProvider migliorato (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e i metodi di estensione in ILoggerBuilder per abilitarlo.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.1 accetta una dipendenza dal nuovo pacchetto e abilita automaticamente l'acquisizione di ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Perché alcuni log di ILogger vengono visualizzati due volte Application Insights?

La duplicazione può verificarsi se la versione precedente (ora obsoleta) di ApplicationInsightsLoggerProvider è abilitata chiamando `AddApplicationInsights` su `ILoggerFactory` . Controllare se il **metodo Configure** include quanto segue e rimuoverlo:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se si verifica una doppia registrazione quando si esegue il debug da Visual Studio, impostare su false nel codice che `EnableDebugLogger` Application Insights, come indicato di seguito.  Questa duplicazione e correzione è rilevante solo quando si esegue il debug dell'applicazione.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>È stato aggiornato [a Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.1 e i log di ILogger vengono acquisiti automaticamente. Ricerca per categorie disattivare completamente questa funzionalità?

Vedere la [sezione Controllare il livello di](#control-logging-level) registrazione per informazioni su come filtrare i log in generale. Per disattivare ApplicationInsightsLoggerProvider, usare `LogLevel.None` :

**Nel codice:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In config:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Perché alcuni log ILogger non hanno le stesse proprietà di altri?

Application Insights acquisisce e invia i log ILogger usando la stessa configurazione di telemetria usata per tutti gli altri dati di telemetria. Ma c'è un'eccezione. Per impostazione predefinita, TelemetryConfiguration non è completamente configurato quando si esegue la registrazione da **Program.cs** o **Startup.cs**. I log da queste posizioni non hanno la configurazione predefinita, quindi non verranno eseguiti tutti i telemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Si usa il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e si vuole registrare manualmente alcuni dati di telemetria personalizzati aggiuntivi. Come è possibile eseguire questa operazione?

Quando si usa il pacchetto autonomo, non viene inserito nel contenitore DI, quindi è necessario creare una nuova istanza di e usare la stessa configurazione utilizzata dal `TelemetryClient` provider del logger, come illustrato nel codice `TelemetryClient` seguente. In questo modo si garantisce che la stessa configurazione sia usata per tutti i dati di telemetria personalizzati e per i dati di telemetria di ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Se si usa il pacchetto Microsoft.ApplicationInsights.AspNetCore per abilitare Application Insights, modificare questo codice per ottenere direttamente `TelemetryClient` nel costruttore. Per un esempio, vedere [queste domande frequenti.](./asp-net-core.md#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Quale Application Insights di telemetria viene prodotto dai log di ILogger? Oppure dove è possibile visualizzare i log di ILogger Application Insights?

ApplicationInsightsLoggerProvider acquisisce i log ILogger e crea TraceTelemetry da essi. Se un oggetto Exception viene passato al metodo in , viene creato `Log` `ILogger` *ExceptionTelemetry* anziché TraceTelemetry. Questi elementi di telemetria sono disponibili nelle stesse posizioni di qualsiasi altro traceTelemetry o ExceptionTelemetry per Application Insights, inclusi il portale, l'analisi o Visual Studio debugger locale.

Se si preferisce inviare sempre TraceTelemetry, usare questo frammento di codice: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>L'SDK non è installato e si usa l'estensione App Web di Azure per abilitare Application Insights per le applicazioni ASP.NET Core. Ricerca per categorie il nuovo provider? 

L Application Insights app Web di Azure usa il nuovo provider. È possibile modificare le regole di filtro nelappsettings.js *file* per l'applicazione.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Si usa il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e si abilita Application Insights provider chiamando **builder. AddApplicationInsights("ikey")**. È possibile ottenere una chiave di strumentazione dalla configurazione?


Modificare Program.cs e appsettings.jsin come indicato di seguito:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Sezione pertinente da `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Questo codice è necessario solo quando si usa un provider di registrazione autonomo. Per il monitoraggio Application Insights, la chiave di strumentazione viene caricata automaticamente dal percorso di configurazione *ApplicationInsights: InstrumentationKey*. Appsettings.jssu dovrebbe essere simile al seguente:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> Le nuove aree di Azure **richiedono l'uso** di stringhe di connessione invece delle chiavi di strumentazione. [La stringa di](./sdk-connection-string.md?tabs=net) connessione identifica la risorsa a cui si vogliono associare i dati di telemetria. Consente inoltre di modificare gli endpoint che la risorsa userà come destinazione per la telemetria. Sarà necessario copiare la stringa di connessione e aggiungerla al codice dell'applicazione o a una variabile di ambiente.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

* [Registrazione in ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Log di traccia .NET in Application Insights](./asp-net-trace-logs.md)

