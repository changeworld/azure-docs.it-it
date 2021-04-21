---
title: Usare pool di nodi di sistema in servizio Azure Kubernetes (servizio Web di gestione delle prestazioni)
description: Informazioni su come creare e gestire pool di nodi di sistema in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 8b41b43c70f72ab327de2f1d59415cc1f49e5a5b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767404"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gestire i pool di nodi di sistema in servizio Azure Kubernetes (servizio Web AKS)

In servizio Azure Kubernetes ,i nodi della stessa configurazione vengono raggruppati in pool *di nodi.* I pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. I pool di nodi di sistema e i pool di nodi utente sono due diverse modalità di pool di nodi per i cluster del servizio Web Disaks. I pool di nodi di sistema servono allo scopo principale di ospitare pod di sistema critici, ad `CoreDNS` esempio e `metrics-server` . I pool di nodi utente servono allo scopo principale di ospitare i pod dell'applicazione. Tuttavia, i pod dell'applicazione possono essere pianificati nei pool di nodi di sistema se si vuole avere un solo pool nel cluster del servizio Web Del servizio Web. Ogni cluster del servizio Web Del servizio Gestione servizio Web deve contenere almeno un pool di nodi di sistema con almeno un nodo.

> [!Important]
> Se si esegue un singolo pool di nodi di sistema per il cluster del servizio Web Disatteso in un ambiente di produzione, è consigliabile usare almeno tre nodi per il pool di nodi.

## <a name="before-you-begin"></a>Prima di iniziare

* È necessaria l'interfaccia della riga di comando di Azure versione 2.3.1 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e gestiscono cluster del servizio Web Del servizio App che supportano i pool di nodi di sistema.

