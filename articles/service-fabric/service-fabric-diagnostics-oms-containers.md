---
title: Monitorare i contenitori con i log di monitoraggio di Azure
description: Usare i log di monitoraggio di Azure per il monitoraggio dei contenitori in esecuzione nei cluster di Service Fabric di Azure.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 6217569dc50517c88a5a8a7bc0f3752e7e327f4e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626657"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorare i contenitori con i log di monitoraggio di Azure
 
Questo articolo illustra i passaggi necessari per configurare la soluzione di monitoraggio del contenitore dei log di monitoraggio di Azure per visualizzare gli eventi del contenitore. Per configurare il cluster per raccogliere gli eventi del contenitore, vedere questa [esercitazione dettagliata](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurazione della soluzione di monitoraggio dei contenitori

> [!NOTE]
> È necessario configurare i log di monitoraggio di Azure per il cluster e fare in modo che l'agente di Log Analytics venga distribuito nei nodi. In caso contrario, seguire la procedura descritta in [configurare i log di monitoraggio di Azure](service-fabric-diagnostics-oms-setup.md) e aggiungere prima [l'agente log Analytics a un cluster](service-fabric-diagnostics-oms-agent.md) .

1. Una volta configurato il cluster con i log di monitoraggio di Azure e l'agente di Log Analytics, distribuire i contenitori. Attendere il completamento della distribuzione dei contenitori prima di passare al passaggio successivo.

2. In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e fare clic sulla risorsa **Soluzione Monitoraggio contenitori** visualizzata nella categoria Monitoraggio e gestione.

    ![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Creare la soluzione all'interno della stessa area di lavoro precedentemente creata per il cluster. Questa modifica attiva automaticamente l'agente, che inizierà a raccogliere i dati di Docker sui contenitori. Dopo circa 15 minuti vengono visualizzati i log e le statistiche in ingresso, come illustrato nella figura seguente.

    ![Dashboard di base di Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L'agente Abilita la raccolta di diversi log specifici del contenitore su cui è possibile eseguire query nei log di monitoraggio di Azure o utilizzati per visualizzare gli indicatori di prestazioni. I tipi di log raccolti sono:

* ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
* ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
* ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
* ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
* Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [soluzione contenitori di log di monitoraggio di Azure](../azure-monitor/containers/containers.md).
* Per altre info sull'orchestrazione dei contenitori in Service Fabric, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md)
* Acquisire familiarità con le funzionalità di [ricerca log ed esecuzione di query](../azure-monitor/logs/log-query-overview.md) incluse nei log di Monitoraggio di Azure
* Configurare i log di monitoraggio di Azure per configurare regole di [avviso automatizzate](../azure-monitor/alerts/alerts-overview.md) per facilitare il rilevamento e la diagnostica
