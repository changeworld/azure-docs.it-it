---
title: Configurare il monitoraggio PV con informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio dei cluster Kubernetes con volumi permanenti con informazioni dettagliate sul contenitore.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713729"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configurare il monitoraggio PV con informazioni dettagliate sul contenitore

A partire dalla versione dell'agente *ciprod10052020*, l'agente integrato di container Insights ora supporta il monitoraggio dell'utilizzo di PV (volume permanente).

## <a name="pv-metrics"></a>Metriche PV

Il contenitore Insights avvia automaticamente il monitoraggio PV raccogliendo le metriche seguenti a intervalli 60sec e archiviando tali metriche nella tabella **InsightMetrics** .

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Spazio utilizzato in byte per un volume permanente specifico con un'attestazione utilizzata da un pod specifico. `pvCapacityBytes` viene ripiegata come dimensione nel campo tag per ridurre i costi di inserimento dei dati e per semplificare le query.|

## <a name="monitor-persistent-volumes"></a>Monitorare i volumi permanenti

Il contenitore Insights include grafici preconfigurati per questa metrica in una cartella di lavoro per ogni cluster. È possibile trovare i grafici nella scheda volume permanente della cartella di lavoro **Dettagli carico** di lavoro direttamente da un cluster AKS selezionando cartelle di lavoro nel riquadro a sinistra e nell'elenco a discesa **Visualizza cartelle di lavoro** di Insight. È anche possibile abilitare un avviso consigliato per l'utilizzo PV, nonché eseguire query su queste metriche in Log Analytics.  

![Esempio di cartella di lavoro PV del monitoraggio di Azure](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle metriche PV raccolte sono disponibili [qui](./container-insights-agent-config.md).
