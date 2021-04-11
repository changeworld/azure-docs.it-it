---
title: Limiti per risorse, SKU, aree
titleSuffix: Azure Kubernetes Service
description: Informazioni sulle quote predefinite, sulle dimensioni dello SKU di VM del nodo limitate e sulla disponibilità delle aree del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011465"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quote, limitazioni delle dimensioni delle macchine virtuali e disponibilità delle aree nel servizio Azure Kubernetes

Tutti i servizi di Azure definiscono le quote e i limiti predefiniti per le risorse e le funzionalità, incluse le restrizioni di utilizzo per determinati SKU di macchine virtuali (VM).

Questo articolo illustra in dettaglio i limiti delle risorse predefinite per le risorse di Azure Kubernetes Service (AKS) e la disponibilità di AKS nelle aree di Azure.

## <a name="service-quotas-and-limits"></a>Quote e limiti del servizio

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruttura sottoposta a provisioning

Tutte le altre limitazioni relative alla rete, al calcolo e all'archiviazione si applicano all'infrastruttura sottoposta a provisioning. Per i limiti rilevanti, vedere [sottoscrizione di Azure e limiti dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Quando si aggiorna un cluster AKS, le risorse aggiuntive vengono temporaneamente utilizzate. Queste risorse includono gli indirizzi IP disponibili in una subnet della rete virtuale o una quota vCPU della macchina virtuale. 
>
> Per i contenitori di Windows Server, è possibile eseguire un'operazione di aggiornamento per applicare gli aggiornamenti più recenti del nodo. Se non si dispone dello spazio di indirizzi IP disponibile o della quota vCPU per gestire queste risorse temporanee, il processo di aggiornamento del cluster avrà esito negativo. Per altre informazioni sul processo di aggiornamento dei nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Dimensioni delle macchine virtuali limitate

Ogni nodo in un cluster AKS contiene una quantità fissa di risorse di calcolo, ad esempio vCPU e memoria. Se un nodo AKS contiene risorse di calcolo insufficienti, i pod potrebbero non funzionare correttamente. Per assicurarsi che i pod del *sistema Kube* richiesti e le applicazioni possano essere pianificati in modo affidabile, **non usare gli SKU di VM seguenti in AKS**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Per altre informazioni sui tipi di VM e le relative risorse di calcolo, vedere [dimensioni delle macchine virtuali in Azure][vm-skus].

## <a name="region-availability"></a>Aree di disponibilità

Per l'elenco più recente di dove è possibile distribuire ed eseguire i cluster, vedere la pagina relativa alla [disponibilità dell'area AKS][region-availability].

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare determinati limiti e quote predefiniti. Se la risorsa supporta un aumento, richiedere l'aumento tramite una [richiesta di supporto tecnico di Azure][azure-support] . per **tipo di problema**, selezionare **quota**.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
