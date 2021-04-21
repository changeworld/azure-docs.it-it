---
title: Ridimensionare un cluster del servizio Azure Kubernetes
description: Scopri come ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782888"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS)

Se la risorsa richiede la modifica delle applicazioni, è possibile ridimensionare manualmente un cluster del servizio Azure Kubernetes per eseguire un numero diverso di nodi. In caso di riduzione, i nodi verranno accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] per ridurre al minimo le interruzioni nelle applicazioni in esecuzione. Quando si esegue la scalabilità verticale, il servizio Kubernetes attende che i nodi siano contrassegnati dal cluster Kubernetes prima della pianificazione `Ready` dei pod.

## <a name="scale-the-cluster-nodes"></a>Ridimensionare i nodi del cluster

Per prima cosa, *ottenere il nome* del pool di nodi usando il comando [az aks show.][az-aks-show] L'esempio seguente ottiene il nome del pool di nodi per il cluster *denominato myAKSCluster* nel gruppo di risorse *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

L'output di esempio seguente illustra che il *nome* è *nodepool1*:

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Usare il [comando az aks scale][az-aks-scale] per ridimensionare i nodi del cluster. L'esempio seguente ridimensiona un cluster denominato *myAKSCluster* passando a un singolo nodo. Specificare il proprio `--nodepool-name` dal comando precedente, ad esempio *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

L'output di esempio seguente mostra che il cluster è stato ridimensionato a un nodo, come illustrato nella sezione *agentPoolProfiles*:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>Ridimensionare `User` i pool di nodi su 0

A `System` differenza dei pool di nodi che richiedono sempre nodi in esecuzione, i pool di nodi consentono di aumentare a `User` 0. Per altre informazioni sulle differenze tra i pool di nodi di sistema e utente, vedere [Pool di nodi di sistema e utente](use-system-pools.md).

Per ridimensionare un pool di utenti su 0, è possibile usare [az aks nodepool scale][az-aks-nodepool-scale] in alternativa al comando precedente e impostare 0 come `az aks scale` numero di nodi.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

È anche possibile ridimensionare automaticamente i pool di nodi su 0 nodi, impostando il parametro di Scalabilità automatica `User` `--min-count` [cluster](cluster-autoscaler.md) su 0.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato ridimensionato manualmente un cluster del servizio AKS per aumentare o ridurre il numero di nodi. È anche possibile usare la [scalabilità automatica del cluster][cluster-autoscaler] per ridimensionare automaticamente il cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale