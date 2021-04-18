---
title: 'Dimensioni della macchina virtuale: procedure consigliate per le prestazioni & linee guida'
description: Fornisce linee guida sulle dimensioni delle macchine virtuali e procedure consigliate per ottimizzare le prestazioni SQL Server macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600897"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Dimensioni della macchina virtuale: procedure consigliate per le prestazioni per SQL Server nelle macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce indicazioni sulle dimensioni delle macchine virtuali con una serie di procedure consigliate e linee guida per ottimizzare le prestazioni per l'SQL Server in macchine virtuali (VM) di Azure.

Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questa serie di procedure consigliate  per le prestazioni è incentrata su come ottenere le prestazioni migliori per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è meno impegnativo, potrebbe non essere necessaria ogni ottimizzazione consigliata. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.


## <a name="checklist"></a>Elenco di controllo

Esaminare l'elenco di controllo seguente per una breve panoramica delle procedure consigliate per le dimensioni delle macchine virtuali descritte più in dettaglio nel resto dell'articolo: 

- Usare le dimensioni delle macchine virtuali con 4 o più vCPU, ad esempio [Standard_M8-4ms,](/azure/virtual-machines/m-series) [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)o DS12_v2 [o](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) versione successiva. 
- Usare [le dimensioni delle macchine](../../../virtual-machines/sizes-memory.md) virtuali ottimizzate per la memoria per ottenere prestazioni ottimali SQL Server carichi di lavoro. 
- Le serie [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4,](../../../virtual-machines/edv4-edsv4-series.md) [M-](/azure/virtual-machines/m-series)e [Mv2 offrono](../../../virtual-machines/mv2-series.md) il rapporto ottimale tra memoria e vCore richiesto per i carichi di lavoro OLTP. Entrambe le macchine virtuali della serie M offrono il rapporto memoria-vCore più elevato necessario per i carichi di lavoro cruciali e sono ideali anche per data warehouse di lavoro. 
- Si consideri un rapporto memoria-vCore più elevato per i carichi di lavoro cruciali e data warehouse virtuali. 
- Sfruttare le immagini del Marketplace delle macchine virtuali di Azure SQL Server le opzioni di archiviazione e le impostazioni di archiviazione sono configurate per ottenere prestazioni ottimali SQL Server prestazioni. 
- Raccogliere le caratteristiche delle prestazioni del carico di lavoro di destinazione e usarle per determinare le dimensioni appropriate della macchina virtuale per l'azienda.