* Vedere Quote, restrizioni relative alle dimensioni delle macchine virtuali e disponibilità dell'area [in servizio Azure Kubernetes (AKS).][quotas-skus-regions]
* Il cluster del servizio AzureKs deve essere compilato con set di scalabilità di macchine virtuali come tipo di macchina virtuale e bilanciamento del carico SKU *Standard.*
* Il nome di un pool di nodi può contenere solo caratteri alfanumerici minuscoli e deve iniziare con una lettera minuscola. Per i pool di nodi Linux, la lunghezza deve essere compresa tra 1 e 12 caratteri. Per i pool di nodi windows, la lunghezza deve essere compresa tra 1 e 6 caratteri.
* Per impostare una modalità pool di nodi, è necessario usare una versione API 2020-03-01 o successiva. I cluster creati in versioni api precedenti alla versione 2020-03-01 contengono solo pool di nodi utente, ma è possibile eseguirne la migrazione per contenere pool di nodi di sistema seguendo i passaggi della [modalità pool](#update-existing-cluster-system-and-user-node-pools)di aggiornamento .
* La modalità di un pool di nodi è una proprietà obbligatoria e deve essere impostata in modo esplicito quando si usano modelli arm o chiamate API dirette.

## <a name="system-and-user-node-pools"></a>Pool di nodi utente e di sistema

Nel caso di un pool di nodi di sistema, il kubernetes.azure.com/mode AKS **assegna automaticamente l'etichetta kubernetes.azure.com/mode: system** ai relativi nodi. Ciò fa sì che il servizio Contenitore di AKS preferisca la pianificazione dei pod di sistema nei pool di nodi che contengono questa etichetta. Questa etichetta non impedisce di pianificare i pod dell'applicazione nei pool di nodi di sistema. Tuttavia, è consigliabile isolare i pod di sistema critici dai pod dell'applicazione per evitare che pod di applicazioni non configurati correttamente o non autorizzati esercitino accidentalmente i pod di sistema. È possibile applicare questo comportamento creando un pool di nodi di sistema dedicato. Usare il `CriticalAddonsOnly=true:NoSchedule` taint per impedire la pianificazione dei pod dell'applicazione nei pool di nodi di sistema.

I pool di nodi di sistema hanno le restrizioni seguenti:

* I pool di sistema osType devono essere Linux.
* I pool di nodi utente osType possono essere Linux o Windows.
* I pool di sistema devono contenere almeno un nodo e i pool di nodi utente possono contenere zero o più nodi.
* I pool di nodi di sistema richiedono uno SKU della macchina virtuale di almeno 2 vCKU e 4 GB di memoria. Tuttavia, la macchina virtuale (serie B) con burst non è consigliata.
* È consigliabile almeno due nodi con 4 vC CPU (ad esempio, Standard_DS4_v2), soprattutto per i cluster di grandi dimensioni (più repliche pod CoreDNS, 3-4+ componenti aggiuntivi e così via).
* I pool di nodi di sistema devono supportare almeno 30 pod, come descritto dalla formula dei valori minimo e [massimo per i pod][maximum-pods].
* I pool di nodi spot richiedono pool di nodi utente.
* L'aggiunta di un pool di nodi di sistema aggiuntivo o la modifica del pool di nodi che è un pool di nodi *di sistema NON sposterà* automaticamente i pod di sistema. I pod di sistema possono continuare a essere eseguiti nello stesso pool di nodi anche se viene modificato in un pool di nodi utente. Se si elimina o si ridimensiona un pool di nodi che esegue pod di sistema che in precedenza erano un pool di nodi di sistema, tali pod di sistema vengono ridistribuito con la pianificazione preferita per il nuovo pool di nodi di sistema.

Con i pool di nodi è possibile eseguire le operazioni seguenti:

* Creare un pool di nodi di sistema dedicato (preferire la pianificazione dei pod di sistema ai pool di nodi di `mode:system` )
* Modificare un pool di nodi di sistema in modo che sia un pool di nodi utente, a condizione che sia disponibile un altro pool di nodi di sistema da sostituire nel cluster del servizio AKS.
* Modificare un pool di nodi utente in modo che sia un pool di nodi di sistema.
* Eliminare i pool di nodi utente.
* È possibile eliminare i pool di nodi di sistema, a condizione che sia disponibile un altro pool di nodi di sistema da inserire nel cluster del servizio Web Del servizio AKS.
* Un cluster del servizio AKS può avere più pool di nodi di sistema e richiede almeno un pool di nodi di sistema.
* Se si vogliono modificare varie impostazioni non modificabili nei pool di nodi esistenti, è possibile creare nuovi pool di nodi per sostituirli. Un esempio è quello di aggiungere un nuovo pool di nodi con una nuova impostazione maxPods ed eliminare il pool di nodi precedente.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Creare un nuovo cluster del servizio AKS con un pool di nodi di sistema

Quando si crea un nuovo cluster del servizio AKS, si crea automaticamente un pool di nodi di sistema con un singolo nodo. Per impostazione predefinita, il pool di nodi iniziale è una modalità di tipo system. Quando si creano nuovi pool di nodi con , tali pool di nodi sono pool di nodi utente a meno che non si specifica `az aks nodepool add` in modo esplicito il parametro mode.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Usare il comando [az aks create][az-aks-create] per creare un cluster del servizio Azure Kubernetes. Nell'esempio seguente viene creato un cluster *denominato myAKSCluster* con un pool di sistema dedicato contenente un nodo. Per i carichi di lavoro di produzione, assicurarsi di usare pool di nodi di sistema con almeno tre nodi. Il completamento dell'operazione può richiedere alcuni minuti.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Aggiungere un pool di nodi di sistema dedicato a un cluster del servizio Controllo di accesso esistente

> [!Important]
> Non è possibile modificare i nodi tramite l'interfaccia della riga di comando dopo la creazione del pool di nodi.

È possibile aggiungere uno o più pool di nodi di sistema ai cluster del servizio Web Del servizio Web di Microsoft Windows esistenti. È consigliabile pianificare i pod dell'applicazione nei pool di nodi utente e dedicare i pool di nodi di sistema solo ai pod di sistema critici. Ciò impedisce ai pod di applicazioni non autorizzati di eliminare accidentalmente i pod di sistema. Applicare questo comportamento con il `CriticalAddonsOnly=true:NoSchedule` [taint per i][aks-taints] pool di nodi di sistema. 

Il comando seguente aggiunge un pool di nodi dedicato del sistema di tipi modalità con un conteggio predefinito di tre nodi.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Visualizzare i dettagli per il pool di nodi

È possibile controllare i dettagli del pool di nodi con il comando seguente.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Viene definita una modalità di tipo **Sistema** per i pool di nodi di sistema e una modalità di tipo **Utente** per i pool di nodi utente. Per un pool di sistema, verificare che il taint sia impostato su , impedendo ai pod dell'applicazione di essere `CriticalAddonsOnly=true:NoSchedule` pianificati in questo pool di nodi.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Aggiornare i pool di nodi utente e di sistema del cluster esistenti

> [!NOTE]
> Per impostare una modalità pool di nodi di sistema, è necessario usare una versione dell'API 2020-03-01 o successiva. I cluster creati nelle versioni api precedenti alla versione 2020-03-01 contengono di conseguenza solo pool di nodi utente. Per ricevere le funzionalità e i vantaggi dei pool di nodi di sistema nei cluster meno recenti, aggiornare la modalità dei pool di nodi esistenti con i comandi seguenti nella versione più recente dell'interfaccia della riga di comando di Azure.

È possibile modificare le modalità sia per i pool di nodi di sistema che per i pool di nodi utente. È possibile modificare un pool di nodi di sistema in un pool di utenti solo se esiste già un altro pool di nodi di sistema nel cluster del servizio Web AKS.

Questo comando modifica un pool di nodi di sistema in un pool di nodi utente.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Questo comando modifica un pool di nodi utente in un pool di nodi di sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Eliminare un pool di nodi di sistema

> [!Note]
> Per usare i pool di nodi di sistema nei cluster del servizio Disassociato di Microsoft Prima dell'API versione 2020-03-02, aggiungere un nuovo pool di nodi di sistema, quindi eliminare il pool di nodi predefinito originale.

Per poter eliminare uno di essi, è necessario disporre di almeno due pool di nodi di sistema nel cluster del servizio Web Diaks.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare il cluster, usare il [comando az group delete][az-group-delete] per eliminare il gruppo di risorse del servizio Web AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare e gestire pool di nodi di sistema in un cluster del servizio Web Disassoce. Per altre informazioni su come usare più pool di nodi, vedere [Usare più pool di nodi.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
