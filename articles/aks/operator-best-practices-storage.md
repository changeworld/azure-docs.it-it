---
title: Procedure consigliate per l'archiviazione e il backup
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per l'archiviazione, la crittografia dei dati e i backup nel servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104916"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes

Quando si creano e si gestiscono cluster nel servizio Azure Kubernetes, spesso le applicazioni hanno bisogno di risorse di archiviazione. Assicurarsi di comprendere le esigenze di prestazioni e i metodi di accesso di Pod, in modo da poter selezionare la migliore archiviazione per l'applicazione. Le dimensioni del nodo AKS possono avere un effetto sulle opzioni di archiviazione. Pianificare i modi per eseguire il backup e il test del processo di ripristino per l'archiviazione collegata.

Questo articolo sulle procedure consigliate è incentrato sulle considerazioni relative all'archiviazione per gli operatori del cluster. Contenuto dell'articolo:

> [!div class="checklist"]
> * Tipi di archiviazione disponibili.
> * Come ridimensionare correttamente i nodi AKS per le prestazioni di archiviazione.
> * Differenze tra il provisioning dinamico e statico dei volumi.
> * Modi per eseguire il backup e proteggere i volumi di dati.

## <a name="choose-the-appropriate-storage-type"></a>Scelta del tipo di archiviazione appropriato

> **Indicazioni sulle procedure consigliate**
> 
> Comprendere le esigenze dell'applicazione per selezionare la risorsa di archiviazione corretta. Usare l'archiviazione basata su SSD a prestazioni elevate per i carichi di lavoro di produzione. Pianificare l'archiviazione basata sulla rete quando sono necessarie più connessioni simultanee.

Le applicazioni spesso richiedono tipi e velocità di archiviazione diversi. Determinare il tipo di archiviazione più appropriato ponendo le domande seguenti. 
* Le applicazioni necessitano di archiviazione che si connette a singoli Pod?
* Le applicazioni richiedono l'archiviazione condivisa tra più Pod? 
* Archiviazione per l'accesso in sola lettura ai dati
* L'archiviazione verrà usata per scrivere grandi quantità di dati strutturati? 

La tabella seguente descrive i tipi di archiviazione disponibili e le relative funzionalità:

| Caso d'uso | Plug-in volume | Una sola operazione di lettura/scrittura | Molte operazioni di sola lettura | Molte operazioni di lettura/scrittura | Supporto per i contenitori di Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configurazione condivisa       | File di Azure   | Sì | Sì | Sì | Sì |
| Dati di app strutturati        | Dischi di Azure   | Sì | No  | No  | Sì |
| Dati non strutturati, operazioni sui file system | [BlobFuse][blobfuse] | Sì | Sì | Sì | No |

AKS fornisce due tipi principali di archiviazione sicura per i volumi supportati da dischi di Azure o File di Azure. Entrambi usano il crittografia del servizio di archiviazione di Azure predefinito (SSE) che crittografa i dati inattivi. Non è possibile crittografare i dischi usando crittografia dischi di Azure a livello di nodo AKS.

Sia File di Azure che i dischi di Azure sono disponibili nei livelli di prestazioni standard e Premium:

- Dischi *Premium*
    - Supportato da dischi a stato solido (SSD) a prestazioni elevate. 
    - Consigliato per tutti i carichi di lavoro di produzione.
- Dischi *standard*
    - Supportato da normali dischi rotanti (HDD).
    - Valido per l'archiviazione o per i dati a cui si accede raramente.

Comprendere le esigenze di prestazioni e gli schemi di accesso dell'applicazione per scegliere il livello di archiviazione appropriato. Per altre informazioni sulle dimensioni dei dischi gestiti e sui livelli di prestazioni, vedere [Panoramica di Azure Managed Disks][managed-disks].

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Creare e usare classi di archiviazione per definire le esigenze delle applicazioni

Definire il tipo di archiviazione che si vuole usare per *le classi di archiviazione* Kubernetes. Viene quindi fatto riferimento alla classe di archiviazione nel pod o nella specifica di distribuzione. Le definizioni delle classi di archiviazione collaborano per creare la risorsa di archiviazione appropriata e connetterla ai pod. 