Per confrontare l'elenco di controllo delle dimensioni della macchina virtuale con gli altri, vedere l'elenco di controllo completo [sulle procedure consigliate per le prestazioni](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Panoramica

Quando si crea un SQL Server in una macchina virtuale di Azure, considerare attentamente il tipo di carico di lavoro necessario. Se si esegue la migrazione di un ambiente esistente, raccogliere [una baseline delle prestazioni](performance-guidelines-best-practices-collect-baseline.md) per determinare SQL Server requisiti delle macchine virtuali di Azure. Se si tratta di una nuova macchina virtuale, creare la nuova macchina SQL Server in base ai requisiti del fornitore. 

Se si sta creando una nuova macchina virtuale SQL Server con una nuova applicazione creata per il cloud, è possibile ridimensionare facilmente la macchina virtuale SQL Server con l'evolversi dei requisiti di dati e utilizzo.
Avviare gli ambienti di sviluppo con la serie D di livello inferiore, la serie B o la serie Av2 e aumentare le dimensioni dell'ambiente nel tempo. 

Usare le SQL Server del marketplace delle macchine virtuali con la configurazione di archiviazione nel portale. In questo modo sarà più semplice creare correttamente i pool di archiviazione necessari per ottenere le dimensioni, le operazioni di I/O al secondo e la velocità effettiva necessarie per i carichi di lavoro. È importante scegliere le macchine virtuali SQL Server che supportano l'archiviazione Premium e la memorizzazione nella cache di Archiviazione Premium. Per altre [informazioni,](performance-guidelines-best-practices-storage.md) vedere l'articolo sull'archiviazione. 

Il minimo consigliato per un ambiente OLTP di produzione è 4 vCore, 32 GB di memoria e un rapporto tra memoria e vCore di 8. Per i nuovi ambienti, iniziare con 4 computer vCore e ridimensionare fino a 8, 16, 32 vCore o più quando cambiano i requisiti di dati e calcolo. Per la velocità effettiva OLTP, SQL Server macchine virtuali con 5000 OPERAZIONI DI I/O al secondo per ogni vCore. 

SQL Server data warehouse e gli ambienti cruciali dovranno spesso essere ridimensionati oltre il rapporto tra 8 memoria e vCore. Per gli ambienti di medie dimensioni, è possibile scegliere un rapporto tra 16 memoria e vCore e un rapporto tra memoria e vCore di 32 per ambienti di dimensioni maggiori data warehouse virtuali. 

SQL Server data warehouse ambienti spesso traggono vantaggio dall'elaborazione parallela di computer di dimensioni maggiori. Per questo motivo, la serie M e la serie Mv2 sono opzioni avanzate per ambienti data warehouse grandi dimensioni.

Usare la configurazione della memoria e della vCPU dal computer di origine come base per la migrazione di un database di SQL Server locale corrente SQL Server nelle macchine virtuali di Azure. Portare la licenza di base in Azure per sfruttare i vantaggi Vantaggio Azure Hybrid [e](https://azure.microsoft.com/pricing/hybrid-benefit/) risparmiare sui costi SQL Server licenze.

## <a name="memory-optimized"></a>Ottimizzate per la memoria

Le [dimensioni delle macchine virtuali ottimizzate per la](../../../virtual-machines/sizes-memory.md) memoria sono una destinazione primaria per SQL Server macchine virtuali e la scelta consigliata da Microsoft. Le macchine virtuali ottimizzate per la memoria offrono rapporti più ampi tra memoria e CPU e opzioni di cache medio-grandi. 

### <a name="m-mv2-and-mdsv2-series"></a>Serie M, Mv2 e Mdsv2

La [serie M offre](/azure/virtual-machines/m-series) conteggi vCore e memoria per alcuni dei carichi di lavoro SQL Server più grandi.  

La [serie Mv2 ha](../../../virtual-machines/mv2-series.md) i conteggi e la memoria vCore più elevati ed è consigliata per i carichi di lavoro cruciali e data warehouse lavoro. Le istanze della serie Mv2 sono dimensioni di macchine virtuali ottimizzate per la memoria che offrono prestazioni di calcolo senza pari per supportare carichi di lavoro e database in memoria di grandi dimensioni con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e analisi in memoria.

Il [Standard_M64ms](/azure/virtual-machines/m-series) ha ad esempio un rapporto tra 28 memoria e vCore.

[La serie Mdsv2 Medium Memory](../../..//virtual-machines/msv2-mdsv2-series.md) è una nuova serie M attualmente [in](https://aka.ms/Mv2MedMemoryPreview) anteprima che offre una gamma di macchine virtuali di Azure a livello di serie M con un'offerta di memoria intermedia. Questi computer sono particolarmente adatti per SQL Server carichi di lavoro con almeno 10 supporto da memoria a vCore fino a 30.

Alcune delle funzionalità della serie M e Mv2 interessanti per le prestazioni di SQL Server includono il supporto per l'archiviazione [Premium](../../../virtual-machines/premium-storage-performance.md) e la memorizzazione nella cache di archiviazione [Premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching) il supporto [per ultra disk](../../../virtual-machines/disks-enable-ultra-ssd.md) e l'accelerazione di [scrittura.](../../../virtual-machines/how-to-enable-write-accelerator.md)

### <a name="edsv4-series"></a>Serie Edsv4

La [serie Edsv4 è progettata](../../../virtual-machines/edv4-edsv4-series.md) per applicazioni a elevato utilizzo di memoria. Queste macchine virtuali hanno una capacità SSD di archiviazione locale di grandi dimensioni, operazioni di I/O al secondo per disco locale, fino a 504 GiB di RAM. La maggior parte di queste macchine virtuali è quasi coerente con 8 GiB di memoria per ogni vCore, ideale per carichi di lavoro SQL Server standard. 

In questo gruppo è disponibile una nuova macchina virtuale [con il Standard_E80ids_v4 che](../../../virtual-machines/edv4-edsv4-series.md) offre 80 vCore, 504 GBs di memoria, con un rapporto memoria-vCore di 6. Questa macchina virtuale è importante [](../../../virtual-machines/isolation.md) perché è isolata, il che significa che è l'unica macchina virtuale in esecuzione nell'host e pertanto è isolata dagli altri carichi di lavoro dei clienti. Questo rapporto tra memoria e vCore è inferiore a quello consigliato per SQL Server, quindi deve essere usato solo se è necessario l'isolamento.

Le macchine virtuali della serie Edsv4 supportano [l'archiviazione Premium](../../../virtual-machines/premium-storage-performance.md)e la [memorizzazione nella cache di Archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2-series 11-15

La [serie DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ha le stesse configurazioni di memoria e disco della serie D precedente. Questa serie ha un rapporto coerente tra memoria e CPU di 7 in tutte le macchine virtuali. Questa è la più piccola della serie ottimizzata per la memoria ed è una buona opzione a basso costo per i carichi di lavoro SQL Server base.

La [serie DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) supporta l'archiviazione [Premium](../../../virtual-machines/premium-storage-performance.md) e la memorizzazione nella cache di Archiviazione [Premium,](../../../virtual-machines/premium-storage-performance.md#disk-caching)che è fortemente consigliata per prestazioni ottimali.

## <a name="general-purpose"></a>Utilizzo generico

Le [dimensioni delle](../../../virtual-machines/sizes-general.md) macchine virtuali per utilizzo generico sono progettate per offrire rapporti bilanciati tra memoria e vCore per carichi di lavoro entry-level più piccoli, ad esempio sviluppo e test, server Web e server di database più piccoli. 

A causa dei rapporti tra memoria e vCore più piccoli con le macchine virtuali per utilizzo generico, è importante monitorare attentamente i contatori delle prestazioni basati sulla memoria per assicurarsi che SQL Server sia in grado di ottenere la memoria cache del buffer di cui ha bisogno. Per altre [informazioni, vedere Baseline delle](performance-guidelines-best-practices-collect-baseline.md#memory) prestazioni di memoria. 

Poiché la raccomandazione iniziale per i carichi di lavoro di produzione è un rapporto tra memoria e vCore di 8, la configurazione minima consigliata per una macchina virtuale per utilizzo generico che esegue SQL Server è 4 vCPU e 32 GB di memoria. 

### <a name="ddsv4-series"></a>Serie Ddsv4

La [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) offre una combinazione equa di vCPU, memoria e disco temporaneo, ma con un supporto da memoria a vCore ridotto. 

Le macchine virtuali Ddsv4 includono una latenza inferiore e una maggiore velocità di archiviazione locale.

Questi computer sono ideali per distribuzioni side-by-side di SQL e app che richiedono l'accesso rapido all'archiviazione temporanea e ai database relazionali di reparto. Esiste un rapporto memoria-vCore standard di 4 in tutte le macchine virtuali di questa serie. 

Per questo motivo, è consigliabile sfruttare il D8ds_v4 come macchina virtuale iniziale in questa serie, con 8 vCore e 32 GB di memoria. Il computer più grande è D64ds_v4, che ha 64 vCore e 256 GBs di memoria.

Le [macchine virtuali serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) supportano [l'archiviazione Premium e](../../../virtual-machines/premium-storage-performance.md) la [memorizzazione nella cache di archiviazione Premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> La [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) non ha il rapporto memoria-vCore di 8 consigliato per i carichi di lavoro SQL Server lavoro. Di conseguenza, prendere in considerazione l'uso di queste macchine virtuali solo per carichi di lavoro di applicazioni e sviluppo più piccoli.

### <a name="b-series"></a>Serie B

Le [dimensioni delle macchine virtuali](../../../virtual-machines/sizes-b-series-burstable.md) serie B burstable sono ideali per i carichi di lavoro che non necessitano di prestazioni coerenti, ad esempio il modello di verifica e i server di applicazioni e sviluppo di dimensioni molto ridotte. 

La maggior parte [delle dimensioni delle](../../../virtual-machines/sizes-b-series-burstable.md) macchine virtuali della serie B burstable ha un rapporto memoria-vCore di 4. Il più grande di questi computer è [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) con 20 vCore e 80 GB di memoria.

Questa serie è univoca perché le app hanno la possibilità di effettuare il **burst** durante l'orario di ufficio con crediti che possono variare in base alle dimensioni del computer. 

Quando i crediti sono esauriti, la macchina virtuale torna alle prestazioni della macchina di base.

Il vantaggio della serie B è il risparmio di calcolo che è possibile ottenere rispetto alle altre dimensioni delle macchine virtuali in altre serie, soprattutto se è necessaria la potenza di elaborazione in tutta la giornata.

Questa serie supporta [l'archiviazione Premium,](../../../virtual-machines/premium-storage-performance.md)ma **non la memorizzazione** [nella cache dell'archiviazione Premium.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> La [serie B burstable](../../../virtual-machines/sizes-b-series-burstable.md) non ha il rapporto memoria-vCore di 8 consigliato per i carichi di lavoro SQL Server burst. Di conseguenza, è consigliabile usare queste macchine virtuali solo per applicazioni, server Web e carichi di lavoro di sviluppo più piccoli.

### <a name="av2-series"></a>Serie Av2

Le macchine virtuali della serie [Av2](../../../virtual-machines/av2-series.md) sono più adatte per carichi di lavoro entry-level come sviluppo e test, server Web a traffico ridotto, database di app di piccole e medie dimensioni e strumenti di verifica.

Solo i [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 vCore e 16 GB di memoria), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCore e 32 GBs di memoria) e il [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCore e 64 GBs di memoria) hanno un buon rapporto memoria-vCore di 8 per queste prime tre macchine virtuali. 

Queste macchine virtuali sono entrambe opzioni buone per macchine di sviluppo e test di SQL Server più piccole. 

L'Standard_A8m_v2 8 [vCore](../../../virtual-machines/av2-series.md) può essere anche un'opzione valida per applicazioni e server Web di piccole dimensioni.

> [!NOTE] 
> La serie Av2 non supporta l'archiviazione Premium e, di conseguenza, non è consigliata per i carichi di lavoro SQL Server di produzione anche con le macchine virtuali con un rapporto tra memoria e vCore pari a 8.

## <a name="storage-optimized"></a>Con ottimizzazione per l'archiviazione

Le [dimensioni delle macchine virtuali ottimizzate per l'archiviazione](../../../virtual-machines/sizes-storage.md) sono per casi d'uso specifici. Queste macchine virtuali sono progettate in modo specifico con velocità effettiva del disco ottimizzata e I/O. 

### <a name="lsv2-series"></a>Serie Lsv2

La [serie Lsv2 offre](../../../virtual-machines/lsv2-series.md) velocità effettiva elevata, bassa latenza e archiviazione NVMe locale. Le macchine virtuali serie Lsv2 sono ottimizzate per l'uso del disco locale nel nodo collegato direttamente alla macchina virtuale invece di usare dischi dati durevoli. 

Queste macchine virtuali sono opzioni avanzate per i carichi di lavoro big data, data warehouse, creazione di report ed ETL. La velocità effettiva elevata e le operazioni di I/O al secondo dell'archiviazione NVMe locale sono un buon caso d'uso per l'elaborazione dei file che verranno caricati nel database e in altri scenari in cui i dati possono essere ricreati dal sistema di origine o da altri repository, ad esempio Archiviazione BLOB di Azure o Azure Data Lake. [Serie Lsv2](../../../virtual-machines/lsv2-series.md) Le macchine virtuali possono anche eseguire il burst delle prestazioni del disco per un massimo di 30 minuti alla volta.

Queste macchine virtuali hanno dimensioni da 8 a 80 vCPU con 8 GiB di memoria per ogni vCPU e ogni 8 vCPU è presente 1,92 TB di unità SSD NVMe. Ciò significa che per la macchina virtuale più grande di questa serie, [L80s_v2](../../../virtual-machines/lsv2-series.md), sono presenti 80 vCPU e 640 BIB di memoria con 10x1,92 TB di spazio di archiviazione NVMe.  Esiste un rapporto coerente tra memoria e vCore di 8 in tutte queste macchine virtuali.

L'archiviazione NVMe è effimera, vale a dire che i dati andranno persi in questi dischi se si dealloca la macchina virtuale o se viene spostata in un host diverso per la correzione del servizio.

Le serie Lsv2 e Ls supportano [l'archiviazione Premium,](../../../virtual-machines/premium-storage-performance.md)ma non la memorizzazione nella cache di archiviazione Premium. La creazione di una cache locale per aumentare le operazioni di I/O al secondo non è supportata. 

> [!WARNING]
> L'archiviazione dei file di dati nell'archiviazione NVMe temporanea può comportare la perdita di dati quando la macchina virtuale viene deallocata. 

## <a name="constrained-vcores"></a>VCore vincolati

I carichi SQL Server ad alte prestazioni spesso necessitano di grandi quantità di memoria, I/O e velocità effettiva senza i conteggi di vCore più elevati. 

La maggior parte dei carichi di lavoro OLTP sono database dell'applicazione guidati da un numero elevato di transazioni più piccole. Con i carichi di lavoro OLTP, solo una piccola quantità di dati viene letta o modificata, ma i volumi di transazioni guidati dai conteggi degli utenti sono molto più elevati. È importante avere a disposizione la memoria SQL Server per memorizzare nella cache i dati a cui si accede di recente per ottenere prestazioni e assicurarsi che le operazioni di lettura fisica possano essere lette rapidamente in memoria. 

Questi ambienti OLTP necessitano di una maggiore quantità di memoria, di archiviazione veloce e della larghezza di banda di I/O necessaria per ottenere prestazioni ottimali. 

Per mantenere questo livello di prestazioni senza i costi SQL Server licenze, Azure offre dimensioni di macchine virtuali con conteggi [vCPU vincolati.](../../../virtual-machines/constrained-vcpu.md) 

Ciò consente di controllare i costi delle licenze riducendo i vCore disponibili mantenendo allo stesso tempo la stessa larghezza di banda di memoria, archiviazione e I/O della macchina virtuale padre.

Il numero di vCPU può essere vincolato a metà-un quarto delle dimensioni della macchina virtuale originale. La riduzione dei vCore disponibili per la macchina virtuale consente di ottenere rapporti più elevati tra memoria e vCore, ma il costo di calcolo rimarrà invariato.

Queste nuove dimensioni di macchine virtuali hanno un suffisso che specifica il numero di vCCU attive per facilitarne l'identificazione. 

Ad esempio, [M64-32ms](../../../virtual-machines/constrained-vcpu.md) richiede solo 32 vCore SQL Server con memoria, I/O e velocità effettiva [degli M64ms](/azure/virtual-machines/m-series) e [M64-16ms](../../../virtual-machines/constrained-vcpu.md) richiede solo 16 vCore.  Anche se [M64-16ms](../../../virtual-machines/constrained-vcpu.md) ha un quarto del costo di licenza SQL Server di M64ms, il costo di calcolo della macchina virtuale sarà lo stesso.

> [!NOTE] 
> - I carichi di lavoro di data warehouse medio-grandi possono comunque trarre vantaggio dalle macchine virtuali [vCore](../../../virtual-machines/constrained-vcpu.md)vincolate, ma i carichi di lavoro data warehouse sono in genere caratterizzati da un minor numero di utenti e processi che indirizzano grandi quantità di dati tramite piani di query eseguiti in parallelo. 
> - Il costo di calcolo, che include le licenze del sistema operativo, rimarrà invariato per la macchina virtuale padre. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli altri articoli di questa serie:
- [Elenco di controllo rapido](performance-guidelines-best-practices-checklist.md)
- [Archiviazione](performance-guidelines-best-practices-storage.md)
- [Raccogliere la baseline](performance-guidelines-best-practices-collect-baseline.md)

Per le procedure consigliate per la sicurezza, vedere [Considerazioni sulla sicurezza SQL Server nelle macchine virtuali di Azure.](security-considerations-best-practices.md)

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md). 
