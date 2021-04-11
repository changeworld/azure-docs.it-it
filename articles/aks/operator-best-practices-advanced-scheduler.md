---
title: Procedure consigliate per le funzionalità dell'utilità di pianificazione
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per l'uso delle funzionalità avanzate dell'utilità di pianificazione, come taint e tolleranze, selettori di nodo e affinità oppure affinità tra pod e anti-affinità, nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 27b32d7d10b691ed806e4d7aa31a095630d2bfc9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103624"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione nel servizio Azure Kubernetes (AKS)

Quando si gestiscono i cluster nel servizio Azure Kubernetes (AKS), spesso è necessario isolare i team e i carichi di lavoro. Le funzionalità avanzate fornite dall'utilità di pianificazione di Kubernetes consentono di controllare:
* Quali Pod è possibile pianificare in determinati nodi.
* Il modo in cui le applicazioni MultiPod possono essere distribuite in modo appropriato nel cluster. 

Questo articolo sulle procedure consigliate è incentrato sulle funzionalità di pianificazione avanzate di Kubernetes per gli operatori del cluster. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Usare macchie e tolleranze per limitare i pod che possono essere pianificati nei nodi.
> * Fornire la preferenza ai pod per l'esecuzione in determinati nodi con selettori di nodo o affinità di nodi.
> * Dividere o raggruppare i baccelli con affinità tra i pod o anti-affinità.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornire nodi dedicati tramite taint e tolleranze

> **Indicazioni sulle procedure consigliate:** 
>
> Limitare l'accesso per le applicazioni con utilizzo intensivo di risorse, ad esempio i controller di ingresso, a nodi specifici. Mantenere le risorse dei nodi disponibili per i carichi di lavoro che le richiedono e non consentire la pianificazione di altri carichi di lavoro sui nodi.

Quando si crea il cluster servizio Azure Kubernetes, è possibile distribuire i nodi con supporto GPU o un numero elevato di potenti CPU. È possibile usare questi nodi per carichi di lavoro di elaborazione dati di grandi dimensioni, ad esempio Machine Learning (ML) o intelligenza artificiale (AI). 

Poiché l'hardware delle risorse del nodo è in genere costoso da distribuire, limitare i carichi di lavoro che possono essere pianificati in questi nodi. Al contrario, si dedicano alcuni nodi del cluster per l'esecuzione dei servizi in ingresso e per impedire altri carichi di lavoro.

Questo supporto per nodi diversi viene fornito usando più pool di nodi. Un cluster AKS fornisce uno o più pool di nodi.

L'utilità di pianificazione Kubernetes usa le macchie e le tolleranze per limitare i carichi di lavoro che possono essere eseguiti nei nodi.

* Applicare una **macchia** a un nodo per indicare che possono essere pianificati solo pod specifici.
* Applicare quindi una **tolleranza** a un pod, consentendo loro di *tollerare* i guasti di un nodo.

Quando si distribuisce un pod in un cluster AKS, Kubernetes Pianifica solo i pod nei nodi il cui Taint è allineato alla tolleranza. Si supponga, ad esempio, di avere un pool di nodi nel cluster AKS per i nodi con supporto GPU. Si definisce il nome, ad esempio *gpu*, quindi un valore per la pianificazione. L'impostazione di questo valore su *NoSchedule* limita l'utilità di pianificazione di Kubernetes alla pianificazione dei pod con tolleranza non definita nel nodo.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Con una macchia applicata ai nodi, si definisce una tolleranza nella specifica pod che consente la pianificazione nei nodi. L'esempio seguente definisce `sku: gpu` e `effect: NoSchedule` per tollerare il taint applicato al nodo nel passaggio precedente:

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

Quando questo pod viene distribuito usando `kubectl apply -f gpu-toleration.yaml` , Kubernetes può pianificare correttamente il Pod nei nodi con il Taint applicato. Questo isolamento logico consente di controllare l'accesso alle risorse all'interno di un cluster.

Quando si applicano i taint, contattare gli sviluppatori e i proprietari delle applicazioni per consentire loro di definire le tolleranze richieste nelle proprie distribuzioni.

