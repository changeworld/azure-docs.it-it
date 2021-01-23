---
title: Eseguire query sui dati in monitoraggio di Azure con Esplora dati di Azure (anteprima)
description: Usare Esplora dati di Azure per eseguire query tra prodotti tra Esplora dati di Azure, aree di lavoro Log Analytics e applicazioni Application Insights classiche in monitoraggio di Azure.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a31ef69d84f64e4bcaa46adac26a29d2cc367351
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731701"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Eseguire query sui dati in Monitoraggio di Azure con Esplora dati di Azure (anteprima)

Il Esplora dati di Azure supporta le query tra i servizi tra Esplora dati di Azure, [Application Insights (ai)](../app/app-insights-overview.md)e [log Analytics (la)](./data-platform-logs.md). È quindi possibile eseguire una query nell'area di lavoro Log Analytics/Application Insights usando gli strumenti di Esplora dati di Azure e farvi riferimento in una query tra servizi. Questo articolo illustra come eseguire una query tra servizi e come aggiungere l'area di lavoro Log Analytics/Application Insights ad Azure Esplora dati interfaccia utente Web.

Flusso di query tra servizi Esplora dati di Azure: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="flusso del proxy di Esplora dati di Azure.":::

> [!NOTE]
> * La possibilità di eseguire query sui dati di monitoraggio di Azure da Azure Esplora dati, direttamente da Azure Esplora dati client Tools o indirettamente eseguendo una query su un cluster di Azure Esplora dati, è in modalità di anteprima.
>* Per eventuali domande, contattare il team di [query tra i servizi](mailto:adxproxy@microsoft.com) .

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Aggiungere un'area di lavoro Log Analytics/Application Insights agli strumenti client di Esplora dati di Azure

1. Verificare che il cluster nativo di Esplora dati di Azure (come il cluster *help*) sia visualizzato nel menu a sinistra prima di connettersi al cluster di Log Analytics o Application Insights.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster nativo di Azure Esplora dati.":::

 Nell'interfaccia utente di Esplora dati di Azure (https://dataexplorer.azure.com/clusters) selezionare **Aggiungi cluster**.

2. Nella finestra **Aggiungi cluster** aggiungere l'URL del cluster di Log Analytics o Application Insights.

    * Per Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Per Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selezionare **Aggiungi**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Aggiungere il cluster.":::
 
>[!NOTE]
>Se si aggiunge una connessione a più di un'area di lavoro Log Analytics/Application Insights, assegnare a ognuno un nome diverso. In caso contrario avranno tutti lo stesso nome nel riquadro sinistro.

 Una volta stabilita la connessione, l'area di lavoro Log Analytics o Application Insights verrà visualizzata nel riquadro sinistro del cluster nativo di Esplora dati di Azure.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e i cluster di Azure Esplora dati.":::
 
> [!NOTE]
> Il numero di aree di lavoro di Monitoraggio di Azure di cui è possibile eseguire il mapping è limitato a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Creare query usando i dati di monitoraggio di Azure

È possibile eseguire le query usando strumenti client che supportano le query Kusto, come: Kusto Explorer, interfaccia utente Web di Esplora dati di Azure, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, API REST.

