---
title: Aggiornare le servizio Azure Kubernetes del nodo del servizio Disattesa
description: Informazioni su come aggiornare le immagini nei nodi e nei pool di nodi del cluster del servizio AppKs.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 4f6ac01c1d4df288c823142abbc93e981048d8db
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767530"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>servizio Azure Kubernetes dell'immagine del nodo del servizio Disattesa

Il servizio AKS supporta l'aggiornamento delle immagini in un nodo in modo da essere aggiornati con il sistema operativo e gli aggiornamenti di runtime più recenti. Il servizio AKS offre una nuova immagine alla settimana con gli aggiornamenti più recenti, quindi è vantaggioso aggiornare regolarmente le immagini del nodo per le funzionalità più recenti, incluse le patch linux o Windows. Questo articolo illustra come aggiornare le immagini dei nodi del cluster del servizio Kubernetes e come aggiornare le immagini del pool di nodi senza aggiornare la versione di Kubernetes.

Per altre informazioni sulle immagini più recenti fornite dal servizio AKS, vedere le note [sulla versione del servizio AKS.](https://github.com/Azure/AKS/releases)

Per informazioni sull'aggiornamento della versione di Kubernetes per il cluster, vedere Aggiornare un cluster del servizio [Kubernetes.][upgrade-cluster]

> [!NOTE]
> Il cluster del servizio AKS deve usare set di scalabilità di macchine virtuali per i nodi.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Controllare se il pool di nodi si trova nell'immagine del nodo più recente

È possibile visualizzare la versione più recente dell'immagine del nodo disponibile per il pool di nodi con il comando seguente: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

Nell'output è possibile visualizzare il `latestNodeImageVersion` codice simile nell'esempio seguente:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Pertanto, `nodepool1` per l'immagine del nodo più recente disponibile è `AKSUbuntu-1604-2020.10.28` . È ora possibile confrontarla con la versione corrente dell'immagine del nodo in uso dal pool di nodi eseguendo:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Un esempio di output è il seguente:

```output
"AKSUbuntu-1604-2020.10.08"
```

In questo esempio è quindi possibile eseguire l'aggiornamento dalla versione `AKSUbuntu-1604-2020.10.08` corrente dell'immagine alla versione più `AKSUbuntu-1604-2020.10.28` recente. 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Aggiornare tutti i nodi in tutti i pool di nodi

L'aggiornamento dell'immagine del nodo viene eseguito con `az aks upgrade` . Per aggiornare l'immagine del nodo, usare il comando seguente:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il comando seguente per ottenere le etichette e filtrare `kubectl` le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Al termine dell'aggiornamento, usare `az aks show` per ottenere i dettagli aggiornati del pool di nodi. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` proprietà .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Aggiornare un pool di nodi specifico

L'aggiornamento dell'immagine in un pool di nodi è simile all'aggiornamento dell'immagine in un cluster.

Per aggiornare l'immagine del sistema operativo del pool di nodi senza eseguire un aggiornamento del cluster Kubernetes, usare l'opzione `--node-image-only` nell'esempio seguente:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il comando seguente per ottenere le etichette e filtrare `kubectl` le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Al termine dell'aggiornamento, usare `az aks nodepool show` per ottenere i dettagli aggiornati del pool di nodi. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` proprietà .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Aggiornare le immagini dei nodi con un picco del nodo

Per velocizzare il processo di aggiornamento dell'immagine del nodo, è possibile aggiornare le immagini dei nodi usando un valore di picco del nodo personalizzabile. Per impostazione predefinita, il servizio AKS usa un nodo aggiuntivo per configurare gli aggiornamenti.

Se si desidera aumentare la velocità degli aggiornamenti, usare il valore per configurare il numero di nodi da usare per gli aggiornamenti in modo che siano `--max-surge` completati più velocemente. Per altre informazioni sui compromessi di varie impostazioni, vedere `--max-surge` Personalizzare l'aggiornamento delle [sovratensioni dei nodi.][max-surge]

Il comando seguente imposta il valore di picco massimo per l'esecuzione di un aggiornamento dell'immagine del nodo:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il comando seguente per ottenere le etichette e filtrare `kubectl` le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Usare `az aks nodepool show` per ottenere i dettagli aggiornati del pool di nodi. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` proprietà .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle [immagini del nodo più recenti,](https://github.com/Azure/AKS/releases) vedere le note sulla versione del server del supporto di Microsoft Service Pack.
- Informazioni su come aggiornare la versione di Kubernetes con Aggiornare un cluster del servizio [Kubernetes.][upgrade-cluster]
- [Applicare automaticamente gli aggiornamenti del cluster e del pool di nodi con GitHub Actions][github-schedule]
- Per altre informazioni su più pool di nodi e su come aggiornare i pool di nodi, vedere [Creare e gestire più pool di nodi.][use-multiple-node-pools]

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
