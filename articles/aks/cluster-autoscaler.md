---
title: Usare il componente di scalabilità automatica del cluster nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il componente di scalabilità automatica per ridimensionare automaticamente il cluster e soddisfare le richieste delle applicazioni in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f02b91f73320786716e356639d8134280325dc19
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775998"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Ridimensionare automaticamente un cluster per soddisfare le richieste delle applicazioni nel servizio Azure Kubernetes (AKS)

Per tenere il passo delle richieste delle applicazioni nel servizio Azure Kubernetes (AKS), potrebbe essere necessario regolare il numero di nodi che eseguono i carichi di lavoro. Il componente di scalabilità automatica del cluster può cercare i pod del cluster che non possono essere pianificati a causa di vincoli delle risorse. Quando vengono rilevati problemi, il numero di nodi di un pool viene incrementato per soddisfare la richiesta delle applicazioni. Inoltre i nodi vengono controllati regolarmente per rilevare la mancanza di pod in esecuzione, con la riduzione del numero di nodi in base alle esigenze. Questa capacità di incrementare o ridurre il numero di nodi nel cluster del servizio Azure Kubernetes permette di gestire il cluster in modo efficace e conveniente.

Questo articolo illustra come abilitare e gestire il componente di scalabilità automatica in un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede che sia in esecuzione l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Informazioni sul componente di scalabilità automatica

Per adattarsi alle variazioni delle richieste delle applicazioni, ad esempio tra l’orario lavorativo diurno e la sera o il fine settimana, è spesso necessario trovare il modo di adattare automaticamente i cluster. La scalabilità dei cluster del servizio Azure Kubernetes può essere ottenuta in uno dei due modi seguenti:

* Il **componente di scalabilità automatica** cerca i pod che non possono essere pianificati nei nodi a causa di vincoli delle risorse. Il cluster aumenta quindi automaticamente il numero di nodi.
* Il **componente di scalabilità automatica orizzontale dei pod** usa il server delle metriche in un cluster Kubernetes per monitorare la richiesta di risorse dei pod. Se un'applicazione richiede più risorse, il numero di pod viene aumentato automaticamente per soddisfare la domanda.

![Il componente di scalabilità automatica del cluster e il componente di scalabilità automatica orizzontale dei pod spesso interagiscono per soddisfare le esigenze delle applicazioni obbligatorie](media/autoscaler/cluster-autoscaler.png)

Sia il componente di scalabilità automatica orizzontale dei pod, sia il componente di scalabilità automatica del cluster possono anche ridurre il numero di pod e nodi in base alle esigenze. Il componente di scalabilità automatica del cluster riduce il numero di nodi quando una certa quantità di capacità è rimasta inutilizzata per un periodo di tempo. I pod in un nodo che devono essere rimossi dal componente di scalabilità automatica del cluster sono pianificati in modo sicuro in un'altra posizione nel cluster. Il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione qualora non fosse possibile spostare i pod, ad esempio nelle situazioni seguenti:

* Un pod viene creato direttamente e non è supportato da un oggetto controller, come un set di distribuzioni o di repliche.
* Un budget di interruzione dei pod (PDB) troppo restrittivo e che non permette la riduzione del numero di pod sotto una determinata soglia.
* Un pod che utilizza selettori di nodo o anti-affinità impossibili da rispettare se pianificati in un nodo diverso.

Per altre informazioni sui casi in cui il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione, vedere [Quali tipi di pod possono impedire al componente di scalabilità automatica del cluster di rimuovere un nodo?][autoscaler-scaledown]

