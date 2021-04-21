---
title: Aggiungere un pool di nodi spot a un cluster del servizio Azure Kubernetes (AKS)
description: Informazioni su come aggiungere un pool di nodi spot a un cluster servizio Azure Kubernetes (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: f46a421ae2ad1a4d9c590c7e0b47784760ebcb9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782802"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Aggiungere un pool di nodi spot a un cluster del servizio Azure Kubernetes (AKS)

Un pool di nodi spot è un pool di nodi supportato da un [set di scalabilità di macchine virtuali spot.][vmss-spot] L'uso di macchine virtuali spot per i nodi con il cluster del servizio AzureKs consente di sfruttare la capacità inutilizzata in Azure con un notevole risparmio sui costi. La quantità di capacità inutilizzata disponibile varia in base a molti fattori, tra cui le dimensioni del nodo, l'area e l'ora del giorno.

Quando si distribuisce un pool di nodi spot, Azure alloca i nodi spot se è disponibile capacità. Ma non esiste alcun contratto di servizio per i nodi spot. Un set di scalabilità spot che supporti il pool di nodi spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata. In qualsiasi momento in cui Azure necessita della capacità, l'infrastruttura di Azure eviterà i nodi spot.

I nodi spot sono ottimi per i carichi di lavoro in grado di gestire interruzioni, terminazioni anticipate o sgomberi. Ad esempio, carichi di lavoro come processi di elaborazione batch, ambienti di sviluppo e test e carichi di lavoro di calcolo di grandi dimensioni possono essere candidati per essere pianificati in un pool di nodi spot.

In questo articolo viene aggiunto un pool di nodi spot secondari a un cluster servizio Azure Kubernetes (AKS) esistente.

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Quando si crea un cluster per usare un pool di nodi spot, tale cluster deve usare anche i set di scalabilità di macchine virtuali per i pool di nodi e il servizio di bilanciamento del carico dello SKU *Standard.* È anche necessario aggiungere un pool di nodi aggiuntivo dopo aver creato il cluster per usare un pool di nodi spot. L'aggiunta di un pool di nodi aggiuntivo verrà trattata in un passaggio successivo.

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.14 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e si gestiscono cluster del servizio Web Disassociato di Microsoft Con un pool di nodi spot:

* Un pool di nodi spot non può essere il pool di nodi predefinito del cluster. Un pool di nodi spot può essere usato solo per un pool secondario.
* Non è possibile aggiornare un pool di nodi spot perché i pool di nodi spot non possono garantire il cordon e lo svuotamento. È necessario sostituire il pool di nodi spot esistente con uno nuovo per eseguire operazioni come l'aggiornamento della versione di Kubernetes. Per sostituire un pool di nodi spot, creare un nuovo pool di nodi spot con una versione diversa di Kubernetes, attendere che lo stato sia *Pronto* e quindi rimuovere il pool di nodi precedente.
* Il piano di controllo e i pool di nodi non possono essere aggiornati contemporaneamente. È necessario aggiornarli separatamente o rimuovere il pool di nodi spot per aggiornare contemporaneamente il piano di controllo e i pool di nodi rimanenti.
* Un pool di nodi spot deve usare set di scalabilità di macchine virtuali.
* Non è possibile modificare ScaleSetPriority o SpotMaxPrice dopo la creazione.
* Quando si imposta SpotMaxPrice, il valore deve essere -1 o un valore positivo con un massimo di cinque cifre decimali.
* Un pool di nodi spot avrà l'etichetta kubernetes.azure.com/scalesetpriority:spot *,* il kubernetes.azure.com/scalesetpriority=spot:NoSchedule *e* i pod di sistema avranno anti-affinità.
* È necessario aggiungere una tolleranza [corrispondente per][spot-toleration] pianificare i carichi di lavoro in un pool di nodi spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Aggiungere un pool di nodi spot a un cluster del servizio Azure Kubernetes

È necessario aggiungere un pool di nodi spot a un cluster esistente con più pool di nodi abilitati. Per altre informazioni sulla creazione di un cluster del servizio Web Disassodato con più pool di nodi, vedere [qui][use-multiple-node-pools].

Creare un pool di nodi usando [az aks nodepool add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Per impostazione predefinita, quando si  crea un cluster con più pool di nodi, si crea un pool di nodi con priorità *Normale* nel cluster del servizio AKS. Il comando precedente aggiunge un pool di nodi ausiliari a un cluster del servizio Web del servizio Web esistente con *priorità* *Spot*. La *priorità di* Spot *rende* il pool di nodi un pool di nodi spot. Il *parametro eviction-policy* è impostato su *Delete* nell'esempio precedente, ovvero il valore predefinito. Quando si imposta il [criterio di][eviction-policy] eliminazione su *Elimina*, i nodi nel set di scalabilità sottostante del pool di nodi vengono eliminati quando vengono eliminati. È anche possibile impostare i criteri di sfratto su *Dealloca*. Quando si impostano i criteri di sfratto su *Dealloca*, i nodi nel set di scalabilità sottostante vengono impostati sullo stato arrestato deallocato al momento dello sfratto. I nodi nello stato arrestato-deallocato vengono conteggiati rispetto alla quota di calcolo e possono causare problemi con il ridimensionamento o l'aggiornamento del cluster. I *valori priority* e *eviction-policy* possono essere impostati solo durante la creazione del pool di nodi. Questi valori non possono essere aggiornati in un secondo momento.

Il comando abilita anche il [ridimensionamento automatico del cluster,][cluster-autoscaler]che è consigliabile usare con i pool di nodi spot. In base ai carichi di lavoro in esecuzione nel cluster, il ridimensionamento automatico del cluster viene ridimensionato e ridotto il numero di nodi nel pool di nodi. Per i pool di nodi spot, il ridimensionamento automatico del cluster scalerà il numero di nodi dopo un'operazione di svimento se sono ancora necessari nodi aggiuntivi. Se si modifica il numero massimo di nodi che un pool di nodi può avere, è anche necessario modificare il valore associato al ridimensionamento automatico `maxCount` del cluster. Se non si usa un ridimensionamento automatico del cluster, al momento della eliminazione il pool spot verrà ridotto a zero e sarà necessaria un'operazione manuale per ricevere eventuali nodi spot aggiuntivi.

> [!Important]
> Pianificare solo i carichi di lavoro nei pool di nodi spot in grado di gestire le interruzioni, ad esempio i processi di elaborazione batch e gli ambienti di test. È consigliabile configurare itaints e [le tolleranze][taints-tolerations] nel pool di nodi spot per assicurarsi che in un pool di nodi spot siano pianificati solo i carichi di lavoro in grado di gestire le entità nodo. Ad esempio, il comando precedente ny default aggiunge un taint di *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* in modo che solo i pod con una tolleranza corrispondente siano pianificati in questo nodo.

## <a name="verify-the-spot-node-pool"></a>Verificare il pool di nodi spot

Per verificare che il pool di nodi sia stato aggiunto come pool di nodi spot:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Verificare *che scaleSetPriority sia* *Spot.*

Per pianificare l'esecuzione di un pod in un nodo spot, aggiungere una tolleranza corrispondente al taint applicato al nodo spot. L'esempio seguente illustra una parte di un file yaml che definisce una tolleranza che corrisponde *a* un taint kubernetes.azure.com/scalesetpriority=spot:NoSchedule usato nel passaggio precedente.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Quando viene distribuito un pod con questa tolleranza, Kubernetes può pianificare correttamente il pod nei nodi con il taint applicato.

## <a name="max-price-for-a-spot-pool"></a>Prezzo massimo per un pool spot
[I prezzi per le istanze spot sono variabili,][pricing-spot]in base all'area e alla SKU. Per altre informazioni, vedere prezzi per [Linux][pricing-linux] e [Windows.][pricing-windows]

Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando al massimo 5 cifre decimali. Ad esempio, il valore *0,98765* sarebbe un prezzo massimo di $ 0,98765 USD all'ora. Se si imposta il prezzo massimo su *-1,* l'istanza non verrà sfrattata in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per Spot o il prezzo per un'istanza standard, a seconda di quale sia minore, purché siano disponibili capacità e quota.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come aggiungere un pool di nodi spot a un cluster del servizio AKS. Per altre informazioni su come controllare i pod tra i pool di nodi, vedere Procedure consigliate per le funzionalità avanzate dell'utilità di [pianificazione nel servizio Web Del servizio Contenitore.][operator-best-practices-advanced-scheduler]

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md