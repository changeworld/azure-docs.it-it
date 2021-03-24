---
title: Eseguire query nei log di monitoraggio di Azure per monitorare i cluster HDInsight di Azure
description: Informazioni su come eseguire query nei log di monitoraggio di Azure per monitorare i processi in esecuzione in un cluster HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 3cf97039983ecec44a7c3a32e178fdcf9f9c45ff
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872184"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Eseguire query nei log di Monitoraggio di Azure per monitorare i cluster HDInsight

Informazioni su alcuni scenari di base su come usare i log di monitoraggio di Azure per monitorare i cluster HDInsight di Azure:

* [Analizzare le metriche dei cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Creare avvisi di eventi](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario aver configurato un cluster HDInsight per l'uso dei log di monitoraggio di Azure e aver aggiunto i registri di monitoraggio di Azure specifici del cluster HDInsight per l'area di lavoro. Per istruzioni, vedere [usare i log di monitoraggio di Azure con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizzare le metriche dei cluster HDInsight

Informazioni su come cercare metriche specifiche per il cluster HDInsight.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
1. In **Generale**, selezionare **Log**.
1. Digitare la query seguente nella casella di ricerca per cercare tutte le metriche per tutte le metriche disponibili per tutti i cluster HDInsight configurati per l'uso dei log di monitoraggio di Azure e quindi selezionare **Esegui**. Esaminare i risultati.

    ```kusto
    search *
    ```

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png" alt-text="Ricerca di tutte le metriche in Apache Ambari Analytics":::

1. Nel menu a sinistra selezionare la scheda **filtro** .

1. In **tipo** selezionare **heartbeat**. Selezionare quindi **applica & Esegui**.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png" alt-text="metriche specifiche per la ricerca in log Analytics":::

1. Si noti che la query nella casella di testo viene modificata in:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. È possibile analizzare più a fondo usando le opzioni disponibili nel menu a sinistra. Ad esempio:

   - Per visualizzare i log da un nodo specifico:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png" alt-text="Cerca errori specifici output1":::

   - Per visualizzare i log in determinati orari:

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png" alt-text="Cerca errori specifici Output2":::

1. Selezionare **applica & Esegui** ed esaminare i risultati. Si noti inoltre che la query è stata aggiornata a:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Query di esempio aggiuntive

Una query di esempio basata sulla media delle risorse usate in un intervallo di 10 minuti, classificate in base al nome del cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Invece di affinare i risultati in base alla media delle risorse usate, è possibile usare la query seguente per affinarli in base a quando sono state usate le risorse massime (nonché il 90° e il 95 ° percentile) in un intervallo di 10 minuti:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Creare avvisi per eventi di rilevamento

Il primo passaggio per creare un avviso consiste nel creare una query in base a cui l'avviso viene attivato. È possibile usare qualsiasi query per creare un avviso.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
1. In **Generale**, selezionare **Log**.
1. Eseguire la query seguente in cui si desidera creare un avviso, quindi selezionare **Esegui**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    La query presenta un elenco delle applicazioni con errori in esecuzione nei cluster HDInsight.

1. Selezionare **nuova regola di avviso** nella parte superiore della pagina.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png" alt-text="Nuova regola di avviso":::

1. Nella finestra **Crea regola** immettere la query e altri dettagli per creare un avviso e selezionare **Crea regola di avviso**.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png" alt-text="Definire la condizione di avviso.":::

### <a name="edit-or-delete-an-existing-alert"></a>Modificare o eliminare un avviso esistente

1. Aprire l'area di lavoro Log Analytics dal portale di Azure.

1. Nel menu a sinistra, in **monitoraggio**, selezionare **avvisi**.

1. Nella parte superiore selezionare **Gestisci regole di avviso**.

1. Selezionare l'avviso da modificare o eliminare.

1. Sono disponibili le opzioni seguenti: **Salva**, **Ignora**, **Disabilita** ed **Elimina**.

    :::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png" alt-text="HDInsight log di monitoraggio di Azure-modifica avviso eliminazione":::

Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../azure-monitor/alerts/alerts-metric.md).

## <a name="see-also"></a>Vedi anche

* [Introduzione alle query su log in Monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md)
* [Creare visualizzazioni personalizzate usando Progettazione viste in monitoraggio di Azure](../azure-monitor/visualize/view-designer.md)