Il componente di scalabilità automatica del cluster usa i parametri di avvio per elementi come gli intervalli di tempo tra gli eventi di scalabilità e le soglie delle risorse. Per altre informazioni sui parametri utilizzati dal ridimensionamento automatico del cluster, vedere [Uso del profilo di scalabilità automatica.](#using-the-autoscaler-profile)

Il componente di scalabilità automatica orizzontale e il componente di scalabilità automatica del cluster possono essere usati insieme e spesso sono entrambi distribuiti in un cluster. Nell’uso combinato, il componente di scalabilità automatica orizzontale dei pod è dedicato all’esecuzione del numero di pod necessari per soddisfare le richieste delle applicazioni. Il componente di scalabilità automatica del cluster è dedicato all’esecuzione del numero di nodi richiesto per supportare i pod pianificati.

> [!NOTE]
> Il ridimensionamento manuale è disabilitato quando si usa il componente di scalabilità automatica del cluster. È il componente di scalabilità automatica del cluster che determina il numero di nodi richiesto. Se si desidera ridimensionare manualmente il cluster [disabilitare il componente di scalabilità automatica del cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Creare un cluster del servizio Azure Kubernetes e abilitare il componente di scalabilità automatica del cluster

Se è necessario creare un cluster AKS, usare il comando [az aks create][az-aks-create]. Per abilitare e configurare il ridimensionamento automatico del cluster nel pool di nodi per il cluster, usare il parametro e `--enable-cluster-autoscaler` specificare un nodo e `--min-count` `--max-count` .

> [!IMPORTANT]
> Il ridimensionamento automatico del cluster è un componente di Kubernetes. Anche se il cluster del servizio Azure Kubernetes usa un set di scalabilità per i nodi di macchine virtuali, non abilitare o modificare manualmente le impostazioni di scalabilità per il ridimensionamento automatico nel portale di Azure o tramite l'interfaccia della riga di comando di Azure. Consentire il ridimensionamento automatico del cluster Kubernetes di gestire le impostazioni di scalabilità necessaria. Per altre informazioni, consultare [È possibile modificare le risorse del servizio Azure Kubernetes nel gruppo di risorse del nodo?][aks-faq-node-resource-group]

L'esempio seguente crea un cluster AKS con un pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Abilita anche il componente di scalabilità automatica del cluster nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Sono necessari alcuni minuti per creare il cluster e configurare le impostazioni del componente di scalabilità automatica del cluster.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Aggiornare un cluster del servizio Web Disassoce esistente per abilitare il ridimensionamento automatico del cluster

Usare il [comando az aks update][az-aks-update] per abilitare e configurare il ridimensionamento automatico del cluster nel pool di nodi per il cluster esistente. Usare il `--enable-cluster-autoscaler` parametro e specificare un nodo e `--min-count` `--max-count` .

> [!IMPORTANT]
> Il ridimensionamento automatico del cluster è un componente di Kubernetes. Anche se il cluster del servizio Azure Kubernetes usa un set di scalabilità per i nodi di macchine virtuali, non abilitare o modificare manualmente le impostazioni di scalabilità per il ridimensionamento automatico nel portale di Azure o tramite l'interfaccia della riga di comando di Azure. Consentire il ridimensionamento automatico del cluster Kubernetes di gestire le impostazioni di scalabilità necessaria. Per altre informazioni, consultare [È possibile modificare le risorse del servizio Azure Kubernetes nel gruppo di risorse del nodo?][aks-faq-node-resource-group]

L'esempio seguente aggiorna un cluster del servizio Servizio Web Di seguito per abilitare il ridimensionamento automatico del cluster nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo *di 3* nodi:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

L'aggiornamento del cluster e la configurazione delle impostazioni di scalabilità automatica del cluster sono necessari alcuni minuti.

## <a name="change-the-cluster-autoscaler-settings"></a>Modificare le impostazioni del componente di scalabilità automatica del cluster

> [!IMPORTANT]
> Se nel cluster AKS sono presenti più pool di nodi, passare alla [sezione della scalabilità automatica con più pool di agenti](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). I cluster con più pool di agenti richiedono l'uso del set di comandi `az aks nodepool` per modificare le proprietà specifiche del pool di nodi anziché `az aks`.

Nel passaggio precedente per creare un cluster AKS o aggiornare un pool di nodi esistente, il numero minimo di nodi del componente di scalabilità automatica del cluster è stato impostato su *1* e il numero massimo di nodi è stato impostato su *3*. Se le richieste delle applicazioni cambiano, potrebbe essere necessario regolare il numero di nodi del componente di scalabilità automatica del cluster.

Per modificare il numero di nodi, usare il comando [az aks update][az-aks-update].

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

L'esempio precedente aggiorna il componente di scalabilità automatica del cluster nel pool a nodo singolo in *myAKSCluster* impostando il numero minimo di nodi su *1* e il numero massimo su *5*.

> [!NOTE]
> Il ridimensionamento automatico del cluster prende decisioni di ridimensionamento in base ai conteggi minimo e massimo impostati in ogni pool di nodi, ma non li applica dopo l'aggiornamento dei conteggi minimo o massimo. Ad esempio, se si imposta un numero minimo di 5 quando il numero di nodi corrente è 3, il pool non verrà ridimensionato immediatamente fino a 5. Se il numero minimo nel pool di nodi ha un valore superiore al numero corrente di nodi, le nuove impostazioni min o max verranno rispettate quando sono presenti un numero sufficiente di pod non configurabili che richiederebbero 2 nuovi nodi aggiuntivi e attiverà un evento di scalabilità automatica. Dopo l'evento di scalabilità, i nuovi limiti di conteggio vengono rispettati.

Monitorare le prestazioni delle applicazioni e dei servizi e modificare il numero di nodi del componente di scalabilità automatica del cluster per ottenere le prestazioni necessarie.

## <a name="using-the-autoscaler-profile"></a>Uso del profilo di scalabilità automatica

È anche possibile configurare dettagli più granulari del componente di scalabilità automatica del cluster modificando i valori predefiniti nel profilo di scalabilità automatica a livello di cluster. Ad esempio, un evento di riduzione si verifica quando i nodi sono sottoutilizzati dopo 10 minuti. Se si hanno carichi di lavoro che vengono eseguiti ogni 15 minuti, può essere opportuno modificare il profilo di scalabilità automatica in modo da ridurre i nodi sottoutilizzati dopo 15 o 20 minuti. Quando si abilita il componente di scalabilità automatica del cluster, viene usato un profilo predefinito, a meno che non si specifichino impostazioni diverse. Il profilo di scalabilità automatica del cluster ha le seguenti impostazioni che è possibile aggiornare:

| Impostazione                          | Descrizione                                                                              | Valore predefinito |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | Frequenza della rivalutazione del cluster per l'aumento o la riduzione                                    | 10 secondi    |
| scale-down-delay-after-add       | Intervallo di tempo tra l'aumento e la ripresa della valutazione per la riduzione                               | 10 minuti    |
| scale-down-delay-after-delete    | Intervallo di tempo tra l'eliminazione del nodo e la ripresa della valutazione per la riduzione                          | scan-interval |
| scale-down-delay-after-failure   | Intervallo di tempo tra un errore di riduzione e la ripresa della valutazione per la riduzione                     | 3 minuti     |
| scale-down-unneeded-time         | Per quanto tempo un nodo non deve essere necessario prima di essere idoneo per la riduzione                  | 10 minuti    |
| scale-down-unready-time          | Per quanto tempo un nodo unready non deve essere necessario prima di essere idoneo per la riduzione         | 20 minuti    |
| scale-down-utilization-threshold | Livello di utilizzo del nodo, definito come somma delle risorse richieste divisa per la capacità, al di sotto del quale un nodo può essere preso in considerazione per la riduzione | 0.5 |
| max-graceful-termination-sec     | Numero massimo di secondi di attesa del ridimensionamento automatico del cluster per la terminazione del pod durante il tentativo di ridimensionamento di un nodo | 600 secondi   |
| balance-similar-node-groups      | Rileva pool di nodi simili e bilancia il numero di nodi tra di essi                 | false         |
| Expander                         | Tipo di espansore del pool [di nodi](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) da usare per la scalabilità verticale. Valori possibili: `most-pods` , `random` , `least-waste` , `priority` | random | 
| skip-nodes-with-local-storage    | Se true, il ridimensionamento automatico del cluster non eliminerà mai i nodi con pod con archiviazione locale, ad esempio EmptyDir o HostPath | true |
| skip-nodes-with-system-pods      | Se true, la scalabilità automatica del cluster non eliminerà mai i nodi con pod da kube-system (ad eccezione dei pod DaemonSet o mirror) | true | 
| max-empty-bulk-delete            | Numero massimo di nodi vuoti che possono essere eliminati contemporaneamente                       | 10 nodi      |
| new-pod-scale-up-delay           | Per scenari come la scalabilità burst/batch in cui non si vuole che ca agirà prima che l'utilità di pianificazione kubernetes possa pianificare tutti i pod, è possibile indicare a CA di ignorare i pod non pianificati prima che siano di una determinata età.                                                                                                                | 0 secondi    |
| max-total-unready-percentage     | Percentuale massima di nodi non letti nel cluster. Dopo il superamento di questa percentuale, l'autorità di certificazione arresta le operazioni | 45% |
| max-node-provision-time          | Tempo massimo di attesa del ridimensionamento automatico per il provisioning di un nodo                           | 15 minuti    |   
| ok-total-unready-count           | Numero di nodi non letti consentiti, indipendentemente da max-total-unready-percentage            | 3 nodi       |

> [!IMPORTANT]
> Il profilo di scalabilità automatica del cluster interessa tutti i pool di nodi che usano il componente di scalabilità automatica del cluster. Non è possibile impostare un profilo di scalabilità automatica per ogni pool di nodi.
>
> Il profilo di scalabilità automatica del cluster richiede la *versione 2.11.1* o successiva dell'interfaccia della riga di comando di Azure. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Impostare il componente di scalabilità automatica del cluster in un cluster AKS esistente

Per impostare il profilo di scalabilità automatica del cluster nel cluster in uso, usare il [comando az aks update][az-aks-update-preview] con il parametro *cluster-autoscaler-profile*. Nell'esempio seguente l'intervallo di analisi viene impostato su 30 secondi nel profilo.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Se si abilita il componente di scalabilità automatica del cluster nei pool di nodi del cluster, anche questi cluster useranno il profilo di scalabilità automatica del cluster. Ad esempio:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Quando si imposta il profilo di scalabilità automatica del cluster, tutti i pool di nodi esistenti con il componente di scalabilità automatica del cluster abilitato inizieranno immediatamente a usare il profilo.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Impostare il componente di scalabilità automatica del cluster quando si crea un cluster AKS

Il parametro *cluster-autoscaler-profile* può essere usato anche quando si crea un cluster. Ad esempio:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Il comando precedente crea un cluster AKS e definisce l'intervallo di analisi come 30 secondi per il profilo di scalabilità automatica a livello di cluster. Il comando abilita anche il componente di scalabilità automatica del cluster nel pool di nodi iniziale, imposta il numero minimo di nodi su 1 e il numero massimo di nodi su 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Reimpostare il profilo di scalabilità automatica del cluster sui valori predefiniti

Usare il comando [az aks update][az-aks-update-preview] per reimpostare il profilo di scalabilità automatica del cluster nel cluster in uso.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Disabilitare il componente di scalabilità automatica del cluster

Se non si vuole più usare il ridimensionamento automatico del cluster, è possibile disabilitarlo usando il [comando az aks update,][az-aks-update-preview] specificando il `--disable-cluster-autoscaler` parametro . I nodi non vengono rimossi quando il componente di scalabilità automatica del cluster è disabilitato.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

È possibile ridimensionare manualmente il cluster dopo aver disabilitato il componente di scalabilità automatica del cluster usando il comando [az aks scale][az-aks-scale]. Se si usa il componente di scalabilità automatica orizzontale dei pod, tale funzionalità continua a essere eseguita con il componente di scalabilità automatica del cluster disabilitato, ma potrebbe non essere possibile pianificare i pod se tutte le risorse del nodo sono in uso.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Riabilitare un componente di scalabilità automatica del cluster disabilitato

Se si desidera ri abilitare la scalabilità automatica del cluster in un cluster esistente, è possibile ri abilitarlo usando il [comando az aks update,][az-aks-update-preview] specificando i parametri `--enable-cluster-autoscaler` , e `--min-count` `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperare i log e lo stato del componente di scalabilità automatica del cluster

Per eseguire la diagnosi e il debug degli eventi di scalabilità automatica, è possibile recuperare i log e lo stato dal componente aggiuntivo di scalabilità automatica.

AKS gestisce il componente di scalabilità automatica per conto dell'utente e lo esegue nel piano di controllo gestito. È possibile abilitare il nodo del piano di controllo per visualizzare i log e le operazioni da CA.

Per configurare i log in modo che il componente di scalabilità automatica del cluster ne esegua il push in Log Analytics, attenersi alla seguente procedura.

1. Configurare una regola per i log delle risorse per eseguire il push dei log del componente di scalabilità automatica del cluster in Log Analytics. [Le istruzioni sono descritte in dettaglio qui][aks-view-master-logs], assicurarsi di selezionare la casella per `cluster-autoscaler` quando si selezionano le opzioni per "Log".
1. Selezionare la sezione "Log" nel cluster tramite il portale di Azure.
1. Immettere la seguente query di esempio in Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Verranno visualizzati log simili all'esempio seguente, purché siano presenti log da recuperare.

![Log di Log Analytics](media/autoscaler/autoscaler-logs.png)

Il ridimensionamento automatico del cluster scriverà anche lo stato di integrità in un `configmap` oggetto denominato `cluster-autoscaler-status` . Per recuperare questi log, eseguire il comando `kubectl` seguente. Verrà segnalato uno stato di integrità per ogni pool di nodi configurato con il componente di scalabilità automatica del cluster.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Per altre informazioni sugli elementi registrati dal componente di scalabilità automatica, vedere le domande frequenti sul [progetto GitHub Kubernetes/AutoScaler][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Usare il componente di scalabilità automatica del cluster con più pool di nodi abilitati

Il componente di scalabilità automatica del cluster può essere usato insieme a [più pool di nodi][aks-multiple-node-pools] abilitati. Consultare questo documento per informazioni su come abilitare più pool di nodi e aggiungere altri pool di nodi a un cluster esistente. Quando si usano entrambe le funzionalità insieme, si abilita il componente di scalabilità automatica del cluster per ogni singolo pool di nodi del cluster e si possono passare regole di scalabilità automatica univoche a ognuno di essi.

Il comando seguente presuppone che l'utente si sia attenuto alle [istruzioni iniziali](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) contenute in questo documento e voglia aggiornare il numero massimo di un pool di nodi esistente da *3* a *5*. Usare il comando [az aks nodepool update][az-aks-nodepool-update] per aggiornare le impostazioni di un pool di nodi esistente.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

È possibile disabilitare il ridimensionamento automatico del cluster con [az aks nodepool update][az-aks-nodepool-update] e passando il parametro `--disable-cluster-autoscaler`.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se si vuole ri abilitare il componente di scalabilità automatica del cluster in un cluster esistente, è possibile abilitarlo di nuovo usando il comando az servizio Web Del servizio Web Diaz [nodepool update,][az-aks-nodepool-update] specificando i parametri `--enable-cluster-autoscaler` , e `--min-count` `--max-count` .

> [!NOTE]
> Se si prevede di usare il ridimensionamento automatico del cluster con pool di nodi che si estendono su più zone e sfruttare le funzionalità di pianificazione correlate alle zone, ad esempio la pianificazione topologica dei volumi, è consigliabile avere un pool di nodi per zona e abilitare tramite il profilo di scalabilità `--balance-similar-node-groups` automatica. In questo modo il ridimensionamento automatico verrà ridimensionato correttamente e si tenterà di mantenere bilanciate le dimensioni dei pool di nodi.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come ridimensionare automaticamente il numero di nodi del servizio Azure Kubernetes. È anche possibile usare il componente di scalabilità automatica orizzontale dei pod per regolare automaticamente il numero di pod che eseguono l'applicazione. Per istruzioni sull'uso del componente di scalabilità automatica orizzontale dei pod, vedere [Ridimensionare le applicazioni nel servizio Azure Kubernetes][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why