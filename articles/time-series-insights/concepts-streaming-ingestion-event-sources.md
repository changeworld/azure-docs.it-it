---
title: Origini eventi di inserimento in streaming - Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sullo streaming dei dati Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 499cb3c978a67f9ef71e6ad9dd03be9f05b45729
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726971"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights di eventi gen2

L'Azure Time Series Insights Gen2 può avere fino a due origini eventi di streaming. Come input sono supportati due tipi di risorse di Azure:

- [Hub IoT Azure](../iot-hub/about-iot-hub.md)
- [Hub eventi di Azure](../event-hubs/event-hubs-about.md)

Gli eventi devono essere inviati come JSON con codifica UTF-8.

## <a name="create-or-edit-event-sources"></a>Creare o modificare origini eventi

L'origine evento è il collegamento tra l'hub e l'ambiente Azure Time Series Insights Gen2 e viene creata una risorsa separata di tipo `Time Series Insights event source` nel gruppo di risorse. Le risorse dell'hub IoT o dell'hub eventi possono essere presenti nella stessa sottoscrizione di Azure dell'ambiente Azure Time Series Insights Gen2 o in una sottoscrizione diversa. È tuttavia consigliabile ospitare l'ambiente Azure Time Series Insights e l'hub IoT o l'hub eventi all'interno della stessa area di Azure.

È possibile usare i [portale di Azure,](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment)l'interfaccia della riga di comando di [Azure,](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source)i modelli Azure Resource Manager e l'API [REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) per creare, modificare o rimuovere le origini eventi dell'ambiente. [](time-series-insights-manage-resources-using-azure-resource-manager-template.md)

> [!WARNING]
> Non limitare l'accesso a Internet pubblico a un hub o a un'origine evento usata Time Series Insights o la connessione necessaria verrà interrotta.

## <a name="start-options"></a>Opzioni di avvio

Quando si crea un'origine evento, è possibile specificare quali dati preesistere devono essere raccolti. Questa impostazione è facoltativa. Sono disponibili le opzioni seguenti:

| Nome   |  Descrizione  |  Azure Resource Manager di modello |
|----------|-------------|------|
| EarliestAvailable | Inserire tutti i dati preesistiti archiviati all'interno dell'Hub eventi o IoT | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Iniziare a inserire i dati che arrivano dopo la creazione dell'origine evento. Tutti i dati preesistnti trasmessi prima della creazione dell'origine evento verranno ignorati. Questa è l'impostazione predefinita nel portale di Azure   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | L'ambiente inserisce i dati dall'ora UTC (Enqueued Time) personalizzata. Tutti gli eventi accodati nell'Hub eventi o IoT in corrispondenza o dopo l'ora di accodamento personalizzata verranno inseriti e archiviati. Tutti gli eventi che sono arrivati prima dell'ora di accodamento personalizzata verranno ignorati. Si noti che "tempo di accodamento" si riferisce all'ora (in UTC) in cui l'evento è arrivato nell'Hub eventi o IoT. Questo comportamento è diverso da una proprietà [timestamp personalizzata](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) che si trova all'interno del corpo dell'evento. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Se si seleziona EarliestAvailable e si hanno molti dati preesistbili, è possibile che si verifichi un'elevata latenza iniziale quando l'ambiente Azure Time Series Insights Gen2 elabora tutti i dati.
> - Questa latenza elevata dovrebbe infine attenuarsi quando i dati vengono indicizzati. Inviare un ticket di supporto con il portale di Azure se si verifica una latenza elevata.

- EarliestAvailable

![Diagramma earliestAvailable](media/concepts-streaming-event-sources/event-source-earliest-available.png)

- EventSourceCreationTime

![Diagramma EventSourceCreationTime](media/concepts-streaming-event-sources/event-source-creation-time.png)

- CustomEnqueuedTime

![Diagramma CustomEnqueuedTime](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)

## <a name="streaming-ingestion-best-practices"></a>Procedure consigliate per l'inserimento di streaming

- Creare sempre un gruppo di consumer univoco per l'ambiente Azure Time Series Insights Gen2 per utilizzare i dati dall'origine evento. Il riutilizzo dei gruppi di consumer può causare disconnessioni casuali e può causare la perdita di dati.

- Configurare l'Azure Time Series Insights Gen2 e l'hub IoT e/o Hub eventi nella stessa area di Azure. Anche se è possibile configurare un'origine eventi in un'area separata, questo scenario non è supportato e non è possibile garantire la disponibilità elevata.

- Non superare il limite di velocità effettiva dell'ambiente [o](./concepts-streaming-ingress-throughput-limits.md) per ogni limite di partizione.

- Configurare un avviso di [ritardo per](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) ricevere una notifica se nell'ambiente si verificano problemi durante l'elaborazione dei dati. Per le [condizioni di avviso suggerite,](./concepts-streaming-ingestion-event-sources.md#production-workloads) vedere Carichi di lavoro di produzione di seguito.

- Usare l'inserimento streaming solo per i dati near real-time e recenti. I dati cronologici di streaming non sono supportati.

- Comprendere in che modo le proprietà verranno precedute da caratteri di escape e i dati JSON [verranno appiattiti e archiviati.](./concepts-json-flattening-escaping-rules.md)

- Seguire il principio dei privilegi minimi quando si forniscono le stringhe di connessione dell'origine evento. Per Hub eventi, configurare un criterio di accesso condiviso solo con l'attestazione *di* invio e per l'hub IoT usare solo l'autorizzazione *di connessione* del servizio.

> [!CAUTION]
> Se si elimina l'hub IoT o l'hub eventi e si crea nuovamente una nuova risorsa con lo stesso nome, è necessario creare una nuova origine evento e collegare il nuovo hub IoT o hub eventi. I dati non verranno inseriti fino a quando non si completa questo passaggio.

## <a name="production-workloads"></a>Carichi di lavoro di produzione

Oltre alle procedure consigliate precedenti, è consigliabile implementare quanto segue per i carichi di lavoro business critical.

- Aumentare il tempo di conservazione dei dati dell'hub IoT o dell'hub eventi fino a un massimo di sette giorni.

- Creare avvisi dell'ambiente nella portale di Azure. Gli avvisi basati sulle metriche [della](./how-to-monitor-tsi-reference.md#metrics) piattaforma consentono di convalidare il comportamento della pipeline end-to-end. Le istruzioni per la creazione e la gestione degli avvisi sono [disponibili qui.](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) Condizioni di avviso suggerite:

  - IngressReceivedMessagesTimeLag è maggiore di 5 minuti
  - IngressReceivedBytes è 0
- Mantenere il carico di inserimento bilanciato tra le partizioni dell'hub IoT o dell'hub eventi.

### <a name="historical-data-ingestion"></a>Inserimento dei dati cronologici

L'uso della pipeline di streaming per importare dati cronologici non è attualmente supportato in Azure Time Series Insights Gen2. Se è necessario importare i dati passati nell'ambiente in uso, seguire le linee guida indicate sotto:

- Non trasmettere dati in tempo reale e cronologici in parallelo. L'inserimento di dati non in ordine comporterà un calo delle prestazioni delle query.
- Inserire i dati cronologici rispettando la sequenza temporale per ottenere prestazioni ottimali.
- Rispettare i limiti di velocità effettiva di inserimento indicati sotto.
- Disabilitare l'archivio ad accesso frequente se i dati sono precedenti al periodo di conservazione dell'archivio ad accesso frequente.

## <a name="event-source-timestamp"></a>Timestamp dell'origine evento

Quando si configura un'origine evento, verrà chiesto di specificare una proprietà id timestamp. La proprietà timestamp viene usata per tenere traccia degli eventi nel tempo, che è l'ora che verrà usata come timestamp nelle API di query e per tracciare le serie `$ts` in Azure Time Series Insights Explorer. [](/rest/api/time-series-insights/dataaccessgen2/query/execute) Se non viene specificata alcuna proprietà in fase di creazione o se la proprietà timestamp non è presente in un evento, come impostazione predefinita verrà usata l'ora di accodamento dell'hub IoT o degli hub eventi dell'evento. I valori delle proprietà timestamp vengono archiviati in formato UTC.

In generale, gli utenti opteranno per personalizzare la proprietà timestamp e usare l'ora in cui il sensore o il tag ha generato la lettura anziché usare l'ora di accodamento predefinita dell'hub. Ciò è particolarmente necessario quando i dispositivi hanno una perdita di connettività intermittente e un batch di messaggi ritardati viene inoltrato a Azure Time Series Insights Gen2.

Se il timestamp personalizzato si trova all'interno di un oggetto JSON annidato o di una matrice, è necessario specificare il nome corretto della proprietà seguendo le convenzioni di denominazione flat [e](concepts-json-flattening-escaping-rules.md)escape. Ad esempio, il timestamp dell'origine evento per il payload JSON mostrato qui [deve](concepts-json-flattening-escaping-rules.md#example-a) essere immesso come `"values.time"` .

### <a name="time-zone-offsets"></a>Offset del fuso orario

I timestamp devono essere inviati in formato ISO 8601 e verranno archiviati in formato UTC. Se viene specificata una differenza di fuso orario, verrà applicata l'offset e quindi l'ora archiviata e restituita in formato UTC. Se l'offset è formattato in modo non corretto, verrà ignorato. Nelle situazioni in cui la soluzione potrebbe non avere il contesto dell'offset originale, è possibile inviare i dati di offset in una proprietà di evento separata aggiuntiva per assicurarsi che venga mantenuta e che l'applicazione possa fare riferimento in una risposta di query.

La offset del fuso orario deve essere formattata come una delle seguenti:

±HHMMZ<br />
±HH:MM<br />
±HH:MMZ

## <a name="next-steps"></a>Passaggi successivi

- Leggere json [Flattening and Escaping Rules (Regole](./concepts-json-flattening-escaping-rules.md) di flatizzazione ed escape JSON) per comprendere come verranno archiviati gli eventi.

- Comprendere le limitazioni della velocità effettiva [dell'ambiente](./concepts-streaming-ingress-throughput-limits.md)
