---
title: 'Dimensioni macchina virtuale: procedure consigliate per le prestazioni & linee guida'
description: Fornisce le linee guida e le procedure consigliate per ottimizzare le prestazioni della SQL Server in una macchina virtuale (VM) di Azure.
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
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572503"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Dimensioni macchina virtuale: procedure consigliate per le prestazioni per SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce indicazioni sulle dimensioni delle VM, una serie di procedure consigliate e linee guida per ottimizzare le prestazioni per la SQL Server in macchine virtuali (VM) di Azure.

Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questa serie di procedure consigliate per le prestazioni è incentrata su come ottenere le *migliori* prestazioni per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è meno impegnativo, potrebbe non essere necessario ogni ottimizzazione consigliata. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.


## <a name="checklist"></a>Elenco di controllo

Esaminare l'elenco di controllo seguente per una breve panoramica delle procedure consigliate per le dimensioni delle macchine virtuali che il resto dell'articolo illustra in modo più dettagliato: 

- Usare le dimensioni delle macchine virtuali con 4 o più vCPU come [Standard_M8-4ms](/../../virtual-machines/m-series), il [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)o il [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) o superiore. 
- Usare le dimensioni delle macchine virtuali con ottimizzazione per la [memoria](../../../virtual-machines/sizes-memory.md) per ottimizzare le prestazioni dei carichi di lavoro SQL Server. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), serie [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , [M-](../../../virtual-machines/m-series.md)e la serie [Mv2](../../../virtual-machines/mv2-series.md) offrono il rapporto ottimale tra memoria e vCore richiesto per i carichi di lavoro OLTP. Entrambe le VM serie M offrono il rapporto massimo tra memoria e vCore richiesto per i carichi di lavoro mission-critical e sono ideali anche per carichi di lavoro data warehouse. 
- Prendere in considerazione un rapporto più elevato tra memoria e vCore per carichi di lavoro mission-critical e data warehouse. 
- Usare le immagini del Marketplace per macchine virtuali di Azure perché le impostazioni di SQL Server e le opzioni di archiviazione sono configurate per ottimizzare le prestazioni di SQL Server. 
- Raccogliere le caratteristiche di prestazioni del carico di lavoro di destinazione e usarle per determinare le dimensioni appropriate della macchina virtuale per l'azienda.

Per confrontare l'elenco di controllo delle dimensioni delle VM con gli altri, vedere l' [elenco di controllo delle procedure consigliate](performance-guidelines-best-practices-checklist.md)per le prestazioni. 

## <a name="overview"></a>Panoramica

Quando si crea un SQL Server in una macchina virtuale di Azure, valutare attentamente il tipo di carico di lavoro necessario. Se si esegue la migrazione di un ambiente esistente, [raccogliere una baseline](performance-guidelines-best-practices-collect-baseline.md) per le prestazioni per determinare la SQL Server nei requisiti delle macchine virtuali di Azure. Se si tratta di una nuova macchina virtuale, creare la nuova macchina virtuale SQL Server in base ai requisiti del fornitore. 

Se si crea una nuova macchina virtuale SQL Server con una nuova applicazione compilata per il cloud, è possibile ridimensionare facilmente la macchina virtuale SQL Server Man mano che i dati e i requisiti di utilizzo si evolvono.
Avviare gli ambienti di sviluppo con la serie D di livello inferiore, la serie B o AV2 e aumentare l'ambiente nel tempo. 

Usare le immagini del Marketplace SQL Server VM con la configurazione di archiviazione nel portale. Questo renderà più semplice creare correttamente i pool di archiviazione necessari per ottenere le dimensioni, le operazioni di IOPS e la velocità effettiva necessarie per i carichi di lavoro. È importante scegliere SQL Server VM che supportano archiviazione Premium e la memorizzazione nella cache di archiviazione Premium. Per altre informazioni, vedere l'articolo [archiviazione](performance-guidelines-best-practices-storage.md) . 

Il valore minimo consigliato per un ambiente OLTP di produzione è 4 vCore, 32 GB di memoria e un rapporto tra memoria e vCore pari a 8. Per i nuovi ambienti, iniziare con 4 macchine vCore e scalare fino a 8, 16, 32 Vcore o più quando i dati e i requisiti di calcolo cambiano. Per la velocità effettiva OLTP, usare come destinazione SQL Server VM con 5000 IOPS per ogni vCore. 

SQL Server data warehouse e gli ambienti mission-critical dovranno spesso essere ridimensionati oltre il rapporto 8 tra memoria e vCore. Per gli ambienti di medie dimensioni, è possibile scegliere un rapporto tra memoria e vCore e un rapporto 32 tra memoria e vCore per ambienti data warehouse più grandi. 

SQL Server ambienti data warehouse spesso traggono vantaggio dall'elaborazione parallela di computer più grandi. Per questo motivo, la serie M e la serie Mv2 sono opzioni complesse per ambienti data warehouse più grandi.

Usare la configurazione vCPU e Memory dal computer di origine come base per la migrazione di un database di SQL Server locale corrente per SQL Server in macchine virtuali di Azure. Usa la tua licenza Core in Azure per sfruttare i vantaggi del [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) e risparmiare sui costi SQL Server delle licenze.

## <a name="memory-optimized"></a>Ottimizzate per la memoria

Le [dimensioni delle macchine virtuali con ottimizzazione](../../../virtual-machines/sizes-memory.md) per la memoria rappresentano una destinazione primaria per le macchine virtuali SQL Server e la scelta consigliata da Microsoft. Le macchine virtuali con ottimizzazione per la memoria offrono un rapporto più elevato tra memoria e CPU e opzioni di cache medio-grandi. 

### <a name="m-mv2-and-mdsv2-series"></a>Serie M, Mv2 e Mdsv2

La [serie M](../../../virtual-machines/m-series.md) offre conteggi di vCore e memoria per alcuni dei più grandi SQL Server carichi di lavoro.  

La [serie Mv2](../../../virtual-machines/mv2-series.md) presenta i conteggi e la memoria vCore più elevati ed è consigliata per carichi di lavoro mission-critical e data warehouse. Le istanze della serie Mv2 sono dimensioni di VM con ottimizzazione per la memoria che offrono prestazioni di calcolo ineguagliabili per supportare database in memoria di grandi dimensioni e carichi di lavoro con un rapporto elevato tra memoria e CPU, ideale per server di database relazionali, cache di grandi dimensioni e analisi in memoria.

Il [Standard_M64ms](../../../virtual-machines/m-series.md) ha un rapporto da 28 a vCore, ad esempio.

[Mdsv2 Media Memory Series](../../..//virtual-machines/msv2-mdsv2-series.md) è una nuova serie m attualmente in [Anteprima](https://aka.ms/Mv2MedMemoryPreview) che offre una gamma di macchine virtuali di Azure a livello di serie m con un'offerta di memoria MidTier. Questi computer sono particolarmente adatti per carichi di lavoro di SQL Server con un minimo di 10 vCore di memoria fino a 30.

Alcune delle funzionalità della serie M e Mv2 sono attraenti per le prestazioni SQL Server includono [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md) e supporto per la [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching) , supporto di più [dischi](../../../virtual-machines/disks-enable-ultra-ssd.md) e [accelerazione in scrittura](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Serie Edsv4

La [serie Edsv4](../../../virtual-machines/edv4-edsv4-series.md) è progettata per applicazioni con utilizzo intensivo della memoria. Queste VM hanno una capacità SSD di archiviazione locale di grandi dimensioni, un numero elevato di IOPS del disco locale, fino a 504 GiB di RAM. Sono presenti 8 GiB di memoria quasi coerenti per ogni vCore per la maggior parte di queste macchine virtuali, ideale per carichi di lavoro SQL Server standard. 

In questo gruppo è presente una nuova macchina virtuale con la [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) che offre 80 vcore, 504 GB di memoria, con un rapporto tra memoria e vCore pari a 6. Questa macchina virtuale è rilevante perché è [isolata](../../../virtual-machines/isolation.md) , ovvero è garantita l'unica macchina virtuale in esecuzione nell'host e pertanto è isolata da altri carichi di lavoro dei clienti. Questo ha un rapporto tra memoria e vCore inferiore rispetto a quello consigliato per SQL Server, quindi deve essere usato solo se è necessario l'isolamento.

Le macchine virtuali della serie Edsv4 supportano [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md)e la [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2-series 11-15

La [serie DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) presenta le stesse configurazioni di memoria e disco della serie D precedente. Questa serie presenta un rapporto coerente tra memoria e CPU di 7 tra tutte le macchine virtuali. Questa è la più piccola delle serie ottimizzate per la memoria ed è un'ottima opzione a basso costo per i carichi di lavoro di SQL Server a livello di voce.

La [serie DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) supporta l' [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md) e la [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), che è fortemente consigliata per prestazioni ottimali.

## <a name="general-purpose"></a>Scopo generico

Le [dimensioni delle macchine virtuali per utilizzo generico](../../../virtual-machines/sizes-general.md) sono progettate per fornire rapporti bilanciati tra memoria e vCore per carichi di lavoro a livello di voce più piccoli, ad esempio sviluppo e test, server Web e server di database più piccoli. 

Dato che i rapporti tra memoria e vCore sono inferiori rispetto alle macchine virtuali per utilizzo generico, è importante monitorare attentamente i contatori delle prestazioni basati sulla memoria per assicurarsi che SQL Server sia in grado di ottenere la memoria cache del buffer necessaria. Per ulteriori informazioni, vedere [baseline Performance Memory](performance-guidelines-best-practices-collect-baseline.md#memory) . 

Poiché la raccomandazione iniziale per i carichi di lavoro di produzione è un rapporto tra memoria e vCore pari a 8, la configurazione minima consigliata per una macchina virtuale per utilizzo generico che esegue SQL Server è 4 vCPU e 32 GB di memoria. 

### <a name="ddsv4-series"></a>Serie Ddsv4

La [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) offre una combinazione equa di vCPU, memoria e disco temporaneo, ma con supporto più piccolo per la memoria a vCore. 

Le macchine virtuali Ddsv4 includono latenza più bassa e archiviazione locale ad alta velocità.

Questi computer sono ideali per le distribuzioni di applicazioni e SQL affiancate che richiedono l'accesso rapido ai database relazionali di archiviazione temporanea e di reparto. In tutte le macchine virtuali di questa serie è presente un rapporto tra memoria e vCore standard di 4. 

Per questo motivo, è consigliabile usare la D8ds_v4 come macchina virtuale iniziale in questa serie, con 8 Vcore e 32 GB di memoria. Il computer più grande è il D64ds_v4, che include 64 Vcore e 256 GB di memoria.

Le macchine virtuali della [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) supportano [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md) e [caching archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> La [serie Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) non ha un rapporto tra memoria e vCore pari a 8 consigliato per i carichi di lavoro SQL Server. In questo modo, è possibile considerare l'uso di queste macchine virtuali solo per carichi di lavoro di sviluppo e applicazioni più piccoli.

### <a name="b-series"></a>Serie B

Le dimensioni delle macchine virtuali [serie B](../../../virtual-machines/sizes-b-series-burstable.md) sono ideali per i carichi di lavoro che non necessitano di prestazioni coerenti, ad esempio il modello di verifica e i server di sviluppo e applicazioni molto piccoli. 

Per la maggior parte delle dimensioni delle macchine virtuali [serie B](../../../virtual-machines/sizes-b-series-burstable.md) è possibile ottenere un rapporto tra memoria e vCore pari a 4. Il più grande di questi computer è la [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) con 20 vcore e 80 GB di memoria.

Questa serie è univoca in quanto le app hanno la possibilità di **irrompere** durante l'orario di lavoro con crediti di espansione che variano in base alle dimensioni del computer. 

Quando i crediti sono esauriti, la macchina virtuale Torna alle prestazioni della macchina di base.

Il vantaggio della serie B è costituito dal risparmio di calcolo che è possibile ottenere rispetto alle altre dimensioni delle macchine virtuali in altre serie, soprattutto se è necessaria una potenza di elaborazione sporadica nel corso della giornata.

Questa serie supporta [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md), ma **non supporta la** [memorizzazione nella cache di archiviazione Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> La [serie B](../../../virtual-machines/sizes-b-series-burstable.md) di espansione non ha il rapporto tra memoria e vCore di 8 consigliato per i carichi di lavoro SQL Server. In questo modo, è consigliabile usare queste macchine virtuali solo per le applicazioni più piccole, i server Web e i carichi di lavoro di sviluppo.

### <a name="av2-series"></a>Serie Av2

Le macchine virtuali della [serie AV2](../../../virtual-machines/av2-series.md) sono più adatte per carichi di lavoro di livello superiore, ad esempio sviluppo e test, server Web con traffico ridotto, database di piccole e medie app e modelli di prova.

Solo i [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 Vcore e 16GBs di memoria), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 Vcore e 32GBs di memoria) e il [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 Vcore e 64GBs di memoria) hanno un rapporto di memoria-vCore ottimale pari a 8 per le prime tre macchine virtuali. 

Queste macchine virtuali sono opzioni valide per le macchine SQL Server di sviluppo e di test più piccole. 

Il [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 8 vCore può anche essere una soluzione ideale per piccoli server Web e applicazioni.

> [!NOTE] 
> La serie AV2 non supporta l'archiviazione Premium e, di conseguenza, non è consigliata per i carichi di lavoro di produzione SQL Server anche con le macchine virtuali con un rapporto da memoria a vCore pari a 8.

## <a name="storage-optimized"></a>Ottimizzate per l'archiviazione

Le [dimensioni delle macchine virtuali ottimizzate](../../../virtual-machines/sizes-storage.md) per l'archiviazione sono per casi d'uso specifici. Queste macchine virtuali sono progettate in modo specifico con velocità effettiva del disco ottimizzate e i/o. 

### <a name="lsv2-series"></a>Serie Lsv2

La [serie Lsv2](../../../virtual-machines/lsv2-series.md) offre una velocità effettiva elevata, bassa latenza e archiviazione NVMe locale. Le macchine virtuali della serie Lsv2 sono ottimizzate per l'uso del disco locale nel nodo collegato direttamente alla macchina virtuale anziché usare dischi dati durevoli. 

Queste macchine virtuali sono opzioni complesse per i carichi di lavoro Big Data, data warehouse, Reporting e ETL. La velocità effettiva elevata e gli IOPS dell'archiviazione NVMe locale sono un caso d'uso valido per l'elaborazione di file che verranno caricati nel database e altri scenari in cui i dati possono essere ricreati dal sistema di origine o da altri repository, ad esempio archiviazione BLOB di Azure o Azure Data Lake. [Serie Lsv2](../../../virtual-machines/lsv2-series.md) Le macchine virtuali possono anche incrementare le prestazioni del disco per un massimo di 30 minuti alla volta.

Queste macchine virtuali sono da 8 a 80 vCPU con 8 GiB di memoria per vCPU e ogni 8 vCPU è 1,92 TB di NVMe SSD. Questo significa che per la macchina virtuale più grande di questa serie, l' [L80s_v2](../../../virtual-machines/lsv2-series.md), sono presenti 80 vCPU e 640 Bib di memoria con 10 TB 1.92 di spazio di archiviazione NVMe.  Il rapporto tra memoria e vCore di 8 per tutte le macchine virtuali è coerente.

L'archiviazione NVMe è effimera, in quanto i dati andranno persi in questi dischi se la macchina virtuale viene deallocata o se viene spostata in un host diverso per la correzione del servizio.

Le serie Lsv2 e LS supportano [archiviazione Premium](../../../virtual-machines/premium-storage-performance.md), ma non la memorizzazione nella cache di archiviazione Premium. La creazione di una cache locale per aumentare i IOPs non è supportata. 

> [!WARNING]
> L'archiviazione dei file di dati nell'archiviazione NVMe temporanea potrebbe causare la perdita di dati quando la macchina virtuale viene deallocata. 

## <a name="constrained-vcores"></a>VCore vincolato

Carichi di lavoro a prestazioni elevate SQL Server spesso richiedono quantità maggiori di memoria, I/O e velocità effettiva senza i conteggi vCore più elevati. 

La maggior parte dei carichi di lavoro OLTP sono database di applicazioni basati su un numero elevato di transazioni più piccole. Con i carichi di lavoro OLTP, viene letta o modificata solo una piccola quantità di dati, ma i volumi di transazioni basate sui conteggi degli utenti sono molto più elevati. È importante fare in modo che la memoria SQL Server disponibile per i piani della cache, archiviare i dati a cui si accede di recente per le prestazioni e assicurarsi che le letture fisiche possano essere lette rapidamente in memoria. 

Questi ambienti OLTP richiedono maggiori quantità di memoria, archiviazione veloce e la larghezza di banda di I/O necessaria per prestazioni ottimali. 

Per mantenere questo livello di prestazioni senza i costi di licenza SQL Server più elevati, Azure offre dimensioni di VM con [conteggi vCPU vincolati](../../../virtual-machines/constrained-vcpu.md). 

Questo consente di controllare i costi di licenza riducendo il vcore disponibile mantenendo la stessa memoria, l'archiviazione e la larghezza di banda di I/O della macchina virtuale padre.

Il numero di vCPU può essere limitato a una metà a un trimestre delle dimensioni originali della macchina virtuale. La riduzione del valore di Vcore disponibile per la macchina virtuale condurrà un rapporto tra memoria e vCore superiore, ma il costo di calcolo rimarrà invariato.

Queste nuove dimensioni di VM hanno un suffisso che specifica il numero di vCPU attive per facilitarne l'identificazione. 

Ad esempio, [M64-32ms standard](../../../virtual-machines/constrained-vcpu.md) richiede la licenza solo 32 SQL Server Vcore con memoria, i/O e velocità effettiva di [M64ms](../../../virtual-machines/m-series.md) e [M64-16ms standard](../../../virtual-machines/constrained-vcpu.md) richiede solo 16 vcore.  Sebbene il [M64-16ms standard](../../../virtual-machines/constrained-vcpu.md) abbia un trimestre del costo della licenza SQL Server della M64ms, il costo di calcolo della macchina virtuale sarà lo stesso.

> [!NOTE] 
> - I carichi di lavoro di data warehouse medio-grandi possono comunque trarre vantaggio dalle [macchine virtuali vCore vincolate](../../../virtual-machines/constrained-vcpu.md), ma i carichi di lavoro data warehouse sono comunemente caratterizzati da un numero minore di utenti e processi che indirizzano grandi quantità di dati tramite piani di query eseguiti in parallelo. 
> - Il costo di calcolo, che include le licenze del sistema operativo, rimarrà uguale a quello della macchina virtuale padre. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli altri articoli di questa serie:
- [Elenco di controllo rapido](performance-guidelines-best-practices-checklist.md)
- [Archiviazione](performance-guidelines-best-practices-storage.md)
- [Raccogli Baseline](performance-guidelines-best-practices-collect-baseline.md)

Per le procedure di sicurezza consigliate, vedere [considerazioni sulla sicurezza per SQL Server in macchine virtuali di Azure](security-considerations-best-practices.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).
