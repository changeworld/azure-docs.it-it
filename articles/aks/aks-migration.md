---
title: Eseguire la migrazione a servizio Azure Kubernetes (AKS)
description: Eseguire la migrazione a servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7856b2b0668eca7a172b738a7eeea640e466f003
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483113"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Eseguire la migrazione a servizio Azure Kubernetes (AKS)

Per facilitare la pianificazione e l'esecuzione di una migrazione corretta al servizio Servizio Azure Kubernetes, questa guida fornisce informazioni dettagliate per la configurazione corrente consigliata del servizio AKS. Questo articolo non tratta tutti gli scenari, ma contiene collegamenti a informazioni più dettagliate per la pianificazione di una migrazione corretta.

Questo documento consente di supportare gli scenari seguenti:

* La containerizzazione di determinate applicazioni e la migrazione ad AKS [tramite Azure Migrate](../migrate/migrate-services-overview.md).
* Migrazione di un cluster del servizio Desktop remoto supportato da [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) a set di [scalabilità di macchine virtuali.](../virtual-machine-scale-sets/overview.md)
* Migrazione di un cluster del servizio AzureKs per l'uso di un servizio [di bilanciamento del carico con SKU Standard.](./load-balancer-standard.md)
* Migrazione da [servizio Azure Container (ACS) - Ritiro del 31 gennaio 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) al servizio Web AKS.
* Migrazione dal [motore del modulo del server Disacks](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ad AKS.
* Migrazione da cluster Kubernetes non basati su Azure al servizio Azure Kubernetes.
* Spostamento delle risorse esistenti in un'area diversa.

Durante la migrazione, assicurarsi che la versione di Kubernetes di destinazione sia all'interno della finestra supportata per il servizio Kubernetes. Le versioni precedenti potrebbero non essere nell'intervallo supportato e richiederanno un aggiornamento della versione per essere supportate dal servizio AKS. Per altre informazioni, vedere Versioni di Kubernetes supportate dal servizio [Kubernetes.](./supported-kubernetes-versions.md)

Se si esegue la migrazione a una versione più recente di Kubernetes, vedere Criteri di supporto dell'afa delle versioni e delle versioni [di Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

Diversi strumenti open source possono essere utili per la migrazione, a seconda dello scenario:

* [Velero](https://velero.io/) (richiede Kubernetes 1.7+)
* [Estensione dell'interfaccia della riga di comando di Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

In questo articolo verranno riepilogati i dettagli della migrazione per:

> [!div class="checklist"]
> * Contenitori di applicazioni tramite Azure Migrate 
> * Servizio Web Del servizio Load Balancer Standard e set di scalabilità di macchine virtuali
> * Servizi di Azure collegati esistenti
> * Verificare le quote valide
> * Disponibilità elevata e continuità aziendale
> * Considerazioni per le applicazioni senza stato
> * Considerazioni per le applicazioni con stato
> * Distribuzione della configurazione del cluster

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Usare Azure Migrate per eseguire la migrazione delle applicazioni al servizio AKS

Azure Migrate offre una piattaforma unificata per valutare ed eseguire la migrazione a server, infrastruttura, applicazioni e dati locali di Azure. Per il server del supporto di Microsoft Azure Migrate, è possibile eseguire le attività seguenti:

* [Creare un contenitore ASP.NET applicazioni ed eseguire la migrazione al servizio Container](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Creare un contenitore di applicazioni Web Java ed eseguire la migrazione al servizio AppK](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Servizio Diaks con Load Balancer Standard e set di scalabilità di macchine virtuali

Il servizio AKS è un servizio gestito che offre funzionalità univoche con un overhead di gestione inferiore. Poiché il servizio AKS è un servizio gestito, è necessario selezionare da un set di [aree](./quotas-skus-regions.md) supportate dal servizio AKS. Potrebbe essere necessario modificare le applicazioni esistenti per mantenerle integre nel piano di controllo gestito dal servizio AKS durante la transizione dal cluster esistente al servizio AKS.

È consigliabile usare cluster [](../virtual-machine-scale-sets/index.yml) del servizio AzureKs supportati da set di scalabilità di macchine virtuali [e azure Load Balancer Standard](./load-balancer-standard.md) per assicurarsi di ottenere funzionalità come:
* [Pool di più nodi](./use-multiple-node-pools.md),
* [zone di disponibilità](../availability-zones/az-overview.md),
* [Intervalli IP autorizzati,](./api-server-authorized-ip-ranges.md)
* [Scalabilità automatica cluster](./cluster-autoscaler.md),
* [Criteri di Azure per il supporto di AKS](../governance/policy/concepts/policy-for-kubernetes.md)e
* Altre nuove funzionalità non appena vengono rilasciate.

I cluster del servizio AKS supportati dai [set di disponibilità delle macchine](../virtual-machines/availability.md#availability-sets) virtuali non supportano molte di queste funzionalità.

L'esempio seguente crea un cluster del servizio Web Disack con pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Cluster:
* Usa un servizio di bilanciamento del carico standard. 
* Abilita il ridimensionamento automatico del cluster nel pool di nodi per il cluster.
* Imposta un minimo *di 1* e un massimo *di 3* nodi.

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

## <a name="existing-attached-azure-services"></a>Servizi di Azure collegati esistenti

Quando si esegue la migrazione dei cluster, è possibile che i servizi di Azure esterni sono stati collegati. Anche se i servizi seguenti non richiedono la ricreazione delle risorse, richiederanno l'aggiornamento delle connessioni da cluster precedenti a nuovi per mantenere la funzionalità.

* Registro Azure Container
* Log Analytics
* Application Insights
* Gestione traffico
* Account di archiviazione
* Database esterni

## <a name="ensure-valid-quotas"></a>Verificare le quote valide

Poiché altre macchine virtuali verranno distribuite nella sottoscrizione durante la migrazione, è necessario verificare che le quote e i limiti siano sufficienti per queste risorse. Se necessario, richiedere un aumento della [quota vCPU.](../azure-portal/supportability/per-vm-quota-requests.md)

Potrebbe essere necessario richiedere un aumento delle [quote di rete](../azure-portal/supportability/networking-quota-requests.md) per assicurarsi di non esaurire gli IP. Per altre informazioni, vedere Networking and IP ranges for AKS ( Rete e [intervalli IP per il servizio Web AKS).](./configure-kubenet.md)

Per altre informazioni, vedere Sottoscrizione [di Azure e limiti del servizio](../azure-resource-manager/management/azure-subscription-service-limits.md). Per controllare le quote correnti, nel portale di Azure passare al pannello [sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selezionare la sottoscrizione e quindi selezionare **Utilizzo e quote**.

## <a name="high-availability-and-business-continuity"></a>Disponibilità elevata e continuità aziendale

Se l'applicazione non è in grado di gestire i tempi di inattività, è necessario seguire le procedure consigliate per gli scenari di migrazione a disponibilità elevata. Altre informazioni sulle procedure consigliate per la pianificazione della continuità aziendale complessa, il ripristino di emergenza e l'ottimizzazione del tempo di attività [in servizio Azure Kubernetes (AKS).](./operator-best-practices-multi-region.md)

Per le applicazioni complesse, in genere si eseguirà la migrazione nel tempo anziché in una sola volta, vale a dire che gli ambienti vecchi e nuovi potrebbero dover comunicare in rete. Le applicazioni che in precedenza usano servizi per comunicare potrebbero dover `ClusterIP` essere esposte come tipo `LoadBalancer` e protette in modo appropriato.

Per completare la migrazione, è necessario puntare i client ai nuovi servizi in esecuzione nel servizio AKS. È consigliabile reindirizzare il traffico aggiornando DNS in modo che punti al Load Balancer davanti al cluster del servizio AKS.

[Gestione traffico di Azure](../traffic-manager/index.yml) indirizzare i clienti al cluster Kubernetes desiderato e all'istanza dell'applicazione. Gestione traffico è un servizio di bilanciamento del carico del traffico basato su DNS in grado di distribuire il traffico di rete tra aree. Per ottenere prestazioni e ridondanza ottimali, indirizzare tutto il traffico dell'applicazione tramite Gestione traffico prima che venga passato al cluster del servizio Servizio Web Dick. 

In una distribuzione a più cluster, i clienti devono connettersi a un nome DNS di Gestione traffico che punta ai servizi in ogni cluster del servizio Azure Kubernetes. Definire questi servizi usando gli endpoint di Gestione traffico. Ogni endpoint è l'INDIRIZZO *IP del servizio di bilanciamento del carico.* Usare questa configurazione per indirizzare il traffico di rete dall'endpoint di Gestione traffico in un'area all'endpoint in un'area diversa.

![Servizio AKS con Gestione traffico](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[servizio Frontdoor di Azure](../frontdoor/front-door-overview.md) è un'altra opzione per il routing del traffico per i cluster del servizio AKS. Con servizio Frontdoor di Azure, è possibile definire, gestire e monitorare il routing globale per il traffico Web ottimizzando le prestazioni migliori e il failover globale immediato per la disponibilità elevata. 

### <a name="considerations-for-stateless-applications"></a>Considerazioni per le applicazioni senza stato

La migrazione di applicazioni senza stato è il caso più semplice:
1. Applicare le definizioni delle risorse (YAML o Helm) al nuovo cluster.
1. Assicurarsi che tutto funzioni come previsto.
1. Reindirizzare il traffico per attivare il nuovo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerazioni per le applicazioni con stato

Pianificare con attenzione la migrazione di applicazioni con stato per evitare perdite di dati o tempi di inattività imprevisti.

* Se si usa File di Azure, è possibile montare la condivisione file come volume nel nuovo cluster. Vedere [Montare File di Azure statica come volume](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* Se si usa Azure Managed Disks, è possibile montare il disco solo se non è possibile accedere a qualsiasi macchina virtuale. Vedere [Montare un disco di Azure statico come volume](./azure-disk-volume.md#mount-disk-as-volume).
* Se nessuno di questi approcci funziona, è possibile usare le opzioni di backup e ripristino. Vedere [Velero in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>File di Azure

A differenza dei dischi, i file di Azure possono essere montati simultaneamente da più host. Nel cluster del servizio Azure Kubernetes Azure e Kubernetes non impediscono la creazione di un pod che il cluster del servizio Azure Kubernetes usa ancora. Per evitare la perdita di dati e il comportamento imprevisto, assicurarsi che i cluster non scrivono contemporaneamente agli stessi file.

Se l'applicazione può ospitare più repliche che puntano alla stessa condivisione file, seguire la procedura di migrazione senza stato e distribuire le definizioni YAML nel nuovo cluster. 

In caso contrario, un approccio di migrazione possibile prevede i passaggi seguenti:

1. Verificare che l'applicazione funzioni correttamente.
1. Puntare il traffico in tempo reale al nuovo cluster del servizio AKS.
1. Disconnettere il cluster precedente.

Se si vuole iniziare con una condivisione vuota ed eseguire una copia dei dati di origine, è possibile usare i [`az storage file copy`](/cli/azure/storage/file/copy) comandi per eseguire la migrazione dei dati.


#### <a name="migrating-persistent-volumes"></a>Migrazione di volumi persistenti

Se si esegue la migrazione di volumi persistenti esistenti al server del cluster di controllo di accesso, in genere seguire questa procedura:

1. Inattiva scrive nell'applicazione. 
    * Questo passaggio è facoltativo e richiede tempi di inattività.
1. Creare snapshot dei dischi.
1. Creare nuovi dischi gestiti dagli snapshot.
1. Creare volumi permanenti nel server del cluster di controllo di accesso.
1. Aggiornare le specifiche dei pod [per usare i volumi esistenti](./azure-disk-volume.md) anziché PersistentVolumeClaims (provisioning statico).
1. Distribuire l'applicazione nel servizio AKS.
1. Verificare che l'applicazione funzioni correttamente.
1. Puntare il traffico in tempo reale al nuovo cluster del servizio AKS.

> [!IMPORTANT]
> Se si sceglie di non disattivare le operazioni di scrittura, sarà necessario replicare i dati nella nuova distribuzione. In caso contrario, si mancheranno i dati scritti dopo aver creato gli snapshot del disco.

Alcuni strumenti open source consentono di creare dischi gestiti ed eseguire la migrazione di volumi tra cluster Kubernetes:

* [L'estensione Copia disco dell'interfaccia](https://github.com/noelbundick/azure-cli-disk-copy-extension) della riga di comando di Azure copia e converte i dischi tra gruppi di risorse e aree di Azure.
* [L'estensione dell'interfaccia della riga](https://github.com/yaron2/azure-kube-cli) di comando di Azure Kube enumera i volumi Kubernetes del servizio Azure Kubernetes ed esegue la migrazione a un cluster del servizio Azure Kube.


### <a name="deployment-of-your-cluster-configuration"></a>Distribuzione della configurazione del cluster

È consigliabile usare la pipeline esistente di integrazione continua (CI) e distribuzione continua (CD) per distribuire una configurazione valida nota nel servizio AKS. È possibile usare Azure Pipelines per [compilare e distribuire le applicazioni nel servizio Web Del servizio AKS.](/azure/devops/pipelines/ecosystems/kubernetes/aks-template) Clonare le attività di distribuzione esistenti e assicurarsi che punti al nuovo cluster del servizio `kubeconfig` AKS.

Se non è possibile, esportare le definizioni delle risorse dal cluster Kubernetes esistente e quindi applicarle al servizio App Kubernetes. È possibile usare `kubectl` per esportare gli oggetti.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Spostamento di risorse esistenti in un'altra area

È possibile spostare il cluster del servizio Web del servizio AKS in [un'area diversa supportata dal servizio AKS.][region-availability] È consigliabile creare un nuovo cluster nell'altra area e quindi distribuire le risorse e le applicazioni nel nuovo cluster. 

Inoltre, se si dispone di servizi come [Azure Dev Spaces][azure-dev-spaces] in esecuzione nel cluster del servizio AKS, sarà necessario installare e configurare tali servizi nel cluster nella nuova area.


In questo articolo sono stati riepilogati i dettagli della migrazione per:

> [!div class="checklist"]
> * Servizio Web Del servizio Load Balancer Standard e set di scalabilità di macchine virtuali
> * Servizi di Azure collegati esistenti
> * Verificare le quote valide
> * Disponibilità elevata e continuità aziendale
> * Considerazioni per le applicazioni senza stato
> * Considerazioni per le applicazioni con stato
> * Distribuzione della configurazione del cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
