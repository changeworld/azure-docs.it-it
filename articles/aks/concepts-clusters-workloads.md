---
title: Concetti - Nozioni di base di Kubernetes per il servizio Azure Kubernetes
description: Informazioni sui componenti di base del cluster e del carico di lavoro di Kubernetes e sulle loro relazioni con le funzionalità del servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105936"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Concetti di base di Kubernetes per il servizio Azure Kubernetes

Lo sviluppo di applicazioni continua a passare a un approccio basato su contenitori, aumentando la necessità di orchestrare e gestire le risorse. In qualità di piattaforma leader, Kubernetes offre una pianificazione affidabile dei carichi di lavoro delle applicazioni a tolleranza di errore. Azure Kubernetes Service (AKS), un'offerta Kubernetes gestita, semplifica ulteriormente la distribuzione e la gestione delle applicazioni basate su contenitori.

Questo articolo descrive:
* Componenti principali dell'infrastruttura Kubernetes:
    * *piano di controllo*
    * *nodi*
    * *pool di nodi*
* Risorse del carico di lavoro: 
    * *Baccelli*
    * *distribuzioni*
    * *imposta* 
* Come raggruppare le risorse in *spazi dei nomi*.

## <a name="what-is-kubernetes"></a>Cos'è Kubernetes

Kubernetes è una piattaforma in rapida evoluzione che gestisce applicazioni basate su contenitori e i componenti di rete e archiviazione associati. Kubernetes si concentra sui carichi di lavoro dell'applicazione, non sui componenti dell'infrastruttura sottostanti. Kubernetes offre un approccio dichiarativo alle distribuzioni, supportato da una gamma completa di API per operazioni di gestione.

È possibile compilare ed eseguire applicazioni moderne, portatili e basate su microservizi, usando Kubernetes per orchestrare e gestire la disponibilità dei componenti dell'applicazione. Kubernetes supporta applicazioni sia con stato che senza stato mentre l'adozione delle applicazioni basate su microservizi da parte dei team aumenta.

In quanto piattaforma aperta, Kubernetes consente di compilare le applicazioni con il linguaggio di programmazione, il sistema operativo, le librerie e il bus di messaggistica preferiti. Gli strumenti esistenti di integrazione continua e recapito continuo (CI/CD) possono integrarsi con Kubernetes per la pianificazione e la distribuzione delle versioni.

