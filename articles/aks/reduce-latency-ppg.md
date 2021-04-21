---
title: Usare i gruppi di posizionamento di prossimità per ridurre la latenza per i cluster servizio Azure Kubernetes (AKS)
description: Informazioni su come usare i gruppi di posizionamento di prossimità per ridurre la latenza per i carichi di lavoro del cluster del servizio Web Diaks.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779598"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Ridurre la latenza con i gruppi di posizionamento di prossimità

> [!Note]
> Quando si usano gruppi di posizionamento di prossimità nel servizio Diaks, la colocazione si applica solo ai nodi agente. La latenza da nodo a nodo e il pod ospitato corrispondente sono stati migliorati. La condivisione della posizione non influisce sul posizionamento del piano di controllo di un cluster.

Quando si distribuisce l'applicazione in Azure, la distribuzione di istanze di macchine virtuali tra aree o zone di disponibilità crea una latenza di rete che può influire sulle prestazioni complessive dell'applicazione. Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino fisicamente vicine tra loro. Alcune applicazioni come giochi, simulazioni di progettazione e trading ad alta frequenza (HFT) richiedono bassa latenza e attività che vengono completate rapidamente. Per scenari HPC (High Performance Computing) come [](../virtual-machines/co-location.md#proximity-placement-groups) questi, prendere in considerazione l'uso di gruppi di posizionamento di prossimità (PPG) per i pool di nodi del cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.14 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="limitations"></a>Limitazioni

* Un gruppo di posizionamento di prossimità può essere mappato al massimo a una zona di disponibilità.
* Un pool di nodi deve usare set di scalabilità di macchine virtuali per associare un gruppo di posizionamento di prossimità.
* Un pool di nodi può associare un gruppo di posizionamento di prossimità solo al momento della creazione del pool di nodi.

## <a name="node-pools-and-proximity-placement-groups"></a>Pool di nodi e gruppi di posizionamento di prossimità

La prima risorsa distribuita con un gruppo di posizionamento di prossimità si collega a una risorsa data center. Le risorse aggiuntive distribuite con lo stesso gruppo di posizionamento di prossimità si trovano nello stesso data center. Dopo che tutte le risorse che usano il gruppo di posizionamento di prossimità sono state arrestate (deallocate) o eliminate, non vengono più collegate.

* Molti pool di nodi possono essere associati a un singolo gruppo di posizionamento di prossimità.
* Un pool di nodi può essere associato solo a un singolo gruppo di posizionamento di prossimità.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Configurare i gruppi di posizionamento di prossimità con le zone di disponibilità

> [!NOTE]
> Anche se i gruppi di posizionamento di prossimità richiedono che un pool di nodi usi al massimo una zona di disponibilità, il contratto di servizio delle macchine virtuali di Azure di base del [99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) è ancora attivo per le macchine virtuali in una singola zona.

I gruppi di posizionamento di prossimità sono un concetto di pool di nodi e associati a ogni singolo pool di nodi. L'uso di una risorsa PPG non ha alcun impatto sulla disponibilità del piano di controllo del servizio Web di Servizio Controllo di Accesso Remoto. Ciò può influire sulla modalità di progettazione di un cluster con le zone. Per assicurarsi che un cluster sia distribuito in più zone, è consigliabile seguire questa progettazione.

* Effettuare il provisioning di un cluster con il primo pool di sistema usando 3 zone e nessun gruppo di posizionamento di prossimità associato. Ciò garantisce che i pod di sistema si atterrino in un pool di nodi dedicato che verrà distribuito tra più zone.
* Aggiungere altri pool di nodi utente con una zona univoca e un gruppo di posizionamento di prossimità associati a ogni pool. Un esempio è nodepool1 nella zona 1 e PPG1, nodepool2 nella zona 2 e PPG2, nodepool3 nella zona 3 con PPG3. In questo modo, a livello di cluster, i nodi vengono distribuiti tra più zone e ogni singolo pool di nodi viene colocato nella zona designata con una risorsa PPG dedicata.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Creare un nuovo cluster del servizio AKS con un gruppo di posizionamento di prossimità

Nell'esempio seguente viene utilizzato [il comando az group create][az-group-create] per creare un gruppo di risorse denominato *myResourceGroup* nell'area *centralus.* Viene quindi creato un cluster del servizio Web Diaks denominato *myAKSCluster* usando il [comando az aks create.][az-aks-create]

La rete accelerata migliora notevolmente le prestazioni di rete delle macchine virtuali. Idealmente, usare i gruppi di posizionamento di prossimità in combinazione con la rete accelerata. Per impostazione predefinita, il servizio AzureKs usa la rete accelerata nelle istanze di [macchine](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)virtuali supportate, che includono la maggior parte delle macchine virtuali di Azure con due o più vCKU.

Creare un nuovo cluster del servizio AKS con un gruppo di posizionamento di prossimità associato al primo pool di nodi di sistema:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Eseguire il comando seguente e archiviare l'ID restituito:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Il comando genera l'output, che include il *valore ID* necessario per i comandi dell'interfaccia della riga di comando futuri:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Usare l'ID risorsa del gruppo di posizionamento di prossimità per *il valore myPPGResourceID* nel comando seguente:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Aggiungere un gruppo di posizionamento di prossimità a un cluster esistente

È possibile aggiungere un gruppo di posizionamento di prossimità a un cluster esistente creando un nuovo pool di nodi. Facoltativamente, è possibile eseguire la migrazione dei carichi di lavoro esistenti al nuovo pool di nodi e quindi eliminare il pool di nodi originale.

Usare lo stesso gruppo di posizionamento di prossimità creato in precedenza e in questo modo i nodi dell'agente in entrambi i pool di nodi nel cluster del servizio Web Distorsi fisicamente nello stesso data center.

Usare l'ID risorsa del gruppo di posizionamento di prossimità creato in precedenza e aggiungere un nuovo pool di nodi con il [`az aks nodepool add`][az-aks-nodepool-add] comando :

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Eseguire la pulizia

Per eliminare il cluster, usare il comando per eliminare il gruppo di risorse del servizio [`az group delete`][az-group-delete] Web AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [gruppi di posizionamento di prossimità.][proximity-placement-groups]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete