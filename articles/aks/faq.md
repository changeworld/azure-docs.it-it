---
title: Domande frequenti relative al servizio Azure Kubernetes
description: Questo articolo include le risposte ad alcune domande frequenti sul servizio Azure Kubernetes.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783198"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative al servizio Azure Kubernetes

Questo articolo tratta alcune domande frequenti relative al servizio Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>In quali aree di Azure è attualmente disponibile il servizio Azure Kubernetes?

Per un elenco completo delle aree disponibili, vedere [Aree e disponibilità del servizio Azure Kubernetes e disponibilità][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>È possibile distribuire un cluster del servizio Azure Kubernetes tra più aree?

No. I cluster del servizio Diaks sono risorse a livello di regione e non possono estendersi su più aree. Per le istruzioni su come creare un'architettura che includa più aree, vedere le [procedure consigliate per la continuità aziendale e il ripristino di emergenza][bcdr-bestpractices].

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>È possibile distribuire un cluster del servizio Azure Kubernetes tra zone di disponibilità?

Sì. È possibile distribuire un cluster del servizio Azure Kubernetes in una o più [zone di disponibilità][availability-zones] nelle [aree][az-regions] che le supportano.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>È possibile limitare chi ha accesso al server API Kubernetes?

Sì. Sono disponibili due opzioni per limitare l'accesso al server API:

- Usare gli [intervalli IP autorizzati del server API][api-server-authorized-ip-ranges] per mantenere un endpoint pubblico per il server API ma limitare l'accesso a un set di indirizzi IP attendibili.
- Usare un [cluster privato][private-clusters] per limitare il server API in modo che sia accessibile *solo* dall'interno della rete virtuale.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>È possibile avere dimensioni diverse delle VM in un singolo cluster?

Sì, è possibile usare dimensioni diverse delle macchine virtuali nel cluster del servizio Azure Kubernetes creando [più pool di nodi][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente servizio Azure Kubernetes?

Azure applica automaticamente le patch di sicurezza ai nodi Linux del cluster in base a una pianificazione notturna. È tuttavia necessario assicurarsi che i nodi Linux siano riavviati in base alle esigenze. Sono disponibili diverse opzioni per il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster servizio Azure Kubernetes. Gli aggiornamenti del cluster [bloccano e svuotano i nodi][cordon-drain], quindi portano un nuovo nodo online con l'immagine Ubuntu più recente e una nuova versione della patch o una versione secondaria di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].
- Tramite [l'aggiornamento dell'immagine del nodo](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Nodi di Windows Server

Per i nodi di Windows Server, Windows Update non viene eseguito automaticamente per applicare gli aggiornamenti più recenti. In base a una pianificazione regolare secondo il ciclo di rilascio di Windows Update e per il processo di convalida interno, è consigliabile eseguire un aggiornamento nel cluster e nei pool del nodi di Windows Server nel servizio Azure Kubernetes. Questo processo di aggiornamento crea nodi che eseguono la versione più recente dell'immagine e delle patch di Windows Server e quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [Aggiornare un pool di nodi nel servizio Azure Kubernetes][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?

Il servizio Azure Kubernetes si basa su una serie di risorse dell'infrastruttura di Azure, inclusi i set di scalabilità di macchine virtuali, le reti virtuali e i dischi gestiti. In questo modo è possibile sfruttare molte funzionalità essenziali della piattaforma Azure all'interno dell'ambiente Kubernetes gestito fornito dal servizio Azure Kubernetes. Ad esempio, la maggior parte dei tipi di macchine virtuali di Azure può essere usata direttamente con il servizio Azure Kubernetes ed è possibile usare le prenotazioni di Azure per ricevere automaticamente sconti su tali risorse.

Per rendere disponibile questa architettura, ogni distribuzione del servizio Azure Kubernetes include due gruppi di risorse:

1. Si crea il primo gruppo di risorse. Questo gruppo contiene solo la risorsa del servizio Kubernetes. Il provider di risorse del servizio Azure Kubernetes crea automaticamente il secondo gruppo di risorse durante la distribuzione. Un esempio del secondo gruppo di risorse è *MC_myResourceGroup_myAKSCluster_eastus*. Per informazioni su come specificare il nome di questo secondo gruppo di risorse, vedere la sezione successiva.
1. Il secondo gruppo di risorse, noto come *gruppo di risorse nodo*, contiene tutte le risorse di infrastruttura associate al cluster, come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Per impostazione predefinita, il gruppo di risorse nodo ha un nome simile a *MC_myResourceGroup_myAKSCluster_eastus*. Il servizio AKS elimina automaticamente la risorsa nodo ogni volta che il cluster viene eliminato, quindi deve essere usato solo per le risorse che condividono il ciclo di vita del cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>È possibile specificare un nome personalizzato per il gruppo di risorse nodo del servizio Azure Kubernetes?

Sì. Per impostazione predefinita, il servizio Azure Kubernetes assegna al gruppo di risorse nodo il nome *MC_resourcegroupname_clustername_location*, ma è anche possibile specificare un nome personalizzato.

Per specificare un nome personalizzato per il gruppo di risorse, installare l'estensione [aks-preview][aks-preview-cli] versione *0.3.2* o successiva dell'interfaccia della riga di comando di Azure. Quando si crea un cluster del servizio Servizio Web Diaz usando il [comando az servizio][az-aks-create] WebKs create, usare il parametro e specificare un nome per il gruppo di `--node-resource-group` risorse. Se si [usa un modello di Azure Resource Manager][aks-rm-template] per distribuire un cluster del servizio Azure Kubernetes, è possibile definire il nome del gruppo di risorse usando la proprietà *nodeResourceGroup*.

* Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella propria sottoscrizione.
* È possibile specificare un nome di gruppo di risorse personalizzato solo quando si crea il cluster.

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

* Specificare un gruppo di risorse esistente come gruppo di risorse del nodo.
* Specificare una sottoscrizione diversa per il gruppo di risorse nodo.
* Cambiare il nome del gruppo di risorse nodo dopo la creazione del cluster.
* Specificare i nomi delle risorse gestite nel gruppo di risorse del nodo.
* Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo. Per altre informazioni, vedere la sezione successiva.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>È possibile modificare i tag e altre proprietà delle risorse del servizio Azure Kubernetes nel gruppo di risorse nodo?

La modifica o l'eliminazione di tag creati da Azure e di altre proprietà delle risorse nel gruppo di risorse nodo può generare risultati imprevisti, ad esempio errori di dimensionamento e di aggiornamento. Il modulo di controllo di accesso consente di creare e modificare tag personalizzati creati dagli utenti finali ed è possibile aggiungere tali tag durante la [creazione di un pool di nodi.](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool) Potrebbe essere necessario creare o modificare tag personalizzati da assegnare ad esempio a una business unit o a un centro di costo. A tale scopo, è anche possibile creare criteri di Azure con un ambito nel gruppo di risorse gestite.

Tuttavia, la modifica di eventuali tag creati da **Azure** nelle risorse nel gruppo di risorse del nodo nel cluster del servizio AzureKs è un'azione non supportata, che interrompe l'obiettivo del livello di servizio. Per altre informazioni, vedere [Il servizio Azure Kubernetes offre un contratto di servizio?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta servizio Azure Kubernetes? È possibile aggiungere o rimuovere i controller di ammissione?

Il servizio Azure Kubernetes supporta i seguenti [controller di ammissione][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

Attualmente non è possibile modificare l'elenco di controller di ammissione nel servizio Azure Kubernetes.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>È possibile usare webhook dei controller di ammissione nel servizio Azure Kubernetes?

Sì, è possibile usare webhook dei controller di ammissione nel servizio Azure Kubernetes. È consigliabile escludere gli spazi dei nomi interni del server Delkks, contrassegnati con **l'etichetta del piano di controllo.** Ad esempio, aggiungere quanto segue alla configurazione del webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS firewalls the API server egress so your admission controller webhooks need to be accessible from within the cluster.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>I webhook dei controller di ammissione possono influire sugli spazi dei nomi di kube-system e su quelli interni del servizio Azure Kubernetes?

Per proteggere la stabilità del sistema ed evitare che i controller di ammissione personalizzati influiscano sui servizi interni in kube-system, lo spazio dei nomi del servizio Azure Kubernetes include la funzionalità **Admissions Enforcer**, che esclude automaticamente gli spazi dei nomi di kube-system e quelli interni del servizio Azure Kubernetes. Questo servizio garantisce che i controller di ammissione personalizzati non influiscano sui servizi in esecuzione in kube-system.

Se si ha un caso d'uso critico per una distribuzione obbligatoria in kube-system (scelta non consigliata), che è necessario coprire con il webhook di ammissione personalizzato, è possibile aggiungere l'etichetta o l'annotazione seguente in modo che Admissions Enforcers lo ignori.

Etichetta ```"admissions.enforcer/disabled": "true"``` o annotazione ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in servizio Azure Kubernetes?

Il servizio Azure Kubernetes non è attualmente integrato in modalità nativa in Azure Key Vault. Tuttavia, il [provider di Azure Key Vault per l'archivio di segreti CSI][csi-driver] consente l'integrazione diretta dai pod di Kubernetes ai segreti di Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in servizio Azure Kubernetes?

Sì, i contenitori di Windows Server sono disponibili nel servizio Azure Kubernetes. Per eseguire i contenitori di Windows Server nel servizio Azure Kubernetes, creare un pool di nodi che esegue Windows Server come sistema operativo guest. I contenitori di Windows Server possono usare solo Windows Server 2019. Per iniziare, vedere [Creare un cluster del servizio Azure Kubernetes con un pool di nodi di Windows Server][aks-windows-cli].

Il supporto di Windows Server per il pool di nodi include alcune limitazioni che fanno parte di Windows Server upstream nel progetto Kubernetes. Per altre informazioni su queste limitazioni, vedere [Limitazioni dei contenitori di Windows Server nel servizio Azure Kubernetes][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Il servizio Azure Kubernetes offre un contratto di servizio?

Il servizio AKS offre garanzie di contratto di servizio come funzionalità di componente aggiuntivo facoltativa con [il contratto di servizio relativo al tempo di attività.][uptime-sla] 

Lo SKU gratuito offerto per impostazione predefinita non ha un contratto di servizio *associato,* ma ha un obiettivo del livello di *servizio* del 99,5%. È possibile che si siano osservati problemi di connettività temporanei in caso di aggiornamenti, nodi sottostanti non integri, manutenzione della piattaforma, applicazione che causa il problema del server API con richieste e così via. Se il carico di lavoro non tollera i riavvii del server API, è consigliabile usare il contratto di servizio relativo al tempo di attività.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>È possibile applicare gli sconti per le prenotazioni di Azure ai nodi dell'agente del servizio Azure Kubernetes?

I nodi dell'agente del servizio AzureKs vengono fatturati come macchine virtuali standard di [Azure,][reservation-discounts] quindi se sono state acquistate prenotazioni di Azure per le dimensioni della macchina virtuale in uso nel servizio Azure Online, questi sconti vengono applicati automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>È possibile spostare o eseguire la migrazione del cluster tra tenant di Azure?

Lo spostamento del cluster del servizio Web Diaks tra tenant non è attualmente supportato.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>È possibile spostare o eseguire la migrazione del cluster tra sottoscrizioni?

Lo spostamento di cluster tra sottoscrizioni non è al momento supportato.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>È possibile spostare i cluster del servizio Azure Kubernetes dalla sottoscrizione di Azure corrente a un'altra?

Lo spostamento del cluster del servizio AzureKs e delle relative risorse associate tra sottoscrizioni di Azure non è supportato.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>È possibile spostare le risorse dell'infrastruttura del servizio AKS o del servizio AKS in altri gruppi di risorse o rinominarle?

Non è supportato lo spostamento o la ridenominazione del cluster del servizio App Web e delle risorse associate.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Perché l'eliminazione del cluster richiede molto tempo?

La maggior parte dei cluster viene eliminata in seguito alla richiesta dell'utente. In alcuni casi, in particolare quando i clienti usano un gruppo di risorse personalizzato o eseguono l'eliminazione di attività tra più gruppi di risorse, l'operazione può richiedere più tempo oppure non riesce. Se si verifica un problema con le eliminazioni, verificare che non siano presenti blocchi sul RG, che tutte le risorse esterne al RG siano disassociate dal RG e così via.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Nel caso di pod/distribuzioni con lo stato 'NodeLost' o 'Unknown', è comunque possibile aggiornare il cluster?

È possibile, ma AKS non consiglia questa operazione. Gli aggiornamenti devono essere eseguiti quando lo stato del cluster è noto e integro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se un cluster include uno o più nodi arrestati o con uno stato non integro, è possibile eseguire un aggiornamento?

No, eliminare/rimuovere eventuali nodi in uno stato di errore o rimossi in altro modo dal cluster prima dell'aggiornamento.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Dopo l'eliminazione di un cluster viene visualizzato l'errore `[Errno 11001] getaddrinfo failed`. Perché?

In genere, questo problema è dovuto al fatto che gli utenti hanno uno o più gruppi di sicurezza di rete ancora in uso e associati al cluster.  Rimuoverli e riprovare a eseguire l'eliminazione.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Dopo un aggiornamento, si verificano arresti anomali ciclici dei pod e i probe di idoneità restituiscono un errore. Come si risolve il problema?

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS service principal](./kubernetes-service-principal.md) and [AKS update credentials](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Il cluster funzionava, ma improvvisamente non è possibile effettuare il provisioning di LoadBalancers, montare i PVC e così via?

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS service principal](./kubernetes-service-principal.md)  and [AKS update credentials](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>È possibile ridimensionare il cluster del servizio AKS a zero?
È possibile arrestare completamente un cluster del servizio [AKS in esecuzione,](start-stop-cluster.md)risparmiando sui rispettivi costi di calcolo. È anche possibile scegliere [ `User` ](scale-cluster.md#scale-user-node-pools-to-0) di ridimensionare o ridimensionare automaticamente tutti i pool di nodi o specifici su 0, mantenendo solo la configurazione del cluster necessaria.
Non è possibile ridimensionare direttamente i [pool di nodi di sistema](use-system-pools.md) a zero.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>È possibile usare le API del set di scalabilità di macchine virtuali per il ridimensionamento manuale?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate. Usare le API del servizio Azure Kubernetes (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>È possibile usare set di scalabilità di macchine virtuali per ridimensionare manualmente fino a zero nodi?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate. È possibile usare l'API del servizio Web Disaks per eseguire il ridimensionamento a zero pool di nodi non di sistema o [arrestare invece il cluster.](start-stop-cluster.md)

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>È possibile arrestare o deallocare tutte le VM?

Anche se il servizio AKS dispone di meccanismi di resilienza per la gestione di tale configurazione e il ripristino da tale configurazione, non si tratta di una configurazione supportata. [Arrestare invece il cluster.](start-stop-cluster.md)

## <a name="can-i-use-custom-vm-extensions"></a>È possibile usare estensioni di VM personalizzate?

L'agente di Log Analytics è supportato perché è un'estensione gestita da Microsoft. In caso contrario, il servizio AKS è un servizio gestito e la manipolazione delle risorse IaaS non è supportata. Per installare componenti personalizzati, usare le API e i meccanismi di Kubernetes. Ad esempio, usare DaemonSets per installare i componenti necessari.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Il servizio AKS archivia i dati dei clienti all'esterno dell'area del cluster?

La funzionalità per abilitare l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) dell'area geografica di Asia Pacifico e in Brasile meridionale (stato di San Paolo) dell'area geografica Brasile meridionale. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo.

## <a name="are-aks-images-required-to-run-as-root"></a>È necessario eseguire le immagini del sito Web di AKS come radice?

Ad eccezione delle due immagini seguenti, non è necessario che le immagini del server del supporto di AKS siano eseguite come radice:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Che cos'è Azure CNI trasparente e la modalità bridge?

A partire dalla versione 1.2.0 Azure CNI la modalità trasparente sarà predefinita per le distribuzioni CNI Linux a tenancy singola. La modalità trasparente sostituisce la modalità bridge. In questa sezione verranno illustrate in modo più chiaro le differenze tra entrambe le modalità e quali sono i vantaggi/limitazioni per l'uso della modalità trasparente in Azure CNI.

### <a name="bridge-mode"></a>Modalità bridge

Come suggerisce il nome, la modalità Azure CNI bridge, in modalità "Just-In-Time", creerà un bridge L2 denominato "azure0". Tutte le interfacce della coppia di pod sul lato host `veth` verranno connesse a questo bridge. Di Pod-Pod comunicazione tra macchine virtuali e il traffico rimanente passa attraverso questo bridge. Il bridge in questione è un dispositivo virtuale di livello 2 che da solo non può ricevere o trasmettere nulla a meno che non si associano uno o più dispositivi reali. Per questo motivo, l'eth0 della macchina virtuale Linux deve essere convertita in un bridge subordinato a "azure0". Ciò crea una topologia di rete complessa all'interno della macchina virtuale Linux e, come sintomo, CNI ha dovuto occuparsi di altre funzioni di rete, ad esempio l'aggiornamento del server DNS e così via.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologia in modalità bridge":::

Di seguito è riportato un esempio dell'aspetto della configurazione della route IP in modalità Bridge. Indipendentemente dal numero di pod presenti nel nodo, saranno presenti solo due route. Il primo afferma che tutto il traffico escluso localmente in azure0 verrà passato al gateway predefinito della subnet tramite l'interfaccia con ip "src 10.240.0.4" (che è l'IP primario del nodo) e il secondo che indica "10.20.x.x" spazio pod al kernel per decidere.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Modalità trasparente
La modalità trasparente adotta un approccio diretto alla configurazione della rete Linux. In questa modalità, Azure CNI modificherà le proprietà dell'interfaccia eth0 nella macchina virtuale Linux. Questo approccio minimo di modifica delle proprietà di rete Linux consente di ridurre i complessi problemi relativi ai casi d'angolo che i cluster potrebbero affrontare con la modalità bridge. In modalità trasparente, Azure CNI creare e aggiungere interfacce di coppia di pod sul lato host che `veth` verranno aggiunte alla rete host. La comunicazione da pod a pod tra macchine virtuali è tramite route IP che verranno aggiunti dal CNI. La comunicazione da pod a pod si trova essenzialmente sul livello 3 e il traffico del pod viene instradato dalle regole di routing L3.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologia in modalità trasparente":::

Di seguito è riportata una configurazione di route IP di esempio in modalità trasparente, l'interfaccia di ogni pod otterrà una route statica collegata in modo che il traffico con ip dest come pod verrà inviato direttamente all'interfaccia della coppia lato host del `veth` pod.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Vantaggi della modalità trasparente

- Fornisce una mitigazione per il dns parallelo race condition ed evitare problemi di latenza DNS di 5 secondi senza la necessità di configurare il DNS locale del nodo (è comunque possibile usare il DNS locale del nodo per motivi di `conntrack` prestazioni).
- Elimina la modalità bridge CNI di latenza DNS iniziale di 5 secondi introduce oggi a causa della configurazione del bridge "just-in-time".
- Uno dei casi d'angolo in modalità bridge è che il Azure CNI non può continuare ad aggiornare il server DNS personalizzato elenca gli utenti aggiunti alla rete virtuale o alla scheda di interfaccia di rete. In questo modo il CNI preleva solo la prima istanza dell'elenco di server DNS. Risolto in modalità Trasparente perché CNI non modifica le proprietà eth0. Per altre [informazioni, vedere qui](https://github.com/Azure/azure-container-networking/issues/713).
- Offre una migliore gestione del traffico UDP e la mitigazione per le tempeste di flood UDP quando si verifica il timeout di ARP. In modalità bridge, quando il bridge non conosce un indirizzo MAC del pod di destinazione nella comunicazione tra pod e pod della macchina virtuale, per impostazione predefinita il pacchetto viene indirizzato a tutte le porte. Risolto in modalità trasparente perché non sono presenti dispositivi L2 nel percorso. Per altre [informazioni, vedere qui](https://github.com/Azure/azure-container-networking/issues/704).
- La modalità trasparente offre prestazioni migliori nella comunicazione da pod a pod all'interno della macchina virtuale in termini di velocità effettiva e latenza rispetto alla modalità bridge.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Come evitare problemi di rallentamento dell'impostazione della proprietà delle autorizzazioni quando il volume contiene molti file?

Tradizionalmente, se il pod è in esecuzione come utente non root (operazione che è consigliabile), è necessario specificare un all'interno del contesto di sicurezza del pod in modo che il volume possa essere leggibile e scrivibile dal `fsGroup` pod. Questo requisito è descritto in modo più dettagliato [qui.](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

Un effetto collaterale dell'impostazione è che, ogni volta che viene montato un volume, Kubernetes deve essere in modo ricorsivo e tutti i file e le directory all'interno del volume, con alcune eccezioni indicate di `fsGroup` `chown()` `chmod()` seguito. Ciò si verifica anche se la proprietà del gruppo del volume corrisponde già all'oggetto richiesto e può essere piuttosto costosa per i volumi di grandi dimensioni con un numero elevato di file di piccole dimensioni, con un'operazione che comporta tempi lunghi per l'avvio `fsGroup` dei pod. Questo scenario è stato un problema noto prima della versione 1.20 e la soluzione alternativa consiste nell'impostare l'esecuzione del pod come radice:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Il problema è stato risolto da Kubernetes v1.20. Per altri dettagli, vedere [Kubernetes 1.20: Controllo granulare](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) delle modifiche alle autorizzazioni per i volumi.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
