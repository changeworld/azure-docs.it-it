---
title: Domande frequenti relative al servizio Azure Kubernetes
description: Questo articolo include le risposte ad alcune domande frequenti sul servizio Azure Kubernetes.
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 94cbaf417413b3e11071fb8c7237cbb3ac7b9a37
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780349"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative al servizio Azure Kubernetes

Questo articolo tratta alcune domande frequenti relative al servizio Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>In quali aree di Azure è attualmente disponibile il servizio Azure Kubernetes?

Per un elenco completo delle aree disponibili, vedere [Aree e disponibilità del servizio Azure Kubernetes e disponibilità][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>È possibile distribuire un cluster del servizio Azure Kubernetes tra più aree?

No. I cluster AKS sono risorse a livello di area e non possono estendersi in aree. Per le istruzioni su come creare un'architettura che includa più aree, vedere le [procedure consigliate per la continuità aziendale e il ripristino di emergenza][bcdr-bestpractices].

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>È possibile distribuire un cluster del servizio Azure Kubernetes tra zone di disponibilità?

Sì. È possibile distribuire un cluster del servizio Azure Kubernetes in una o più [zone di disponibilità][availability-zones] nelle [aree][az-regions] che le supportano.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>È possibile limitare chi ha accesso al server API Kubernetes?

Sì. Sono disponibili due opzioni per limitare l'accesso al server API:

- Usare gli [intervalli IP autorizzati del server API][api-server-authorized-ip-ranges] per mantenere un endpoint pubblico per il server API ma limitare l'accesso a un set di indirizzi IP attendibili.
- Usare un [cluster privato][private-clusters] per limitare il server API in modo che sia accessibile *solo* dall'interno della rete virtuale.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>È possibile avere dimensioni diverse delle VM in un singolo cluster?

Sì, è possibile usare dimensioni diverse delle macchine virtuali nel cluster del servizio Azure Kubernetes creando [più pool di nodi][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente servizio Azure Kubernetes?

Azure applica automaticamente le patch di sicurezza ai nodi Linux del cluster in base a una pianificazione notturna. Tuttavia, l'utente è responsabile di assicurarsi che questi nodi Linux vengano riavviati in modo obbligatorio. Sono disponibili diverse opzioni per il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster servizio Azure Kubernetes. Gli aggiornamenti del cluster [bloccano e svuotano i nodi][cordon-drain], quindi portano un nuovo nodo online con l'immagine Ubuntu più recente e una nuova versione della patch o una versione secondaria di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].
- Tramite l' [aggiornamento dell'immagine del nodo](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Nodi di Windows Server

Per i nodi di Windows Server, Windows Update non viene eseguito automaticamente per applicare gli aggiornamenti più recenti. In base a una pianificazione regolare secondo il ciclo di rilascio di Windows Update e per il processo di convalida interno, è consigliabile eseguire un aggiornamento nel cluster e nei pool del nodi di Windows Server nel servizio Azure Kubernetes. Questo processo di aggiornamento crea nodi che eseguono la versione più recente dell'immagine e delle patch di Windows Server e quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [Aggiornare un pool di nodi nel servizio Azure Kubernetes][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?

Il servizio Azure Kubernetes si basa su una serie di risorse dell'infrastruttura di Azure, inclusi i set di scalabilità di macchine virtuali, le reti virtuali e i dischi gestiti. In questo modo è possibile sfruttare molte funzionalità essenziali della piattaforma Azure all'interno dell'ambiente Kubernetes gestito fornito dal servizio Azure Kubernetes. Ad esempio, la maggior parte dei tipi di macchine virtuali di Azure può essere usata direttamente con il servizio Azure Kubernetes ed è possibile usare le prenotazioni di Azure per ricevere automaticamente sconti su tali risorse.

Per rendere disponibile questa architettura, ogni distribuzione del servizio Azure Kubernetes include due gruppi di risorse:

1. Si crea il primo gruppo di risorse. Questo gruppo contiene solo la risorsa del servizio Kubernetes. Il provider di risorse del servizio Azure Kubernetes crea automaticamente il secondo gruppo di risorse durante la distribuzione. Un esempio del secondo gruppo di risorse è *MC_myResourceGroup_myAKSCluster_eastus*. Per informazioni su come specificare il nome di questo secondo gruppo di risorse, vedere la sezione successiva.
1. Il secondo gruppo di risorse, noto come *gruppo di risorse nodo*, contiene tutte le risorse di infrastruttura associate al cluster, come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Per impostazione predefinita, il gruppo di risorse nodo ha un nome simile a *MC_myResourceGroup_myAKSCluster_eastus*. AKS Elimina automaticamente la risorsa del nodo ogni volta che il cluster viene eliminato, quindi deve essere usato solo per le risorse che condividono il ciclo di vita del cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>È possibile specificare un nome personalizzato per il gruppo di risorse nodo del servizio Azure Kubernetes?

Sì. Per impostazione predefinita, il servizio Azure Kubernetes assegna al gruppo di risorse nodo il nome *MC_resourcegroupname_clustername_location*, ma è anche possibile specificare un nome personalizzato.

Per specificare un nome personalizzato per il gruppo di risorse, installare l'estensione [aks-preview][aks-preview-cli] versione *0.3.2* o successiva dell'interfaccia della riga di comando di Azure. Quando si crea un cluster AKS usando il comando [AZ AKS create][az-aks-create] , usare il `--node-resource-group` parametro e specificare un nome per il gruppo di risorse. Se si [usa un modello di Azure Resource Manager][aks-rm-template] per distribuire un cluster del servizio Azure Kubernetes, è possibile definire il nome del gruppo di risorse usando la proprietà *nodeResourceGroup*.

* Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella propria sottoscrizione.
* È possibile specificare un nome di gruppo di risorse personalizzato solo quando si crea il cluster.

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:

* Specificare un gruppo di risorse esistente come gruppo di risorse del nodo.
* Specificare una sottoscrizione diversa per il gruppo di risorse nodo.
* Cambiare il nome del gruppo di risorse nodo dopo la creazione del cluster.
* Specificare i nomi delle risorse gestite nel gruppo di risorse del nodo.
* Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo. Per altre informazioni, vedere la sezione successiva.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>È possibile modificare i tag e altre proprietà delle risorse del servizio Azure Kubernetes nel gruppo di risorse nodo?

La modifica o l'eliminazione di tag creati da Azure e di altre proprietà delle risorse nel gruppo di risorse nodo può generare risultati imprevisti, ad esempio errori di dimensionamento e di aggiornamento. AKS consente di creare e modificare tag personalizzati creati dagli utenti finali ed è possibile aggiungere tali tag quando si [Crea un pool di nodi](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Potrebbe essere necessario creare o modificare tag personalizzati da assegnare ad esempio a una business unit o a un centro di costo. Questa operazione può essere eseguita anche creando criteri di Azure con un ambito nel gruppo di risorse gestite.

Tuttavia, la modifica di eventuali **tag creati da Azure** sulle risorse nel gruppo di risorse nodo nel cluster AKS è un'azione non supportata, che interrompe l'obiettivo del livello di servizio (SLO). Per altre informazioni, vedere [Il servizio Azure Kubernetes offre un contratto di servizio?](#does-aks-offer-a-service-level-agreement)

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

Sì, è possibile usare webhook dei controller di ammissione nel servizio Azure Kubernetes. È consigliabile escludere gli spazi dei nomi AKS interni, contrassegnati con l' **etichetta del piano di controllo.** Ad esempio, aggiungere quanto segue alla configurazione del webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS firewall il server API in uscita in modo che i webhook del controller di ammissione debbano essere accessibili dal cluster.

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

AKS fornisce garanzie per il contratto di servizio come funzionalità facoltativa del componente aggiuntivo con contratto di servizio con [tempo di esecuzione][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>È possibile applicare gli sconti per le prenotazioni di Azure ai nodi dell'agente del servizio Azure Kubernetes?

I nodi dell'agente AKS vengono fatturati come macchine virtuali standard di Azure, pertanto se sono state acquistate [prenotazioni di Azure][reservation-discounts] per le dimensioni della macchina virtuale in uso nel servizio contenitore di Azure, tali sconti vengono applicati automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>È possibile spostare o eseguire la migrazione del cluster tra tenant di Azure?

Lo stato di trasferimento del cluster AKS tra i tenant non è al momento supportato.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>È possibile spostare o eseguire la migrazione del cluster tra sottoscrizioni?

Lo spostamento di cluster tra sottoscrizioni non è al momento supportato.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>È possibile spostare i cluster del servizio Azure Kubernetes dalla sottoscrizione di Azure corrente a un'altra? 

Lo stato di trasferimento del cluster AKS e delle risorse associate tra le sottoscrizioni di Azure non è supportato.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>È possibile spostare il cluster AKS o le risorse dell'infrastruttura AKS in altri gruppi di risorse o rinominarli?

Lo stato di trasferimento o ridenominazione del cluster AKS e delle risorse associate non è supportato.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Perché l'eliminazione del cluster richiede molto tempo? 

La maggior parte dei cluster viene eliminata in seguito alla richiesta dell'utente. In alcuni casi, in particolare quando i clienti usano un gruppo di risorse personalizzato o eseguono l'eliminazione di attività tra più gruppi di risorse, l'operazione può richiedere più tempo oppure non riesce. In caso di problemi con le eliminazioni, verificare che non siano presenti blocchi sull'RG, che tutte le risorse esterne all'RG siano dissociate dall'RG e così via.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Nel caso di pod/distribuzioni con lo stato 'NodeLost' o 'Unknown', è comunque possibile aggiornare il cluster?

È possibile, ma AKS non lo consiglia. Gli aggiornamenti devono essere eseguiti quando lo stato del cluster è noto e integro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se un cluster include uno o più nodi arrestati o con uno stato non integro, è possibile eseguire un aggiornamento?

No, eliminare o rimuovere tutti i nodi in uno stato di errore o rimossi in altro modo dal cluster prima di eseguire l'aggiornamento.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Dopo l'eliminazione di un cluster viene visualizzato l'errore `[Errno 11001] getaddrinfo failed`. Perché? 

In genere, questo problema è dovuto al fatto che gli utenti hanno uno o più gruppi di sicurezza di rete ancora in uso e associati al cluster.  Rimuoverli e ritentare l'eliminazione.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Dopo un aggiornamento, si verificano arresti anomali ciclici dei pod e i probe di idoneità restituiscono un errore. Come si risolve il problema?

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS Service Principal](./kubernetes-service-principal.md) e [AKS Update Credentials](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Il mio cluster funzionava, ma improvvisamente non riesce a effettuare il provisioning di LoadBalancers, montare PVC e così via? 

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS Service Principal](./kubernetes-service-principal.md)  e [AKS Update Credentials](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>È possibile ridimensionare il cluster AKS a zero?
È possibile [arrestare completamente un cluster AKS in esecuzione](start-stop-cluster.md), risparmiando sui rispettivi costi di calcolo. Inoltre, è anche possibile scegliere di ridimensionare [o ridimensionare automaticamente tutti i `User` pool di nodi o specifici](scale-cluster.md#scale-user-node-pools-to-0) in 0, mantenendo solo la configurazione del cluster necessaria.
Non è possibile ridimensionare direttamente i [pool di nodi di sistema](use-system-pools.md) a zero.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>È possibile usare le API del set di scalabilità di macchine virtuali per il ridimensionamento manuale?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate. Usare le API del servizio Azure Kubernetes (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>È possibile usare I set di scalabilità di macchine virtuali per scalare manualmente fino a zero nodi?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate. È possibile usare l'API AKS per la scalabilità a zero pool di nodi non di sistema o per [arrestare il cluster](start-stop-cluster.md) .

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>È possibile arrestare o deallocare tutte le VM?

Sebbene AKS includa meccanismi di resilienza per supportare tale configurazione ed eseguire il ripristino da tale configurazione, questa non è una configurazione supportata. [Arrestare invece il cluster](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>È possibile usare estensioni di VM personalizzate?

L'agente di Log Analytics è supportato perché è un'estensione gestita da Microsoft. In caso contrario, AKS è un servizio gestito e la manipolazione delle risorse IaaS non è supportata. Per installare i componenti personalizzati, usare le API e i meccanismi di Kubernetes. Ad esempio, usare gli elementi daemonset per installare i componenti richiesti.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS archivia i dati dei clienti al di fuori dell'area del cluster?

La funzionalità che consente di archiviare i dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sudorientale (Singapore) del Asia Pacifico Geo. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo.

## <a name="are-aks-images-required-to-run-as-root"></a>Le immagini AKS sono necessarie per l'esecuzione come radice?

Ad eccezione delle due immagini seguenti, le immagini AKS non devono essere eseguite come radice:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Che cos'è la modalità trasparente di Azure CNI rispetto alla modalità Bridge?

Da v 1.2.0 Azure CNI avrà la modalità Transparent come predefinita per le distribuzioni CNI di Linux con tenant singolo. La modalità trasparente sostituisce la modalità Bridge. In questa sezione verranno illustrate in dettaglio le differenze relative a entrambe le modalità e quali sono i vantaggi e le limitazioni per l'uso della modalità trasparente in Azure CNI.

### <a name="bridge-mode"></a>Modalità Bridge

Come suggerisce il nome, in modalità Bridge Azure CNI, in un modo "just in Time", creerà un Bridge L2 denominato "azure0". Tutte le interfacce delle coppie Pod lato host `veth` saranno connesse a questo Bridge. Quindi Pod-Pod la comunicazione tra macchine virtuali e il traffico rimanente passa attraverso questo Bridge. Il Bridge in questione è un dispositivo virtuale di livello 2 che non è in grado di ricevere o trasmettere, a meno che non venga associato uno o più dispositivi reali. Per questo motivo, eth0 della VM Linux deve essere convertita in un Bridge subordinato a "azure0". In questo modo viene creata una topologia di rete complessa all'interno della VM Linux e come sintomo CNI ha dovuto occuparsi di altre funzioni di rete come l'aggiornamento del server DNS e così via.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologia in modalità Bridge":::

Di seguito è riportato un esempio di come viene visualizzata la configurazione della route IP in modalità Bridge. Indipendentemente dal numero di Pod del nodo, saranno presenti solo due route. Il primo dice che tutto il traffico, escluso il locale in azure0, viene indirizzato al gateway predefinito della subnet tramite l'interfaccia con IP "src 10.240.0.4" (che è l'IP primario del nodo) e la seconda la parola "10.20. x. x" Pod per il kernel per la decisione del kernel.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Modalità trasparente
La modalità trasparente prevede un approccio semplice per la configurazione della rete Linux. In questa modalità, Azure CNI non modificherà alcuna proprietà dell'interfaccia eth0 nella VM Linux. Questo approccio minimo per modificare le proprietà di rete di Linux consente di ridurre i problemi di casi d'angolo complessi che i cluster potrebbero affrontare con la modalità Bridge. In modalità trasparente, Azure CNI creerà e aggiungerà `veth` le interfacce di coppia pod sul lato host che verranno aggiunte alla rete host. La comunicazione da Pod a Pod tra macchine virtuali avviene tramite route IP che i CNI aggiungono. Essenzialmente la comunicazione da Pod a Pod è basata sul livello 3 e il traffico Pod viene instradato in base alle regole di routing L3.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologia in modalità trasparente":::

Di seguito è riportato un esempio di configurazione della route IP in modalità trasparente. ogni interfaccia del Pod otterrà una route statica collegata in modo che il traffico con l'indirizzo IP di destinazione come Pod venga inviato direttamente all'interfaccia della coppia lato host del Pod `veth` .

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

- Fornisce la mitigazione per la `conntrack` race condition parallela DNS e la prevenzione di problemi di latenza DNS di 5 secondi senza la necessità di configurare il DNS locale del nodo. per motivi di prestazioni, è comunque possibile utilizzare il DNS locale del nodo.
- Elimina la latenza DNS iniziale di 5 secondi che la modalità Bridge CNI introduce oggi grazie alla configurazione del Bridge "just-in-Time".
- Uno dei casi estremi in modalità Bridge è che Azure CNI non è in grado di aggiornare il server DNS personalizzato elenca gli utenti Aggiungi a VNET o NIC. In questo modo, CNI preleva solo la prima istanza dell'elenco di server DNS. Risolto in modalità trasparente perché CNI non modifica alcuna proprietà eth0. Per altre informazioni, vedere [qui](https://github.com/Azure/azure-container-networking/issues/713).
- Fornisce una gestione migliore del traffico UDP e della mitigazione per il Storm Flood di UDP quando si verifica il timeout di ARP. In modalità Bridge, quando Bridge non conosce un indirizzo MAC del pod di destinazione nella comunicazione da Pod a Pod tra macchine virtuali, in base alla progettazione, questa operazione comporta Storm del pacchetto per tutte le porte. Risolto in modalità trasparente perché non sono presenti dispositivi L2 nel percorso. Per altre informazioni, vedere [qui](https://github.com/Azure/azure-container-networking/issues/704).
- La modalità trasparente garantisce prestazioni migliori in termini di velocità effettiva e latenza rispetto alla modalità Bridge tra macchine virtuali intra-pod.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
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
