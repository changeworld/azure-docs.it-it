---
title: Unificare più risorse di Application Insights in Monitoraggio di Azure | Microsoft Docs
description: Questo articolo include informazioni dettagliate su come usare una funzione disponibile in Log di Monitoraggio di Azure per eseguire query su più risorse di Application Insights e visualizzare i dati.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7f49bd23347b53f9a5287973c6b2aba82da50ca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100619457"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificare più risorse di Application Insights in Monitoraggio di Azure 
Questo articolo descrive come eseguire una query e visualizzare tutti i dati di log Application Insights in un'unica posizione, anche quando si trovano in sottoscrizioni di Azure diverse, sostituendo la deprecazione del Connettore di Application Insights. Il numero di risorse di Application Insights che è possibile includere in una singola query è limitato a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Approccio consigliato per eseguire query su più risorse di Application Insights 
Includere l'elenco di più risorse di Application Insights in una query può risultare impegnativo e difficile da gestire. In alternativa, è possibile sfruttare la funzione che consente di separare la logica di query dalla definizione dell'ambito delle applicazioni.  

Questo esempio mostra come monitorare più risorse di Application Insights e visualizzare il numero di richieste non riuscite per nome di applicazione.

Creare una funzione usando l'operatore di unione con l'elenco delle applicazioni e quindi salvare la query nell'area di lavoro come funzione con l'alias *applicationsScoping*. 

È possibile modificare in qualsiasi momento le applicazioni elencate nel portale passando a Esplora query nell'area di lavoro e selezionando la funzione di modifica e salvando oppure usando il cmdlet `SavedSearch` di PowerShell. 

>[!NOTE]
>Questo metodo non può essere usato con gli avvisi del log perché la convalida dell'accesso delle risorse della regola di avviso, incluse le aree di lavoro e le applicazioni, viene eseguita al momento della creazione dell'avviso. L'aggiunta di nuove risorse alla funzione dopo la creazione dell'avviso non è supportata. Se si preferisce usare la funzione per l'ambito delle risorse negli avvisi del log, è necessario modificare la regola di avviso nel portale o con un modello di Gestione risorse per aggiornare le risorse con ambito. In alternativa, è possibile includere l'elenco delle risorse nella query di avviso del log.

Il comando `withsource= SourceApp` aggiunge ai risultati una colonna che indica l'applicazione da cui è stato inviato il log. L'operatore Parse è facoltativo in questo esempio e USA per estrarre il nome dell'applicazione dalla proprietà SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

A questo punto si può usare la funzione applicationsScoping nella query su più risorse:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

La query usa lo schema di Application Insights, anche se viene eseguita nell'area di lavoro, perché la funzione applicationsScoping restituisce la struttura dei dati di Application Insights. L'alias della funzione restituisce l'unione delle richieste da tutte le applicazioni definite. La query filtra quindi le richieste non riuscite e visualizza le tendenze per applicazione.

![Esempio di risultati di query su più risorse](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>Le [query tra risorse](../logs/cross-workspace-query.md) negli avvisi del log sono supportate solo nell' [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)corrente. Se si usa l'API legacy Log Analytics Alerts, sarà necessario [passare all'API corrente](../alerts/alerts-log-api-switch.md). [Vedere modelli di esempio](../alerts/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Differenze tra gli schemi dell'area di lavoro Log Analytics e delle risorse di Application Insights
La tabella seguente illustra le differenze tra gli schemi di Log Analytics e Application Insights.  

| Proprietà dell'area di lavoro Log Analytics| Proprietà delle risorse di Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | posizione |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp |  timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Paese | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | tipo |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | esito positivo | 
| ResponseCode | resultCode | 
| Ruolo | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | tipo |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Passaggi successivi

Usare la [ricerca log](../logs/log-query-overview.md) per visualizzare informazioni dettagliate per le app Application Insights.

