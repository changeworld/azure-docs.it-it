---
title: Dimensioni delle macchine virtuali di Azure-HPC | Microsoft Docs
description: Elenca le diverse dimensioni disponibili per High Performance Computing macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772434"
---
# <a name="high-performance-computing-vm-sizes"></a>Dimensioni delle macchine virtuali di calcolo ad alte prestazioni

Le macchine virtuali della serie H di Azure sono progettate per offrire prestazioni, scalabilità e efficienza dei costi della classe di leadership per diversi carichi di lavoro HPC reali.

[Serie modello HBV3](hbv3-series.md) Le macchine virtuali sono ottimizzate per applicazioni HPC come fluidodinamica, analisi degli elementi finiti esplicita e implicita, modellazione del meteo, elaborazione sismica, simulazione del serbatoio e simulazione RTL. Le macchine virtuali modello HBV3 presentano fino a 120 AMD EPYC™ core CPU della serie 7003 (Milano), 448 GB di RAM e senza hyperthreading. Le macchine virtuali della serie modello HBV3 offrono anche 350 GB al secondo di larghezza di banda di memoria, fino a 32 MB di cache L3 per core, fino a 7 GB/s di prestazioni SSD del dispositivo a blocchi e frequenze di clock fino a 3,675 GHz. 

Tutte le macchine virtuali della serie modello HBV3 includono 200 GB/sec HDR InfiniBand da NVIDIA networking per abilitare i carichi di lavoro MPI con scalabilità a livello di computer. Queste VM sono connesse in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. L'infrastruttura InfiniBand HDR supporta anche il routing adattivo e il trasporto con connessione dinamica (DCT, oltre ai trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e il loro utilizzo è fortemente consigliato.