Per altre informazioni, vedere [Classi di archiviazione][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Ridimensionare i nodi per le esigenze di archiviazione

> **Indicazioni sulle procedure consigliate**
> 
> Ogni dimensione del nodo supporta un numero massimo di dischi. Dimensioni diverse forniscono quantità diverse di spazio di archiviazione locale e larghezza di banda della rete. Pianificare in modo appropriato le esigenze dell'applicazione per distribuire la dimensione corretta dei nodi.

I nodi AKS vengono eseguiti come diverse dimensioni e tipi di VM di Azure. Ogni dimensione della macchina virtuale fornisce:
* Una quantità diversa di risorse principali, ad esempio CPU e memoria. 
* Numero massimo di dischi che possono essere collegati. 

Le prestazioni dell'archiviazione variano anche tra le dimensioni di macchina virtuale per le operazioni di I/O al secondo massime nei dischi locali e collegati.

Se le applicazioni richiedono dischi di Azure come soluzione di archiviazione, è possibile elaborare strategie per le dimensioni della VM del nodo appropriate. Le funzionalità di archiviazione e gli importi di CPU e memoria svolgono un ruolo fondamentale per la decisione sulle dimensioni della macchina virtuale. 

Ad esempio, anche se le dimensioni delle macchine virtuali *Standard_B2ms* e *Standard_DS2_v2* includono una quantità simile di risorse di CPU e di memoria, le prestazioni di archiviazione potenziali sono diverse:

| Tipo e dimensioni del nodo | vCPU | Memoria (GiB) | Numero massimo di dischi dati | Operazioni di I/O al secondo del disco senza memorizzazione nella cache | Velocità effettiva massima senza memorizzazione nella cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1.920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6.400                  | 96                             |

In questo esempio, il *Standard_DS2_v2* offre il doppio del numero di dischi collegati e tre-quattro volte la quantità di IOPS e la velocità effettiva del disco. Se si confrontano solo le risorse di calcolo di base e i costi confrontati, è possibile che sia stata scelta la *Standard_B2ms* dimensioni della macchina virtuale con scarse prestazioni di archiviazione. 

È opportuno consultare il team di sviluppo delle applicazioni per comprendere le esigenze di capacità e prestazioni di archiviazione. Scegliere quindi la dimensione di macchina virtuale appropriata per i nodi del servizio Azure Kubernetes in modo da soddisfare o superare queste esigenze di prestazioni. Stabilire regolarmente una baseline delle applicazioni per modificare la dimensione di macchina virtuale in base alle necessità.

Per altre informazioni sulle dimensioni di macchina virtuale disponibili, vedere [Dimensioni delle macchine virtuali Linux in Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Effettuare il provisioning dinamico dei volumi

> **Indicazioni sulle procedure consigliate** 
>
> Per ridurre il sovraccarico di gestione e abilitare la scalabilità, evitare di creare e assegnare in modo statico volumi permanenti. Usare invece il provisioning dinamico. Nelle classi di archiviazione definire i criteri di recupero appropriati per ridurre al minimo i costi di archiviazione non necessari dopo l'eliminazione dei pod.

Per aggiungere spazio di archiviazione ai pod, usare i volumi permanenti. I volumi permanenti possono essere creati manualmente o dinamicamente. La creazione manuale di volumi permanenti comporta un sovraccarico di gestione e consente di limitare la scalabilità. Eseguire invece il provisioning di un volume permanente in modo dinamico per semplificare la gestione dell'archiviazione e consentire alle applicazioni di aumentare e ridimensionare in base alle esigenze

![Attestazioni di volume permanente in un cluster del servizio Azure Kubernetes](media/concepts-storage/persistent-volume-claims.png)

Un'attestazione di volume permanente consente di creare dinamicamente le risorse di archiviazione necessarie. I dischi di Azure sottostanti vengono creati come Pod richiesti. Nella definizione Pod richiedere un volume da creare e allegare a un percorso di montaggio designato.

Per i concetti relativi alla creazione dinamica e all'uso dei volumi, vedere [Attestazioni di volume permanente][aks-concepts-storage-pvcs].

Per una dimostrazione di questi volumi in azione, vedere come creare in modo dinamico e usare un volume permanente con [Dischi di Azure][dynamic-disks] o [File di Azure][dynamic-files].

Nell'ambito delle definizioni di classe di archiviazione impostare i criteri *reclaimPolicy* appropriati. Questo reclaimPolicy controlla il comportamento della risorsa di archiviazione di Azure sottostante quando il Pod viene eliminato. La risorsa di archiviazione sottostante può essere eliminata o mantenuta per l'uso futuro dei pod. Impostare reclaimPolicy su *Mantieni* o *Elimina*. 

Comprendere le esigenze dell'applicazione e implementare controlli regolari per l'archiviazione conservata per ridurre al minimo la quantità di spazio di archiviazione non usato e fatturato.

Per altre informazioni sulle opzioni delle classi di archiviazione, vedere [Classi di archiviazione][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteggere ed eseguire il backup dei dati

> **Indicazioni sulle procedure consigliate** 
> 
> Eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero o backup di Azure. Verificare l'integrità e la sicurezza di tali backup.

Quando le applicazioni archiviano e utilizzano dati salvati in modo permanente su dischi o in file, è necessario eseguire regolari backup o snapshot di tali dati. Dischi di Azure supporta l'uso di tecnologie snapshot integrate. Prima di eseguire l'operazione di snapshot, potrebbe essere necessario scaricare le Scritture su disco. [Velero][velero] può eseguire il backup di volumi permanenti insieme a risorse e configurazioni aggiuntive del cluster. Se non è possibile [rimuovere lo stato dall'applicazione][remove-state], eseguire il backup dei dati di volumi permanenti e testare regolarmente le operazioni di ripristino per verificare l'integrità dei dati e i processi necessari.

Comprendere le limitazioni dei diversi approcci ai backup dei dati e la necessità o meno di disattivare i dati prima di creare lo snapshot. Non sempre i backup dei dati consentono di ripristinare l'ambiente applicativo della distribuzione cluster. Per altre informazioni su questi scenari, vedere le [procedure consigliate per continuità aziendale e ripristino di emergenza nel servizio Azure Kubernetes][best-practices-multi-region].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate in particolare le procedure consigliate di archiviazione nel servizio Azure Kubernetes. Per altre informazioni di base sull'archiviazione in Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