> [!NOTE]
> La capacità di query tra servizi viene utilizzata solo per il recupero dei dati. Per altre informazioni, vedere [Supportabilità delle funzioni](#function-supportability).

> [!TIP]
> * Il nome del database deve avere lo stesso nome della risorsa specificata nella query tra servizi. I nomi fanno distinzione tra maiuscole e minuscole.
> * Nelle query tra cluster assicurarsi che la denominazione delle app di Application Insights e delle aree di lavoro Log Analytics sia corretta.
> * Se i nomi contengono caratteri speciali, vengono sostituiti dalla codifica URL nella query tra servizi.
> * Se un nome contiene caratteri non conformi alle [regole di denominazione degli identificatori KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), questi vengono sostituiti con il carattere trattino ( **-** ).

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Indirizzare la query sulle aree di lavoro Log Analytics o Application Insights dagli strumenti client di Esplora dati di Azure

Eseguire query sulle aree di lavoro Log Analytics o Application Insights. Verificare che l'area di lavoro sia selezionata nel riquadro sinistro.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Eseguire query Log Analytics area di lavoro.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Query incrociata del Log Analytics o Application Insights e del cluster nativo di Azure Esplora dati

Quando si eseguono query tra servizi, verificare che il cluster nativo di Esplora dati di Azure sia selezionato nel riquadro sinistro. Gli esempi seguenti illustrano come combinare le tabelle del cluster di Azure Esplora dati [usando Unione](/azure/data-explorer/kusto/query/unionoperator) con log Analytics area di lavoro.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Query tra i servizi dalla Esplora dati di Azure.":::

Se si usa l'[operatore `join`](/azure/data-explorer/kusto/query/joinoperator) invece di union potrebbe essere necessario un [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) per l'esecuzione nel cluster nativo di Esplora dati di Azure.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unire tramite join i dati di un cluster di Esplora dati di Azure in un tenant con una risorsa di Monitoraggio di Azure in un altro

Le query tra tenant tra i servizi non sono supportate. Si è connessi a un solo tenant per eseguire la query che si estende su entrambe le risorse.

Se la risorsa di Esplora dati di Azure è nel tenant 'A' e l'area di lavoro Log Analytics è nel tenant 'B', usare uno dei due metodi seguenti:

1. Esplora dati di Azure consente di aggiungere ruoli per le entità di sicurezza in tenant diversi. Aggiungere il proprio ID utente nel tenant "B" come utente autorizzato nel cluster di Esplora dati di Azure. Verificare che la proprietà *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* nel cluster di Esplora dati di Azure contenga il tenant "B". Eseguire la query incrociata completamente nel tenant "B".

2. Usare [Lighthouse](../../lighthouse/index.yml) per proiettare la risorsa di Monitoraggio di Azure nel tenant "A".
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Connettersi a cluster di Esplora dati di Azure da tenant diversi

Kusto Explorer esegue automaticamente l'accesso dell'utente nel tenant a cui appartiene originariamente l'account utente. Per accedere a risorse in altri tenant con lo stesso account utente, è necessario specificare in modo esplicito il `tenantId` nella stringa di connessione: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Supportabilità delle funzioni

Le query di Azure Esplora dati cross Service supportano le funzioni sia per Application Insights che per Log Analytics.
Questa funzionalità consente alle query tra cluster di fare riferimento direttamente a una funzione tabulare di Monitoraggio di Azure.
Con la query Cross Service sono supportati i comandi seguenti:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

L'immagine seguente illustra un esempio di query su una funzione tabulare dall'interfaccia utente Web di Esplora dati di Azure.
Per usare la funzione, eseguire il nome nella finestra Query.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Eseguire una query su una funzione tabulare dall'interfaccia utente Web di Azure Esplora dati.":::

## <a name="additional-syntax-examples"></a>Esempi di sintassi aggiuntivi

Quando si chiamano i cluster Log Analytics o Application Insights, sono disponibili le opzioni di sintassi seguenti:

|Descrizione della sintassi  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database all'interno di un cluster che contiene solo la risorsa definita in questa sottoscrizione (**consigliata per le query tra cluster**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster che contiene tutte le app e le aree di lavoro in questa sottoscrizione    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster che contiene tutte le app e le aree di lavoro nella sottoscrizione che sono membri di questo gruppo di risorse    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster che contiene solo la risorsa definita in questa sottoscrizione      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [struttura dei dati di log Analytics aree di lavoro e Application Insights](data-platform-logs.md).
- Informazioni su come [scrivere query in Azure Esplora dati](/azure/data-explorer/write-queries).