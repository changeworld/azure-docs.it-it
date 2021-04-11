---
title: Avviare e arrestare un servizio Azure Kubernetes (AKS)
description: Informazioni su come arrestare o avviare un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201013"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Arrestare e avviare un cluster Azure Kubernetes Service (AKS)

È possibile che i carichi di lavoro AKS non debbano essere eseguiti in modo continuo, ad esempio un cluster di sviluppo usato solo durante l'orario di ufficio. Ciò comporta tempi in cui il cluster del servizio Kubernetes di Azure (AKS) potrebbe essere inattivo, in esecuzione non più dei componenti di sistema. È possibile ridurre il footprint del cluster [scalando tutti i `User` pool di nodi a 0](scale-cluster.md#scale-user-node-pools-to-0), ma il [ `System` pool](use-system-pools.md) è ancora necessario per eseguire i componenti di sistema mentre il cluster è in esecuzione. Per ottimizzare ulteriormente i costi durante questi periodi, è possibile disattivare completamente (arrestare) il cluster. Questa azione arresterà completamente il piano di controllo e i nodi dell'agente, consentendo di risparmiare su tutti i costi di calcolo, mantenendo al tempo stesso tutti gli oggetti e lo stato del cluster archiviati per il riavvio. È quindi possibile scegliere subito dopo un week-end o se il cluster è in esecuzione solo quando si eseguono i processi batch.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

### <a name="limitations"></a>Limitazioni

Quando si usa la funzionalità di avvio/arresto del cluster, si applicano le restrizioni seguenti:

- Questa funzionalità è supportata solo per i cluster con backup di set di scalabilità di macchine virtuali.
- Lo stato del cluster di un cluster AKS arrestato viene mantenuto per un massimo di 12 mesi. Se il cluster viene arrestato per più di 12 mesi, non è possibile recuperare lo stato del cluster. Per ulteriori informazioni, vedere i [criteri di supporto di AKS](support-policies.md).
- È possibile avviare o eliminare solo un cluster AKS interrotto. Per eseguire qualsiasi operazione come la scala o l'aggiornamento, avviare innanzitutto il cluster.

## <a name="stop-an-aks-cluster"></a>Arrestare un cluster AKS

È possibile usare il `az aks stop` comando per arrestare i nodi e il piano di controllo di un cluster AKS in esecuzione. Nell'esempio seguente viene arrestato un cluster denominato *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

È possibile verificare quando il cluster viene arrestato usando il comando [AZ AKS Show][az-aks-show] e confermando l'impostazione `powerState` Mostra come nell' `Stopped` output seguente:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se il `provisioningState` Mostra `Stopping` che indica che il cluster non è stato ancora arrestato completamente.

> [!IMPORTANT]
> Se si usano i [budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) per l'interruzione del Pod, l'operazione di arresto può richiedere più tempo perché il completamento del processo di svuotamento potrebbe richiedere più tempo.

## <a name="start-an-aks-cluster"></a>Avviare un cluster AKS

È possibile usare il `az aks start` comando per avviare i nodi del cluster AKS e il piano di controllo interrotti. Il cluster viene riavviato con lo stato del piano di controllo precedente e il numero di nodi agente.  
Nell'esempio seguente viene avviato un cluster denominato *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

È possibile verificare quando il cluster è stato avviato usando il comando [AZ AKS Show][az-aks-show] e confermare che il `powerState` Mostra `Running` come nell'output seguente:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se il `provisioningState` Mostra `Starting` che indica che il cluster non è ancora stato completamente avviato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come ridimensionare i `User` pool in 0, vedere [ridimensionare i `User` pool a 0](scale-cluster.md#scale-user-node-pools-to-0).
- Per informazioni su come risparmiare sui costi usando le istanze di spot, vedere [aggiungere un pool di nodi spot ad AKS](spot-node-pool.md).
- Per altre informazioni sui criteri di supporto di AKS, vedere [criteri di supporto AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