AKS fornisce un servizio Kubernetes gestito che riduce la complessità delle attività di distribuzione e di gestione di base, ad esempio il coordinamento degli aggiornamenti. La piattaforma Azure gestisce il piano di controllo AKS e paga solo per i nodi AKS che eseguono le applicazioni. AKS è basato sul motore del servizio Kubernetes di Azure open source, ovvero [AKS-Engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Architettura del cluster Kubernetes

Un cluster Kubernetes è suddiviso in due componenti:

- *Piano di controllo*: fornisce i servizi Kubernetes principali e l'orchestrazione dei carichi di lavoro dell'applicazione.
- *Nodi*: eseguire i carichi di lavoro dell'applicazione.

![Componenti del piano e del nodo di controllo Kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Piano di controllo

Quando si crea un cluster AKS, viene creato e configurato automaticamente un piano di controllo. Questo piano di controllo viene fornito gratuitamente come una risorsa di Azure gestita astratta dall'utente. Paghi solo per i nodi collegati al cluster AKS. Il piano di controllo e le relative risorse si trovano solo nell'area in cui è stato creato il cluster.

Il piano di controllo include i componenti Kubernetes principali seguenti:

| Componente | Descrizione |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | Il server API è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes.                                                        |  
| *ETCD* | Per mantenere lo stato del cluster e della configurazione di Kubernetes, il *ETCD* a disponibilità elevata è un archivio di valori chiave all'interno di Kubernetes.                                      |  
| *kube-scheduler*                                                                            | Quando si creano o si ridimensionano le applicazioni, l'utilità di pianificazione determina quali nodi possono eseguire il carico di lavoro e avviarli.                                                                                    |  
| *kube-controller-manager*                                                                            | Gestione controller controlla un numero di controller più piccoli che eseguono azioni come la replica di Pod e la gestione delle operazioni dei nodi.                                                                  |  

AKS fornisce un piano di controllo a tenant singolo con un server API dedicato, un'utilità di pianificazione e così via. Si definiscono il numero e le dimensioni dei nodi e la piattaforma Azure configura la comunicazione protetta tra il piano di controllo e i nodi. L'interazione con il piano di controllo si verifica tramite le API Kubernetes, ad esempio `kubectl` o il dashboard di Kubernetes.

Sebbene non sia necessario configurare componenti (ad esempio un archivio *ETCD* a disponibilità elevata) con questo piano di controllo gestito, non è possibile accedere direttamente al piano di controllo. Gli aggiornamenti del piano e del nodo di controllo Kubernetes vengono orchestrati tramite l'interfaccia della riga di comando di Azure o portale di Azure Per risolvere i problemi possibili, è possibile esaminare i log del piano di controllo tramite i log di monitoraggio di Azure.

Per configurare o accedere direttamente a un piano di controllo, distribuire il proprio cluster Kubernetes usando [AKS-Engine][aks-engine].

Per le procedure consigliate associate, vedere procedure consigliate [per la sicurezza e gli aggiornamenti del cluster in AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nodi e pool di nodi

Per eseguire le applicazioni e i servizi di supporto, è necessario un *nodo* Kubernetes. Un cluster AKS ha almeno un nodo, una macchina virtuale (VM) di Azure che esegue i componenti del nodo Kubernetes e il runtime del contenitore.

| Componente | Descrizione |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | Agente Kubernetes che elabora le richieste di orchestrazione dal piano di controllo e pianifica l'esecuzione dei contenitori richiesti.                                                        |  
| *Kube-proxy* | Gestisce la rete virtuale in ogni nodo. Il proxy instrada il traffico di rete e gestisce gli indirizzi IP per i servizi e i pod.                                      |  
| *runtime del contenitore*                                                                            | Consente l'esecuzione di applicazioni in contenitori e l'interazione con risorse aggiuntive, ad esempio la rete virtuale e l'archiviazione. I cluster AKS che usano i pool di nodi Kubernetes versione 1.19 + usano `containerd` come runtime del contenitore. I cluster AKS che usano Kubernetes prima della versione 1,19 del pool di nodi per i pool di nodi usano [Moby](https://mobyproject.org/) (upstream Docker) come runtime del contenitore.                                                                                    |  


![Macchina virtuale di Azure e risorse di supporto per un nodo di Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Le dimensioni della macchina virtuale di Azure per i nodi definiscono le CPU, la memoria, le dimensioni e il tipo di archiviazione disponibili, ad esempio unità SSD a prestazioni elevate o HDD normale. Pianificare le dimensioni del nodo in modo da determinare se le applicazioni possono richiedere grandi quantità di CPU e memoria o archiviazione a prestazioni elevate. Scalare in orizzontale il numero di nodi nel cluster AKS per soddisfare la domanda.

In AKS l'immagine della macchina virtuale per i nodi del cluster si basa su Ubuntu Linux o Windows Server 2019. Quando si crea un cluster AKS o si aumenta il numero di nodi, la piattaforma Azure crea e configura automaticamente il numero di macchine virtuali richiesto. I nodi agente vengono fatturati come VM standard, quindi vengono applicati automaticamente eventuali sconti per le dimensioni delle VM (incluse le [prenotazioni di Azure][reservation-discounts]).

Distribuire un cluster Kubernetes personalizzato con il [motore AKS][aks-engine] se si usa un sistema operativo host diverso, un runtime contenitore o inclusi pacchetti personalizzati diversi. Le funzionalità di `aks-engine` rilascio upstream e forniscono le opzioni di configurazione prima del supporto nei cluster AKS. Se quindi si vuole usare un runtime del contenitore diverso da `containerd` o [Moby](https://mobyproject.org/), è possibile eseguire `aks-engine` per configurare e distribuire un cluster Kubernetes che soddisfi le esigenze correnti.

### <a name="resource-reservations"></a>Prenotazioni di risorse

AKS usa le risorse del nodo per aiutare la funzione node come parte del cluster. Questo utilizzo può creare una discrepanza tra le risorse totali del nodo e le risorse allocabili in AKS. Tenere presente queste informazioni durante l'impostazione delle richieste e dei limiti per i pod distribuiti dall'utente.

Per trovare le risorse allocabili di un nodo, eseguire:
```kubectl
kubectl describe node [NODE_NAME]
```

Per mantenere le prestazioni e le funzionalità del nodo, AKS riserva risorse in ogni nodo. Man mano che un nodo cresce più in risorse, la prenotazione delle risorse cresce a causa di una maggiore necessità di gestione dei Pod distribuiti dall'utente.

>[!NOTE]
> Usando i componenti aggiuntivi di AKS, ad esempio container Insights (OMS), utilizzerà risorse del nodo aggiuntive.

Sono riservati due tipi di risorse:

- **CPU**  
    La CPU riservata dipende dal tipo di nodo e dalla configurazione del cluster, che può causare una CPU meno allocabile a causa dell'esecuzione di funzionalità aggiuntive.

   | Core CPU nell'host | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube-riservato (millicore)|60|100|140|180|260|420|740|

- **Memoria**  
    La memoria usata da AKS include la somma di due valori.

   1. **`kubelet` DAEMON**   
       Il `kubelet` daemon viene installato in tutti i nodi dell'agente Kubernetes per gestire la creazione e la terminazione del contenitore. 
   
        Per impostazione predefinita, in AKS, `kubelet` daemon dispone della *memoria. disponibile<* regola di rimozione 750Mi, garantendo che un nodo disponga sempre almeno di 750 allocabile in ogni momento. Quando un host è sotto la soglia di memoria disponibile, attiverà il `kubelet` trigger per terminare uno dei pod in esecuzione e liberare memoria nel computer host.

   2. **Una velocità regressiva di prenotazione di memoria** per il daemon kubelet per la corretta funzione (*Kube-reserved*).
      - 25% dei primi 4 GB di memoria
      - 20% dei 4 GB di memoria successivi (fino a 8 GB)
      - 10% dei prossimi 8 GB di memoria (fino a 16 GB)
      - 6% dei 112 GB di memoria successivi (fino a 128 GB)
      - 2% di memoria superiore a 128 GB

Regole di allocazione di memoria e CPU:
* Mantieni i nodi dell'agente integri, inclusi alcuni pod di sistema host critici per l'integrità del cluster. 
* Fare in modo che il nodo segnali una quantità minore di memoria e CPU allocabile rispetto a quando non faceva parte di un cluster Kubernetes. 

Non è possibile modificare le prenotazioni di risorse sopra indicate.

Se, ad esempio, un nodo dispone di 7 GB, verrà segnalato il 34% della memoria non allocabile, inclusa la soglia di rimozione hardware 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Oltre alle prenotazioni per Kubernetes, il sistema operativo node sottostante riserva anche una quantità di risorse di CPU e memoria per mantenere le funzioni del sistema operativo.

Per le procedure consigliate associate, vedere procedure consigliate [per le funzionalità dell'utilità di pianificazione di base in AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pool di nodi

I nodi della stessa configurazione sono raggruppati in *pool di nodi*. Un cluster Kubernetes contiene almeno un pool di nodi. Il numero e la dimensione iniziale dei nodi sono definiti quando si crea un cluster servizio Azure Kubernetes, infatti viene creato un *pool di nodi predefinito*. Questo pool di nodi predefinito in servizio Azure Kubernetes contiene le macchine virtuali sottostanti che eseguono i nodi dell'agente.

> [!NOTE]
> Per garantire un funzionamento affidabile del cluster, è consigliabile eseguire almeno due (2) nodi nel pool di nodi predefinito.

Si ridimensiona o si aggiorna un cluster AKS rispetto al pool di nodi predefinito. È possibile scegliere di ridimensionare o aggiornare un pool di nodi specifico. Per le operazioni di aggiornamento, i contenitori in esecuzione vengono pianificati in altri nodi del pool fino a quando non vengono aggiornati tutti i nodi.

Per altre informazioni su come usare più pool di nodi in AKS, vedere [creare e gestire pool di nodi multipli per un cluster in AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selettori di nodo

In un cluster AKS con più pool di nodi, potrebbe essere necessario indicare all'utilità di pianificazione Kubernetes il pool di nodi da usare per una determinata risorsa. I controller in ingresso, ad esempio, non devono essere eseguiti nei nodi di Windows Server. 

I selettori di nodo consentono di definire vari parametri, come il sistema operativo node, per controllare la posizione in cui deve essere pianificato un pod.

Nell'esempio di base seguente viene pianificata un'istanza NGINX in un nodo Linux usando il selettore di nodo *"beta.kubernetes.io/OS": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Per altre informazioni su come controllare dove sono pianificati i pod, vedere [procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pod

Kubernetes usa i *pod* per eseguire un'istanza dell'applicazione. Un pod rappresenta una singola istanza dell'applicazione. 

I pod hanno in genere un mapping 1:1 con un contenitore. Negli scenari avanzati, un pod può contenere più contenitori. I pod a più contenitori sono pianificati insieme nello stesso nodo e consentono ai contenitori di condividere le risorse correlate.

Quando si crea un pod, è possibile definire *richieste di risorse* per richiedere una determinata quantità di risorse di CPU o di memoria. L'utilità di pianificazione Kubernetes tenta di soddisfare la richiesta pianificando i pod da eseguire in un nodo con risorse disponibili. È anche possibile specificare limiti massimi per le risorse per impedire a un pod di consumare una quantità eccessiva di risorse di calcolo dal nodo sottostante. La procedura consigliata consiste nell'includere i limiti delle risorse per tutti i pod per consentire all'utilità di pianificazione Kubernetes di identificare le risorse necessarie e consentite.

Per altre informazioni, vedere [Pod di Kubernetes][kubernetes-pods] e [Ciclo di vita dei pod di Kubernetes][kubernetes-pod-lifecycle].

Un pod è una risorsa logica, ma i carichi di lavoro dell'applicazione vengono eseguiti nei contenitori. I pod sono in genere risorse monouso e effimere. I pod pianificati singolarmente mancano alcune delle funzionalità Kubernetes di disponibilità elevata e ridondanza. I pod vengono invece distribuiti e gestiti dai *controller* Kubernetes, ad esempio il controller di distribuzione.

## <a name="deployments-and-yaml-manifests"></a>Distribuzioni e manifesti YAML

Una *distribuzione* rappresenta Pod identici gestiti dal controller di distribuzione Kubernetes. Una distribuzione definisce il numero di *repliche* pod da creare. L'utilità di pianificazione di Kubernetes garantisce che i pod aggiuntivi siano pianificati in nodi integri se i pod o i nodi riscontrano problemi.

È possibile aggiornare le distribuzioni per modificare la configurazione dei pod, l'immagine del contenitore utilizzata o la risorsa di archiviazione collegata. Il controller di distribuzione:
* Svuota e termina un determinato numero di repliche.
* Crea le repliche dalla nuova definizione di distribuzione.
* Continua il processo finché tutte le repliche nella distribuzione non vengono aggiornate.

La maggior parte delle applicazioni senza stato in servizio Azure Kubernetes dovrebbe usare il modello di distribuzione anziché pianificare singoli pod. Kubernetes può monitorare l'integrità e lo stato della distribuzione per garantire che il numero di repliche necessario venga eseguito all'interno del cluster. Quando pianificati singolarmente, i Pod non vengono riavviati se si verifica un problema e non vengono ripianificati in nodi integri se il nodo corrente rileva un problema.

Non è necessario interferire con un processo di aggiornamento se l'applicazione richiede un numero minimo di istanze disponibili. I *budget di interruzione del Pod* definiscono il numero di repliche in una distribuzione che possono essere disattivate durante un aggiornamento del nodo o dell'aggiornamento. Se, ad esempio, si dispone di *cinque (5)* repliche nella distribuzione, è possibile definire un'interferenza pod di *4 (quattro)* per consentire l'eliminazione o la pianificazione di una sola replica alla volta. Come per i limiti delle risorse Pod, la procedura consigliata consiste nel definire i budget per l'interferenza dei Pod sulle applicazioni che richiedono che sia sempre presente un numero minimo di repliche.

Le distribuzioni vengono in genere create e gestite con `kubectl create` o `kubectl apply`. Creare una distribuzione definendo un file manifesto nel formato YAML. 

L'esempio seguente crea una distribuzione di base del server Web NGINX. La distribuzione specifica *tre (3)* repliche da creare e richiede che la porta *80* sia aperta nel contenitore. Richieste e limiti relativi alle risorse sono definiti anche per la CPU e la memoria.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

È possibile creare applicazioni più complesse includendo i servizi, ad esempio i servizi di bilanciamento del carico, all'interno del manifesto YAML.

Per altre informazioni, vedere le [distribuzioni Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestione dei pacchetti con Helm

[Helm][helm] viene comunemente usato per gestire le applicazioni in Kubernetes. È possibile distribuire le risorse compilando e usando i *grafici* Helm pubblici esistenti che contengono una versione in pacchetto del codice dell'applicazione e i manifesti YAML di Kubernetes. È possibile archiviare i grafici Helm in locale o in un repository remoto, ad esempio un repository del [grafico Helm di Azure container Registry][acr-helm].

Per usare Helm, installare il client Helm nel computer oppure usare il client Helm nella [Azure cloud Shell][azure-cloud-shell]. Cercare o creare grafici Helm, quindi installarli nel cluster Kubernetes. Per altre informazioni, vedere [Install existing Applications with Helm in AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>Oggetti StatefulSet e DaemonSet

Usando l'utilità di pianificazione Kubernetes, il controller di distribuzione esegue le repliche in qualsiasi nodo disponibile con le risorse disponibili. Sebbene questo approccio possa essere sufficiente per le applicazioni senza stato, il controller di distribuzione non è ideale per le applicazioni che richiedono:
* Convenzione di denominazione permanente o archiviazione. 
* Una replica da esistere in ogni nodo selezionato all'interno di un cluster.

Due risorse di Kubernetes, tuttavia, consentono di gestire questi tipi di applicazioni:

- *StatefulSets* mantiene lo stato delle applicazioni oltre un singolo ciclo di vita del Pod, ad esempio l'archiviazione.
- *Gli elementi daemonset* assicurare un'istanza in esecuzione in ogni nodo, all'inizio del processo di bootstrap di Kubernetes.

### <a name="statefulsets"></a>Oggetti StatefulSet

Lo sviluppo di applicazioni moderne spesso mira per le applicazioni senza stato. Per le applicazioni con stato, come quelle che includono i componenti del database, è possibile usare *StatefulSets*. Analogamente alle distribuzioni, un StatefulSet crea e gestisce almeno un pod identico. Le repliche in un StatefulSet seguono un normale approccio sequenziale per la distribuzione, la scalabilità, l'aggiornamento e la chiusura. La convenzione di denominazione, i nomi di rete e l'archiviazione vengono mantenuti quando le repliche vengono ripianificate con un StatefulSet.

Definire l'applicazione in formato YAML usando `kind: StatefulSet` . A questo punto, il controller StatefulSet gestisce la distribuzione e la gestione delle repliche richieste. I dati vengono scritti nella risorsa di archiviazione permanente fornita da Managed Disks di Azure o File di Azure. Con StatefulSets, l'archiviazione persistente sottostante rimane, anche quando il StatefulSet viene eliminato.

Per altre informazioni, vedere [Oggetti StatefulSet di Kubernetes][kubernetes-statefulsets].

Le repliche in un oggetto StatefulSet sono pianificate ed eseguire su tutti i nodi disponibili in un cluster servizio Azure Kubernetes. Per assicurarsi che almeno un pod nel set venga eseguito in un nodo, usare invece un DaemonSet.

### <a name="daemonsets"></a>Oggetti DaemonSet

Per la raccolta o il monitoraggio di log specifici, potrebbe essere necessario eseguire un pod in tutti i nodi o selezionati. È possibile usare *DaemonSet* per distribuire uno o più POD identici, ma il controller DaemonSet garantisce che ogni nodo specificato esegua un'istanza del Pod.

Il controller DaemonSet può pianificare i pod nei nodi in una delle prime fasi del processo di avvio del cluster, prima che sia avviata l'utilità di pianificazione predefinita di Kubernetes. Questa capacità garantisce che i pod di un oggetto DaemonSet vengono avviati prima che siano pianificati i pod tradizionali in una distribuzione o in un oggetto StatefulSet.

Come gli oggetti StatefulSet, un oggetto DaemonSet viene definito come parte di una definizione YAML usando `kind: DaemonSet`.

Per altre informazioni, vedere [Oggetti DaemonSet di Kubernetes][kubernetes-daemonset].

> [!NOTE]
> Se si usa il [componente aggiuntivo dei nodi virtuali](virtual-nodes-cli.md#enable-virtual-nodes-addon), gli elementi daemonset non creerà i Pod nel nodo virtuale.

## <a name="namespaces"></a>Spazi dei nomi

Le risorse di Kubernetes, ad esempio i pod e le distribuzioni, sono raggruppate logicamente in uno *spazio dei nomi* per dividere un cluster AKS e limitare l'accesso alle risorse per creare, visualizzare o gestire. Ad esempio, è possibile creare spazi dei nomi per separare i gruppi aziendali. Gli utenti possono interagire solo con le risorse all'interno degli spazi dei nomi assegnati.

![Spazi dei nomi di Kubernetes per dividere in modo logico risorse e applicazioni](media/concepts-clusters-workloads/namespaces.png)

Quando si crea un cluster servizio Azure Kubernetes, sono disponibili gli spazi dei nomi seguenti:

| Spazio dei nomi | Descrizione |  
| ----------------- | ------------- |  
| *default*                                                                                 | Quando i pod e le distribuzioni vengono creati per impostazione predefinita quando non viene specificato alcun valore. Negli ambienti più piccoli è possibile distribuire le applicazioni direttamente nello spazio dei nomi predefinito senza creare altre suddivisioni logiche. Quando si interagisce con l'API di Kubernetes, ad esempio `kubectl get pods`, viene usato lo spazio dei nomi predefinito se non ne viene specificato un altro.                                                        |  
| *kube-system* | Dove si trovano le risorse principali, ad esempio le funzionalità di rete come DNS e proxy o il dashboard di Kubernetes. In genere non si distribuiscono le proprie applicazioni in questo spazio dei nomi.                                      |  
| *kube-public*                                                                            | In genere non viene usato, ma può essere usato per visualizzare le risorse nell'intero cluster e può essere visualizzato da qualsiasi utente.                                                                                    |  


Per altre informazioni, vedere [Spazi dei nomi di Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Passaggi successivi

Questo articolo tratta alcuni dei componenti principali di Kubernetes descrivendo come si applicano ai cluster servizio Azure Kubernetes. Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Accesso e identità per Kubernetes/servizio Azure Kubernetes][aks-concepts-identity]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
