---
title: Monitorare il carico di lavoro - portale di Azure
description: Monitorare Synapse SQL usando il portale di Azure
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4f4c50588a67e2e69d0975c9f4414242ecf23617
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568268"
---
# <a name="monitor-workload---azure-portal"></a>Monitorare il carico di lavoro - portale di Azure

Questo articolo descrive come usare il portale di Azure per monitorare il carico di lavoro. Ciò include la configurazione di log Monitoraggio di Azure per analizzare le tendenze di esecuzione delle query e del carico di lavoro usando Log Analytics [per Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Pool SQL: verranno raccolti i log per un pool SQL. Se non è stato effettuato il provisioning di un pool SQL, vedere le istruzioni in [Creare un pool SQL.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Passare al pannello Sfoglia per le aree di lavoro Log Analytics e creare un'area di lavoro

![Aree di lavoro di Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Screenshot che mostra le aree di lavoro di Log Analytics in cui è possibile selezionare Aggiungi.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Screenshot che mostra l'area di lavoro Log Analytics in cui è possibile immettere i valori.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Per altre informazioni sulle aree di lavoro, vedere la documentazione [seguente.](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-resource-logs"></a>Attivare i log delle risorse

Configurare le impostazioni di diagnostica per generare log dal pool SQL. I log sono costituiti da viste di telemetria equivalenti alle DMV per la risoluzione dei problemi di prestazioni usate più di frequente. Attualmente sono supportate le visualizzazioni seguenti:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Abilitazione dei log delle risorse](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

I log possono essere generati in Archiviazione di Azure, Analisi di flusso o Log Analytics. Per questa esercitazione, selezionare Log Analytics.

![Specificare i log](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Eseguire query su Log Analytics

Passare all'area di lavoro Log Analytics in cui è possibile eseguire le operazioni seguenti:

- Analizzare i log usando le query di log e salvare le query per il riutilizzo
- Salvare le query per il riutilizzo
- Creare avvisi di log
- Aggiungere i risultati della query a un dashboard

Per informazioni dettagliate sulle funzionalità delle query di log, vedere la documentazione [seguente.](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)

![Editor dell'area di lavoro Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Query dell'area di lavoro Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Query di log di esempio

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato e configurato i log di Monitoraggio di Azure, personalizzare [i dashboard](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) di Azure da condividere nel team.