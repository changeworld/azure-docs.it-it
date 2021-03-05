---
title: Configurare il monitoraggio PV con informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio dei cluster Kubernetes con volumi permanenti con informazioni dettagliate sul contenitore.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 578cfe128b7445f8b09771999d1e653e92c4befa
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200700"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configurare il monitoraggio PV con informazioni dettagliate sul contenitore

A partire dalla versione dell'agente *ciprod10052020*, monitoraggio di Azure per i contenitori Integrated Agent supporta ora il monitoraggio dell'utilizzo di PV (volume permanente). Con la versione dell'agente *ciprod01112021*, l'agente supporta il monitoraggio dell'inventario PV, incluse le informazioni sullo stato, la classe di archiviazione, il tipo, le modalità di accesso e altri dettagli.
## <a name="pv-metrics"></a>Metriche PV

Il contenitore Insights avvia automaticamente il monitoraggio dell'utilizzo fotovoltaico raccogliendo le metriche seguenti a intervalli di 60 secondi e archiviarle nella tabella **InsightMetrics** .

| Nome metrica | Dimensione metrica (tag) | Descrizione metrica | | `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, clustername | Spazio utilizzato in byte per un volume permanente specifico con un'attestazione utilizzata da un pod specifico. `capacityBytes` viene ripiegata come dimensione nel campo tag per ridurre i costi di inserimento dei dati e per semplificare le query. |

Altre informazioni sulla configurazione di metriche PV raccolte sono disponibili [qui](https://aka.ms/ci/pvconfig).

## <a name="pv-inventory"></a>Inventario PV

Monitoraggio di Azure per i contenitori avvia automaticamente il monitoraggio PVs raccogliendo le informazioni seguenti a intervalli di 60 secondi e archiviarle nella tabella **KubePVInventory** .

|Dati |origine dati| Tipo di dati| Campi|
|-----|-----------|----------|-------|
|Inventario dei volumi permanenti in un cluster Kubernetes |API Kube |`KubePVInventory` | PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, clustername, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Monitorare i volumi permanenti

Il monitoraggio di Azure per i contenitori include grafici preconfigurati per la metrica di utilizzo e le informazioni di inventario nei modelli di cartella di lavoro per ogni cluster. È anche possibile abilitare un avviso consigliato per l'utilizzo PV ed eseguire query su queste metriche in Log Analytics.  

### <a name="workload-details-workbook"></a>Cartella di lavoro dettagli carico di lavoro

È possibile trovare i grafici di utilizzo per carichi di lavoro specifici nella scheda volume permanente della cartella di lavoro **Dettagli carico** di lavoro direttamente da un cluster AKS selezionando cartelle di lavoro nel riquadro a sinistra, dall'elenco a discesa **Visualizza cartelle di lavoro** nel riquadro Insights o dalla **scheda report (anteprima)** nel riquadro Insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Esempio di cartella di lavoro PV del monitoraggio di Azure":::

### <a name="persistent-volume-details-workbook"></a>Cartella di lavoro dettagli volume permanente

È possibile trovare una panoramica dell'inventario del volume permanente nella cartella di lavoro dei **Dettagli del volume permanente** direttamente da un cluster AKS selezionando cartelle di lavoro nel riquadro a sinistra, dall'elenco a discesa **Visualizza cartelle di lavoro** nel riquadro Insights o dalla scheda **report (anteprima)** nel riquadro Insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Esempio di cartella di lavoro dei dettagli PV di monitoraggio di Azure":::

### <a name="persistent-volume-usage-recommended-alert"></a>Avviso di utilizzo del volume permanente consigliato
È possibile abilitare un avviso consigliato quando l'utilizzo medio PV per un pod è superiore al 80%. Altre informazioni sugli avvisi sono disponibili [qui e su](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts) come eseguire l'override [della soglia predefinita](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle metriche PV raccolte sono disponibili [qui](./container-insights-agent-config.md).
