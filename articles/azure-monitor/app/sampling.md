---
title: Campionamento della telemetria in Azure Application Insights | Documentazione Microsoft
description: Come tenere sotto controllo il volume della telemetria.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: ba7892c8afbe8e557c7dcf9aa3bd663f53a5728f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834751"
---
# <a name="sampling-in-application-insights"></a>Campionamento in Application Insights

Il campionamento è una funzionalità di [Azure Application Insights](./app-insights-overview.md). È il modo consigliato per ridurre il traffico di telemetria, i costi dei dati e i costi di archiviazione, mantenendo un'analisi statisticamente corretta dei dati dell'applicazione. Il campionamento consente anche di evitare di Application Insights i dati di telemetria. Il filtro di campionamento seleziona gli elementi correlati, in modo che sia possibile spostarsi tra gli elementi durante le indagini diagnostiche.

Quando i conteggi delle metriche vengono presentati nel portale, vengono rinormalizzati per prendere in considerazione il campionamento. In questo modo si riduce al minimo qualsiasi effetto sulle statistiche.

## <a name="brief-summary"></a>Breve riepilogo

* Esistono tre diversi tipi di campionamento: campionamento adattivo, campionamento a frequenza fissa e campionamento per inserimento.
* Il campionamento adattivo è abilitato per impostazione predefinita in tutte le versioni più recenti di Application Insights ASP.NET e ASP.NET Core Software Development Kit (SDK). Viene usato anche da [Funzioni di Azure](../../azure-functions/functions-overview.md).
* Il campionamento a frequenza fissa è disponibile nelle versioni recenti degli SDK di Application Insights per ASP.NET, ASP.NET Core, Java (sia l'agente che l'SDK) e Python.
* Il campionamento per inserimento funziona nell'endpoint Application Insights servizio. Si applica solo quando non è attivo nessun altro campionamento. Se l'SDK campiona i dati di telemetria, il campionamento per inserimento è disabilitato.
* Per le applicazioni Web, se si registrano eventi personalizzati ed è necessario assicurarsi che un set di eventi sia mantenuto o eliminato insieme, gli eventi devono avere lo stesso `OperationId` valore.
* Se si scrivono query di Dati di analisi, è necessario [tener conto del campionamento](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations). In particolare, anziché eseguire semplicemente il conteggio dei record, è necessario usare `summarize sum(itemCount)`.
* Alcuni tipi di telemetria, incluse le metriche delle prestazioni e le metriche personalizzate, vengono sempre mantenuti indipendentemente dal fatto che il campionamento sia abilitato o meno.

La tabella seguente riepiloga i tipi di campionamento disponibili per ogni SDK e tipo di applicazione:

