---
title: Procedure consigliate per le funzionalità dell'utilità di pianificazione
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per l'uso delle funzionalità avanzate dell'utilità di pianificazione, come taint e tolleranze, selettori di nodo e affinità oppure affinità tra pod e anti-affinità, nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831583"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione nel servizio Azure Kubernetes (AKS)

Quando si gestiscono i cluster nel servizio Azure Kubernetes (AKS), spesso è necessario isolare i team e i carichi di lavoro. Le funzionalità avanzate fornite dall'utilità di pianificazione di Kubernetes consentono di controllare:
* Quali pod possono essere pianificati in determinati nodi.
* Come distribuire in modo appropriato le applicazioni multi pod nel cluster. 

Questo articolo sulle procedure consigliate è incentrato sulle funzionalità di pianificazione avanzate di Kubernetes per gli operatori del cluster. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Usare taints e tolleranze per limitare i pod che possono essere pianificati nei nodi.
> * Assegnare la preferenza ai pod per l'esecuzione in determinati nodi con selettori di nodo o affinità dei nodi.
> * Suddividere o raggruppare i pod con affinità tra pod o anti-affinità.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornire nodi dedicati tramite taint e tolleranze

> **Indicazioni sulle procedure consigliate:** 
>
> Limitare l'accesso per le applicazioni a elevato utilizzo di risorse, ad esempio i controller di ingresso, a nodi specifici. Mantenere le risorse dei nodi disponibili per i carichi di lavoro che le richiedono e non consentire la pianificazione di altri carichi di lavoro sui nodi.

Quando si crea il cluster servizio Azure Kubernetes, è possibile distribuire i nodi con supporto GPU o un numero elevato di potenti CPU. È possibile usare questi nodi per carichi di lavoro di elaborazione dati di grandi dimensioni, ad esempio Machine Learning (ML) o Intelligenza artificiale (AI). 

Poiché l'hardware delle risorse del nodo è in genere costoso da distribuire, limitare i carichi di lavoro che possono essere pianificati in questi nodi. È invece necessario dedicare alcuni nodi del cluster per eseguire i servizi in ingresso e impedire altri carichi di lavoro.

Questo supporto per nodi diversi viene fornito tramite più pool di nodi. Un cluster del servizio AKS fornisce uno o più pool di nodi.

L'utilità di pianificazione di Kubernetes usa taints e tolleranze per limitare i carichi di lavoro che possono essere eseguiti nei nodi.

* Applicare un **taint** a un nodo per indicare che su di essi possono essere pianificati solo pod specifici.
* Applicare quindi una **tolleranza** a un pod, consentendo loro di *tollerare* il taint di un nodo.

Quando si distribuisce un pod in un cluster del servizio Kubernetes, Kubernetes pianifica solo i pod nei nodi il cui taint è allineato alla tolleranza. Si supponga, ad esempio, di aver aggiunto un pool di nodi nel cluster del servizio AKS per i nodi con supporto GPU. Si definisce il nome, ad esempio *gpu*, quindi un valore per la pianificazione. L'impostazione di questo valore su *NoSchedule* impedisce all'utilità di pianificazione kubernetes di pianificare pod con tolleranza non definita nel nodo.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Con un taint applicato ai nodi nel pool di nodi, si definirà una tolleranza nella specifica del pod che consente la pianificazione sui nodi. L'esempio seguente definisce `sku: gpu` e `effect: NoSchedule` per tollerare il taint applicato al pool di nodi nel passaggio precedente:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Quando questo pod viene distribuito usando , Kubernetes può pianificare correttamente il pod nei `kubectl apply -f gpu-toleration.yaml` nodi con il taint applicato. Questo isolamento logico consente di controllare l'accesso alle risorse all'interno di un cluster.

Quando si applicano i taint, contattare gli sviluppatori e i proprietari delle applicazioni per consentire loro di definire le tolleranze richieste nelle proprie distribuzioni.

Per altre informazioni su come usare più pool di nodi nel servizio Web Diaks, vedere Creare e gestire più pool di nodi per un cluster nel servizio Web [Diaks.][use-multiple-node-pools]

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento deitaints e delle tolleranze nel server del server di controllo di accesso

Quando si aggiorna un pool di nodi nel servizio AKS, itaints e tolerations seguono un modello di impostazione applicato ai nuovi nodi:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Cluster predefiniti che usano set di scalabilità di macchine virtuali
È possibile eseguire [il taint di un pool][taint-node-pool] di nodi dall'API del servizio Web Diaks per fare in modo che i nodi appena scalati ricevano i nodi specificati dall'API.

Supponiamo:
1. Si inizia con un cluster a due nodi: *node1* e *node2*. 
1. Aggiornare il pool di nodi.
1. Vengono creati due nodi aggiuntivi: *node3* e *node4.* 
1. Itaints vengono passati rispettivamente.
1. I nodi *originali node1* *e node2* vengono eliminati.

#### <a name="clusters-without-vm-scale-set-support"></a>Cluster senza supporto del set di scalabilità di macchine virtuali

Anche in questo caso, si supponga:
1. Si dispone di un cluster a due nodi: *node1* e *node2.* 
1. Si esegue quindi l'aggiornamento del pool di nodi.
1. Viene creato un nodo aggiuntivo: *node3*.
1. I taints di *node1* vengono applicati a *node3.*
1. *node1* viene eliminato.
1. Viene creato *un nuovo nodo 1* da sostituire con *node1 originale.*
1. I *nodi node2* vengono applicati al nuovo *node1.* 
1. *node2* viene eliminato.

