---
title: Concetti - Sicurezza nei servizi Azure Kubernetes
description: Informazioni sulla sicurezza nel servizio Azure Kubernetes, inclusi comunicazione master e tra nodi, criteri di rete e segreti di Kubernetes.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105307"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes

La sicurezza del cluster protegge i dati dei clienti durante l'esecuzione di carichi di lavoro dell'applicazione in Azure Kubernetes Service (AKS). 

Kubernetes include componenti di sicurezza, ad esempio i *criteri di rete* e i *segreti*. Nel frattempo, Azure include componenti come gruppi di sicurezza di rete e aggiornamenti cluster orchestrati. AKS combina questi componenti di sicurezza per:
* Mantieni il cluster AKS che esegue gli aggiornamenti della sicurezza del sistema operativo più recenti e le versioni Kubernetes.
* Fornire il traffico Pod sicuro e l'accesso alle credenziali riservate.

Questo articolo introduce i principali concetti per proteggere le applicazioni nel servizio Azure Kubernetes:

- [Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Sicurezza master](#master-security)
  - [Sicurezza dei nodi](#node-security)
    - [Isolamento del calcolo](#compute-isolation)
  - [Aggiornare un cluster di Service Fabric](#cluster-upgrades)
    - [Blocco e svuotamento](#cordon-and-drain)
  - [Sicurezza di rete](#network-security)
    - [Gruppi di sicurezza di rete di Azure](#azure-network-security-groups)
  - [Segreti di Kubernetes](#kubernetes-secrets)
  - [Passaggi successivi](#next-steps)

## <a name="master-security"></a>Sicurezza master

In AKS i componenti Master di Kubernetes sono parte del servizio gestito fornito, gestito e gestito da Microsoft. Ogni cluster AKS ha un Master Kubernetes dedicato a tenant singolo per fornire il server API, l'utilità di pianificazione e così via.

Per impostazione predefinita, il server dell'API Kubernetes usa un indirizzo IP pubblico e un nome di dominio completo (FQDN). È possibile limitare l'accesso all'endpoint del server dell'API usando [intervalli IP autorizzati][authorized-ip-ranges]. È anche possibile creare un [cluster completamente privato][private-clusters] per limitare l'accesso del server dell'API alla rete virtuale.

È possibile controllare l'accesso al server API usando il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) e il controllo degli accessi in base al ruolo Per altre informazioni, vedere [Integrazione di Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

## <a name="node-security"></a>Sicurezza dei nodi

I nodi AKS sono macchine virtuali (VM) di Azure che è possibile gestire e gestire. 
* I nodi Linux eseguono una distribuzione Ubuntu ottimizzata usando il `containerd` runtime del contenitore di o Moby. 
* I nodi di Windows Server eseguono una versione ottimizzata di Windows Server 2019 usando il `containerd` runtime del contenitore di o Moby. 

Quando un cluster del servizio Azure Kubernetes viene creato o fatto passare a un piano superiore, i nodi vengono distribuiti automaticamente con le configurazioni e gli aggiornamenti della sicurezza del sistema operativo più recenti.

> [!NOTE]
> Cluster AKS con:
> * Pool di nodi Kubernetes versione 1,19 e utilizzo maggiore `containerd` come runtime del contenitore. 
> * Kubernetes precedenti ai pool di nodi v 1.19 usano [Moby](https://mobyproject.org/) (upstream Docker) come runtime del contenitore.

### <a name="node-security-patches"></a>Patch di sicurezza del nodo

#### <a name="linux-nodes"></a>Nodi Linux
La piattaforma Azure applica automaticamente le patch di sicurezza del sistema operativo ai nodi di Linux durante la notte. Se un aggiornamento della sicurezza del sistema operativo Linux richiede un riavvio dell'host, non verrà riavviato automaticamente. È possibile:
* Riavviare manualmente i nodi Linux.
* Usare [KURED][kured], un daemon di riavvio open source per Kubernetes. KURED viene eseguito come [DaemonSet][aks-daemonsets] e monitora ogni nodo per un file che indica che è necessario riavviare il computer. 

I riavvii sono gestiti all'interno del cluster usando lo stesso [processo di blocco e svuotamento](#cordon-and-drain) come aggiornamento del cluster.

#### <a name="windows-server-nodes"></a>Nodi di Windows Server

Per i nodi di Windows Server, Windows Update non viene eseguito automaticamente e applica gli aggiornamenti più recenti. Pianificare gli aggiornamenti del pool di nodi di Windows Server nel cluster AKS intorno al ciclo di rilascio Windows Update regolare e al processo di convalida. Questo processo di aggiornamento crea nodi che eseguono la versione più recente dell'immagine e delle patch di Windows Server e quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [Aggiornare un pool di nodi nel servizio Azure Kubernetes][nodepool-upgrade].

### <a name="node-deployment"></a>Distribuzione del nodo
I nodi vengono distribuiti in una subnet di rete privata virtuale, senza indirizzi IP pubblici assegnati. Per la risoluzione dei problemi e la gestione, SSH è abilitato per impostazione predefinita e accessibile solo usando l'indirizzo IP interno.

### <a name="node-storage"></a>Archiviazione del nodo
Per fornire spazio di archiviazione, i nodi usano Azure Managed Disks. Per la maggior parte delle dimensioni dei nodi delle VM, Azure Managed Disks sono dischi Premium supportati da SSD a prestazioni elevate. I dati inattivi archiviati nei dischi gestiti vengono automaticamente crittografati all'interno della piattaforma Azure. Per migliorare la ridondanza, i Managed Disks di Azure vengono replicati in modo sicuro nel Data Center di Azure.

### <a name="hostile-multi-tenant-workloads"></a>Carichi di lavoro multi-tenant ostili

Attualmente, gli ambienti Kubernetes non sono sicuri per l'utilizzo di multi-tenant ostili. Funzionalità di sicurezza aggiuntive, ad esempio i *criteri di sicurezza Pod* o il controllo degli accessi in base al ruolo Kubernetes per i nodi, blocca Per una sicurezza reale quando si eseguono carichi di lavoro multi-tenant ostili, è sufficiente considerare attendibile un hypervisor. Il dominio di sicurezza per Kubernetes diventa l'intero cluster, non un singolo nodo. 

Per questi tipi di carichi di lavoro multi-tenant ostili è consigliabile usare cluster fisicamente isolati. Per altre informazioni sui modi per isolare i carichi di lavoro, vedere [procedure consigliate per l'isolamento del cluster in AKS][cluster-isolation].

### <a name="compute-isolation"></a>Isolamento del calcolo

A causa dei requisiti normativi o di conformità, determinati carichi di lavoro possono richiedere un livello elevato di isolamento da altri carichi di lavoro dei clienti. Per questi carichi di lavoro, Azure fornisce [VM isolate](../virtual-machines/isolation.md) da usare come nodi agente in un cluster AKS. Queste macchine virtuali sono isolate a un tipo di hardware specifico e sono dedicate a un singolo cliente. 

Selezionare [una delle dimensioni delle VM isolate](../virtual-machines/isolation.md) come **dimensioni del nodo** durante la creazione di un cluster AKS o l'aggiunta di un pool di nodi.

## <a name="cluster-upgrades"></a>Aggiornamenti dei cluster

Azure fornisce strumenti di orchestrazione dell'aggiornamento per l'aggiornamento di un cluster e di componenti AKS, per garantire la sicurezza e la conformità e per accedere alle funzionalità più recenti. Questa orchestrazione dell'aggiornamento include sia il master che i componenti agente di Kubernetes. 

Per avviare il processo di aggiornamento, specificare una delle [versioni Kubernetes disponibili elencate](supported-kubernetes-versions.md). Azure quindi blocca e svuota in modo sicuro ogni nodo AKS e gli aggiornamenti.

### <a name="cordon-and-drain"></a>Blocco e svuotamento

Durante il processo di aggiornamento, i nodi AKS vengono sottolineati singolarmente dal cluster per impedire che vengano pianificati nuovi pod. I nodi vengono quindi svuotati e aggiornati nel modo seguente:

1.  Un nuovo nodo viene distribuito nel pool di nodi. 
    * Il nodo esegue l'immagine e le patch più recenti del sistema operativo.
1. Uno dei nodi esistenti viene identificato per l'aggiornamento. 
1. I Pod nel nodo identificato vengono terminati normalmente e pianificati negli altri nodi del pool di nodi.
1. Il nodo svuotato viene eliminato dal cluster AKS.
1. I passaggi 1-4 vengono ripetuti fino a quando tutti i nodi non vengono sostituiti correttamente come parte del processo di aggiornamento.

Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade-cluster].

## <a name="network-security"></a>Sicurezza di rete

Per la connettività e sicurezza con le reti locali, è possibile distribuire il cluster del servizio Azure Kubernetes nelle subnet di rete virtuale di Azure esistenti. Queste reti virtuali si connettono alla rete locale usando la VPN da sito a sito di Azure o Express route. Definire i controller di ingresso Kubernetes con indirizzi IP interni privati per limitare l'accesso ai servizi alla connessione di rete interna.

### <a name="azure-network-security-groups"></a>Gruppi di sicurezza di rete di Azure

Per filtrare il flusso del traffico della rete virtuale, Azure usa le regole del gruppo di sicurezza di rete. Queste regole definiscono gli intervalli, le porte e i protocolli IP di origine e destinazione a cui è consentito o negato l'accesso alle risorse. Vengono create regole predefinite per consentire il traffico TLS al server dell'API Kubernetes. È possibile creare servizi con servizi di bilanciamento del carico, mapping delle porte o route di ingresso. AKS modifica automaticamente il gruppo di sicurezza di rete per il flusso del traffico.

Se si fornisce una propria subnet per il cluster AKS, **non modificare il** gruppo di sicurezza di rete a livello di subnet gestito da AKS. In alternativa, creare più gruppi di sicurezza di rete a livello di subnet per modificare il flusso di traffico. Assicurarsi che non interferiscono con il traffico necessario che gestisce il cluster, ad esempio l'accesso al servizio di bilanciamento del carico, la comunicazione con il piano di controllo e l' [uscita][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Criteri di rete di Kubernetes

Per limitare il traffico di rete tra i pod nel cluster, il servizio Azure Kubernetes offre supporto per i [criteri di rete Kubernetes][network-policy]. Con i criteri di rete è possibile consentire o negare percorsi di rete specifici all'interno del cluster in base agli spazi dei nomi e ai selettori di etichette.

## <a name="kubernetes-secrets"></a>Segreti di Kubernetes

Con un *segreto* Kubernetes, è possibile inserire dati sensibili in pod, ad esempio le credenziali di accesso o le chiavi. 
1. Creare un segreto usando l'API Kubernetes. 
1. Definire il pod o la distribuzione e richiedere un segreto specifico. 
    * I segreti vengono forniti solo ai nodi con un pod pianificato che li richiede.
    * Il segreto viene archiviato in *tmpfs*, non scritto su disco. 
1. Quando si elimina l'ultimo POD in un nodo che richiede un segreto, il segreto viene eliminato dal tmpfs del nodo. 
   * I segreti vengono archiviati all'interno di un determinato spazio dei nomi e sono accessibili solo dai pod all'interno dello stesso spazio dei nomi.

L'uso dei segreti riduce le informazioni riservate definite nel manifesto YAML del servizio pod o del servizio. Si richiede invece il segreto archiviato nel server dell'API di Kubernetes come parte del manifesto YAML. Questo approccio fornisce solo l'accesso del pod specifico al segreto. 

> [!NOTE]
> I file manifesto del segreto non elaborato contengono i dati Secret in formato Base64. per ulteriori informazioni, vedere la [documentazione ufficiale][secret-risks] . Considerare questi file come informazioni riservate e non eseguirne mai il commit nel controllo del codice sorgente.

I segreti Kubernetes vengono archiviati in etcd, un archivio chiave-valore distribuito. ETCD Store è completamente gestito da AKS e [i dati vengono crittografati a riposo nella piattaforma Azure][encryption-atrest]. 

## <a name="next-steps"></a>Passaggi successivi

Per acquisire familiarità con la protezione dei cluster del servizio Azure Kubernetes, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade-cluster].

Per le procedure consigliate associate, vedere [Procedure consigliate per la sicurezza e gli aggiornamenti del cluster nel servizio Azure Kubernetes][operator-best-practices-cluster-security] e [Procedure consigliate per la sicurezza dei pod nel servizio Azure Kubernetes][developer-best-practices-pod-security].

Per ulteriori informazioni sui concetti di base di Kubernetes e AKS, vedere:

- [Kubernetes/Cluster e carichi di lavoro del servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Kubernetes/Identità del servizio Azure Kubernetes][aks-concepts-identity]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
