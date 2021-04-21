---
title: Configurazione del cluster nei servizi Azure Kubernetes (AKS)
description: Informazioni su come configurare un cluster nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5740c1c299e8a6a2e8874bd13aae76b0353cc6a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775871"
---
# <a name="configure-an-aks-cluster"></a>Configurare un cluster del servizio Azure Kubernetes

Per creare un cluster AKS, potrebbe essere necessario personalizzare la configurazione del cluster in base alle esigenze. Questo articolo introduce alcune opzioni per la personalizzazione del cluster AKS.

## <a name="os-configuration"></a>Configurazione del sistema operativo

Il servizio Kubernetes supporta ora Ubuntu 18.04 come sistema operativo del nodo predefinito nella disponibilità generale (GA) per i cluster nelle versioni kubernetes superiori alla 1.18 Per le versioni successive alla 1.18, il servizio Kubernetes Ubuntu 16.04 è ancora l'immagine di base predefinita. Da kubernetes v1.18 e versioni successive, la base predefinita è AKS Ubuntu 18.04.

> [!IMPORTANT]
> I pool di nodi creati in Kubernetes v1.18 o versione successiva hanno come impostazione predefinita `AKS Ubuntu 18.04` l'immagine del nodo. I pool di nodi in una versione kubernetes supportata inferiore alla 1.18 ricevono come immagine del nodo, ma verranno aggiornati a dopo l'aggiornamento della versione kubernetes del pool di nodi alla `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` versione 1.18 o successiva.
> 
> È consigliabile testare i carichi di lavoro nei pool di nodi Ubuntu 18.04 del servizio Web del servizio AKS prima di usare i cluster nella versione 1.18 o successiva.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Usare AKS Ubuntu 18.04 (GA) nei nuovi cluster

I cluster creati in Kubernetes v1.18 o versione successiva hanno come impostazione predefinita `AKS Ubuntu 18.04` l'immagine del nodo. I pool di nodi in una versione di Kubernetes supportata inferiore alla 1.18 riceveranno comunque come immagine del nodo, ma verranno aggiornati a dopo l'aggiornamento della versione kubernetes del cluster o del pool di nodi alla `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` versione 1.18 o successiva.

È consigliabile testare i carichi di lavoro nei pool di nodi Ubuntu 18.04 del servizio AKS prima di usare cluster nella versione 1.18 o successiva.

Per creare un cluster usando l'immagine del nodo, è sufficiente creare un cluster che esegue `AKS Ubuntu 18.04` kubernetes v1.18 o versione successiva, come illustrato di seguito

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Usare AKS Ubuntu 18.04 (GA) nei cluster esistenti

I cluster creati in Kubernetes v1.18 o versione successiva hanno come impostazione predefinita `AKS Ubuntu 18.04` l'immagine del nodo. I pool di nodi in una versione di Kubernetes supportata inferiore alla 1.18 riceveranno comunque come immagine del nodo, ma verranno aggiornati a dopo l'aggiornamento della versione kubernetes del cluster o del pool di nodi alla `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` versione 1.18 o successiva.

È consigliabile testare i carichi di lavoro nei pool di nodi Ubuntu 18.04 del servizio Web del servizio AKS prima di usare i cluster nella versione 1.18 o successiva.

Se i cluster o i pool di nodi sono pronti per l'immagine del nodo, è sufficiente `AKS Ubuntu 18.04` aggiornarli a una versione 1.18 o successiva, come indicato di seguito.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Se si vuole solo aggiornare un solo pool di nodi:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Testare AKS Ubuntu 18.04 (GA) in cluster esistenti

I pool di nodi creati in Kubernetes v1.18 o versione successiva hanno come impostazione predefinita `AKS Ubuntu 18.04` l'immagine del nodo. I pool di nodi in una versione di Kubernetes supportata inferiore alla 1.18 riceveranno comunque come immagine del nodo, ma verranno aggiornati a dopo l'aggiornamento della versione kubernetes del pool di nodi alla `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` versione 1.18 o successiva.

È consigliabile testare i carichi di lavoro nei pool di nodi di Ubuntu Ubuntu 18.04 del AKS prima di aggiornare i pool di nodi di produzione.

Per creare un pool di nodi usando l'immagine del nodo, è sufficiente creare un pool di nodi che `AKS Ubuntu 18.04` esegue kubernetes v1.18 o versione successiva. Il piano di controllo del cluster deve essere almeno sulla versione 1.18 o successiva, ma gli altri pool di nodi possono rimanere in una versione precedente di kubernetes.
Di seguito viene prima aggiornato il piano di controllo e quindi viene creato un nuovo pool di nodi con la versione 1.18 che riceverà la nuova versione del sistema operativo dell'immagine del nodo.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Configurazione del runtime del contenitore