Per altre informazioni su come usare più pool di nodi in AKS, vedere [creare e gestire pool di nodi multipli per un cluster in AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento di macchie e tolleranze in AKS

Quando si esegue l'aggiornamento di un pool di nodi in AKS, i guasti e le tollerazioni seguono un modello set quando vengono applicati ai nuovi nodi:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Cluster predefiniti che usano i set di scalabilità di macchine virtuali
È possibile [intaccare un pool di nodi][taint-node-pool] dall'API AKS in modo che i nodi appena scalati in uscita ricevano le macchie del nodo specificate dall'API.

Supponiamo:
1. Si inizia con un cluster a due nodi: *node1* e *node2*. 
1. Si aggiorna il pool di nodi.
1. Vengono creati due nodi aggiuntivi: *Nodo3* e *Nodo4*. 
1. I guasti vengono passati rispettivamente.
1. I *node1* e *node2* originali vengono eliminati.

#### <a name="clusters-without-vm-scale-set-support"></a>Cluster senza supporto del set di scalabilità di macchine virtuali

Anche in questo caso, supponiamo che:
1. Si dispone di un cluster a due nodi: *node1* e *node2*. 
1. Si aggiorna quindi il pool di nodi.
1. Viene creato un nodo aggiuntivo: *Nodo3*.
1. Le macchie da *node1* vengono applicate a *Nodo3*.
1. *node1* è stato eliminato.
1. Viene creato un nuovo *node1* per sostituire il *node1* originale.
1. Le macchie *node2* vengono applicate al nuovo *node1*. 
1. *node2* è stato eliminato.

In sostanza *node1* diventa *Nodo3* e *node2* diventa il nuovo *node1*.

Quando si ridimensiona un pool di nodi in AKS, i guasti e le tollerazioni non vengono riportate in base alla progettazione.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Controllare la pianificazione dei pod tramite selettori di nodo e affinità

> **Indicazioni sulle procedure consigliate** 
> 
> Controllare la pianificazione dei Pod nei nodi usando selettori di nodo, affinità di nodi o affinità tra i pod. Queste impostazioni consentono all'utilità di pianificazione di Kubernetes di isolare in modo logico i carichi di lavoro, ad esempio in base all'hardware nel nodo.

Macchie e tolleranze isolano logicamente le risorse con un taglio rigido. Se il Pod non tollera il danneggiamento di un nodo, non è pianificato nel nodo. 

In alternativa, è possibile usare i selettori di nodo. È ad esempio possibile etichettare i nodi per indicare l'archiviazione SSD collegata localmente o una grande quantità di memoria e quindi definire nella specifica Pod un selettore di nodo. Kubernetes Pianifica i pod in un nodo corrispondente. 

Diversamente dalle tolleranze, i pod senza un selettore di nodo corrispondente possono comunque essere pianificati in nodi con etichetta. Questo comportamento consente di utilizzare le risorse inutilizzate sui nodi, ma classifica in ordine di priorità i pod che definiscono il selettore del nodo corrispondente.

Verrà ora preso in esame un esempio di nodi con una quantità elevata di memoria. Questi nodi assegnano priorità ai pod che richiedono una quantità elevata di memoria. Per assicurarsi che le risorse non siano inattive, consentono anche l'esecuzione di altri pod.

```console
kubectl label node aks-nodepool1 hardware=highmem
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

Un selettore di nodo è una soluzione di base per l'assegnazione di pod a un determinato nodo. L' *affinità dei nodi* offre maggiore flessibilità, consentendo di definire cosa accade se non è possibile trovare una corrispondenza per il pod con un nodo. È possibile: 
* *Richiedere* che l'utilità di pianificazione di Kubernetes corrisponda a un pod con un host con etichetta. Oppure
* *Preferire* una corrispondenza, ma consentire la pianificazione del pod in un host diverso se non è disponibile alcuna corrispondenza.

Nell'esempio seguente l'affinità tra nodi viene impostata su *requiredDuringSchedulingIgnoredDuringExecution*. Questa affinità richiede che l'utilità di pianificazione di Kubernetes usi un nodo con un'etichetta corrispondente. Se non è disponibile alcun nodo, il pod rimane in attesa che la pianificazione continui. Per consentire la pianificazione del pod in un nodo diverso, è possibile impostare il valore su * DuringSchedulingIgnoreDuringExecution **preferito***:

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

La parte *IgnoredDuringExecution* dell'impostazione indica che il Pod non deve essere rimosso dal nodo se le etichette del nodo cambiano. L'utilità di pianificazione di Kubernetes usa solo le etichette dei nodi aggiornate per la pianificazione dei nuovi pod, e non i pod già pianificati sui nodi.

Per altre informazioni, vedere la sezione relativa ad [affinità e anti-affinità][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affinità tra pod e anti-affinità

Un ultimo approccio per consentire all'utilità di pianificazione di Kubernetes di isolare in modo logico i carichi di lavoro consiste nell'usare l'affinità tra pod o l'anti-affinità. Queste impostazioni definiscono che i pod *non* devono o *devono* essere pianificati in un nodo con un pod corrispondente esistente. Per impostazione predefinita, l'utilità di pianificazione di Kubernetes tenta di pianificare più pod in un set di repliche tra i nodi. È possibile definire più regole specifiche in base a questo comportamento.

Ad esempio, si dispone di un'applicazione Web che usa anche una cache di Azure per Redis. 
1. Usare le regole di anti-affinità pod per richiedere che l'utilità di pianificazione Kubernetes distribuisca le repliche tra i nodi. 
1. Usare le regole di affinità per verificare che ogni componente dell'app Web sia pianificato nello stesso host di una cache corrispondente. 

La distribuzione dei pod tra i nodi è simile alla seguente:

| **Nodo 1** | **Nodo 2** | **Nodo 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

L'affinità tra i pod e l'anti-affinità forniscono una distribuzione più complessa rispetto ai selettori di nodo o all'affinità del nodo. Con la distribuzione, si isola logicamente le risorse e si controlla il modo in cui Kubernetes Pianifica i pod nei nodi. 

Per un esempio completo di questa applicazione Web con cache di Azure per l'esempio Redis, vedere [la pagina relativa alla condivisione percorso di Pod nello stesso nodo][k8s-pod-affinity].

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
