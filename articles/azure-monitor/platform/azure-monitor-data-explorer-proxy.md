---
title: Eseguire query tra risorse Esplora dati di Azure con monitoraggio di Azure
description: Usare monitoraggio di Azure per eseguire query tra prodotti tra Esplora dati di Azure, aree di lavoro Log Analytics e applicazioni Application Insights classiche in monitoraggio di Azure.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8884fd56cfc1e18a0a358d6902f7f8bcbfc1f5d3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552644"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Eseguire query tra risorse Esplora dati di Azure con monitoraggio di Azure
Monitoraggio di Azure supporta le query tra i servizi tra Esplora dati di Azure, [Application Insights (ai)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs). È quindi possibile eseguire query sul cluster di Esplora dati di Azure usando gli strumenti Log Analytics/Application Insights e farvi riferimento in una query tra servizi. Questo articolo illustra come eseguire una query tra servizi.

Flusso tra i servizi di monitoraggio di Azure: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="monitoraggio di Azure e flusso di azure Esplora dati cross Service.":::

>[!NOTE]
>* Il proxy di monitoraggio di Azure è in anteprima privata: è necessario AllowListing.
>* Per eventuali domande, contattare il [team del servizio](mailto:ADXProxy@microsoft.com) .
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Eseguire query incrociate sulle risorse Log Analytics o Application Insights e su Azure Esplora dati

È possibile eseguire le query tra risorse usando gli strumenti client che supportano le query kusto, ad esempio: Log Analytics interfaccia utente Web, cartelle di lavoro, PowerShell, API REST e altro ancora.

* Immettere l'identificatore per un cluster di Esplora dati di Azure in una query all'interno del modello ' ADX ' seguito dal nome del database e dalla tabella.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Esempio di query tra servizi.":::

> [!NOTE]
>* I nomi dei database fanno distinzione tra maiuscole e minuscole
>* La query tra risorse come avviso non è supportata.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Combinare le tabelle del cluster di Azure Esplora dati (usando Unione e join) con l'area di lavoro.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Esempio di query tra servizi con l'operatore Union.":::

>[!Tip]
>* Il formato abbreviato è allowed-clustername/InitialCatalog. Ad esempio, ADX (' Help/samples ') viene convertito in ADX (' Help. kusto. Windows. NET/Samples ')
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unire i dati di un cluster Esplora dati di Azure in un tenant con una risorsa di monitoraggio di Azure in un altro

Le query tra tenant tra i servizi non sono supportate. È stato eseguito l'accesso a un singolo tenant per eseguire la query che si estende su entrambe le risorse.

Se la risorsa di Azure Esplora dati si trova nel tenant ' A ' e Log Analytics area di lavoro si trova nel tenant ' B ', usare uno dei due metodi seguenti:

*  Esplora dati di Azure consente di aggiungere ruoli per le entità in tenant diversi. Aggiungere l'ID utente nel tenant ' B ' come utente autorizzato nel cluster Esplora dati di Azure. Verificare che la proprietà *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* nel cluster Esplora dati di Azure contenga il tenant ' B '. Eseguire la query incrociata completamente nel tenant ' B '.
*  Usare [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) per proiettare la risorsa di monitoraggio di Azure nel tenant ' A '.
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Connettersi a cluster di Esplora dati di Azure da tenant diversi

Kusto Explorer accede automaticamente al tenant a cui appartiene originariamente l'account utente. Per accedere alle risorse in altri tenant con lo stesso account utente, `tenantId` è necessario specificare in modo esplicito nella stringa di connessione: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Passaggi successivi
* [Scrivere query](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Eseguire query di log su più risorse in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)






