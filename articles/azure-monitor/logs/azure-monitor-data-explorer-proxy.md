---
title: Eseguire query tra risorse in Azure Esplora dati usando monitoraggio di Azure
description: Usare monitoraggio di Azure per eseguire query tra prodotti tra Esplora dati di Azure, aree di lavoro Log Analytics e applicazioni Application Insights classiche in monitoraggio di Azure.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: a800f78df26ce76144994bb9da2cac6271323eb4
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419423"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Eseguire query tra risorse in Azure Esplora dati usando monitoraggio di Azure
Monitoraggio di Azure supporta query tra servizi tra Esplora dati di Azure, [Application Insights](../app/app-insights-overview.md)e [log Analytics](../logs/data-platform-logs.md). È quindi possibile eseguire query nel cluster di Azure Esplora dati usando gli strumenti Log Analytics/Application Insights e farvi riferimento in una query tra servizi. Questo articolo illustra come eseguire una query tra servizi.

Il diagramma seguente illustra il flusso tra servizi di monitoraggio di Azure:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagramma che mostra il flusso di query tra un utente, monitoraggio di Azure, un proxy e Esplora dati di Azure.":::

>[!NOTE]
> La query tra servizi di monitoraggio di Azure è in versione di anteprima pubblica. Per eventuali domande, contattare il [team del servizio](mailto:ADXProxy@microsoft.com) .

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Eseguire query tra le risorse Log Analytics o Application Insights e Azure Esplora dati

È possibile eseguire query tra risorse usando gli strumenti client che supportano le query kusto. Esempi di questi strumenti includono l'interfaccia utente Web di Log Analytics, le cartelle di lavoro, PowerShell e l'API REST.

Immettere l'identificatore per un cluster di Esplora dati di Azure in una query all'interno del `adx` modello, seguito dal nome del database e dalla tabella.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Screenshot che mostra un esempio di query tra servizi.":::

> [!NOTE]
>* I nomi dei database fanno distinzione tra maiuscole e minuscole
>* La query tra risorse come avviso non è supportata.
>* L'identificazione della colonna timestamp nel cluster non è supportata. Log Analytics API query non passerà lungo il filtro temporale

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Combinare le tabelle del cluster di Esplora dati di Azure con un'area di lavoro Log Analytics

Usare il `union` comando per combinare le tabelle del cluster con un'area di lavoro log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Screenshot che mostra un esempio di query tra servizi con il comando Union.":::

> [!Tip]
> È consentito il formato abbreviato: *clustername* / *InitialCatalog*. Ad esempio, `adx('help/Samples')` viene convertito in `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unire tramite join i dati di un cluster di Esplora dati di Azure in un tenant con una risorsa di Monitoraggio di Azure in un altro

Le query tra tenant tra i servizi non sono supportate. È stato eseguito l'accesso a un singolo tenant per l'esecuzione della query che si estende su entrambe le risorse.

Se la risorsa Azure Esplora dati si trova nel tenant A e l'area di lavoro Log Analytics è nel tenant B, usare uno dei metodi seguenti:

*  Esplora dati di Azure consente di aggiungere ruoli per le entità di sicurezza in tenant diversi. Aggiungere l'ID utente nel tenant B come utente autorizzato nel cluster di Azure Esplora dati. Verificare che la proprietà [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) nel cluster Esplora dati di Azure contenga il tenant b. eseguire la query incrociata completamente nel tenant b.
*  Usare [Lighthouse](../../lighthouse/index.yml) per proiettare la risorsa di monitoraggio di Azure nel tenant a.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Connettersi a cluster di Esplora dati di Azure da tenant diversi

Kusto Explorer accede automaticamente al tenant a cui appartiene originariamente l'account utente. Per accedere alle risorse in altri tenant con lo stesso account utente, è necessario specificare in modo esplicito `TenantId` nella stringa di connessione:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Passaggi successivi
* [Scrivere query](/azure/data-explorer/write-queries)
* [Eseguire query sui dati in monitoraggio di Azure tramite Esplora dati di Azure](/azure/data-explorer/query-monitor-data)
* [Eseguire query di log su più risorse in Monitoraggio di Azure](../logs/cross-workspace-query.md)