Un runtime del contenitore è un software che esegue contenitori e gestisce le immagini dei contenitori in un nodo. Il runtime consente di astrarre le chiamate sys o funzionalità specifiche del sistema operativo per eseguire contenitori in Linux o Windows. Cluster del servizio Kubernetes che usano pool di nodi Kubernetes versione 1.19 e un uso maggiore `containerd` come runtime del contenitore. I cluster del servizio Kubernetes che usano Kubernetes prima della versione 1.19 per i pool di nodi usano [Moby](https://mobyproject.org/) (docker upstream) come runtime del contenitore.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) è un runtime del contenitore core conforme a [OCI](https://opencontainers.org/) (Open Container Initiative) che fornisce il set minimo di funzionalità necessarie per eseguire contenitori e gestire le immagini in un nodo. È stato [donato](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) a Cloud Native Compute Foundation (CNCF) nel marzo del 2017. La versione corrente di Moby utilizzata dal supporto di AKS sfrutta già e si basa su `containerd` , come illustrato in precedenza.

Con un pool di nodi e nodi basato su , invece di parlare con , il kubelet parlerà direttamente con tramite il plug-in CRI (interfaccia di runtime del contenitore), rimuovendo hop aggiuntivi nel flusso rispetto all'implementazione di `containerd` `dockershim` `containerd` Docker CRI. Di conseguenza, si può vedere una migliore latenza di avvio dei pod e un minore utilizzo di risorse (CPU e memoria).

Usando per i nodi del servizio Contenitore di Microsoft Container, la latenza di avvio dei pod migliora e l'utilizzo delle risorse del nodo da parte del `containerd` runtime del contenitore diminuisce. Questi miglioramenti sono abilitati da questa nuova architettura in cui kubelet dialoga direttamente con tramite il `containerd` plug-in CRI, mentre nell'architettura Moby/docker kubelet parla con il motore Docker e prima di raggiungere , con hop aggiuntivi nel `dockershim` `containerd` flusso.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funziona in ogni versione ga di Kubernetes nel servizio Kubernetes e in ogni versione upstream di Kubernetes precedente alla versione 1.19 e supporta tutte le funzionalità di Kubernetes e del servizio Kubernetes.

> [!IMPORTANT]
> Per impostazione predefinita, i cluster con pool di nodi creati in Kubernetes v1.19 o versione successiva sono `containerd` per il runtime del contenitore. I cluster con pool di nodi in una versione di Kubernetes supportata precedente alla 1.19 ricevono per il runtime del contenitore, ma verranno aggiornati a dopo che la versione di Kubernetes del pool di nodi è stata aggiornata alla `Moby` `ContainerD` versione 1.19 o successiva. È comunque possibile usare i pool di nodi e i cluster nelle versioni supportate precedenti fino `Moby` a quando non viene disattivato il supporto.
> 
> È consigliabile testare i carichi di lavoro nei pool di nodi del servizio Web Diaks con prima di usare `containerD` i cluster nella versione 1.19 o successiva.

### <a name="containerd-limitationsdifferences"></a>`Containerd` limitazioni/differenze

* Per usare `containerd` come runtime del contenitore, è necessario usare il servizio Contenitore di AKS Ubuntu 18.04 come immagine del sistema operativo di base.
* Mentre il set di strumenti Docker è ancora presente nei nodi, Kubernetes usa `containerd` come runtime del contenitore. Pertanto, poiché Moby/Docker non gestisce i contenitori creati da Kubernetes nei nodi, non è possibile visualizzare o interagire con i contenitori usando comandi Docker (ad esempio ) o `docker ps` l'API Docker.
* Per , è consigliabile usare come interfaccia della riga di comando sostitutiva anziché l'interfaccia della riga di comando di Docker per la risoluzione dei problemi relativi a pod, contenitori e immagini del contenitore nei `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) nodi Kubernetes,  ad esempio `crictl ps` . 
   * Non fornisce la funzionalità completa dell'interfaccia della riga di comando di Docker. È destinato solo alla risoluzione dei problemi.
   * `crictl` offre una visualizzazione dei contenitori più facile da usare per kubernetes, con concetti come pod e così via.
* `Containerd` configura la registrazione usando il formato di registrazione standardizzato, che è diverso da quello attualmente disponibile `cri` nel driver JSON di Docker. La soluzione di registrazione deve supportare il formato di registrazione (ad esempio `cri` Monitoraggio di Azure per [contenitori](../azure-monitor/containers/container-insights-enable-new-cluster.md))
* Non è più possibile accedere al motore `/var/run/docker.sock` Docker, o usare Docker-in-Docker (DinD).
  * Se attualmente si estraggono i log dell'applicazione o i dati di monitoraggio dal motore Docker, usare qualcosa di simile [Monitoraggio di Azure per i contenitori.](../azure-monitor/containers/container-insights-enable-new-cluster.md) Inoltre, il servizio Web Del servizio App non supporta l'esecuzione di comandi fuori banda nei nodi dell'agente che potrebbero causare instabilità.
  * Anche quando si usa Moby/docker, la creazione di immagini e l'uso diretto del motore Docker tramite i metodi precedenti sono fortemente sconsigliati. Kubernetes non è completamente a conoscenza di queste risorse utilizzate [](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) e questi approcci presentano numerosi problemi descritti in dettaglio qui e [qui,](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)ad esempio.
* Compilazione di immagini: è possibile continuare a usare il flusso di lavoro di compilazione docker corrente come di consueto, a meno che non si compilano immagini all'interno del cluster del servizio AKS. In questo caso, è consigliabile passare all'approccio consigliato per la compilazione di immagini usando attività del registro di controllo di accesso o un'opzione in cluster più sicura, ad esempio [docker buildx](https://github.com/docker/buildx). [](../container-registry/container-registry-quickstart-task-cli.md)

## <a name="generation-2-virtual-machines"></a>Macchine virtuali di seconda generazione

Azure supporta macchine [virtuali (VM)](../virtual-machines/generation-2.md)di seconda generazione . Le macchine virtuali di seconda generazione supportano funzionalità chiave non supportate nelle macchine virtuali di prima generazione (Gen1). Queste funzionalità includono una maggior quantità di memoria, Intel Software Guard Extensions (Intel SGX) e la memoria persistente virtuale (vPMEM).

Le macchine virtuali di seconda generazione usano la nuova architettura di avvio basata su UEFI anziché l'architettura basata su BIOS usata dalle macchine virtuali di prima generazione.
Solo SKU e dimensioni specifiche supportano macchine virtuali di generazione 2. Controllare [l'elenco delle dimensioni supportate](../virtual-machines/generation-2.md#generation-2-vm-sizes)per verificare se lo SKU supporta o richiede Gen2.

Inoltre, non tutte le immagini di macchine virtuali supportano Gen2, nelle macchine virtuali del servizio Web Del servizio Web di AKS Gen2 verrà utilizzata la nuova immagine del servizio [AKS Ubuntu 18.04.](#os-configuration) Questa immagine supporta tutti gli SKU e le dimensioni di Gen2.

## <a name="ephemeral-os"></a>Sistema operativo ffemero

Per impostazione predefinita, Azure replica automaticamente il disco del sistema operativo per una macchina virtuale nell'archiviazione di Azure per evitare la perdita di dati nel caso in cui la macchina virtuale debba essere rilocata in un altro host. Tuttavia, poiché i contenitori non sono progettati per rendere persistente lo stato locale, questo comportamento offre un valore limitato, offrendo al tempo stesso alcuni svantaggi, tra cui il provisioning più lento dei nodi e una latenza di lettura/scrittura più elevata.

Al contrario, i dischi temporanei del sistema operativo vengono archiviati solo nel computer host, proprio come un disco temporaneo. In questo modo si riduce la latenza di lettura/scrittura, oltre a un ridimensionamento più rapido dei nodi e agli aggiornamenti del cluster.

Analogamente al disco temporaneo, un disco temporaneo del sistema operativo è incluso nel prezzo della macchina virtuale, quindi non si incorre in costi di archiviazione aggiuntivi.

> [!IMPORTANT]
>Quando un utente non richiede in modo esplicito i dischi gestiti per il sistema operativo, il servizio Web Del servizio Web del servizio Windows Server 2003 imposta il sistema operativo ffimetto, se possibile, per una determinata configurazione del pool di nodi.

Quando si usa un sistema operativo ffemero, il disco del sistema operativo deve essere contenuto nella cache della macchina virtuale. Le dimensioni della cache delle macchine virtuali sono disponibili nella documentazione [di Azure](../virtual-machines/dv3-dsv3-series.md) tra parentesi accanto alla velocità effettiva di I/O ("dimensioni della cache in GiB").

Usando le dimensioni predefinite della macchina virtuale del servizio Standard_DS2_v2 con le dimensioni predefinite del disco del sistema operativo di 100 GB, ad esempio, queste dimensioni della macchina virtuale supportano il sistema operativo ffemero ma hanno solo 86 GB di dimensioni della cache. Questa configurazione verrà impostata per impostazione predefinita su managed disks se l'utente non specifica in modo esplicito. Se un utente ha richiesto in modo esplicito un sistema operativo ffemero, riceverebbe un errore di convalida.

Se un utente richiede lo stesso Standard_DS2_v2 con un disco del sistema operativo da 60 GB, per impostazione predefinita questa configurazione corrisponde al sistema operativo ffimetto: le dimensioni richieste di 60 GB sono inferiori alle dimensioni massime della cache di 86 GB.

Usando Standard_D8s_v3 con disco del sistema operativo da 100 GB, le dimensioni della macchina virtuale supportano il sistema operativo ffemero e hanno 200 GB di spazio nella cache. Se un utente non specifica il tipo di disco del sistema operativo, per impostazione predefinita il pool di nodi riceverà un sistema operativo ffemero. 

Il sistema operativo ffemeral richiede almeno la versione 2.15.0 dell'interfaccia della riga di comando di Azure.

### <a name="use-ephemeral-os-on-new-clusters"></a>Usare il sistema operativo ffemeral nei nuovi cluster

Configurare il cluster per l'uso dei dischi del sistema operativo ffemeri al momento della creazione del cluster. Usare il `--node-osdisk-type` flag per impostare Ephemeral OS come tipo di disco del sistema operativo per il nuovo cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Per creare un cluster normale usando dischi del sistema operativo collegati alla rete, è possibile specificare `--node-osdisk-type=Managed` . È anche possibile scegliere di aggiungere altri pool di nodi del sistema operativo effimeri come indicato di seguito.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Usare il sistema operativo ffemeral nei cluster esistenti
Configurare un nuovo pool di nodi per l'uso dei dischi del sistema operativo ffemeri. Usare il flag per impostare come tipo di disco del sistema operativo il tipo di disco `--node-osdisk-type` del sistema operativo per il pool di nodi.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Con il sistema operativo phemeral è possibile distribuire immagini di macchine virtuali e istanze fino alle dimensioni della cache della macchina virtuale. Nel caso del servizio AKS, la configurazione predefinita del disco del sistema operativo del nodo usa 128 GB, il che significa che è necessaria una dimensione della macchina virtuale con una cache maggiore di 128 GB. Il valore Standard_DS2_v2 ha una dimensione della cache di 86 GB, che non è sufficientemente grande. La Standard_DS3_v2 ha una dimensione della cache di 172 GB, che è sufficientemente grande. È anche possibile ridurre le dimensioni predefinite del disco del sistema operativo usando `--node-osdisk-size` . Le dimensioni minime per le immagini del server del server del supporto di Microsoft Sono 30 GB. 

Per creare pool di nodi con dischi del sistema operativo collegati alla rete, è possibile specificare `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Nome del gruppo di risorse personalizzato

Quando si distribuisce un cluster del servizio Kubernetes di Azure in Azure, viene creato un secondo gruppo di risorse per i nodi di lavoro. Per impostazione predefinita, AKS assegna un nome al gruppo di risorse del nodo `MC_resourcegroupname_clustername_location`, ma è anche possibile specificare un nome personalizzato.

Per specificare un nome per il gruppo di risorse, installare la versione 0.3.2 o una versione successiva dell'estensione aks-preview dell'interfaccia della riga di comando di Azure. Nell'interfaccia della riga di comando di Azure, usare il parametro `--node-resource-group` del comando `az aks create` per specificare un nome personalizzato per il gruppo di risorse. Se si usa un modello di Azure Resource Manager per distribuire un cluster AKS, è possibile definire il nome del gruppo di risorse usando la proprietà `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse Azure nella propria sottoscrizione. Quando viene creato il cluster, è possibile specificare solo il nome del gruppo di risorse personalizzato. 

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

- Specificare un gruppo di risorse esistente come gruppo di risorse del nodo.
- Specificare una sottoscrizione diversa per il gruppo di risorse nodo.
- Cambiare il nome del gruppo di risorse nodo dopo la creazione del cluster.
- Specificare i nomi delle risorse gestite nel gruppo di risorse del nodo.
- Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come aggiornare le immagini dei](node-image-upgrade.md) nodi nel cluster.
- Per informazioni su come aggiornare il cluster alla versione più recente di Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes](upgrade-cluster.md).
- Altre informazioni [ `containerd` su e Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Per trovare le risposte ad alcune domande comuni su AKS, vedere l'elenco delle [domande frequenti su AKS](faq.md).
- Altre informazioni sui dischi del sistema operativo [effimeri](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
