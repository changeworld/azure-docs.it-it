---
title: Usare più pool di nodi in servizio Azure Kubernetes (AKS)
description: Informazioni su come creare e gestire più pool di nodi per un cluster in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: b7b54ccf6662e172ebfe95a84189df5e8e6e990f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832249"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Creare e gestire più pool di nodi per un cluster nel servizio Azure Kubernetes (AKS)

In servizio Azure Kubernetes (AKS) i nodi della stessa configurazione vengono raggruppati in pool *di nodi*. Questi pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. Il numero iniziale di nodi e le relative dimensioni (SKU) vengono definiti quando si crea un cluster del servizio Web Diaks, che crea un [pool di nodi di sistema.][use-system-pool] Per supportare applicazioni con esigenze di calcolo o archiviazione diverse, è possibile creare pool *di nodi utente aggiuntivi.* I pool di nodi di sistema servono allo scopo principale di ospitare pod di sistema critici, ad esempio CoreDNS e tunnelfront. I pool di nodi utente servono allo scopo principale di ospitare i pod dell'applicazione. Tuttavia, i pod dell'applicazione possono essere pianificati nei pool di nodi di sistema se si vuole avere un solo pool nel cluster del servizio Contenitore di Microsoft. I pool di nodi utente sono la posizione in cui inserire i pod specifici dell'applicazione. Ad esempio, usare questi pool di nodi utente aggiuntivi per fornire GPU per applicazioni a elevato utilizzo di calcolo o per accedere all'archiviazione SSD ad alte prestazioni.

> [!NOTE]
> Questa funzionalità consente un maggiore controllo su come creare e gestire più pool di nodi. Di conseguenza, sono necessari comandi separati per la creazione, l'aggiornamento e l'eliminazione. In precedenza le operazioni del cluster venivano eseguite tramite o usavano l'API managedCluster e erano l'unica opzione per modificare il piano di controllo e `az aks create` un pool a nodo `az aks update` singolo. Questa funzionalità espone un set di operazioni separato per i pool di agenti tramite l'API agentPool e richiede l'uso del set di comandi per eseguire operazioni `az aks nodepool` in un singolo pool di nodi.

Questo articolo illustra come creare e gestire più pool di nodi in un cluster del servizio Web AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano pool di nodi multipli, si applicano le limitazioni seguenti:

* Vedere [Quote, restrizioni relative alle dimensioni delle macchine virtuali e disponibilità dell'area in servizio Azure Kubernetes (AKS).][quotas-skus-regions]
* È possibile eliminare i pool di nodi di sistema, a condizione che sia disponibile un altro pool di nodi di sistema da usare nel cluster del servizio Web Diaks.
* I pool di sistema devono contenere almeno un nodo e i pool di nodi utente possono contenere zero o più nodi.
* Il cluster del servizio AzureKs deve usare il servizio di bilanciamento del carico SKU Standard per usare più pool di nodi. La funzionalità non è supportata con i servizi di bilanciamento del carico SKU basici.
* Il cluster del servizio AKS deve usare set di scalabilità di macchine virtuali per i nodi.
* Il nome di un pool di nodi può contenere solo caratteri alfanumerici minuscoli e deve iniziare con una lettera minuscola. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.
* Tutti i pool di nodi devono risiedere nella stessa rete virtuale.
* Quando si creano più pool di nodi in fase di creazione del cluster, tutte le versioni di Kubernetes usate dai pool di nodi devono corrispondere al set di versioni per il piano di controllo. Questa operazione può essere aggiornata dopo il provisioning del cluster tramite operazioni per ogni pool di nodi.

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

> [!Important]
> Se si esegue un singolo pool di nodi di sistema per il cluster del servizio Web Disatteso in un ambiente di produzione, è consigliabile usare almeno tre nodi per il pool di nodi.

Per iniziare, creare un cluster del servizio Web Del servizio Web con un pool a nodo singolo. Nell'esempio seguente viene utilizzato [il comando az group create][az-group-create] per creare un gruppo di risorse denominato *myResourceGroup* *nell'area eastus.* Viene quindi creato un cluster del servizio Web Diaks denominato *myAKSCluster* usando il [comando az aks create.][az-aks-create]

> [!NOTE]
> Lo *SKU del* servizio di bilanciamento del carico Basic **non è supportato** quando si usano più pool di nodi. Per impostazione predefinita, i cluster del servizio AzureKs vengono creati con lo SKU del servizio di bilanciamento del carico *Standard* dall'interfaccia della riga di comando di Azure e portale di Azure.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

La creazione del cluster richiede alcuni minuti.

> [!NOTE]
> Per garantire che il cluster funzioni in modo affidabile, è necessario eseguire almeno 2 (due) nodi nel pool di nodi predefinito, in quanto i servizi di sistema essenziali sono in esecuzione in questo pool di nodi.

Quando il cluster è pronto, usare il [comando az aks get-credentials][az-aks-get-credentials] per ottenere le credenziali del cluster da usare con `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Aggiungere un pool di nodi

Il cluster creato nel passaggio precedente ha un pool a nodo singolo. Aggiungere un secondo pool di nodi usando il [comando az aks nodepool add.][az-aks-nodepool-add] L'esempio seguente crea un pool di nodi *denominato mynodepool* che esegue *3* nodi:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Il nome di un pool di nodi deve iniziare con una lettera minuscola e può contenere solo caratteri alfanumerici. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.

Per visualizzare lo stato dei pool di nodi, usare il [comando az aks node pool list][az-aks-nodepool-list] e specificare il gruppo di risorse e il nome del cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

L'output di esempio seguente mostra *che mynodepool* è stato creato correttamente con tre nodi nel pool di nodi. Quando nel passaggio precedente è stato creato il cluster del servizio Web Diaks, è stato creato un pool di nodi *predefinito* con un numero di nodi *di 2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Se non *viene specificato alcun vmSize* quando si  aggiunge un pool di nodi, le dimensioni predefinite sono Standard_D2s_v3 per i pool di nodi Windows e Standard_DS2_v2 *per* i pool di nodi Linux. Se non *si specifica OrchestratorVersion,* per impostazione predefinita viene impostata la stessa versione del piano di controllo.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Aggiungere un pool di nodi con una subnet univoca (anteprima)

Un carico di lavoro può richiedere la suddivisione dei nodi di un cluster in pool separati per l'isolamento logico. Questo isolamento può essere supportato con subnet separate dedicate a ogni pool di nodi nel cluster. Ciò può soddisfare requisiti come la presenza di uno spazio di indirizzi di rete virtuale non contiguo da suddividere tra pool di nodi.

#### <a name="limitations"></a>Limitazioni

* Tutte le subnet assegnate a pool di nodi devono appartenere alla stessa rete virtuale.
* I pod di sistema devono avere accesso a tutti i nodi/pod nel cluster per fornire funzionalità critiche, ad esempio la risoluzione DNS e il tunneling kubectl logs/exec/port-forward proxy.
* Se si espande la rete virtuale dopo aver creato il cluster, è necessario aggiornare il cluster (eseguire qualsiasi operazione clster gestita ma le operazioni del pool di nodi non vengono conteggiati) prima di aggiungere una subnet al di fuori del cidr originale. AKS restituirà un errore nell'aggiunta del pool di agenti anche se è stato originariamente consentito. Se non si sa come riconciliare il cluster, creare un ticket di supporto. 
* I criteri di rete Calico non sono supportati. 
* Criteri di rete di Azure non è supportato.
* Kube-proxy prevede un singolo cidr contiguo e lo usa per tre optmization. Vedere questo [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) e --cluster-cidr [qui per](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) informazioni dettagliate. In Azure cni la subnet del primo pool di nodi verrà data a kube-proxy. 

Per creare un pool di nodi con una subnet dedicata, passare l'ID risorsa subnet come parametro aggiuntivo durante la creazione di un pool di nodi.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Aggiornare un pool di nodi

> [!NOTE]
> Le operazioni di aggiornamento e ridimensionamento in un cluster o in un pool di nodi non possono verificarsi contemporaneamente, se viene restituito un errore. Ogni tipo di operazione deve invece essere completato nella risorsa di destinazione prima della richiesta successiva nella stessa risorsa. Per altre informazioni, vedere la guida [alla risoluzione dei problemi](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

I comandi di questa sezione illustrano come aggiornare un singolo pool di nodi specifico. La relazione tra l'aggiornamento della versione Kubernetes del piano di controllo e del pool di nodi è illustrata nella [sezione seguente.](#upgrade-a-cluster-control-plane-with-multiple-node-pools)

> [!NOTE]
> La versione dell'immagine del sistema operativo del pool di nodi è associata alla versione kubernetes del cluster. Si otterrà solo gli aggiornamenti delle immagini del sistema operativo, dopo un aggiornamento del cluster.

Poiché in questo esempio sono presenti due pool di nodi, è necessario usare [az aks nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare un pool di nodi. Per visualizzare gli aggiornamenti disponibili, usare [az aks get-upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Aggiornare *mynodepool*. Usare il [comando az aks nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare il pool di nodi, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi usando il [comando az aks node pool list.][az-aks-nodepool-list] L'esempio seguente mostra che *mynodepool* è nello *stato Upgrading* to *KUBERNETES_VERSION*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

L'aggiornamento dei nodi alla versione specificata richiede alcuni minuti.

Come procedura consigliata, è consigliabile aggiornare tutti i pool di nodi in un cluster del servizio Kubernetes alla stessa versione di Kubernetes. Il comportamento predefinito di `az aks upgrade` è l'aggiornamento di tutti i pool di nodi con il piano di controllo per ottenere questo allineamento. La possibilità di aggiornare i singoli pool di nodi consente di eseguire un aggiornamento in sequenza e pianificare i pod tra i pool di nodi per mantenere il tempo di attività dell'applicazione entro i vincoli indicati in precedenza.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Aggiornare un piano di controllo del cluster con più pool di nodi

> [!NOTE]
> Kubernetes usa lo schema di [controllo delle versioni semantico](https://semver.org/) standard. Il numero di versione è espresso come *x.y.z*, dove *x* è la versione principale, *y* è la versione secondaria e *z* è la versione della patch. Ad esempio, nella *versione 1.12.6,* 1 è la versione principale, 12 è la versione secondaria e 6 è la versione della patch. La versione Kubernetes del piano di controllo e il pool di nodi iniziale vengono impostati durante la creazione del cluster. A tutti i pool di nodi aggiuntivi è impostata la versione di Kubernetes quando vengono aggiunti al cluster. Le versioni di Kubernetes possono differire tra pool di nodi e tra un pool di nodi e il piano di controllo.

Un cluster del servizio Kubernetes dispone di due oggetti risorsa cluster a cui sono associate versioni di Kubernetes.

1. Versione kubernetes del piano di controllo del cluster.
2. Un pool di nodi con una versione di Kubernetes.

Un piano di controllo esegue il mapping a uno o più pool di nodi. Il comportamento di un'operazione di aggiornamento dipende dal comando dell'interfaccia della riga di comando di Azure usato.

L'aggiornamento di un piano di controllo del servizio Web Disatteso richiede l'uso di `az aks upgrade` . Questo comando aggiorna la versione del piano di controllo e tutti i pool di nodi nel cluster.

L'esecuzione del `az aks upgrade` comando con il flag aggiorna solo il piano di controllo del `--control-plane-only` cluster. Nessuno dei pool di nodi associati nel cluster viene modificato.

L'aggiornamento di singoli pool di nodi richiede l'uso di `az aks nodepool upgrade` . Questo comando aggiorna solo il pool di nodi di destinazione con la versione di Kubernetes specificata

### <a name="validation-rules-for-upgrades"></a>Regole di convalida per gli aggiornamenti

Gli aggiornamenti kubernetes validi per il piano di controllo e i pool di nodi di un cluster vengono convalidati dai set di regole seguenti.

* Regole per le versioni valide per l'aggiornamento dei pool di nodi:
   * La versione del pool di nodi deve avere la stessa *versione* principale del piano di controllo.
   * La versione secondaria del pool *di* nodi deve essere all'interno di due *versioni* secondarie della versione del piano di controllo.
   * La versione del pool di nodi non può essere maggiore della versione del `major.minor.patch` controllo.

* Regole per l'invio di un'operazione di aggiornamento:
   * Non è possibile eseguire il downgrade del piano di controllo o di una versione kubernetes del pool di nodi.
   * Se non viene specificata una versione kubernetes del pool di nodi, il comportamento dipende dal client in uso. La dichiarazione nei modelli Resource Manager di controllo viene impostata sulla versione esistente definita per il pool di nodi, se usata, se non ne viene impostata nessuna.
   * È possibile aggiornare o ridimensionare un piano di controllo o un pool di nodi in un determinato momento, non è possibile inviare contemporaneamente più operazioni su una singola risorsa del piano di controllo o del pool di nodi.

## <a name="scale-a-node-pool-manually"></a>Ridimensionare manualmente un pool di nodi

Quando il carico di lavoro dell'applicazione richiede modifiche, potrebbe essere necessario ridimensionare il numero di nodi in un pool di nodi. Il numero di nodi può essere ridimensionato verso l'alto o verso il basso.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Per ridimensionare il numero di nodi in un pool di nodi, usare il [comando az aks node pool scale.][az-aks-nodepool-scale] L'esempio seguente ridimensiona il numero di nodi in *mynodepool* a *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi usando il [comando az aks node pool list.][az-aks-nodepool-list] L'esempio seguente mostra che *mynodepool* è nello *stato Scaling* con un nuovo conteggio *di 5* nodi:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Il completamento dell'operazione di ridimensionamento richiede alcuni minuti.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Ridimensionare automaticamente un pool di nodi specifico abilitando il ridimensionamento automatico del cluster

Il servizio AKS offre una funzionalità separata per ridimensionare automaticamente i pool di nodi con una funzionalità denominata [scalabilità automatica del cluster.](cluster-autoscaler.md) Questa funzionalità può essere abilitata per ogni pool di nodi con conteggi di scalabilità minima e massima univoci per ogni pool di nodi. Informazioni su come usare [il ridimensionamento automatico del cluster per ogni pool di nodi.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Eliminare un pool di nodi

Se un pool non è più necessario, è possibile eliminarlo e rimuovere i nodi della macchina virtuale sottostanti. Per eliminare un pool di nodi, usare il [comando az aks node pool delete][az-aks-nodepool-delete] e specificare il nome del pool di nodi. L'esempio seguente elimina *il pool mynoodepool* creato nei passaggi precedenti:

> [!CAUTION]
> Non sono disponibili opzioni di ripristino per la perdita di dati che possono verificarsi quando si elimina un pool di nodi. Se i pod non possono essere pianificati in altri pool di nodi, tali applicazioni non sono disponibili. Assicurarsi di non eliminare un pool di nodi quando le applicazioni in uso non hanno backup dei dati o la possibilità di essere eseguite in altri pool di nodi nel cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

L'output di esempio seguente del [comando az aks node pool list][az-aks-nodepool-list] mostra che *mynodepool* si trova nello *stato Deleting:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

L'eliminazione dei nodi e del pool di nodi richiede alcuni minuti.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Specificare le dimensioni di una macchina virtuale per un pool di nodi

Negli esempi precedenti per creare un pool di nodi è stata usata una dimensione di macchina virtuale predefinita per i nodi creati nel cluster. Uno scenario più comune consiste nel creare pool di nodi con dimensioni e funzionalità diverse per le macchine virtuali. Ad esempio, è possibile creare un pool di nodi che contiene nodi con grandi quantità di CPU o memoria o un pool di nodi che fornisce supporto gpu. Nel passaggio successivo si userà [taints](#setting-nodepool-taints) e tolleranze per indicare all'utilità di pianificazione di Kubernetes come limitare l'accesso ai pod che possono essere eseguiti in questi nodi.

Nell'esempio seguente viene creato un pool di nodi basato su GPU che usa le dimensioni Standard_NC6 *macchina* virtuale. Queste macchine virtuali sono basate sulla scheda NVIDIA Tesla K80. Per informazioni sulle dimensioni delle macchine virtuali disponibili, vedere [Dimensioni per le macchine virtuali Linux in Azure.][vm-sizes]

Creare di nuovo un pool di nodi usando [il comando az aks node pool add.][az-aks-nodepool-add] Questa volta, specificare il nome *gpunodepool* e usare il parametro per specificare le Standard_NC6 `--node-vm-size` dimensioni: 

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

L'output di esempio seguente del [comando az aks node pool list][az-aks-nodepool-list] mostra che *gpunodepool* è *Creating* nodes with the *specified VmSize*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

La creazione del *pool di gpunode richiede* alcuni minuti.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Specificare un taint, un'etichetta o un tag per un pool di nodi

Quando si crea un pool di nodi, è possibile aggiungere taints, etichette o tag a tale pool di nodi. Quando si aggiunge un taint, un'etichetta o un tag, anche tutti i nodi all'interno del pool di nodi ottengono tale taint, etichetta o tag.

> [!IMPORTANT]
> L'aggiunta di taints, etichette o tag ai nodi deve essere eseguita per l'intero pool di nodi usando `az aks nodepool` . Non è consigliabile applicare taints, lablels o tag a singoli nodi in un pool `kubectl` di nodi usando .  

### <a name="setting-nodepool-taints"></a>Impostazione di nodepool taints

Per creare un pool di nodi con un taint, usare [az aks nodepool add][az-aks-nodepool-add]. Specificare il nome *taintnp* e usare il parametro per `--node-taints` specificare *sku=gpu:NoSchedule* per il taint.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Un taint può essere impostato solo per i pool di nodi durante la creazione del pool di nodi.

L'output di esempio seguente del [comando az aks nodepool list][az-aks-nodepool-list] mostra che *taintnp* è *Creating* nodes with the specified *nodeTaints*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Le informazioni sulla gestazione sono visibili in Kubernetes per la gestione delle regole di pianificazione per i nodi. L'utilità di pianificazione di Kubernetes può usare taint e tolleranze per limitare i carichi di lavoro che possono essere eseguiti sui nodi.

* Un **taint** viene applicato a un nodo per indicare che possono essere pianificati solo pod specifici.
* Una **tolleranza** viene quindi applicata a un pod per *tollerare* un taint di un nodo.

Per altre informazioni su come usare le funzionalità pianificate avanzate di Kubernetes, vedere Procedure consigliate per le funzionalità avanzate dell'utilità di [pianificazione nel servizio Kubernetes.][taints-tolerations]

Nel passaggio precedente è stato applicato *il taint sku=gpu:NoSchedule* quando è stato creato il pool di nodi. Il manifesto YAML di esempio di base seguente usa una tolleranza per consentire all'utilità di pianificazione di Kubernetes di eseguire un pod NGINX in un nodo nel pool di nodi.

Creare un file denominato `nginx-toleration.yaml` e copiarlo nell'esempio YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Pianificare il pod usando il `kubectl apply -f nginx-toleration.yaml` comando :

```console
kubectl apply -f nginx-toleration.yaml
```

La pianificazione del pod e il pull dell'immagine NGINX sono necessari alcuni secondi. Usare il [comando kubectl describe pod][kubectl-describe] per visualizzare lo stato del pod. L'output di esempio condensato seguente mostra che viene applicata la tolleranza *sku=gpu:NoSchedule.* Nella sezione events l'utilità di pianificazione ha assegnato il pod al nodo *aks-taintnp-28993262-vmss0000000:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Solo i pod a cui è applicata questa tolleranza possono essere pianificati nei nodi in *taintnp*. Qualsiasi altro pod verrà pianificato nel pool *di nodi nodepool1.* Se si creano pool di nodi aggiuntivi, è possibile usare altritaints e tolleranze per limitare i pod che possono essere pianificati in tali risorse del nodo.

### <a name="setting-nodepool-labels"></a>Impostazione delle etichette del pool di nodi

È anche possibile aggiungere etichette a un pool di nodi durante la creazione del pool di nodi. Le etichette impostate nel pool di nodi vengono aggiunte a ogni nodo nel pool di nodi. Queste [etichette sono visibili in Kubernetes per][kubernetes-labels] la gestione delle regole di pianificazione per i nodi.

Per creare un pool di nodi con un'etichetta, usare [az aks nodepool add][az-aks-nodepool-add]. Specificare il nome *labelnp e* usare il parametro per specificare `--labels` *dept=IT* e *costcenter=9999 per* le etichette.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> L'etichetta può essere impostata solo per i pool di nodi durante la creazione del pool di nodi. Le etichette devono anche essere una coppia chiave/valore e avere una [sintassi valida.][kubernetes-label-syntax]

L'output di esempio seguente del [comando az aks nodepool list][az-aks-nodepool-list] mostra che *labelnp* è *Creating* nodes with the *specified nodeLabels*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Impostazione dei tag di Azure nodepool

È possibile applicare un tag di Azure ai pool di nodi nel cluster del servizio AzureKs. I tag applicati a un pool di nodi vengono applicati a ogni nodo all'interno del pool di nodi e vengono resi persistenti tramite aggiornamenti. I tag vengono applicati anche ai nuovi nodi aggiunti a un pool di nodi durante le operazioni di scalabilità orizzontale. L'aggiunta di un tag può essere utile per attività come il rilevamento dei criteri o la stima dei costi.

I tag di Azure hanno chiavi senza distinzione tra maiuscole e minuscole per le operazioni, ad esempio quando si recupera un tag eseguendo una ricerca nella chiave. In questo caso, un tag con la chiave specificata verrà aggiornato o recuperato indipendentemente dall'utilizzo di maiuscole e minuscole. Per i valori dei tag viene fatto distinzione tra maiuscole e minuscole.

In AKS, se più tag sono impostati con chiavi identiche ma maiuscole e minuscole diverse, il tag usato è il primo in ordine alfabetico. Ad esempio, `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` i risultati sono e vengono `Key1` `val1` impostati.

Creare un pool di nodi usando [az aks nodepool add][az-aks-nodepool-add]. Specificare il nome *tagnodepool* e usare il `--tag` parametro per specificare *dept=IT* e *costcenter=9999 per* i tag.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> È anche possibile usare il `--tags` parametro quando si usa il comando [az aks nodepool update,][az-aks-nodepool-update] nonché durante la creazione del cluster. Durante la creazione del cluster, `--tags` il parametro applica il tag al pool di nodi iniziale creato con il cluster. Tutti i nomi di tag devono rispettare le limitazioni in [Usare i tag per organizzare le risorse di Azure.][tag-limitation] L'aggiornamento di un pool di nodi con il parametro aggiorna `--tags` tutti i valori di tag esistenti e aggiunge eventuali nuovi tag. Ad esempio, se il pool di nodi ha *dept=IT* e *costcenter=9999 per* i tag ed è stato aggiornato con *team=dev* e *costcenter=111* per i tag, nodepool avrebbe *dept=IT,* *costcenter=111* e *team=dev* per i tag.

L'output di esempio seguente del [comando az aks nodepool list][az-aks-nodepool-list] mostra che *tagnodepool* è *Creating* nodes with the specified *tag*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gestire i pool di nodi usando un modello Resource Manager nodo

Quando si usa un modello Azure Resource Manager per creare e gestire le risorse, è in genere possibile aggiornare le impostazioni nel modello e ridistribuire per aggiornare la risorsa. Con i pool di nodi nel servizio AKS, il profilo del pool di nodi iniziale non può essere aggiornato dopo la creazione del cluster del servizio AKS. Questo comportamento significa che non è possibile aggiornare un modello di Resource Manager esistente, apportare una modifica ai pool di nodi e ridistribuire. Al contrario, è necessario creare un modello di Resource Manager che aggiorna solo i pool di nodi per un cluster del servizio Web Diaks esistente.

Creare un modello come e `aks-agentpools.json` incollare il manifesto di esempio seguente. Questo modello di esempio configura le impostazioni seguenti:

* Aggiorna il pool di nodi *Linux* denominato *myagentpool per* eseguire tre nodi.
* Imposta i nodi nel pool di nodi per l'esecuzione di Kubernetes *versione 1.15.7.*
* Definisce le dimensioni del nodo *come Standard_DS2_v2*.

Modificare questi valori in base alle esigenze per aggiornare, aggiungere o eliminare pool di nodi in base alle esigenze:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Distribuire questo modello usando il [comando az deployment group create,][az-deployment-group-create] come illustrato nell'esempio seguente. Viene richiesto di specificare il nome e il percorso del cluster del servizio Servizio Web Di seguito:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> È possibile aggiungere un tag al pool di nodi aggiungendo la proprietà *tag* nel modello, come illustrato nell'esempio seguente.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

L'aggiornamento del cluster del servizio AKS potrebbe richiedere alcuni minuti a seconda delle impostazioni del pool di nodi e delle operazioni definite nel modello Resource Manager nodo.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Assegnare un indirizzo IP pubblico per nodo per i pool di nodi

Per la comunicazione, i nodi del servizio Web Diaks non richiedono i propri indirizzi IP pubblici. Tuttavia, gli scenari possono richiedere che i nodi in un pool di nodi ricevano i propri indirizzi IP pubblici dedicati. Uno scenario comune è quello dei carichi di lavoro di gioco, in cui una console deve stabilire una connessione diretta a una macchina virtuale cloud per ridurre al minimo gli hop. Questo scenario può essere ottenuto nel servizio Disacks usando l'indirizzo IP pubblico del nodo.

Fare clic su Crea un nuovo gruppo di risorse.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Creare un nuovo cluster del servizio Web Disassoce e collegare un indirizzo IP pubblico per i nodi. Ogni nodo nel pool riceve un indirizzo IP pubblico univoco. È possibile verificarlo esaminando le istanze del set di scalabilità di macchine virtuali.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Per i cluster del servizio AKS esistenti, è anche possibile aggiungere un nuovo pool di nodi e collegare un indirizzo IP pubblico per i nodi.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Usare un prefisso IP pubblico

L'uso di un prefisso IP pubblico offre numerosi [vantaggi.][public-ip-prefix-benefits] Il servizio AKS supporta l'uso di indirizzi da un prefisso IP pubblico esistente per i nodi passando l'ID risorsa con il flag durante la creazione di un nuovo cluster o `node-public-ip-prefix` l'aggiunta di un pool di nodi.

Creare prima di tutto un prefisso IP pubblico usando [az network public-ip prefix create:][az-public-ip-prefix-create]

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Visualizzare l'output e prendere nota di `id` per il prefisso:

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

Infine, quando si crea un nuovo cluster o si aggiunge un nuovo pool di nodi, usare il flag e `node-public-ip-prefix` passare l'ID risorsa del prefisso:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Individuare gli IP pubblici per i nodi

È possibile individuare gli IP pubblici per i nodi in diversi modi:

* Usare il comando dell'interfaccia della riga di comando di Azure [az vmss list-instance-public-ips][az-list-ips].
* Usare [i comandi PowerShell o Bash][vmss-commands]. 
* È anche possibile visualizzare gli IP pubblici nella portale di Azure visualizzando le istanze nel set di scalabilità di macchine virtuali.

> [!Important]
> Il [gruppo di risorse del][node-resource-group] nodo contiene i nodi e i relativi IP pubblici. Usare il gruppo di risorse del nodo durante l'esecuzione di comandi per trovare gli IP pubblici per i nodi.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo è stato creato un cluster del servizio AKS che include nodi basati su GPU. Per ridurre i costi non necessari, è possibile eliminare *gpunodepool* o l'intero cluster del servizio Web Del servizio Web.

Per eliminare il pool di nodi basato su GPU, usare il [comando az aks nodepool delete,][az-aks-nodepool-delete] come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Per eliminare il cluster stesso, usare il [comando az group delete][az-group-delete] per eliminare il gruppo di risorse del servizio AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

È anche possibile eliminare il cluster aggiuntivo creato per lo scenario IP pubblico per i pool di nodi.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui pool [di nodi di sistema.][use-system-pool]

In questo articolo si è appreso come creare e gestire più pool di nodi in un cluster del servizio Web Diaks. Per altre informazioni su come controllare i pod tra pool di nodi, vedere Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione nel servizio [Contenitore di AKS.][operator-best-practices-advanced-scheduler]

Per creare e usare pool di nodi contenitore di Windows Server, vedere [Creare un contenitore di Windows Server nel servizio Contenitore di Windows Server.][aks-windows]

Usare [i gruppi di posizionamento di prossimità per][reduce-latency-ppg] ridurre la latenza per le applicazioni del servizio Web AKS.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create
