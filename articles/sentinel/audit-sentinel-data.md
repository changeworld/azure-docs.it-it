---
title: Controllare le query e le attività di Azure Sentinel | Microsoft Docs
description: Questo articolo descrive come controllare le query e le attività eseguite in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a8ea32d84da521c8a1af926c6cb5e26bc2738de2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054757"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Controllare le query e le attività di Azure Sentinel

Questo articolo descrive come è possibile visualizzare i dati di controllo per le query eseguite e le attività eseguite nell'area di lavoro di Azure Sentinel, ad esempio per i requisiti di conformità interni ed esterni nell'area di lavoro delle operazioni di sicurezza (SOC).

Azure Sentinel fornisce l'accesso a:

- La tabella **AzureActivity** , che fornisce informazioni dettagliate su tutte le azioni eseguite in Sentinel di Azure, ad esempio la modifica delle regole di avviso. La tabella **AzureActivity** non registra dati di query specifici. Per altre informazioni, vedere [controllo con i log attività di Azure](#auditing-with-azure-activity-logs).

- La tabella **LAQueryLogs** , che fornisce informazioni dettagliate sulle query eseguite in log Analytics, incluse le query eseguite da Azure Sentinel. Per ulteriori informazioni, vedere [controllo con LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Oltre alle query manuali descritte in questo articolo, Azure Sentinel fornisce una cartella di lavoro predefinita che consente di controllare le attività nell'ambiente SOC.
>
> Nell'area cartelle di **lavoro** di Azure Sentinel cercare la cartella di lavoro di **controllo dell'area di lavoro** .



## <a name="auditing-with-azure-activity-logs"></a>Controllo con i log attività di Azure

I log di controllo di Azure Sentinel vengono conservati nei [log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md), in cui la tabella **AzureActivity** include tutte le azioni eseguite nell'area di lavoro di Azure Sentinel.

È possibile usare la tabella **AzureActivity** quando si controlla l'attività nell'ambiente SOC con Azure Sentinel.

**Per eseguire una query sulla tabella AzureActivity**:

1. Connettere l'origine dati di [Azure Activity](connect-azure-activity.md) per avviare lo streaming degli eventi di controllo in una nuova tabella nella schermata **logs** denominata AzureActivity.

1. Eseguire quindi una query sui dati usando KQL, come qualsiasi altra tabella.

    La tabella **AzureActivity** include i dati di molti servizi, tra cui Sentinel di Azure. Per filtrare solo i dati da Azure Sentinel, avviare la query con il codice seguente:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Per scoprire, ad esempio, chi è l'ultimo utente a modificare una particolare regola di analisi, usare la query seguente (sostituendo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID regola della regola da controllare):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Aggiungere altri parametri alla query per esplorare ulteriormente la tabella **AzureActivities** , a seconda di ciò che è necessario segnalare. Le sezioni seguenti forniscono altre query di esempio da usare per il controllo con i dati della tabella **AzureActivity** . 

Per altre informazioni, vedere [dati sentinella di Azure inclusi nei log attività di Azure](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Trova tutte le azioni eseguite da un utente specifico nelle ultime 24 ore

Nella query della tabella **AzureActivity** seguente sono elencate tutte le azioni eseguite da un utente Azure ad specifico nelle ultime 24 ore.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Trova tutte le operazioni di eliminazione

La query di tabella **AzureActivity** seguente elenca tutte le operazioni di eliminazione eseguite nell'area di lavoro di Azure Sentinel.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Dati sentinella di Azure inclusi nei log attività di Azure
 
I log di controllo di Azure Sentinel sono conservati nei [log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md)e includono i tipi di informazioni seguenti:

|Operazione  |Tipi di informazioni  |
|---------|---------|
|**Creato**     |Regole di avviso <br> Commenti case <br>Commenti evento imprevisto <br>Ricerche salvate<br>Watchlists    <br>Workbooks     |
|**Eliminata**     |Regole di avviso <br>Segnalibri <br>Connettori dati <br>Eventi imprevisti <br>Ricerche salvate <br>Impostazioni <br>Report di intelligence per le minacce <br>Watchlists      <br>Workbooks <br>Flusso di lavoro  |
|**Aggiornato**     |  Regole di avviso<br>Segnalibri <br> Casi <br> Connettori dati <br>Eventi imprevisti <br>Commenti evento imprevisto <br>Report di intelligence per le minacce <br> Workbooks <br>Flusso di lavoro       |
|     |         |

È anche possibile usare i log attività di Azure per verificare la presenza di autorizzazioni e licenze utente. 

Ad esempio, la tabella seguente elenca le operazioni selezionate trovate nei log attività di Azure con la risorsa specifica da cui vengono estratti i dati di log.

|Nome operazione|    Tipo di risorsa|
|----|----|
|Crea o Aggiorna cartella di lavoro  |Microsoft. Insights/cartelle di lavoro|
|Elimina cartella di lavoro    |Microsoft. Insights/cartelle di lavoro|
|Imposta flusso di lavoro   |Microsoft.Logic/workflows|
|Elimina flusso di lavoro    |Microsoft.Logic/workflows|
|Creare una ricerca salvata    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Elimina ricerca salvata    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Aggiornare le regole di avviso |Microsoft. SecurityInsights/alertRules|
|Elimina regole di avviso |Microsoft. SecurityInsights/alertRules|
|Aggiornare le azioni di risposta alle regole di avviso |Microsoft. SecurityInsights/alertRules/actions|
|Elimina azioni di risposta regola di avviso |Microsoft. SecurityInsights/alertRules/actions|
|Aggiornare i segnalibri   |Microsoft. SecurityInsights/segnalibri|
|Elimina segnalibri   |Microsoft. SecurityInsights/segnalibri|
|Casi di aggiornamento   |Microsoft. SecurityInsights/case|
|Analisi case di aggiornamento  |Microsoft. SecurityInsights/Cases/Investigations|
|Crea commenti case   |Microsoft. SecurityInsights/case/Commenti|
|Aggiornare i connettori dati |Microsoft. SecurityInsights/dataconnectors|
|Elimina connettori dati |Microsoft. SecurityInsights/dataconnectors|
|Aggiornamento delle impostazioni    |Microsoft. SecurityInsights/Settings|
| | |

Per altre informazioni, vedere [schema degli eventi del log attività di Azure](/azure/azure-monitor/essentials/activity-log-schema).


## <a name="auditing-with-laquerylogs"></a>Controllo con LAQueryLogs

La tabella **LAQueryLogs** fornisce informazioni dettagliate sulle query di log eseguite in log Analytics. Poiché Log Analytics viene usato come archivio dati sottostante di Sentinel di Azure, è possibile configurare il sistema in modo da raccogliere i dati di LAQueryLogs nell'area di lavoro di Azure Sentinel.

I dati di LAQueryLogs includono informazioni quali:

- Quando sono state eseguite query
- Chi ha eseguito le query in Log Analytics
- Quale strumento è stato usato per eseguire query in Log Analytics, ad esempio Azure Sentinel
- Testo della query
- Dati sulle prestazioni per ogni esecuzione della query

> [!NOTE]
> - La tabella **LAQueryLogs** include solo le query che sono state eseguite nel pannello logs di Azure Sentinel. Non include le query eseguite dalle regole di analisi pianificate, usando il **grafico di analisi** o **nella pagina di ricerca di** Azure Sentinel.
> - Potrebbe verificarsi un breve ritardo tra il momento in cui una query viene eseguita e i dati vengono popolati nella tabella **LAQueryLogs** . È consigliabile attendere circa 5 minuti per eseguire una query sulla tabella **LAQueryLogs** per i dati di controllo.
>


**Per eseguire una query sulla tabella LAQueryLogs**:

1. Per impostazione predefinita, la tabella **LAQueryLogs** non è abilitata nell'area di lavoro log Analytics. Per usare i dati di **LAQueryLogs** durante il controllo in Azure Sentinel, abilitare prima di tutto il **LAQueryLogs** nell'area **impostazioni di diagnostica** dell'area di log Analytics.

    Per altre informazioni, vedere [Audit queries in log di monitoraggio di Azure](/azure/azure-monitor/logs/query-audit).


1. Eseguire quindi una query sui dati usando KQL, come qualsiasi altra tabella.

    Ad esempio, la query seguente mostra il numero di query eseguite nell'ultima settimana, su base giornaliera:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

Le sezioni seguenti illustrano altre query di esempio da eseguire nella tabella **LAQueryLogs** quando si controllano le attività nell'ambiente SOC usando Azure Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Il numero di query eseguite dove la risposta non era "OK"

La query di tabella **LAQueryLogs** seguente mostra il numero di query eseguite, in cui è stato ricevuto un valore diverso da una risposta HTTP di **200 OK** . Ad esempio, questo numero includerà le query che non sono state eseguite.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Mostra gli utenti per le query con utilizzo intensivo della CPU

La query della tabella **LAQueryLogs** seguente elenca gli utenti che hanno eseguito la maggior parte delle query con utilizzo intensivo della CPU, in base alla CPU utilizzata e alla durata della query.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Mostra gli utenti che hanno eseguito la maggior parte delle query nell'ultima settimana

La query della tabella **LAQueryLogs** seguente elenca gli utenti che hanno eseguito la maggior parte delle query nell'ultima settimana.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Configurazione degli avvisi per le attività di Azure Sentinel

Per creare avvisi proattivi, è consigliabile usare le risorse di controllo di Azure Sentinel.

Se, ad esempio, sono presenti tabelle sensibili nell'area di lavoro di Azure Sentinel, usare la query seguente per notificare ogni volta che viene eseguita una query su queste tabelle:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Passaggi successivi

In Sentinel di Azure usare la cartella di lavoro di **controllo dell'area di lavoro** per controllare le attività nell'ambiente Soc.

Per altre informazioni, vedere [esercitazione: visualizzare e monitorare i dati](tutorial-monitor-your-data.md).
