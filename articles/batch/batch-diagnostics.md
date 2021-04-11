---
title: Metriche, avvisi e log di diagnostica
description: Registrare e analizzare gli eventi di registrazione diagnostica per le risorse dell'account Azure Batch, come pool e attività.
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: seodec18
ms.openlocfilehash: 22fdf00b6e144e022f955aed6fd24b7a6bcb7300
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606029"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriche, avvisi e log di Batch per la valutazione diagnostica e il monitoraggio

Monitoraggio di Azure raccoglie [metriche](../azure-monitor/essentials/data-platform-metrics.md) e [log di diagnostica](../azure-monitor/essentials/platform-logs-overview.md) per le risorse nell'account Azure batch.

È possibile raccogliere e utilizzare questi dati in diversi modi per monitorare l'account batch e diagnosticare i problemi. È anche possibile configurare [avvisi sulle metriche](../azure-monitor/alerts/alerts-overview.md) per ricevere notifiche quando una metrica raggiunge un valore specificato.

## <a name="batch-metrics"></a>Metriche di Batch

Le [metriche](../azure-monitor/essentials/data-platform-metrics.md) sono dati di telemetria di Azure (detti anche contatori delle prestazioni) che vengono emessi dalle risorse di Azure e utilizzati dal servizio monitoraggio di Azure. Esempi di metriche in un account batch sono gli eventi di creazione del pool, il numero di nodi Low-Priority e gli eventi di completamento dell'attività. Queste metriche consentono di identificare le tendenze e possono essere usate per l'analisi dei dati.

Vedere l'[elenco delle metriche di Batch supportate](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts).

Le metriche:

- Vengono abilitate per impostazione predefinita in ogni account Batch senza alcuna configurazione aggiuntiva
- Vengono generate ogni minuto
- Non sono automaticamente persistenti, ma hanno una cronologia in sequenza di 30 giorni. È possibile mantenere persistenti le metriche di attività come parte della registrazione diagnostica.

## <a name="view-batch-metrics"></a>Visualizzare le metriche batch

Nella portale di Azure la pagina **Panoramica** per l'account batch mostrerà le metriche principali per nodo, core e attività per impostazione predefinita.

Per visualizzare le metriche aggiuntive per un account batch:

1. Nella portale di Azure selezionare tutti i **Servizi**  >  **account batch** e quindi selezionare il nome dell'account batch.
1. In **Monitoraggio** selezionare **Metrica**.
1. Selezionare **Aggiungi metrica** , quindi scegliere una metrica dall'elenco a discesa.
1. Selezionare un'opzione di **aggregazione** per la metrica. Per le metriche basate su conteggi (ad esempio "conteggio dei core dedicati" o "numero di nodi con priorità bassa"), usare l'aggregazione **AVG** . Per le metriche basate su eventi, ad esempio "eventi di completamento ridimensionamento pool", usare l'aggregazione **count**. Evitare l'utilizzo dell'aggregazione **Sum** , che somma i valori di tutti i punti dati ricevuti nel periodo del grafico.
1. Per aggiungere altre metriche, ripetere i passaggi 3 e 4.

È anche possibile recuperare le metriche a livello di codice con le API di monitoraggio di Azure. Per un esempio, vedere [recuperare le metriche di monitoraggio di Azure con .NET](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/).

> [!NOTE]
> Le metriche emesse negli ultimi 3 minuti potrebbero ancora essere aggregate, quindi i valori possono essere sottoposti a report durante questo intervallo di tempo. Il recapito della metrica non è garantito e potrebbe essere influenzato da recapito non ordinato, perdita di dati o duplicazione.

## <a name="batch-metric-alerts"></a>Avvisi sulle metriche di Batch

È possibile configurare gli avvisi delle metriche near Real Time che si attivano quando il valore di una metrica specificata supera una soglia assegnata. L'avviso genera una notifica quando viene "attivato" (quando la soglia viene superata e viene soddisfatta la condizione di avviso) e quando viene "risolto" (quando il valore rientra nella soglia e la condizione non viene più soddisfatta).