[Serie HBv2](hbv2-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti e simulazione del serbatoio. Macchine virtuali HBv2 funzionalità 120 processori AMD EPYC 7742 core, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Ogni macchina virtuale HBv2 offre fino a 340 GB al secondo di larghezza di banda di memoria e fino a 4 teraflop di calcolo FP64.

Funzionalità VM HBv2 200 GB/sec Mellanox HDR InfiniBand, mentre le VM serie HB e HC includono 100 GB/sec Mellanox EDR InfiniBand. Ognuno di questi tipi di VM è connesso in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. Le macchine virtuali HBv2 supportano il routing adattivo e il trasporto con connessione dinamica (DCT, oltre ai trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e il loro utilizzo è fortemente consigliato.

[Serie HB](hb-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti espliciti e modellazione meteorologica. Macchine virtuali HB funzionalità 60 processori AMD EPYC 7551 core, 4 GB di RAM per core CPU e senza hyperthreading. La piattaforma EPYC AMD offre più di 260 GB al secondo di larghezza di banda di memoria.

[Serie HC](hc-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate su un calcolo denso, ad esempio l'analisi implicita degli elementi finiti, le dinamiche molecolari e la chimica computazionale. Macchine virtuali HC funzionalità 44 Core processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta il ricco ecosistema di strumenti software di Intel, ad esempio la libreria del kernel matematico di Intel.

[Serie H](h-series.md) Le macchine virtuali sono ottimizzate per le applicazioni basate su frequenze di CPU elevate o memoria di grandi dimensioni per i requisiti di base. Le macchine virtuali serie H dispongono di core del processore Intel Xeon E5 2667 V3, 7 o 14 GB di RAM per core CPU e senza hyperthreading. Le funzionalità della serie H 56 GB/sec Mellanox FDR InfiniBand in una configurazione di albero FAT non bloccante per prestazioni RDMA coerenti. Le macchine virtuali serie H supportano Intel MPI 5. x e MS-MPI.

> [!NOTE]
> Tutte le VM della serie modello HBV3, HBv2, HB e HC hanno accesso esclusivo ai server fisici. Esiste una sola macchina virtuale per ogni server fisico e non è disponibile una condivisione multi-tenant condivisa con altre macchine virtuali per queste dimensioni di VM.

> [!NOTE]
> Le [macchine virtuali a8-a11](./sizes-previous-gen.md#a-series---compute-intensive-instances) sono ritirate al 3/2021. Non sono ora disponibili nuove distribuzioni di macchine virtuali di queste dimensioni. Se si dispone di macchine virtuali esistenti, fare riferimento alle notifiche tramite posta elettronica per i passaggi successivi, inclusa la migrazione ad altre dimensioni di VM nella [Guida alla migrazione HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Istanze con supporto per RDMA

La maggior parte delle dimensioni delle VM HPC dispone di un'interfaccia di rete per la connettività di accesso diretto a memoria remota (RDMA). Anche le dimensioni selezionate della [serie N](./nc-series.md) designate con ' r ' sono compatibili con RDMA. Questa interfaccia è aggiunta all'interfaccia di rete Ethernet standard di Azure disponibile nelle altre dimensioni delle macchine virtuali.

Questa interfaccia secondaria consente alle istanze con supporto per RDMA di comunicare attraverso una rete InfiniBand (IB), operando a frequenze HDR per modello HBV3, HBv2, tariffe EDR per le tariffe HB, HC, NDv2 e FDR per H16r, H16mr e altre macchine virtuali serie N con supporto per RDMA. Queste funzionalità RDMA possono migliorare la scalabilità e le prestazioni delle applicazioni basate su MPI (Message Passing Interface).

> [!NOTE]
> **Supporto SR-IOV**: in Azure HPC, attualmente sono disponibili due classi di macchine virtuali a seconda che siano abilitate SR-IOV per InfiniBand. Attualmente, quasi tutte le macchine virtuali abilitate per la generazione, RDMA o InfiniBand in Azure sono abilitate per SR-IOV ad eccezione di H16r, H16mr e NC24r.
> RDMA è abilitato solo sulla rete InfiniBand (IB) ed è supportato per tutte le VM con supporto per RDMA.
> IP over IB è supportato solo nelle VM abilitate per SR-IOV.
> RDMA non è abilitato sulla rete Ethernet.

- **Sistema operativo** : le distribuzioni Linux, ad esempio CentOS, RHEL, Ubuntu e SUSE, sono comunemente usate. Windows Server 2016 e versioni successive sono supportati in tutte le VM della serie HPC. Windows Server 2012 R2 e Windows Server 2012 sono supportati anche nelle VM non abilitate per SR-IOV. Si noti che [Windows Server 2012 R2 non è supportato in HBv2 come dimensioni delle VM con più di 64 core (virtuali o fisici)](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Vedere [Immagini VM](./workloads/hpc/configure.md) per un elenco di immagini di VM supportate nel Marketplace e come è possibile configurarle in modo appropriato. Le rispettive pagine delle dimensioni della VM elencano anche il supporto dello stack software.

- **InfiniBand e driver** : per le macchine virtuali abilitate per InfiniBand, i driver appropriati sono necessari per abilitare RDMA. Vedere [Immagini VM](./workloads/hpc/configure.md) per un elenco di immagini di VM supportate nel Marketplace e come è possibile configurarle in modo appropriato. Vedere anche [Abilitazione di InfiniBand](./workloads/hpc/enable-infiniband.md) per informazioni sulle estensioni VM o sull'installazione manuale di driver InfiniBand.

- **MPI** : le dimensioni delle VM abilitate per SR-IOV in Azure consentono di usare praticamente qualsiasi versione di MPI con Mellanox OFED. Nelle VM abilitate per non SR-IOV, le implementazioni MPI supportate usano l'interfaccia di Microsoft Network Direct (ND) per la comunicazione tra le macchine virtuali. Di conseguenza, sono supportati solo Intel MPI 5. x e Microsoft MPI (MS-MPI) 2012 R2 o versioni successive. Le versioni più recenti della libreria di runtime Intel MPI possono essere o meno compatibili con i driver RDMA di Azure. Per altri dettagli sulla configurazione di MPI nelle VM HPC in Azure, vedere [Setup MPI for HPC](./workloads/hpc/setup-mpi.md) .

  > [!NOTE]
  > **Spazio di indirizzi della rete RDMA**: la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.

## <a name="cluster-configuration-options"></a>Opzioni di configurazione del cluster

Azure offre diverse opzioni per la creazione di cluster di VM HPC in grado di comunicare tramite la rete RDMA, tra cui: 

- **Macchine virtuali**  : distribuire le VM HPC con supporto per RDMA nello stesso set di scalabilità o set di disponibilità (quando si usa il modello di distribuzione Azure Resource Manager). Se si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud.

- **Set di scalabilità di macchine virtuali** : in un set di scalabilità di macchine virtuali, assicurarsi di limitare la distribuzione a un singolo gruppo di posizionamento per la comunicazione InfiniBand all'interno del set di scalabilità. In un modello di Resource Manager, ad esempio, impostare la proprietà `singlePlacementGroup` su `true`. Si noti che la dimensione massima del set di scalabilità che può essere riattivata con `singlePlacementGroup=true` è limitata alle vm 100 per impostazione predefinita. Se le esigenze di scalabilità dei processi HPC sono superiori a 100 VM in un singolo tenant, è possibile richiedere un aumento, [aprire una richiesta di assistenza clienti online](../azure-portal/supportability/how-to-create-azure-support-request.md) senza alcun addebito. Il limite per il numero di macchine virtuali in un singolo set di scalabilità può essere aumentato a 300. Si noti che quando si distribuiscono macchine virtuali usando i set di disponibilità, il limite massimo è di 200 VM per set di disponibilità.

  > [!NOTE]
  > **MPI tra macchine virtuali**: se è necessario RDMA (ad esempio, usando la comunicazione MPI) tra macchine virtuali (VM), assicurarsi che le VM si trovino nello stesso set di scalabilità di macchine virtuali o nel set di disponibilità.

- **Azure CycleCloud** : creare un cluster HPC usando [Azure CycleCloud](/azure/cyclecloud/) per eseguire i processi MPI.

- **Azure batch** creare un pool di [Azure batch](../batch/index.yml) per eseguire carichi di lavoro MPI. Per usare istanze a elevato uso di calcolo quando si eseguono applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) include un ambiente di runtime per MS-MPI che usa la rete RDMA di Azure in caso di distribuzione in VM Linux con supporto per RDMA. Per le distribuzioni di esempio, vedere [configurare un cluster Linux RDMA con HPC Pack per eseguire applicazioni MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

- **Sottoscrizione di Azure**: per distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

- **Prezzi e disponibilità** : controllare la [disponibilità](https://azure.microsoft.com/global-infrastructure/services/) e i [prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) per aree di Azure.

- **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure rispetto al valore predefinito. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di macchina virtuale, inclusa la serie H. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../azure-portal/supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

  > [!NOTE]
  > Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.
  
- **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molte distribuzioni è tuttavia necessaria almeno una rete virtuale di Azure basata sul cloud. Per l'accesso alle risorse locali, è necessaria anche una connessione da sito a sito. Quando è necessaria, creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una VM a elevato uso di calcolo a una rete virtuale in un gruppo di affinità non è supportata.

- **Ridimensionamento: a** causa dell'hardware specifico, è possibile ridimensionare solo le istanze a elevato utilizzo di calcolo all'interno della stessa famiglia di dimensioni (serie H o serie N). Ad esempio, è possibile ridimensionare una VM della serie H solo da una dimensione della serie H a un'altra. Per alcune macchine virtuali, potrebbe essere necessario prendere in considerazione ulteriori considerazioni sul supporto dei driver InfiniBand e sui dischi NVMe.


## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [configurazione delle macchine virtuali](./workloads/hpc/configure.md), sull' [Abilitazione di InfiniBand](./workloads/hpc/enable-infiniband.md), sulla configurazione di [MPI](./workloads/hpc/setup-mpi.md) e sull'ottimizzazione di applicazioni HPC per Azure in [carichi di lavoro HPC](./workloads/hpc/overview.md).
- Vedere [Panoramica della serie modello HBV3](./workloads/hpc/hbv3-series-overview.md) e [Panoramica della serie HC](./workloads/hpc/hc-series-overview.md).
- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
