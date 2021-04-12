---
title: Visualizzare le metriche del cluster per il servizio Azure Kubernetes (AKS)
description: Visualizzare le metriche del cluster per il servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969327"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Visualizzare le metriche del cluster per il servizio Azure Kubernetes (AKS)

AKS fornisce un set di metriche per il piano di controllo, tra cui il server API e il ridimensionatore automatico del cluster e i nodi del cluster. Queste metriche consentono di monitorare l'integrità del cluster e risolvere i problemi. È possibile visualizzare le metriche per il cluster usando il portale di Azure.

> [!NOTE]
> Queste metriche del cluster AKS si sovrappongono a un subset delle [metriche fornite da Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Visualizzare le metriche per il cluster AKS usando il portale di Azure

Per visualizzare le metriche per il cluster AKS:

1. Accedere al [portale di Azure][azure-portal] e passare al cluster AKS.
1. Sul lato sinistro sotto *monitoraggio* selezionare *metriche*.
1. Creare un grafico per le metriche che si desidera visualizzare. Ad esempio, creare un grafico:
    1. Per *ambito*, scegliere il cluster.
    1. Per *spazio dei nomi metrica* scegliere *metrica standard del servizio contenitore (gestito)*.
    1. Per la *metrica*, in *Pod* scegliere il *numero di pod per fase*.
    1. Per l' *aggregazione* scegliere *AVG*.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

L'esempio precedente mostra le metriche per il numero medio di pod per *myAKSCluster*.

## <a name="available-metrics"></a>Metriche disponibili

Sono disponibili le metriche del cluster seguenti:

| Nome | Group | ID | Descrizione |
| --- | --- | --- | ---- |
| Richieste in viaggio | Server API (anteprima) |apiserver_current_inflight_requests | Numero massimo di richieste in corso attive sul server API per tipo di richiesta. |
| Integrità del cluster | Scalabilità automatica cluster (anteprima) | cluster_autoscaler_cluster_safe_to_autoscale | Determina se il servizio di scalabilità automatica del cluster eseguirà un'azione sul cluster. |
| Ridimensionare il cooldown | Scalabilità automatica cluster (anteprima) | cluster_autoscaler_scale_down_in_cooldown | Determina se il ridimensionamento è in fase di ricarica-nessun nodo verrà rimosso durante questo intervallo di tempo. |
| Nodi non necessari | Scalabilità automatica cluster (anteprima) | cluster_autoscaler_unneeded_nodes_count | Il auotscaler del cluster contrassegna i nodi come candidati per l'eliminazione e vengono eliminati. |
| Pod non pianificabili | Scalabilità automatica cluster (anteprima) | cluster_autoscaler_unschedulable_pods_count | Numero di Pod attualmente non pianificabili nel cluster. |
| Numero totale di core CPU disponibili in un cluster gestito | Nodi | kube_node_status_allocatable_cpu_cores | Numero totale di core CPU disponibili in un cluster gestito. |
| Numero totale di memoria disponibile in un cluster gestito | Nodi | kube_node_status_allocatable_memory_bytes | Quantità totale di memoria disponibile in un cluster gestito. |
| Stati per diverse condizioni dei nodi | Nodi | kube_node_status_condition | Stati per diverse condizioni dei nodi |
| Utilizzo CPU Millicore | Nodi (anteprima) | node_cpu_usage_millicores | Misurazione aggregata dell'utilizzo della CPU in millicore nel cluster. |
| CPU Usage Percentage (Percentuale di utilizzo CPU) | Nodi (anteprima) | node_cpu_usage_percentage | Utilizzo medio della CPU aggregato misurato in percentuale nel cluster. |
| Byte RSS memoria | Nodi (anteprima) | node_memory_rss_bytes | Memoria RSS del contenitore utilizzata in byte. |
| Percentuale RSS memoria | Nodi (anteprima) | node_memory_rss_percentage | Memoria RSS del contenitore utilizzata nella percentuale. |
| Byte working set di memoria | Nodi (anteprima) | node_memory_working_set_bytes | Contenitore working set memoria usato in byte. |
| Percentuale working set della memoria | Nodi (anteprima) | node_memory_working_set_percentage | Contenitore working set memoria utilizzata nella percentuale. |
| Byte utilizzati dal disco | Nodi (anteprima) | node_disk_usage_bytes | Spazio su disco utilizzato in byte dal dispositivo. |
| Percentuale di utilizzo del disco | Nodi (anteprima) | node_disk_usage_percentage | Spazio su disco utilizzato in percentuale dal dispositivo. |
| Rete in byte | Nodi (anteprima) | node_network_in_bytes | Byte ricevuti dalla rete. |
| Byte di rete in uscita | Nodi (anteprima) | node_network_out_bytes | Byte trasmessi dalla rete. |
| Numero di pod in stato Pronto | Pod | kube_pod_status_ready | Numero di Pod nello stato *pronto* . |
| Numero di pod per fase | Pod | kube_pod_status_phase | Numero di pod per fase. |

> [!IMPORTANT]
> È possibile aggiornare o modificare le metriche in anteprima, inclusi i relativi nomi e descrizioni, durante l'anteprima.

## <a name="next-steps"></a>Passaggi successivi

Oltre alle metriche del cluster per AKS, è anche possibile usare monitoraggio di Azure con il cluster AKS. Per altre informazioni sull'uso di monitoraggio di Azure con AKS, vedere [monitoraggio di Azure per contenitori][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/