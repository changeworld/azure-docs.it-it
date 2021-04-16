---
title: Monitorare le applicazioni Python con Monitoraggio di Azure | Microsoft Docs
description: Fornisce istruzioni per collegare OpenCensus Python con Monitoraggio di Azure
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 92d954a865a2d4a8c55177b132139dcd7d0444ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515924"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurare Monitoraggio di Azure per l'applicazione Python

Monitoraggio di Azure supporta la traccia distribuita, la raccolta di metriche e la registrazione delle applicazioni Python tramite l'integrazione con [OpenCensus](https://opencensus.io). Questo articolo illustra il processo di configurazione di OpenCensus per Python e l'invio dei dati di monitoraggio Monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installazione di Python. Questo articolo usa [Python 3.7.0,](https://www.python.org/downloads/release/python-370/)anche se è probabile che altre versioni funzionino con modifiche secondarie. L'SDK supporta solo Python v2.7 e v3.4-v3.7.
- Creazione di una[risorsa](./create-new-resource.md) di Application Insights. Verrà assegnata la propria chiave di strumentazione (ikey) per la risorsa.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentare con l'SDK OpenCensus Python per Monitoraggio di Azure

Installare le utilità di esportazione di Monitoraggio di Azure per OpenCensus:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> Il comando `python -m pip install opencensus-ext-azure` presuppone la presenza di una variabile di ambiente `PATH` impostata per l'installazione di Python. Se questa non è stata configurata, sarà necessario specificare il percorso completo della directory in cui si trova l'eseguibile di Python. Il risultato è un comando simile al seguente: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

L'SDK usa tre Monitoraggio di Azure di esportazione per inviare diversi tipi di dati di telemetria Monitoraggio di Azure. Si tratta di traccia, metriche e log. Per altre informazioni su questi tipi di telemetria, vedere la [panoramica della piattaforma dati](../data-platform.md). Usare le istruzioni seguenti per inviare questi tipi di dati di telemetria tramite le tre utilità di esportazione.

## <a name="telemetry-type-mappings"></a>Mapping dei tipi di telemetria

Di seguito sono riportate le unità di esportazione fornite da OpenCensus mappate ai tipi di dati di telemetria visualizzati in Monitoraggio di Azure.

| Pilastro dell'osservabilità | Tipo di telemetria Monitoraggio di Azure    | Spiegazione                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Log                    | Tracce, eccezioni, customEvents   | Telemetria dei log, telemetria delle eccezioni, telemetria degli eventi |
| Metriche                 | customMetrics, performanceCounters | Contatori delle prestazioni delle metriche personalizzate                |
| Traccia                 | Richiede le dipendenze             | Richieste in ingresso, richieste in uscita                |

### <a name="logs"></a>Log

1. Prima di tutto, generare alcuni dati di log locali.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Il codice richiede continuamente l'immissione di un valore. Per ogni valore immesso viene generata una voce di log.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere i dati di log in Monitoraggio di Azure. Passare la stringa di connessione direttamente nell'esportatore. In caso contrario, è possibile specificarlo in una variabile di ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. L'esportatore invia i dati di log Monitoraggio di Azure. È possibile trovare i dati in `traces`. 

    > [!NOTE]
    > In questo contesto, `traces` non è uguale a `tracing` . In questo caso, si riferisce al tipo di telemetria che verrà visualizzato in Monitoraggio di Azure `traces` quando si usa `AzureLogHandler` . Ma `tracing` fa riferimento a un concetto in OpenCensus e si riferisce alla traccia [distribuita](./distributed-tracing.md).

    > [!NOTE]
    > Il logger radice è configurato con il livello WARNING. Ciò significa che tutti i log inviati con una gravità inferiore vengono ignorati e, a loro volta, non verranno inviati a Monitoraggio di Azure. Per altre informazioni, vedere la [documentazione](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. È anche possibile aggiungere proprietà personalizzate ai messaggi di log nell'argomento *della* parola chiave aggiuntivo usando il custom_dimensions campo. Queste proprietà vengono visualizzate come coppie chiave-valore `customDimensions` in Monitoraggio di Azure.
    > [!NOTE]
    > Per il corretto funzionamento di questa caratteristica, è necessario passare un dizionario al campo custom_dimensions. Se si passano argomenti di qualsiasi altro tipo, il logger li ignora.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Configurare la registrazione per le applicazioni Django

È possibile configurare la registrazione in modo esplicito nel codice dell'applicazione come sopra per le applicazioni Django oppure è possibile specificarla nella configurazione di registrazione di Django. Questo codice può essere inserito in qualsiasi file utilizzato per la configurazione delle impostazioni Django. Per informazioni su come configurare le impostazioni django, vedere [Impostazioni django](https://docs.djangoproject.com/en/3.0/topics/settings/). Per altre informazioni sulla configurazione della registrazione, vedere [Registrazione Django](https://docs.djangoproject.com/en/3.0/topics/logging/).

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Assicurarsi di usare il logger con lo stesso nome di quello specificato nella configurazione.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Inviare eccezioni

OpenCensus Python non tiene traccia e invia automaticamente dati `exception` di telemetria. Vengono inviati tramite `AzureLogHandler` tramite le eccezioni tramite la libreria di registrazione Python. È possibile aggiungere proprietà personalizzate, come con la registrazione normale.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Poiché è necessario registrare le eccezioni in modo esplicito, è l'utente a voler registrare le eccezioni non gestite. OpenCensus non posiziona restrizioni su come un utente vuole eseguire questa operazione, purché registri in modo esplicito i dati di telemetria delle eccezioni.

#### <a name="send-events"></a>Inviare eventi

È possibile inviare i dati di telemetria esattamente nello stesso modo in cui si inviano i dati `customEvent` `trace` di telemetria, ad eccezione dell'uso `AzureEventHandler` di .

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere l'articolo sul [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlazione dei log

Per informazioni dettagliate su come arricchire i log con i dati del contesto di traccia, vedere l'[integrazione dei log](./correlation.md#log-correlation) OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni dettagliate su come modificare i dati di telemetria tracciati prima che siano inviati Monitoraggio di Azure, vedere Processori di telemetria di OpenCensus [Python.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)


### <a name="metrics"></a>Metriche

OpenCensus.stats supporta 4 metodi di aggregazione, ma fornisce supporto parziale per Monitoraggio di Azure:

- **Conteggio:** Conteggio del numero di punti di misurazione. Il valore è cumulativo, può aumentare e reimpostarlo su 0 solo al riavvio. 
- **Somma:** Somma dei punti di misurazione. Il valore è cumulativo, può aumentare e reimpostarlo su 0 solo al riavvio. 
- **LastValue:** Mantiene l'ultimo valore registrato, elimina tutto il resto.
- **Distribuzione:** Distribuzione dell'istogramma dei punti di misurazione. Questo metodo NON **è supportato dall'esportazione di Azure.**

### <a name="count-aggregation-example"></a>Esempio di aggregazione count

1. Prima di tutto, generare alcuni dati di metrica locali. Verrà creata una metrica semplice per tenere traccia del numero di volte in cui l'utente seleziona **il tasto** INVIO.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. L'esecuzione ripetuta del codice richiede di premere **INVIO.** Viene creata una metrica per tenere traccia del numero di volte in cui **viene selezionato** INVIO. Con ogni voce, il valore viene incrementato e le informazioni sulla metrica vengono visualizzate nella console. Le informazioni includono il valore corrente e il timestamp corrente al momento dell'aggiornamento della metrica.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Sebbene l'immissione di valori sia utile a scopo dimostrativo, in definitiva si vogliono trasmettere i dati della metrica in Monitoraggio di Azure. Passare la stringa di connessione direttamente nell'esportatore. In caso contrario, è possibile specificarlo in una variabile di ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. L'esportatore invia i dati delle metriche Monitoraggio di Azure a un intervallo fisso. Il valore predefinito è ogni 15 secondi. Si sta verificando una singola metrica, quindi i dati della metrica, con qualsiasi valore e timestamp che contiene, vengono inviati a ogni intervallo. Il valore è cumulativo, può aumentare e reimpostarlo su 0 solo al riavvio. È possibile trovare i dati in , ma le proprietà `customMetrics` `customMetrics` valueCount, valueSum, valueMin, valueMax e valueStdDev non vengono effettivamente usate.

### <a name="setting-custom-dimensions-in-metrics"></a>Impostazione di dimensioni personalizzate nelle metriche

Opencensus Python SDK consente di aggiungere dimensioni personalizzate ai dati di telemetria delle metriche tramite , che sono essenzialmente un dizionario di `tags` coppie chiave/valore. 

1. Inserire i tag da usare nella mappa tag. La mappa tag funziona come una sorta di "pool" di tutti i tag disponibili che è possibile usare.

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. Per un oggetto specifico, specificare i tag da usare durante la registrazione delle metriche `View` con tale visualizzazione tramite la chiave del tag.

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. Assicurarsi di usare la mappa tag durante la registrazione nella mappa delle misurazioni. Le chiavi di tag specificate in `View` devono essere trovate nella mappa tag usata per registrare.

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. Nella tabella `customMetrics` tutti i record delle metriche generati usando avranno dimensioni personalizzate `prompt_view` `{"url":"http://example.com"}` .

1. Per produrre tag con valori diversi usando le stesse chiavi, creare nuove mappe tag.

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>Contatori delle prestazioni

Per impostazione predefinita, l'esportatore di metriche invia un set di contatori delle prestazioni Monitoraggio di Azure. Si può disabilitare questa impostazione impostando il flag di `enable_standard_metrics` su `False` nel costruttore dell'utilità di esportazione delle metriche.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Questi contatori delle prestazioni sono attualmente inviati:

- Memoria disponibile (byte)
- Tempo processore CPU (percentuale)
- Frequenza richieste in ingresso (al secondo)
- Tempo di esecuzione medio richieste in ingresso (millisecondi)
- Utilizzo CPU processi (percentuale)
- Byte privati processi (byte)

Dovrebbe essere possibile visualizzare queste metriche in `performanceCounters`. Per altre informazioni, vedere i [contatori delle prestazioni](./performance-counters.md).

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per informazioni su come modificare i dati di telemetria tracciati prima che siano inviati Monitoraggio di Azure, vedere Processori di telemetria di OpenCensus [Python.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)

### <a name="tracing"></a>Traccia

> [!NOTE]
> In OpenCensus fa `tracing` riferimento alla traccia [distribuita](./distributed-tracing.md). `AzureExporter` invia la telemetria per `requests` e `dependency` a Monitoraggio di Azure.

1. Prima di tutto, è necessario generare alcuni dati di traccia in locale. In Python IDLE o nell'editor preferito immettere il codice seguente:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. L'esecuzione ripetuta del codice richiede di immettere un valore. Con ogni voce, il valore viene stampato nella shell. Il modulo OpenCensus Python genera una parte corrispondente di `SpanData` . Nel progetto OpenCensus, una [trace è un albero di span](https://opencensus.io/core-concepts/tracing/).
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Anche se l'immissione di valori è utile a scopo dimostrativo, in definitiva si vuole generare `SpanData` per Monitoraggio di Azure. Passare la stringa di connessione direttamente nell'esportatore. In caso contrario, è possibile specificarlo in una variabile di ambiente, `APPLICATIONINSIGHTS_CONNECTION_STRING` . Modificare il codice del passaggio precedente in base all'esempio di codice seguente:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. A questo punto, quando si esegue lo script Python, verrà ancora richiesto di immettere valori, ma verrà stampato nella shell solo il valore. `SpanData`L'oggetto creato viene inviato Monitoraggio di Azure. È possibile trovare i dati di span emessi in `dependencies`. Per altre informazioni sulle richieste in uscita, vedere Dipendenze [di](./opencensus-python-dependency.md)OpenCensus Python.
Per altre informazioni sulle richieste in ingresso, vedere Richieste [Python](./opencensus-python-request.md)OpenCensus.

#### <a name="sampling"></a>campionamento

Per informazioni sul campionamento in OpenCensus, vedere l'articolo sul [campionamento in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlazione delle tracce

Per altre informazioni sulla correlazione dei dati di telemetria nei dati di traccia, vedere Correlazione dei dati di telemetria [di](./correlation.md#telemetry-correlation-in-opencensus-python)OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificare la telemetria

Per altre informazioni su come modificare i dati di telemetria tracciati prima che siano inviati Monitoraggio di Azure, vedere Processori di telemetria di OpenCensus [Python.](./api-filtering-sampling.md#opencensus-python-telemetry-processors)

## <a name="configure-azure-monitor-exporters"></a>Configurare Monitoraggio di Azure esportatori

Come illustrato, esistono tre diversi tipi di Monitoraggio di Azure che supportano OpenCensus. Ognuno invia tipi diversi di dati di telemetria Monitoraggio di Azure. Per visualizzare i tipi di dati di telemetria inviati da ogni esportatore, vedere l'elenco seguente.

Ogni esportatore accetta gli stessi argomenti per la configurazione, passati tramite i costruttori. È possibile visualizzare i dettagli su ognuno di essi qui:

- `connection_string`: stringa di connessione usata per connettersi alla Monitoraggio di Azure risorsa. Ha la priorità rispetto a `instrumentation_key` .
- `enable_standard_metrics`: usato per `AzureMetricsExporter` . Segnala all'esportatore di inviare [automaticamente le metriche dei contatori](../essentials/app-insights-metrics.md#performance-counters) delle prestazioni Monitoraggio di Azure. Il valore predefinito è `True`.
- `export_interval`: usato per specificare la frequenza in secondi di esportazione.
- `instrumentation_key`: chiave di strumentazione usata per connettersi alla Monitoraggio di Azure risorsa.
- `logging_sampling_rate`: usato per `AzureLogHandler` . Fornisce una frequenza di campionamento [0,1.0] per l'esportazione dei log. Il valore predefinito è 1.0.
- `max_batch_size`: specifica le dimensioni massime dei dati di telemetria esportati contemporaneamente.
- `proxies`: specifica una sequenza di proxy da usare per l'invio di dati Monitoraggio di Azure. Per altre informazioni, vedere [proxy](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: percorso in cui è presente la cartella di archiviazione locale (telemetria non ent). A causa `opencensus-ext-azure` della versione 1.0.3, il percorso predefinito è la directory temporanea del sistema operativo + `opencensus-python`  +  `your-ikey` . Prima della versione 1.0.3, il percorso predefinito è $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Visualizzare i dati usando query

È possibile visualizzare i dati di telemetria inviati dall'applicazione tramite la scheda **Log (Analisi)** .

![Screenshot del riquadro della panoramica con "Log (Analisi)" selezionato in una casella rossa](./media/opencensus-python/0010-logs-query.png)

Nell'elenco sotto **Attiva**:

- Per i dati di telemetria inviati con l'utilità di esportazione log di Monitoraggio di Azure, le richieste in ingresso vengono visualizzate in `requests`. Le richieste in uscita o in elaborazione vengono visualizzate in `dependencies`.
- Per i dati di telemetria inviati con l'utilità di esportazione delle metriche di Monitoraggio di Azure, le metriche inviate vengono visualizzate in `customMetrics`.
- Per i dati di telemetria inviati con l'utilità di esportazione log di Monitoraggio di Azure, i log vengono visualizzati in `traces`. Le eccezioni vengono visualizzate in `exceptions`.

Per informazioni più dettagliate su come usare le query e i log, vedere [Log in Monitoraggio di Azure](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Altre informazioni su OpenCensus per Python

* [OpenCensus Python su GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalizzazione](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Utilità di esportazione di Monitoraggio di Azure su GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrazioni di OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Applicazioni di esempio di Monitoraggio di Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Passaggi successivi

* [Rilevamento delle richieste in ingresso](./opencensus-python-dependency.md)
* [Rilevamento delle richieste in uscita](./opencensus-python-request.md)
* [Mappa delle applicazioni](./app-map.md)
* [Monitoraggio delle prestazioni end-to-end](../app/tutorial-performance.md)

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](./monitor-web-app-availability.md): creare test per assicurarsi che il sito sia visibile sul Web.
* [Diagnostica intelligente](./proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi delle metriche](../alerts/alerts-log.md): Impostare avvisi per essere avvertiti se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.