| Application Insights SDK | Campionamento adattivo supportato | Campionamento a frequenza fissa supportato | Campionamento per inserimento supportato |
|-|-|-|-|
| ASP.NET | [Sì (per impostazione predefinita)](#configuring-adaptive-sampling-for-aspnet-applications) | [Sì](#configuring-fixed-rate-sampling-for-aspnet-applications) | Solo se non è attivo alcun altro campionamento |
| Risultato dell'azione di | [Sì (sì per impostazione predefinita)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Sì](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Solo se non è attivo alcun altro campionamento |
| Funzioni di Azure | [Sì (sì per impostazione predefinita)](#configuring-adaptive-sampling-for-azure-functions) | No | Solo se non è attivo alcun altro campionamento |
| Java | No | [Sì](#configuring-fixed-rate-sampling-for-java-applications) | Solo se non è attivo alcun altro campionamento |
| Node.JS | No | [Sì](./nodejs.md#sampling) | Solo se non è attivo alcun altro campionamento
| Python | No | [Sì](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Solo se non è attivo alcun altro campionamento |
| Tutti gli altri | No | No | [Sì](#ingestion-sampling) |

> [!NOTE]
> Le informazioni nella maggior parte di questa pagina si applicano alle versioni correnti Application Insights SDK. Per informazioni sulle versioni precedenti degli SDK, [vedere la sezione seguente.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Tipi di campionamento

Esistono tre diversi metodi di campionamento:

* **Il campionamento** adattivo regola automaticamente il volume di dati di telemetria inviati dall'SDK nell'app ASP.NET/ASP.NET Core e da Funzioni di Azure. Si tratta del campionamento predefinito quando si usa ASP.NET o ASP.NET Core SDK. Il campionamento adattivo è attualmente disponibile solo ASP.NET dati di telemetria lato server e per Funzioni di Azure.

* **Il campionamento** a frequenza fissa riduce il volume di dati di telemetria inviati dal server ASP.NET o ASP.NET Core o Java e dai browser degli utenti. È necessario impostare la frequenza. Il client e il server sincronizzeranno il rispettivo campionamento in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

* **Il campionamento dell'inserimento** avviene nell'endpoint Application Insights servizio. Rimuove alcuni dati di telemetria provenienti dall'app, a una velocità di campionamento impostata. Non riduce il traffico di telemetria inviato dall'app, ma consente all'utente di rispettare la quota mensile. Il vantaggio principale del campionamento di inserimento è che è possibile impostare la frequenza di campionamento senza ridistribuire l'app. Il campionamento di inserimento funziona in modo uniforme per tutti i server e i client, ma non si applica quando sono in esecuzione altri tipi di campionamento.

> [!IMPORTANT]
> Se i metodi di campionamento a frequenza fissa o adattiva sono abilitati per un tipo di telemetria, il campionamento di inserimento è disabilitato per i dati di telemetria. Tuttavia, i tipi di telemetria esclusi dal campionamento a livello di SDK saranno comunque soggetti al campionamento di inserimento alla frequenza impostata nel portale.

## <a name="adaptive-sampling"></a>Campionamento adattivo

Il campionamento adattivo riguarda il volume dei dati di telemetria inviati dall'app del server Web all'endpoint del servizio Application Insights.

> [!TIP]
> Il campionamento adattivo è abilitato per impostazione predefinita quando si usa ASP.NET SDK o ASP.NET Core SDK ed è abilitato anche per impostazione predefinita per Funzioni di Azure.

Il volume viene regolato automaticamente per rimanere entro una velocità massima di traffico specificata ed è controllato tramite l'impostazione `MaxTelemetryItemsPerSecond` . Se l'applicazione produce una quantità bassa di dati di telemetria, ad esempio durante il debug o a causa di un utilizzo ridotto, gli elementi non verranno eliminati dal processore di campionamento purché il volume sia inferiore a `MaxTelemetryItemsPerSecond` . Con l'aumentare del volume dei dati di telemetria, la frequenza di campionamento viene regolata in modo da ottenere il volume di destinazione. La rettifica viene ricalcolata a intervalli regolari e si basa su una media mobile della frequenza di trasmissione in uscita.

Per ottenere il volume di destinazione, alcuni dei dati di telemetria generati vengono eliminati. Tuttavia, come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione.

I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono adattati per compensare la frequenza di campionamento, in modo che mostrino i valori corretti in Esplora metriche.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configurazione del campionamento adattivo per ASP.NET applicazioni

> [!NOTE]
> Questa sezione si applica alle ASP.NET, non alle ASP.NET Core. [Informazioni sulla configurazione del campionamento adattivo per ASP.NET Core più avanti in questo documento.](#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) è possibile modificare diversi parametri nel `AdaptiveSamplingTelemetryProcessor` nodo. Le cifre indicate sono i valori predefiniti:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Frequenza di destinazione delle [operazioni logiche che](./correlation.md#data-model-for-telemetry-correlation) l'algoritmo adattivo mira a raccogliere **in ogni host server.** Se l'app Web viene eseguita su più host, ridurre questo valore per non superare la frequenza di destinazione del traffico nel portale di Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallo in base al quale viene rivalutata la frequenza corrente dei dati di telemetria. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando il valore percentuale di campionamento cambia, quanto tempo dopo è possibile ridurre di nuovo la percentuale di campionamento per acquisire meno dati?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando il valore percentuale di campionamento cambia, quanto tempo dopo è possibile aumentare di nuovo la percentuale di campionamento per acquisire più dati?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Quando la percentuale di campionamento varia, qual è il valore minimo che è possibile impostare?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Quando la percentuale di campionamento varia, qual è il valore massimo che è possibile impostare?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Nel calcolo della media mobile, specifica il peso che deve essere assegnato al valore più recente. Usare un valore uguale o inferiore a 1. I valori più bassi rendono l'algoritmo meno reattivo alle modifiche improvvise.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Quantità di dati di telemetria da campionare quando l'app è appena avviata. Non ridurre questo valore durante il debug.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Elenco delimitato da punto e virgola di tipi di cui non si desidera essere soggetti al campionamento. I tipi riconosciuti sono: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . Vengono trasmessi tutti i dati di telemetria dei tipi specificati. I tipi non specificati verranno campionati.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Elenco delimitato da punti e virgola di tipi che si vuole sottosorvi sotto il campionamento. I tipi riconosciuti sono: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . I tipi specificati verranno campionati. tutti i dati di telemetria degli altri tipi verranno sempre trasmessi.

**Per disattivare il** campionamento adattivo, `AdaptiveSamplingTelemetryProcessor` rimuovere i nodi da `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Configurare il campionamento adattivo nel codice

Anziché impostare il parametro di campionamento nel file, è possibile impostare questi valori `.config` a livello di codice.

1. Rimuovere tutti `AdaptiveSamplingTelemetryProcessor` i nodi dal `.config` file.
2. Usare il frammento di codice seguente per configurare il campionamento adattivo:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Informazioni sui processori di telemetria](./api-filtering-sampling.md#filtering).)

È anche possibile regolare la frequenza di campionamento per ogni tipo di telemetria singolarmente o escludere alcuni tipi dal campionamento:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configurazione del campionamento adattivo per ASP.NET Core

Non è disponibile `ApplicationInsights.config` per le ASP.NET Core, quindi tutte le configurazioni vengono eseguite tramite codice.
Il campionamento adattivo è abilitato per impostazione predefinita in tutte le applicazioni ASP.NET Core, ma può essere disabilitato o è possibile personalizzarne il comportamento.

#### <a name="turning-off-adaptive-sampling"></a>Disattivazione del campionamento adattivo

La funzionalità di campionamento predefinita può essere disabilitata durante l'aggiunta Application Insights servizio, nel metodo `ConfigureServices` , usando `ApplicationInsightsServiceOptions` all'interno del `Startup.cs` file:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Il codice precedente disabilita il campionamento adattivo. Seguire la procedura descritta di seguito per aggiungere al campionamento più opzioni di personalizzazione.

#### <a name="configure-sampling-settings"></a>Configurare le impostazioni di campionamento

Usare i metodi di estensione di `TelemetryProcessorChainBuilder`, come illustrato di seguito, per personalizzare il comportamento della funzionalità di campionamento.

> [!IMPORTANT]
> Se si usa questo metodo per configurare il campionamento, assicurarsi di impostare la `aiOptions.EnableAdaptiveSampling` proprietà su quando si chiama `false` `AddApplicationInsightsTelemetry()` . Dopo aver apportato questa modifica, è necessario seguire  esattamente le istruzioni nel blocco di codice seguente per abilitare nuovamente il campionamento adattivo con le personalizzazioni in uso. In caso negativo, l'inserimento dei dati può risultare in eccesso. Testare sempre le impostazioni di campionamento post-modifica e impostare un limite dati [giornaliero appropriato](pricing.md#set-the-daily-cap) per controllare i costi.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Configurazione del campionamento adattivo per Funzioni di Azure

Seguire le istruzioni di [questa pagina per](../../azure-functions/configure-monitoring.md#configure-sampling) configurare il campionamento adattivo per le app in esecuzione in Funzioni di Azure.

## <a name="fixed-rate-sampling"></a>Campionamento a frequenza fissa

Il campionamento a frequenza fissa riduce il traffico inviato dal server Web e dai Web browser. A differenza del campionamento adattivo, riduce i dati di telemetria a una frequenza fissa definita dall'utente. Il campionamento a frequenza fissa è disponibile per ASP.NET, ASP.NET Core, Java e Python.

Analogamente ad altre tecniche di campionamento, vengono mantenuti anche gli elementi correlati. Sincronizza anche il campionamento client e server in modo che gli elementi correlati siano mantenuti. Ad esempio, quando si osserva una visualizzazione pagina in Ricerca, è possibile trovare le richieste del server correlate. 

In Esplora metriche, frequenze quali il numero di richieste ed eccezioni vengono moltiplicate per un fattore in modo da compensare la frequenza di campionamento ed essere quindi corretti.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Configurazione del campionamento a frequenza fissa per ASP.NET applicazioni

1. **Disabilitare il campionamento** adattivo: in [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) rimuovere o impostare come commento il `AdaptiveSamplingTelemetryProcessor` nodo.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** Aggiungere questo frammento di codice a [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      In alternativa, invece di impostare il parametro di campionamento nel file, è possibile impostare questi valori a `ApplicationInsights.config` livello di codice:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Informazioni sui processori di telemetria](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Configurazione del campionamento a frequenza fissa per ASP.NET Core

1. **Disabilitare il campionamento** adattivo: è possibile apportare modifiche `ConfigureServices` nel metodo usando `ApplicationInsightsServiceOptions` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** È possibile apportare modifiche nel `Configure` metodo come illustrato nel frammento di codice seguente:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Configurazione del campionamento a frequenza fissa per le applicazioni Java

Per impostazione predefinita, nell'agente Java e nell'SDK non è abilitato alcun campionamento. Attualmente supporta solo il campionamento a frequenza fissa. Il campionamento adattivo non è supportato in Java.

#### <a name="configuring-java-agent"></a>Configurazione dell'agente Java

1. Scaricare [applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Per abilitare il campionamento, aggiungere quanto segue al `applicationinsights.json` file:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Configurazione di Java SDK

1. Scaricare e configurare l'applicazione Web con la versione più [Application Insights Java SDK.](./java-get-started.md)

2. **Abilitare il modulo di campionamento a frequenza fissa** aggiungendo il frammento di codice seguente al `ApplicationInsights.xml` file:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. È possibile includere o escludere tipi specifici di dati di telemetria dal campionamento usando i tag seguenti `Processor` all'interno del tag `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

I tipi di telemetria che possono essere inclusi o esclusi dal campionamento sono: `Dependency` , , , , e `Event` `Exception` `PageView` `Request` `Trace` .

> [!NOTE]
> Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero.  Il campionamento attualmente non supporta altri valori.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Configurazione del campionamento a frequenza fissa per applicazioni Python OpenCensus

Instrumentare l'applicazione con gli strumenti [di Monitoraggio di Azure OpenCensus più recenti.](./opencensus-python.md)

> [!NOTE]
> Il campionamento a velocità fissa non è disponibile per l'esportatore di metriche. Ciò significa che le metriche personalizzate sono gli unici tipi di dati di telemetria in cui il campionamento NON può essere configurato. L'esportatore di metriche invierà tutti i dati di telemetria di cui tiene traccia.

#### <a name="fixed-rate-sampling-for-tracing"></a>Campionamento a frequenza fissa per la traccia ####
È possibile specificare un `sampler` come parte della configurazione `Tracer`. Se non viene fornito alcun campionatore esplicito, `ProbabilitySampler` verrà usato per impostazione predefinita. Per impostazione predefinita, verrà utilizzata una frequenza di `ProbabilitySampler` 1/10000, ovvero una richiesta su 10000 verrà inviata a Application Insights. Se si desidera specificare una frequenza di campionamento, vedere di seguito.

Per specificare la frequenza di campionamento, assicurarsi che specifichi un campionatore con una frequenza di campionamento compresa tra `Tracer` 0,0 e 1,0 inclusi. Una frequenza di campionamento di 1,0 rappresenta il 100%, ovvero tutte le richieste verranno inviate come dati di telemetria Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Campionamento a frequenza fissa per i log ####
È possibile configurare il campionamento a frequenza fissa `AzureLogHandler` per modificando `logging_sampling_rate` l'argomento facoltativo. Se non viene specificato alcun argomento, verrà usata una frequenza di campionamento di 1,0. Una frequenza di campionamento di 1,0 rappresenta il 100%, ovvero tutte le richieste verranno inviate come dati di telemetria Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Configurazione del campionamento a frequenza fissa per le pagine Web con JavaScript

Le pagine Web basate su JavaScript possono essere configurate per l'uso Application Insights. I dati di telemetria vengono inviati dall'applicazione client in esecuzione nel browser dell'utente e le pagine possono essere ospitate da qualsiasi server.

Quando si [configurano le pagine](javascript.md)Web basate su JavaScript per Application Insights , modificare il frammento di codice JavaScript che si ottiene dal portale Application Insights.

> [!TIP]
> Nelle ASP.NET con JavaScript incluso, il frammento di codice viene in genere inserito in `_Layout.cshtml` .

Inserire una riga simile a `samplingPercentage: 10,` prima della chiave di strumentazione:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Coordinamento del campionamento lato server e lato client

JavaScript SDK lato client partecipa al campionamento a frequenza fissa in combinazione con l'SDK lato server. Le pagine instrumentate invieranno solo dati di telemetria lato client dallo stesso utente per cui l'SDK lato server ha deciso di includere nel campionamento. Questa logica è progettata per mantenere l'integrità delle sessioni utente tra applicazioni lato client e server. Di conseguenza, da qualsiasi elemento di telemetria specifico in Application Insights è possibile trovare tutti gli altri elementi di telemetria per questo utente o sessione e in Ricerca è possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

Se i dati di telemetria lato client e server non mostrano esempi coordinati:

* Verificare di aver abilitato il campionamento sia nel server che nel client.
* Controllare di avere impostato la stessa percentuale di campionamento sia nel client che nel server.
* Assicurarsi che la versione dell'SDK sia 2.0 o successiva.

## <a name="ingestion-sampling"></a>Campionamento per inserimento

Il campionamento per inserimento funziona nel punto in cui i dati di telemetria del server Web, dei browser e dei dispositivi raggiungono l Application Insights endpoint di servizio. Anche se non riduce il traffico dei dati di telemetria inviato dall'app, riduce la quantità di dati elaborati, conservati e addebitati da Application Insights.

Usare questo tipo di campionamento se l'app spesso supera la quota mensile e non si ha la possibilità di usare uno dei tipi di campionamento basati sull'SDK. 

Impostare la frequenza di campionamento nella pagina Utilizzo e costi stimati:

![Nel pannello Panoramica dell'applicazione fare clic su Impostazioni, Quota, Esempi, quindi selezionare una frequenza di campionamento e fare clic su Aggiorna.](./media/sampling/data-sampling.png)

Come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione. I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono mantenuti correttamente.

I punti dati che vengono rimossi dal campionamento non sono disponibili in alcuna funzionalità di Application Insights, ad esempio nell' [esportazione continua](./export-telemetry.md).

Il campionamento per inserimento non funziona mentre è in funzione il campionamento adattivo o a frequenza fissa. Il campionamento adattivo è abilitato per impostazione predefinita quando si usa ASP.NET SDK o ASP.NET Core SDK o quando Application Insights è abilitato [in Servizio app di Azure ](azure-web-apps.md) o tramite Status Monitor. Quando i dati di telemetria vengono ricevuti dall'endpoint del servizio Application Insights, esamina i dati di telemetria e se la frequenza di campionamento viene segnalata come inferiore al 100% (che indica che i dati di telemetria vengono campionati), la frequenza di campionamento dell'inserimento impostata viene ignorata.

> [!WARNING]
> Il valore visualizzato nel riquadro del portale indica il valore impostato per il campionamento dell'inserimento. Non rappresenta la frequenza di campionamento effettiva se è in esecuzione qualsiasi tipo di campionamento SDK (campionamento adattivo o a frequenza fissa).

## <a name="when-to-use-sampling&quot;></a>Quando usare il campionamento

In generale, per la maggior parte delle applicazioni di piccole e medie dimensioni non è necessario il campionamento. Le informazioni di diagnostica più utili e le statistiche più accurate si ottengono raccogliendo dati su tutte le attività utente. 

I vantaggi principali del campionamento sono:

* Application Insights il servizio elimina (&quot;limitazione") i punti dati quando l'app invia una frequenza di telemetria molto elevata in un breve intervallo di tempo. Il campionamento riduce la probabilità che l'applicazione riduca la limitazione.
* Non superare la [quota](pricing.md) di punti dati per il proprio piano tariffario. 
* Ridurre il traffico di rete dalla raccolta di telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Quale tipo di campionamento è opportuno usare?

**Usare il campionamento per inserimento se:**

* Spesso si usa la quota mensile di dati di telemetria.
* Si stanno ottenendo troppi dati di telemetria dai Web browser degli utenti.
* Si usa una versione dell'SDK che non supporta il campionamento, ad esempio le versioni di ASP.NET precedenti alla 2.

**Usare il campionamento a frequenza fissa se:**

* Si vuole eseguire il campionamento sincronizzato tra client e server in modo che, quando si analizzano gli eventi [in](./diagnostic-search.md)Ricerca, sia possibile spostarsi tra gli eventi correlati nel client e nel server, ad esempio le visualizzazioni di pagina e le richieste HTTP.
* Se è certi della percentuale di campionamento appropriata per l'app. Deve essere abbastanza elevata da ottenere metriche accurate, ma al di sotto della frequenza che fa superare la quota di prezzo e le soglie di limitazione.

**Usare il campionamento adattivo:**

Se le condizioni per l'uso di altre forme di campionamento non sono valide per uno scenario specifico, è consigliabile adottare il campionamento adattativo. Questa impostazione è abilitata per impostazione predefinita in ASP.NET/ASP.NET Core SDK. Non ridurrà il traffico fino a quando non viene raggiunta una determinata frequenza minima, pertanto è probabile che i siti con utilizzo ridotto non verranno campionati affatto.

## <a name="knowing-whether-sampling-is-in-operation"></a>Sapere se il campionamento è in esecuzione

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analisi](../logs/log-query-overview.md) simile alla seguente:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se si vede che `RetainedPercentage` per qualsiasi tipo è minore di 100, viene campionato tale tipo di dati di telemetria.

> [!IMPORTANT]
> Application Insights non esegue il campionamento di sessioni, metriche (incluse metriche personalizzate) o tipi di telemetria del contatore delle prestazioni in una delle tecniche di campionamento. Questi tipi sono sempre esclusi dal campionamento perché una riduzione della precisione può essere altamente indesiderabile per questi tipi di telemetria.

## <a name="how-sampling-works"></a>Funzionamento del campionamento

L'algoritmo di campionamento decide quali elementi di telemetria eliminare e quali mantenere. Questo vale se il campionamento viene eseguito dall'SDK o nel Application Insights servizio. La decisione sul campionamento si basa su alcune regole che hanno lo scopo di lasciare intatti tutti i punti dati correlati, mantenendo in Application Insights un'esperienza di diagnostica sfruttabile e affidabile anche con un set di dati ridotto. Ad esempio, se l'app include una richiesta non riuscita in un esempio, gli elementi di telemetria aggiuntivi, ad esempio l'eccezione e le tracce registrate per questa richiesta, verranno mantenuti. Il campionamento li mantiene o li elimina tutti insieme. Di conseguenza, quando si osservano i dettagli della richiesta in Application Insights, è sempre possibile visualizzare la richiesta con gli elementi di telemetria associati.

La decisione di campionamento si basa sull'ID operazione della richiesta, il che significa che tutti gli elementi di telemetria appartenenti a una determinata operazione vengono mantenuti o eliminati. Per gli elementi di telemetria per cui non è impostato un ID operazione (ad esempio gli elementi di telemetria segnalati da thread asincroni senza contesto HTTP), il campionamento acquisisce semplicemente una percentuale di elementi di telemetria di ogni tipo.

Quando la telemetria viene ripresentata all'utente, il servizio Application Insights modifica le metriche in base alla stessa percentuale di campionamento usata in fase di raccolta, per compensare i punti dati mancanti. Quindi, quando osservano la telemetria in Application Insights, gli utenti visualizzano approssimazioni statisticamente corrette molto vicine ai numeri reali.

La precisione dell'approssimazione dipende in gran parte dalla percentuale di campionamento configurata. La precisione è anche maggiore per le applicazioni che gestiscono un volume elevato di richieste generalmente simili da una grande quantità di utenti. Per le applicazioni che non gestiscono un carico di lavoro significativo, invece, il campionamento non è necessario perché queste applicazioni in genere riescono a inviare tutti i dati di telemetria senza superare la quota e senza causare perdite di dati dovute alla limitazione. 

## <a name="frequently-asked-questions"></a>Domande frequenti

*Qual è il comportamento di campionamento predefinito negli SDK ASP.NET e ASP.NET Core?*

* Se si usa una delle versioni più recenti dell'SDK precedente, il campionamento adattivo è abilitato per impostazione predefinita con cinque elementi di telemetria al secondo.
  Sono presenti due nodi aggiunti per impostazione predefinita e uno include il tipo nel campionamento, mentre l'altro esclude `AdaptiveSamplingTelemetryProcessor` `Event` il tipo dal `Event` campionamento. Questa configurazione significa che l'SDK tenterà di limitare gli elementi di telemetria a cinque elementi di telemetria di tipo e cinque elementi di telemetria di tutti gli altri tipi combinati, assicurando in tal modo che siano campionati separatamente da altri tipi di `Event` `Events` telemetria. Gli eventi vengono in genere usati per i dati di telemetria aziendali e molto probabilmente non dovrebbero essere interessati dai volumi di telemetria di diagnostica.
  
  Di seguito viene illustrato il `ApplicationInsights.config` file predefinito generato. In ASP.NET Core lo stesso comportamento predefinito è abilitato nel codice. Usare gli [esempi nella sezione precedente di questa pagina per](#configuring-adaptive-sampling-for-aspnet-core-applications) modificare questo comportamento predefinito.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*I dati di telemetria possono essere campionati più di una volta?*

* No. SamplingTelemetryProcessors ignora gli elementi da considerazioni sul campionamento se l'elemento è già stato campionato. Lo stesso vale anche per il campionamento per inserimento, che non applicherà il campionamento agli elementi già campionati nell'SDK stesso.

*Perché il campionamento non è una semplice "raccolta di percentuale X di ogni tipo di telemetria"?*

* Sebbene questo approccio di campionamento fornisse un elevato livello di precisione nelle approssimazioni delle metriche, interromperebbe la possibilità di correlare i dati di diagnostica per utente, sessione e richiesta, che è fondamentale per la diagnostica. Di conseguenza, il campionamento funziona meglio con criteri come "raccogliere tutti gli elementi di telemetria per la percentuale X degli utenti dell'app" o "raccogliere tutti i dati di telemetria per la percentuale X delle richieste di app". Per gli elementi di telemetria non associati alle richieste (ad esempio l'elaborazione asincrona in background), il fallback è quello di "raccogliere la percentuale X di tutti gli elementi per ogni tipo di telemetria". 

*La percentuale di campionamento può variare nel tempo?*

* Sì, il campionamento adattivo modifica gradualmente la percentuale di campionamento, in base al volume attualmente osservato della telemetria.

*Se si usa il campionamento a frequenza fissa, come stabilire quale sarà la percentuale di campionamento ideale per l'app?*

* Una modalità è quella di iniziare con il campionamento adattivo, scoprire quale frequenza è impostata (vedere la domanda precedente) e quindi cambiarla a campionamento a frequenza fissa usando quella frequenza. 
  
    In caso contrario, è necessario usare l'intuito. Analizzare l'uso della telemetria corrente in Application Insights, osservare le possibili limitazioni in corso e stimare il volume della telemetria raccolta. Questi tre input, insieme al piano tariffario selezionato, suggeriscono di quanto ridurre il volume dei dati di telemetria raccolti. Tuttavia, un aumento nel numero degli utenti o qualsiasi altra migrazione nel volume di telemetria potrebbe invalidare la stima.

*Cosa accade se si configura la percentuale di campionamento in modo che sia troppo bassa?*

* Le percentuali di campionamento eccessivamente basse causano un campionamento troppo aggressivo e riducono l'accuratezza delle approssimazioni quando Application Insights tenta di compensare la visualizzazione dei dati per la riduzione del volume dei dati. Anche l'esperienza di diagnostica potrebbe essere influenzata negativamente, in quanto alcune delle richieste che raramente hanno esito negativo o lente possono essere campionate.

*Cosa accade se si configura la percentuale di campionamento in modo che sia troppo elevata?*

* La configurazione di una percentuale di campionamento troppo elevata (non abbastanza aggressiva) comporta una riduzione insufficiente del volume dei dati di telemetria raccolti. Potrebbe tuttavia verificarsi una perdita dei dati di telemetria correlata alla limitazione e il costo per l'uso di Application Insights potrebbe essere superiore al previsto per l'applicazione di tariffe aggiuntive.

*Su quali piattaforme è possibile usare il campionamento?*

* Se nell'SDK non è impostato il campionamento, si verifica automaticamente il campionamento per inserimento per i dati di telemetria superiori a un determinato volume. Questa configurazione funziona, ad esempio, se si usa una versione precedente di ASP.NET SDK o Java SDK.
* Se si usano gli SDK ASP.NET o ASP.NET Core correnti (ospitati in Azure o nel proprio server), si ottiene il campionamento adattivo per impostazione predefinita, ma è possibile passare a velocità fissa come descritto in precedenza. Con il campionamento a frequenza fissa, l'SDK del browser si sincronizza automaticamente con gli eventi correlati al campione. 
* Se si usa l'agente Java corrente, è possibile configurare (per Java SDK, configurare ) per attivare il campionamento `applicationinsights.json` `ApplicationInsights.xml` a frequenza fissa. Il campionamento viene disattivato per impostazione predefinita. Con il campionamento a frequenza fissa, l'SDK del browser e il server si sincronizzano automaticamente con gli eventi correlati al campione.

*Esistono alcuni eventi rari che si vuole visualizzare sempre. Come è possibile passarli al modulo di campionamento?*

* Il modo migliore per ottenere questo risultato è scrivere un oggetto [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)personalizzato, che imposta su 100 l'elemento di telemetria che si vuole mantenere, come illustrato `SamplingPercentage` di seguito. Poiché è garantita l'esecuzione degli inizializzatori prima dei processori di telemetria (incluso il campionamento), in questo modo tutte le tecniche di campionamento ignoreranno questo elemento da eventuali considerazioni sul campionamento. Gli inizializzatori di telemetria personalizzati sono disponibili in ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK e Java SDK. Ad esempio, è possibile configurare un inizializzatore di telemetria usando ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Versioni precedenti dell'SDK

Il campionamento adattivo è disponibile per Application Insights SDK per ASP.NET v2.0.0-beta3 e versioni successive, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 e versioni successive ed è abilitato per impostazione predefinita.

Il campionamento a frequenza fissa è una funzionalità dell'SDK nelle versioni ASP.NET dalla 2.0.0 e Java SDK versione 2.0.1 e successive.

Prima della versione 2.5.0-beta2 di ASP.NET SDK e della versione 2.2.0-beta3 di ASP.NET Core SDK, la decisione di campionamento era basata sull'hash dell'ID utente per le applicazioni che definiscono "utente", ovvero le applicazioni Web più comuni. Per i tipi di applicazioni che non definiscono gli utenti (ad esempio i servizi Web), la decisione di campionamento si basava sull'ID operazione della richiesta. Le versioni recenti degli SDK ASP.NET e ASP.NET Core usano l'ID operazione per la decisione di campionamento.

## <a name="next-steps"></a>Passaggi successivi

* [applicazione di filtri](./api-filtering-sampling.md) può garantire un controllo più rigoroso sui dati inviati dall'SDK.
* Leggere l'articolo Rete per sviluppatori [Ottimizzare i dati di telemetria con Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

