---
title: Analizzare le prestazioni
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582379"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Analizzare le prestazioni in Azure ricerca cognitiva

Questo articolo descrive gli strumenti, i comportamenti e gli approcci per l'analisi delle prestazioni delle query e dell'indicizzazione in ricerca cognitiva.

## <a name="develop-baseline-numbers"></a>Sviluppare numeri di base

In qualsiasi implementazione di grandi dimensioni, è fondamentale eseguire un test di benchmarking delle prestazioni del servizio ricerca cognitiva prima di eseguirne il rollup nell'ambiente di produzione. È necessario testare il carico della query di ricerca previsto, ma anche i carichi di lavoro di inserimento dei dati previsti, se possibile, eseguirli simultaneamente. Con i numeri di benchmark è possibile convalidare il [livello di ricerca](search-sku-tier.md)appropriato, la [configurazione del servizio](search-capacity-planning.md)e la [latenza delle query](search-performance-analysis.md#average-query-latency)prevista.

Per sviluppare i benchmark, è consigliabile usare lo strumento [Azure-Search-performance-testing (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) .

Per isolare gli effetti di un'architettura del servizio distribuito, provare a eseguire il test delle configurazioni del servizio di una replica e di una partizione.

> [!NOTE]
> Per i livelli Ottimizzato per l'archiviazione (L1 e L2), sono previste una velocità effettiva delle query inferiore e una latenza superiore rispetto ai livelli Standard.
>

## <a name="use-diagnostic-logging"></a>Usare la registrazione diagnostica

Lo strumento più importante che un amministratore ha per diagnosticare i potenziali problemi di prestazioni è la [registrazione diagnostica](search-monitor-logs.md) che raccoglie dati operativi e metriche sul servizio di ricerca. La registrazione diagnostica viene abilitata tramite [monitoraggio di Azure](../azure-monitor/overview.md). L'uso di monitoraggio di Azure e l'archiviazione dei dati sono associati a costi. Tuttavia, se si Abilita il servizio per il servizio, può essere fondamentale per analizzare i problemi di prestazioni.

È possibile che si verifichino più opportunità di latenza, sia durante il trasferimento di rete che durante l'elaborazione del contenuto nel livello servizi app o in un servizio di ricerca. Un vantaggio principale della registrazione diagnostica è che le attività vengono registrate dal punto di vista del servizio di ricerca, il che significa che il log consente di determinare se i problemi di prestazioni sono dovuti a problemi di query o di indicizzazione o a un altro punto di errore.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Catena di eventi registrati" border="true":::

La registrazione diagnostica offre le opzioni per archiviare le informazioni registrate. È consigliabile usare [log Analytics](../azure-monitor/logs/log-analytics-overview.md) in modo che sia possibile eseguire query kusto avanzate sui dati per rispondere a molte domande sull'utilizzo e sulle prestazioni. 

Nelle pagine del portale per il servizio di ricerca è possibile abilitare la registrazione tramite **le impostazioni di diagnostica** e quindi eseguire query Kusto su log Analytics scegliendo **log**. Per ulteriori informazioni sulla configurazione, vedere [Collect and analyze log data](search-monitor-logs.md).

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Opzioni del menu registrazione" border="true":::

## <a name="throttling-behaviors"></a>Comportamenti di limitazione

La limitazione si verifica quando il servizio di ricerca ha raggiunto il limite di ciò che può gestire dal punto di vista delle risorse. La limitazione può verificarsi durante le query o l'indicizzazione. Dal lato client, una chiamata API genera una risposta HTTP 503 quando è stata limitata. Durante l'indicizzazione, è anche possibile ricevere una risposta HTTP 207, che indica che non è stato possibile indicizzare uno o più elementi. Questo errore indica che il servizio di ricerca sta per avvicinarsi alla capacità. 

Come regola generale, è preferibile quantificare la quantità di limitazione visualizzata. Se, ad esempio, una query di ricerca su 500.000 è limitata, potrebbe non essere quella di un problema. Tuttavia, se un'alta percentuale di query è limitata a un periodo, questo potrebbe essere un problema maggiore. Osservando la limitazione delle richieste in un periodo, è anche possibile identificare i frame temporali in cui la limitazione potrebbe essere più probabile e aiutarti a decidere come adattarla al meglio.

Una semplice correzione alla maggior parte dei problemi di limitazione consiste nel generare più risorse nel servizio di ricerca, in genere le repliche per la limitazione basata sulle query o le partizioni per la limitazione basata sull'indicizzazione. Tuttavia, l'aumento dei costi per le repliche o le partizioni aumenta, motivo per cui è importante capire il motivo per cui si verifica la limitazione delle richieste. L'analisi delle condizioni che provocano la limitazione delle richieste verrà illustrata nelle sezioni successive. 

Di seguito è riportato un esempio di una query kusto in grado di identificare la suddivisione delle risposte HTTP dal servizio di ricerca che è stato sottoposto a caricamento. Eseguendo una query su un periodo di 7 giorni, il grafico a barre di cui è stato eseguito il rendering Mostra che una percentuale relativamente elevata delle query di ricerca è stata limitata rispetto al numero di risposte con esito positivo (200).

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Grafico a barre dei conteggi degli errori http" border="true":::

Esaminando la limitazione in un periodo di tempo specifico, è possibile identificare le ore in cui la limitazione potrebbe verificarsi con maggiore frequenza. Nell'esempio seguente viene usato un grafico Time Series per visualizzare il numero di query limitate che si sono verificate in un intervallo di tempo specificato. In questo caso, sono state eseguite le query limitate correlate ai tempi di esecuzione del benchmarking delle prestazioni.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Grafico a linee delle query limitate" border="true":::

## <a name="measure-individual-queries"></a>Misurare le singole query

In alcuni casi, può essere utile testare singole query per verificarne le prestazioni. A tale scopo, è importante essere in grado di verificare il tempo richiesto dal servizio di ricerca per completare il lavoro, nonché il tempo necessario per eseguire la richiesta di round trip dal client e tornare al client. I log di diagnostica possono essere usati per cercare le singole operazioni, ma potrebbe essere più semplice eseguire questa operazione da uno strumento client, ad esempio un postazione.

Nell'esempio seguente è stata eseguita una query di ricerca basata su REST. Ricerca cognitiva include in ogni risposta il numero di millisecondi necessari per completare la query, visibile nella scheda intestazioni, in "tempo trascorso". Accanto a stato nella parte superiore della risposta, è possibile trovare la durata del round trip. in questo caso, 418 millisecondi. Nella sezione dei risultati è stata scelta la scheda "intestazioni". Usando questi due valori evidenziati con una casella rossa nell'immagine seguente, il servizio di ricerca ha richiesto 21 MS per completare la query di ricerca e l'intera richiesta di round trip del client ha richiesto 125 ms. Sottraendo questi due numeri è possibile determinare che è stato impiegato 104 ms di tempo aggiuntivo per trasmettere la query di ricerca al servizio di ricerca e per trasferire i risultati della ricerca al client.

Questo può essere molto utile per determinare se potrebbero esserci latenze di rete o altri fattori che influiscono sulle prestazioni delle query.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Metrica durata query" border="true":::

## <a name="query-rates"></a>Frequenza query

Un potenziale motivo per cui il servizio di ricerca può limitare le richieste è dovuto al numero totale di query eseguite, in cui il volume viene acquisito come query al secondo (query al secondo) o query al minuto (QPM). Poiché il servizio di ricerca riceve più query al secondo, richiederà in genere più tempo e più tempo per rispondere alle query fino a quando non potrà più rimanere attivo, in quanto invierà una risposta HTTP di limitazione 503. 

La query kusto seguente mostra il volume di query misurato in QPM, insieme alla durata media di una query in millisecondi (AvgDurationMS) e al numero medio di documenti (AvgDocCountReturned) restituiti in ognuno di essi.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Grafico che mostra le query al minuto" border="true":::

> [!TIP]
> Per visualizzare i dati sottostanti questo grafico, rimuovere la riga `| render timechart` e quindi eseguire di nuovo la query.

## <a name="impact-of-indexing-on-queries"></a>Conseguenze dell'indicizzazione sulle query

Un fattore importante da considerare quando si esaminano le prestazioni è che l'indicizzazione usa le stesse risorse delle query di ricerca. Se si esegue l'indicizzazione di una grande quantità di contenuto, è possibile prevedere che la latenza cresca quando il servizio tenta di gestire entrambi i carichi di lavoro.

Se le query sono rallentate, esaminare la tempistica dell'attività di indicizzazione per verificare se coincide con il calo delle query. Ad esempio, un indicizzatore esegue un processo giornaliero o orario correlato alle prestazioni diminuite delle query di ricerca. 

In questa sezione viene fornito un set di query che consentono di visualizzare le tariffe di ricerca e di indicizzazione. Per questi esempi, l'intervallo di tempo viene impostato nella query. Assicurarsi di indicare **set in query** durante l'esecuzione delle query in portale di Azure.

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Impostazione degli intervalli di tempo nello strumento query" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Latenza media query

Nella query seguente viene usata una dimensione di intervallo di 1 minuto per visualizzare la latenza media delle query di ricerca. Dal grafico è possibile vedere che la latenza media era bassa fino a 5:45pm e infine fino a 5:53pm.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Grafico che mostra la latenza media delle query" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Media di query al minuto (QPM)

La query seguente consente di esaminare il numero medio di query al minuto per assicurarsi che non vi siano alcune specie di picchi nelle richieste di ricerca che potrebbero avere influito sulla latenza. Dal grafico è possibile vedere che è presente una varianza, ma niente per indicare un picco nel numero di richieste.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Grafico che mostra le query medie al minuto" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Operazioni di indicizzazione al minuto (OPM)

Qui si esaminerà il numero di operazioni di indicizzazione al minuto. Dal grafico è possibile vedere che è stata indicizzata una grande quantità di dati avviata alle 5:42 PM e terminata alle 5:50pm. Questa indicizzazione è iniziata 3 minuti prima che le query di ricerca iniziano a diventare latenti e terminano 3 minuti prima che le query di ricerca non fossero più latenti.

In questo modo si noterà che sono necessari circa 3 minuti affinché il servizio di ricerca sia sufficientemente occupato dall'indicizzazione per iniziare a influenzare la latenza delle query di ricerca. Si noterà inoltre che dopo il completamento dell'indicizzazione sono stati necessari altri 3 minuti affinché il servizio di ricerca completi tutto il lavoro dal contenuto appena indicizzato, fino a quando le query di ricerca non iniziano a essere più latenti.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Grafico che mostra le operazioni di indicizzazione al minuto" border="true":::

## <a name="background-service-processing"></a>Elaborazione del servizio in background

Non è insolito vedere picchi periodici nella latenza di query o di indicizzazione. I picchi possono verificarsi in risposta all'indicizzazione o a frequenze di query elevate, ma possono verificarsi anche durante le operazioni di merge. Gli indici di ricerca vengono archiviati in blocchi o partizioni. Periodicamente, il sistema unisce partizioni più piccole in partizioni di grandi dimensioni, che consentono di ottimizzare le prestazioni del servizio. Questo processo di merge pulisce anche i documenti precedentemente contrassegnati per l'eliminazione dall'indice, con conseguente recupero dello spazio di archiviazione. 

L'Unione di partizioni è veloce, ma anche di utilizzo intensivo delle risorse e pertanto può causare un peggioramento delle prestazioni del servizio. Per questo motivo, se si verificano picchi brevi di latenza di query e tali picchi coincidono con le recenti modifiche apportate al contenuto indicizzato, è probabile che la latenza venga attribuita alle operazioni di Unione delle partizioni. 

## <a name="next-steps"></a>Passaggi successivi

Esaminare questi articoli aggiuntivi relativi all'analisi delle prestazioni del servizio.

+ [Suggerimenti per incrementare le prestazioni](search-performance-tips.md)
+ [Scegliere un livello di servizio](search-sku-tier.md)
+ [Gestire la capacità](search-capacity-planning.md)
