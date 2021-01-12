---
title: Log per la risoluzione dei problemi di monitoraggio di Azure (anteprima)
description: Usare monitoraggio di Azure per analizzare rapidamente o periodicamente i problemi, risolvere problemi di codice o di configurazione o risolvere i casi di supporto, che spesso si basano sulla ricerca di volumi elevati di dati per informazioni dettagliate specifiche.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: b3d1ad2d327da60874d2d07ba697b8f5ab815189
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127091"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Log per la risoluzione dei problemi di monitoraggio di Azure (anteprima)
USA monitoraggio di Azure per analizzare rapidamente e/o periodicamente i problemi, risolvere problemi di codice o di configurazione o risolvere i casi di supporto, che spesso si basano sulla ricerca di volumi elevati di dati per informazioni dettagliate specifiche.

>[!NOTE]
> * I log per la risoluzione dei problemi sono in modalità di anteprima.
>* Contattare il [team di log Analytics](mailto:orens@microsoft.com) per eventuali domande o per applicare la funzionalità.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Risolvere i problemi ed eseguire query sul codice o sui problemi di configurazione
Usare i log per la risoluzione dei problemi di monitoraggio di Azure per recuperare i record ed esaminare i problemi e i problemi in modo più semplice e conveniente usando KQL.
I log per la risoluzione dei problemi decretano gli addebiti fornendo funzionalità di base per la risoluzione dei problemi.

> [!NOTE]
>* La scelta della modalità di risoluzione dei problemi è configurabile.
>* I log per la risoluzione dei problemi possono essere applicati a tabelle specifiche, attualmente in "log del contenitore" e nelle tabelle di "tracce di app".
>* È previsto un periodo di conservazione gratuito di 4 giorni, che può essere esteso a costi aggiuntivi.
>* Per impostazione predefinita, le tabelle ereditano la conservazione dell'area di lavoro. Per evitare addebiti aggiuntivi, è consigliabile modificare la conservazione delle tabelle. [Fare clic qui per informazioni su come modificare la conservazione delle tabelle](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Attivare i log di risoluzione dei problemi nelle tabelle

Per attivare i log di risoluzione dei problemi nell'area di lavoro, è necessario usare la chiamata API seguente.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Controllare se la funzionalità log per la risoluzione dei problemi è abilitata per una determinata tabella
Per verificare se il log per la risoluzione dei problemi è abilitato per una determinata tabella, è possibile usare la chiamata API seguente.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"
                }

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Controllare se la funzionalità log per la risoluzione dei problemi è abilitata per tutte le tabelle in un'area di lavoro
Per verificare in quale tabella è abilitato il log per la risoluzione dei problemi, è possibile usare la chiamata API seguente.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
 },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Disabilitare i log di risoluzione dei problemi nelle tabelle

Per disattivare i log di risoluzione dei problemi nell'area di lavoro, è necessario usare la chiamata API seguente.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
}
```
>[!TIP]
>* Per eseguire i comandi, è possibile usare qualsiasi strumento API REST. [Altre informazioni](https://docs.microsoft.com/rest/api/azure/)
>* È necessario usare il token di porta per l'autenticazione. [Altre informazioni](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* Il flag "isTroubleshootingAllowed": descrive se la tabella è consentita nel servizio
>* "IsTroubleshootEnabled" indica se la funzionalità è abilitata per la tabella. può essere attivata o disattivata (true o false)
>* Quando si disabilita il flag "isTroubleshootEnabled" per una tabella specifica, la nuova abilitazione è possibile solo una settimana dopo la data di abilitazione precedente.
>* Attualmente è supportata solo per le tabelle in (anche altre SKU saranno supportate in futuro). per altre [informazioni sui prezzi, vedere](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Limitazioni delle query per la risoluzione dei problemi
Esistono alcune limitazioni per una tabella contrassegnata come "log di risoluzione dei problemi":
*   Otterrà una minore quantità di risorse di elaborazione e pertanto non sarà adatto per dashboard di grandi dimensioni, analisi complesse o molte chiamate API simultanee.
*   Le query sono limitate a un intervallo di tempo di due giorni.
* l'eliminazione non funzionerà. [altre informazioni sull'eliminazione](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* Gli avvisi non sono supportati tramite questo servizio.
## <a name="next-steps"></a>Passaggi successivi
* [Scrivere query](https://docs.microsoft.com/azure/data-explorer/write-queries)


