---
title: Usare il servizio Azure Kubernetes con il contratto di servizio relativo al tempo di attività
description: Informazioni sull'offerta opzionale del contratto di servizio relativo al tempo di attività per il server API del servizio Azure Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 846446b4c19c066afe789bf636d68ad37b20709e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779562"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Servizio Azure Kubernetes: contratto di servizio relativo al tempo di attività

Il contratto di servizio relativo al tempo di attività è una funzionalità facoltativa che consente di ottenere un contratto di servizio con copertura finanziaria più elevata per un cluster. Il contratto di servizio relativo al tempo di attività garantisce il 99,95% di disponibilità dell'endpoint del server dell'API Kubernetes per i cluster che usano [zone di disponibilità][availability-zones] e il 99,9% della disponibilità per i cluster che non usano zone di disponibilità. Il servizio Azure Kubernetes usa le repliche dei nodi master nei domini di aggiornamento e di errore per garantire che siano soddisfatti i requisiti del contratto di servizio.

I clienti che necessitano di un contratto di servizio per soddisfare i requisiti di conformità o richiedere l'estensione di un contratto di servizio agli utenti finali devono abilitare questa funzionalità. Anche i clienti con carichi di lavoro critici che trarranno vantaggio da un contratto di servizio relativo al tempo di attività possono usufruire di questa opzione. L'uso di un contratto di servizio relativo al tempo di attività con zone di disponibilità consente una disponibilità più elevata per il tempo di attività del server dell'API Kubernetes.  

I clienti possono comunque creare cluster gratuiti senza limiti con un obiettivo del livello di servizio pari al 99,5% e optare per il tempo di attività relativo all'obiettivo del livello di servizio o al contratto di servizio in base alle esigenze.

> [!Important]
> Per i cluster con blocco in uscita, vedere la pagina relativa a come [limitare il traffico in uscita](limit-egress-traffic.md) per aprire le porte appropriate.

## <a name="region-availability"></a>Aree di disponibilità

* Il contratto di servizio relativo al tempo di attività è disponibile nelle aree pubbliche e nelle Azure per enti pubblici in cui è [supportato il servizio Servizio Web di gestione degli aggiornamenti.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)
* Il contratto di servizio relativo al tempo di attività è [disponibile per i cluster del servizio Web Diaks privati][private-clusters] in tutte le aree pubbliche in cui è supportato il servizio.

## <a name="sla-terms-and-conditions"></a>Termini e condizioni del contratto di servizio

Il contratto di servizio relativo al tempo di attività è una funzionalità a pagamento abilitata per cluster. Il prezzo del contratto di servizio relativo al tempo di attività è determinato dal numero di cluster discreti e non dalle dimensioni dei singoli cluster. Per altre informazioni, consultare i [dettagli sui prezzi del contratto di servizio relativo al tempo di attività](https://azure.microsoft.com/pricing/details/kubernetes-service/).

## <a name="before-you-begin"></a>Prima di iniziare

* Installare [l'interfaccia della riga di](/cli/azure/install-azure-cli) comando di Azure versione 2.8.0 o successiva

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Creazione di un nuovo cluster con contratto di servizio relativo al tempo di attività

Per creare un nuovo cluster con il contratto di servizio relativo al tempo di attività, usare l'interfaccia della riga di comando di Azure.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Usare il comando [`az aks create`][az-aks-create] per creare un cluster del servizio Web Diaks. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Il completamento di questa operazione richiede alcuni minuti:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Il frammento di codice JSON seguente mostra il livello a pagamento per lo SKU, che indica che il cluster è abilitato con il contratto di servizio relativo al tempo di attività:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modificare un cluster esistente per usare il contratto di servizio relativo al tempo di attività

Facoltativamente, è possibile aggiornare i cluster esistenti per usare il contratto di servizio relativo al tempo di attività.

Se è stato creato un cluster del servizio Servizio Web Di seguito sono stati creati i passaggi precedenti, eliminare il gruppo di risorse:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Creare un nuovo gruppo di risorse:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Creare un nuovo cluster e non usare il contratto di servizio relativo al tempo di attività:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Usare il [`az aks update`][az-aks-update] comando per aggiornare il cluster esistente:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Il frammento di codice JSON seguente mostra il livello a pagamento per lo SKU, che indica che il cluster è abilitato con il contratto di servizio relativo al tempo di attività:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Rifiutare esplicitamente il contratto di servizio relativo al tempo di attività

È possibile aggiornare il cluster per passare al livello gratuito e rifiutare esplicitamente il contratto di servizio relativo al tempo di attività.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare addebiti, pulire tutte le risorse create. Per eliminare il cluster, usare il comando per eliminare il gruppo di risorse del servizio [`az group delete`][az-group-delete] Web AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster del servizio Azure Kubernetes.

Configurare il cluster per [limitare il traffico in uscita](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