Poiché il recapito della metrica può essere soggetto a incoerenze come il recapito non ordinato, la perdita di dati o la duplicazione, è consigliabile evitare gli avvisi che vengono attivati in un singolo punto dati. Usare invece le soglie per tenere conto di eventuali incoerenze, ad esempio il recapito non ordinato, la perdita di dati e la duplicazione in un periodo di tempo.

Ad esempio, è possibile configurare un avviso sulle metriche quando il numero di core per priorità bassa diminuisce a un determinato livello, per consentire la regolazione della composizione dei pool. Per ottenere risultati ottimali, impostare un periodo di 10 o più minuti, in cui l'avviso verrà attivato se il numero medio di core con priorità bassa scende al di sotto del valore di soglia per l'intero periodo. Ciò consente di aggregare le metriche in modo da ottenere risultati più accurati.

Per configurare un avviso per la metrica nel portale di Azure:

1. Selezionare **Tutti i servizi** > **Account Batch** e quindi selezionare il nome dell'account Batch.
1. In **monitoraggio** selezionare **avvisi**, quindi selezionare **nuova regola di avviso**.
1. Selezionare **Aggiungi condizione**, quindi scegliere una metrica.
1. Selezionare i valori desiderati per il **periodo del grafico**, la **soglia**, l' **operatore** e il **tipo di aggregazione**.
1. Immettere un **valore soglia** e selezionare l' **unità** per la soglia.  Al termine selezionare **Done** (Fine).
1. Aggiungere un [gruppo di azioni](../azure-monitor/alerts/action-groups.md) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
1. Nella sezione **Dettagli regola di avviso** immettere un nome e una **Descrizione** per la **regola di avviso** . Se si desidera che l'avviso venga abilitato immediatamente, verificare che sia selezionata la casella **Abilita regola di avviso al momento della creazione** .
1. Selezionare **Crea regola di avviso**.

Per altre informazioni sulla creazione di avvisi per le metriche, vedere [comprendere il funzionamento degli avvisi delle metriche in monitoraggio di Azure](../azure-monitor/alerts/alerts-metric-overview.md) e [creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../azure-monitor/alerts/alerts-metric.md).

È anche possibile configurare un avviso quasi in tempo reale usando l' [API REST di monitoraggio di Azure](/rest/api/monitor/). Per ulteriori informazioni, vedere [Panoramica degli avvisi in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Per includere le informazioni specifiche relative a processi, attività o pool negli avvisi, vedere [rispondere agli eventi con gli avvisi di monitoraggio di Azure](../azure-monitor/alerts/tutorial-response.md).

## <a name="batch-diagnostics"></a>Diagnostica di Batch

I [log di diagnostica](../azure-monitor/essentials/platform-logs-overview.md) contengono informazioni emesse dalle risorse di Azure che descrivono il funzionamento di ogni risorsa. Per Batch, è possibile raccogliere i log seguenti:

- **ServiceLog**: [eventi generati dal servizio batch](#service-log-events) durante il ciclo di vita di una singola risorsa, ad esempio un pool o un'attività.
- **AllMetrics**: metrica a livello di account batch.

È necessario abilitare in modo esplicito le impostazioni di diagnostica per ogni account batch che si desidera monitorare.

### <a name="log-destination-options"></a>Opzioni di destinazione log

Uno scenario comune consiste nel selezionare un account di archiviazione di Azure come destinazione dei log. Per archiviare i log in Archiviazione di Azure, creare l'account prima di abilitare la raccolta dei log. Se all'account Batch è stato associato un account di archiviazione, è possibile scegliere l'account come destinazione dei log.

In alternativa, è possibile:

- Trasmettere gli eventi dei log di diagnostica di Batch a un [hub eventi di Azure](../event-hubs/event-hubs-about.md). Hub eventi è in grado di inserire milioni di eventi al secondo, che è quindi possibile trasformare e archiviare tramite un qualsiasi provider di analisi in tempo reale.
- Inviare i log di diagnostica ai [log di Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md) dove è possibile analizzarli o esportarli per l'analisi in Power BI o Excel.

> [!NOTE]
> Potrebbero essere previsti costi aggiuntivi per archiviare o elaborare dati dei log di diagnostica con servizi di Azure.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Abilitare la raccolta dei log di diagnostica di Batch

Per creare una nuova impostazione di diagnostica nella portale di Azure, attenersi alla procedura riportata di seguito.

1. Nella portale di Azure selezionare tutti i **Servizi**  >  **account batch** e quindi selezionare il nome dell'account batch.
2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
3. In **impostazioni di diagnostica** selezionare **Aggiungi impostazione di diagnostica**.
4. Immettere un nome per l'impostazione.
5. Selezionare una destinazione: **inviare a log Analytics**, **archiviare in un account di archiviazione** o **trasmettere a un hub eventi**. Se si seleziona un account di archiviazione, è possibile selezionare facoltativamente il numero di giorni per cui conservare i dati per ogni log. Se non si specifica un numero di giorni per la conservazione, i dati vengono mantenuti per tutto il ciclo di vita dell'account di archiviazione.
6. Selezionare **ServiceLog**, **AllMetrics** o entrambi.
7. Selezionare **Save (Salva** ) per creare l'impostazione di diagnostica.

È anche possibile abilitare la raccolta di log [creando impostazioni di diagnostica nell'portale di Azure](../azure-monitor/essentials/diagnostic-settings.md), usando un [modello di Gestione risorse](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)o usando Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Panoramica dei log della piattaforma Azure](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Accedere ai log di diagnostica nell'archiviazione

Se si [archiviano i log di diagnostica di batch in un account di archiviazione](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento correlato. I BLOB vengono creati in base al modello di denominazione seguente:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Ogni file BLOB `PT1H.json` contiene eventi in formato JSON che si sono verificati nell'ora specificata nell'URL del BLOB, ad esempio `h=12`. Nell'ora corrente gli eventi che si verificano vengono aggiunti al file `PT1H.json`. Il valore dei minuti (`m=00`) è sempre `00` poiché gli eventi del log di diagnostica vengono suddivisi in singoli BLOB per ogni ora. Tutte le ore sono in formato UTC.

Di seguito è riportato un esempio di voce `PoolResizeCompleteEvent` in un file di log `PT1H.json`. Sono incluse informazioni sul numero corrente e di destinazione dei nodi dedicati e a priorità bassa, oltre all'ora di inizio e di fine dell'operazione:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Per accedere ai log nell'account di archiviazione a livello di codice, usare le API di Archiviazione.

### <a name="service-log-events"></a>Eventi del log del servizio

I log del servizio Azure Batch contengono gli eventi generati dal servizio batch durante il ciclo di vita di una singola risorsa batch, ad esempio un pool o un'attività. Ogni evento generato da Batch viene registrato in formato JSON. Ad esempio, questo è il corpo di un **evento di creazione pool** di esempio:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Gli eventi del log del servizio generati dal servizio batch includono quanto segue:

- [Creazione di pool](batch-pool-create-event.md)
- [Avvio dell'eliminazione di pool](batch-pool-delete-start-event.md)
- [Completamento dell'eliminazione di pool](batch-pool-delete-complete-event.md)
- [Avvio del ridimensionamento di pool](batch-pool-resize-start-event.md)
- [Completamento del ridimensionamento di pool](batch-pool-resize-complete-event.md)
- [Scalabilità automatica del pool](batch-pool-autoscale-event.md)
- [Avvio dell'attività](batch-task-start-event.md)
- [Attività completata](batch-task-complete-event.md)
- [Errore dell'attività](batch-task-fail-event.md)
- [Pianificazione attività non riuscita](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
- Leggere altre informazioni sul [monitoraggio di soluzioni Batch](monitoring-overview.md).
