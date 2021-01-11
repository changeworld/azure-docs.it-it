---
title: Query tra i servizi tra monitoraggio di Azure e Esplora dati di Azure (anteprima)
description: Eseguire query sui dati di Azure Esplora dati tramite gli strumenti di Azure Log Analytics viceversa per aggiungere e analizzare tutti i dati in un'unica posizione.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 5aadd9d96f538f92e1b9e0100b2c1055ee0b0633
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065270"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Query tra servizi-monitoraggio di Azure e Esplora dati di Azure (anteprima)
Creazione di query tra servizi tra [Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/), [Application Insights](/azure/azure-monitor/app/app-insights-overview)e [log Analytics](/azure/azure-monitor/platform/data-platform-logs).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Monitoraggio di Azure e query tra servizi Esplora dati di Azure
Questa esperienza consente di [creare query tra i servizi tra azure Esplora dati e monitoraggio di Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) e di [creare query tra i servizi tra monitoraggio di azure e Azure Esplora dati](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy).

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Flusso del proxy di Esplora dati di Azure.":::

Ad esempio, (esecuzione di query su Esplora dati di Azure da Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Dove la query esterna esegue una query su una tabella nell'area di lavoro e quindi si unisce a un'altra tabella in un cluster di Azure Esplora dati (in questo caso, clustername = Help, DatabaseName = Samples) usando una nuova funzione "ADX ()", come per eseguire una query su un'altra area di lavoro dall'interno del testo della query.

> [!NOTE]
> * La possibilità di eseguire query sui dati di monitoraggio di Azure da Azure Esplora dati, direttamente da Azure Esplora dati client Tools o indirettamente eseguendo una query su un cluster di Azure Esplora dati, è in modalità di anteprima.
> * Per eventuali domande, contattare il team di [query tra i servizi](mailto:adxproxy@microsoft.com) .

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Eseguire query sui dati di Log Analytics esportati dall'account di archiviazione BLOB di Azure

L'esportazione di dati da monitoraggio di Azure a un account di archiviazione di Azure consente la conservazione a basso costo e la possibilità di riallocare i log in aree diverse.

Usare Esplora dati di Azure per eseguire query sui dati esportati dalle aree di lavoro di Log Analytics. Una volta configurate, le tabelle supportate inviate dalle aree di lavoro a un account di archiviazione di Azure saranno disponibili come origine dati per Esplora dati di Azure. [Eseguire query sui dati esportati da monitoraggio di Azure con Esplora dati di Azure (anteprima)](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Query Esplora dati di Azure dal flusso di archiviazione.":::

>[!tip] 
> * Per esportare tutti i dati dall'area di lavoro di Log Analytics a un account di archiviazione di Azure o a un hub eventi, usare la funzionalità di esportazione dei dati Log Analytics area di lavoro dei log di monitoraggio di Azure [Vedere log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:
* [creazione di query tra servizi tra Esplora dati di Azure e monitoraggio di Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Eseguire query sui dati di monitoraggio di Azure da Azure Esplora dati
* [creazione di query tra servizi tra monitoraggio di Azure e azure Esplora dati](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Eseguire query sui dati di Azure Esplora dati da monitoraggio di Azure
* [Log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Collegare ed eseguire query sull'account di archiviazione BLOB di Azure con Log Analytics dati esportati.