In *sostanza node1* diventa *node3* e *node2* diventa il nuovo *node1.*

Quando si ridimensiona un pool di nodi in AKS, le taints e le tolleranze non vengono trasportate in base alla progettazione.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Controllare la pianificazione dei pod tramite selettori di nodo e affinità

> **Indicazioni sulle procedure consigliate** 
> 
> Controllare la pianificazione dei pod nei nodi usando selettori di nodo, affinità dei nodi o affinità tra pod. Queste impostazioni consentono all'utilità di pianificazione di Kubernetes di isolare in modo logico i carichi di lavoro, ad esempio in base all'hardware nel nodo.

Le taints e le tolleranze isolano logicamente le risorse con un cut-off rigido. Se il pod non tollera il taint di un nodo, non viene pianificato nel nodo.

In alternativa, è possibile usare selettori di nodo. Ad esempio, si etichettano i nodi per indicare l'archiviazione SSD collegata localmente o una grande quantità di memoria e quindi si definisce nella specifica del pod un selettore di nodo. Kubernetes pianifica tali pod in un nodo corrispondente.

A differenza delle tolleranze, i pod senza un selettore di nodo corrispondente possono comunque essere pianificati nei nodi etichettati. Questo comportamento consente l'utilizzo delle risorse inutilizzate nei nodi, ma assegna la priorità ai pod che definiscono il selettore di nodo corrispondente.

Verrà ora preso in esame un esempio di nodi con una quantità elevata di memoria. Questi nodi assegnano la priorità ai pod che richiedono una quantità elevata di memoria. Per garantire che le risorse non siano inattive, consentono anche l'esecuzione di altri pod. Il comando di esempio seguente aggiunge un pool di nodi con etichetta *hardware=highmem* a *myAKSCluster* in *myResourceGroup*. Tutti i nodi nel pool di nodi avranno questa etichetta.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

Una specifica del pod aggiunge quindi la proprietà `nodeSelector` per definire un selettore di nodo corrispondente all'etichetta impostata su un nodo:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Quando si usano queste opzioni dell'utilità di pianificazione, contattare gli sviluppatori e i proprietari delle applicazioni per consentire loro di definire correttamente le specifiche dei pod.

Per altre informazioni sull'uso dei selettori di nodo, vedere [Assigning Pods to Nodes][k8s-node-selector] (Assegnazione di pod ai nodi).

### <a name="node-affinity"></a>Affinità tra nodi

Un selettore di nodo è una soluzione di base per l'assegnazione di pod a un determinato nodo. *L'affinità* dei nodi offre maggiore flessibilità, consentendo di definire cosa accade se il pod non può essere abbinato a un nodo. È possibile: 
* *Richiedere* che l'utilità di pianificazione di Kubernetes corrisponda a un pod con un host con etichetta. Oppure
* *Preferisce* una corrispondenza, ma consente al pod di essere pianificato in un host diverso se non è disponibile alcuna corrispondenza.

Nell'esempio seguente l'affinità tra nodi viene impostata su *requiredDuringSchedulingIgnoredDuringExecution*. Questa affinità richiede che l'utilità di pianificazione di Kubernetes usi un nodo con un'etichetta corrispondente. Se non è disponibile alcun nodo, il pod rimane in attesa che la pianificazione continui. Per consentire la pianificazione del pod in un nodo diverso, è invece possibile impostare il valore su ***preferred** DuringSchedulingIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

La *parte IgnoredDuringExecution* dell'impostazione indica che il pod non deve essere rimosso dal nodo se le etichette del nodo cambiano. L'utilità di pianificazione di Kubernetes usa solo le etichette dei nodi aggiornate per la pianificazione dei nuovi pod, e non i pod già pianificati sui nodi.

Per altre informazioni, vedere la sezione relativa ad [affinità e anti-affinità][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affinità tra pod e anti-affinità

Un ultimo approccio per consentire all'utilità di pianificazione di Kubernetes di isolare in modo logico i carichi di lavoro consiste nell'usare l'affinità tra pod o l'anti-affinità. Queste impostazioni definiscono che i pod *non* devono o *devono* essere pianificati in un nodo con un pod corrispondente esistente. Per impostazione predefinita, l'utilità di pianificazione di Kubernetes tenta di pianificare più pod in un set di repliche tra i nodi. È possibile definire più regole specifiche in base a questo comportamento.

Ad esempio, si dispone di un'applicazione Web che usa anche un cache di Azure per Redis. 
1. Si usano regole di anti-affinità dei pod per richiedere che l'utilità di pianificazione di Kubernetes distribuisca le repliche tra i nodi. 
1. Le regole di affinità vengono usate per assicurarsi che ogni componente dell'app Web sia pianificato nello stesso host di una cache corrispondente. 

La distribuzione dei pod tra i nodi è simile alla seguente:

| **Nodo 1** | **Nodo 2** | **Nodo 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

L'affinità tra pod e l'anti-affinità offrono una distribuzione più complessa rispetto ai selettori di nodo o all'affinità dei nodi. Con la distribuzione si isolano logicamente le risorse e si controlla il modo in cui Kubernetes pianifica i pod nei nodi. 

Per un esempio completo di questa applicazione Web con cache di Azure per Redis, vedere Condivisione del percorso dei [pod nello stesso nodo.][k8s-pod-affinity]

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato le funzionalità avanzate dell'utilità di pianificazione di Kubernetes. Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Isolamento cluster e multi-tenant][aks-best-practices-scheduler]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Autenticazione e autorizzazione][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
