---
title: Monitoraggio Azure Machine Learning | Microsoft Docs
description: Informazioni su come usare Monitoraggio di Azure per visualizzare, analizzare e creare avvisi sulle metriche Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: e5fd0fdd5a6f9a4a7537a844b096efdfef253638
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816855"
---
# <a name="monitor-azure-machine-learning"></a>Monitorare Azure Machine Learning

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da Azure Machine Learning e come analizzare e determinare avvisi per questi dati con Monitoraggio di Azure.

> [!TIP]
> Le informazioni contenute in questo documento sono principalmente per gli __amministratori,__ in quanto descrive il monitoraggio per il servizio Azure Machine Learning e i servizi di Azure associati. Se si è __un data scientist__ o uno __sviluppatore__ e si vogliono monitorare informazioni specifiche per le esecuzioni di *training* del modello, vedere i documenti seguenti:
>
> * [Avviare, monitorare e annullare le esecuzioni di training](how-to-track-monitor-analyze-runs.md)
> * [Registrare le metriche per le esecuzioni di training](how-to-log-view-metrics.md)
> * [Tenere traccia di esperimenti con MLflow](how-to-use-mlflow.md)
> * [Visualizzare le esecuzioni con TensorBoard](how-to-monitor-tensorboard.md)
>
> Per monitorare le informazioni generate dai modelli distribuiti come servizi Web o moduli IoT Edge, vedere [Raccogliere](how-to-enable-data-collection.md) i dati del modello e Monitorare [con Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Azure Machine Learning i dati di monitoraggio [usando Monitoraggio di Azure](../azure-monitor/overview.md), un servizio di monitoraggio dello stack completo in Azure. Monitoraggio di Azure offre un set completo di funzionalità per monitorare le risorse di Azure. Può anche monitorare le risorse in altri cloud e in locale.

Iniziare con l'articolo Monitoraggio delle risorse [di Azure Monitoraggio di Azure](../azure-monitor/essentials/monitor-azure-resource.md), che descrive i concetti seguenti:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti per Azure Machine Learning. Queste sezioni forniscono anche esempi per la configurazione della raccolta dati e l'analisi di questi dati con gli strumenti di Azure.

> [!TIP]
> Per informazioni sui costi associati alle Monitoraggio di Azure, vedere [Utilizzo e costi stimati.](../azure-monitor//usage-estimated-costs.md) Per comprendere il tempo necessario per visualizzare i dati in Monitoraggio di Azure, vedere Tempo di inserimento dei dati [del log.](../azure-monitor/logs/data-ingestion-time.md)

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitoraggio dei dati da Azure Machine Learning

Azure Machine Learning raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in Monitoraggio [dei dati dalle risorse di Azure.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Vedere [Azure Machine Learning informazioni di riferimento sui dati](monitor-resource-reference.md) di monitoraggio per informazioni di riferimento dettagliate sui log e sulle metriche create da Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti e archiviati automaticamente, ma possono essere indirizzati ad altre posizioni usando un'impostazione di diagnostica.  

I log delle risorse non vengono raccolti e archiviati fino a quando non si crea un'impostazione di diagnostica e li si instradare a una o più posizioni.

Vedere [Creare un'impostazione di](../azure-monitor/essentials/diagnostic-settings.md) diagnostica per raccogliere i log e le metriche della piattaforma in Azure per il processo dettagliato per la creazione di un'impostazione di diagnostica usando portale di Azure, l'interfaccia della riga di comando o PowerShell. Quando si crea un'impostazione di diagnostica, si specificano le categorie di log da raccogliere. Le categorie per Azure Machine Learning sono elencate in informazioni [Azure Machine Learning riferimento ai dati di monitoraggio.](monitor-resource-reference.md#resource-logs)

> [!IMPORTANT]
> L'abilitazione di queste impostazioni richiede servizi di Azure aggiuntivi (account di archiviazione, hub eventi o Log Analytics), che possono aumentare i costi. Per calcolare un costo stimato, visitare il calcolatore [prezzi di Azure.](https://azure.microsoft.com/pricing/calculator)

È possibile configurare i log seguenti per Azure Machine Learning:

| Categoria | Descrizione |
|:---|:---|
| AmlComputeClusterEvent | Eventi di Azure Machine Learning cluster di calcolo. |
| AmlComputeClusterNodeEvent | Eventi dai nodi all'interno di un cluster Azure Machine Learning di calcolo. |
| AmlComputeJobEvent | Eventi dei processi in esecuzione Azure Machine Learning calcolo. |

> [!NOTE]
> Quando si abilitano le metriche in un'impostazione di diagnostica, le informazioni sulle dimensioni non sono attualmente incluse come parte delle informazioni inviate a un account di archiviazione, a un hub eventi o a log analytics.

Le metriche e i log che è possibile raccogliere sono illustrati nelle sezioni seguenti.

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche per Azure Machine Learning, insieme alle metriche di altri servizi di Azure, aprendo **Metriche** dal **menu** Monitoraggio di Azure dati. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../azure-monitor/essentials/metrics-getting-started.md).

Per un elenco delle metriche della piattaforma raccolte, vedere Monitoraggio delle Azure Machine Learning [di riferimento ai dati.](monitor-resource-reference.md#metrics)

Tutte le metriche per i Azure Machine Learning sono nello spazio dei nomi Machine Learning **di lavoro del servizio**.

![Esplora metriche con l'Machine Learning di lavoro del servizio di archiviazione selezionata](./media/monitor-azure-machine-learning/metrics.png)

Per riferimento, è possibile visualizzare un elenco di tutte [le metriche delle risorse supportate in Monitoraggio di Azure](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Monitoraggio di Azure dati delle metriche sono disponibili per 90 giorni. Tuttavia, quando si creano grafici è possibile visualizzare solo 30 giorni. Ad esempio, se si vuole visualizzare un periodo di 90 giorni, è necessario suddividerlo in tre grafici di 30 giorni entro il periodo di 90 giorni.
### <a name="filtering-and-splitting"></a>Filtro e suddivisione

Per le metriche che supportano le dimensioni, è possibile applicare filtri usando un valore di dimensione. Ad esempio, **filtrando Active Cores** per un **nome cluster di** `cpu-cluster` . 

È anche possibile dividere una metrica per dimensione per visualizzare il confronto tra segmenti diversi della metrica. Ad esempio, suddividendo il tipo **di passaggio** della pipeline per visualizzare un conteggio dei tipi di passaggi usati nella pipeline.

Per altre informazioni sul filtro e la suddivisione, vedere [Funzionalità avanzate di Monitoraggio di Azure](../azure-monitor/essentials/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analisi dei log

Per Monitoraggio di Azure Log Analytics è necessario creare una configurazione di diagnostica e abilitare __Invia informazioni a Log Analytics.__ Per altre informazioni, vedere la [sezione Raccolta e routing.](#collection-and-routing)

I dati Monitoraggio di Azure log vengono archiviati in tabelle, in cui ogni tabella ha un proprio set di proprietà univoche. Azure Machine Learning archivia i dati nelle tabelle seguenti:

| Tabella | Descrizione |
|:---|:---|
| AmlComputeClusterEvent | Eventi da Azure Machine Learning di calcolo. |
| AmlComputeClusterNodeEvent | Eventi dai nodi all'interno di un cluster Azure Machine Learning di calcolo. |
| AmlComputeJobEvent | Eventi dei processi in esecuzione Azure Machine Learning calcolo. |

> [!IMPORTANT]
> Quando si seleziona **Log dal** menu Azure Machine Learning, Log Analytics viene aperto con l'ambito query impostato sull'area di lavoro corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che includa dati di altri database o dati di altri servizi di Azure, selezionare **Log** dal menu di **Monitoraggio di Azure**. Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../azure-monitor/logs/scope.md).

Per informazioni di riferimento dettagliate sui log e sulle metriche, vedere Azure Machine Learning [informazioni di riferimento sui dati di monitoraggio.](monitor-resource-reference.md)

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

> [!IMPORTANT]
> Quando si seleziona **Log** dal menu [service-name], Log Analytics viene aperto con l'ambito della query impostato sull'area di lavoro Azure Machine Learning corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che include i dati di  altre aree di lavoro o dati di **altri** servizi di Azure, selezionare Log dal menu Monitoraggio di Azure dati. Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../azure-monitor/logs/scope.md).

Di seguito sono riportate le query che è possibile usare per monitorare le risorse Azure Machine Learning seguenti: 

+ Ottenere processi non riusciti negli ultimi cinque giorni:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Ottenere record per un nome di processo specifico:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Ottenere gli eventi del cluster negli ultimi cinque giorni per i cluster in cui le dimensioni della macchina virtuale sono Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Ottenere i nodi allocati negli ultimi otto giorni:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Avvisi

È possibile accedere agli avvisi per Azure Machine Learning aprendo **Avvisi** dal menu **Monitoraggio di Azure.** Per [informazioni dettagliate sulla creazione di avvisi,](../azure-monitor/alerts/alerts-metric.md) vedere Creare, visualizzare e gestire gli avvisi delle metriche usando Monitoraggio di Azure.

Nella tabella seguente sono elencate le regole di avviso delle metriche comuni e consigliate per Azure Machine Learning:

| Tipo di avviso | Condizione | Descrizione |
|:---|:---|:---|
| Model Deploy Failed (Distribuzione di modelli non riuscita) | Tipo di aggregazione: Totale, Operatore: Maggiore di, Valore soglia: 0 | Quando una o più distribuzioni di modelli hanno avuto esito negativo |
| Quota Utilization Percentage (Percentuale di utilizzo quota) | Tipo di aggregazione: Media, Operatore: Maggiore di, Valore soglia: 90| Quando la percentuale di utilizzo della quota è maggiore del 90% |
| Unusable Nodes (Nodi non utilizzabili) | Tipo di aggregazione: Totale, Operatore: Maggiore di, Valore soglia: 0 | Quando sono presenti uno o più nodi inutilizzabili |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui log e sulle metriche, vedere Monitoraggio [e Azure Machine Learning informazioni di riferimento sui dati.](monitor-resource-reference.md)
- Per informazioni sull'uso delle quote correlate Azure Machine Learning, vedere Gestire e [richiedere quote per le risorse di Azure.](how-to-manage-quotas.md)
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/essentials/monitor-azure-resource.md).
