---
title: 'Archiviazione: procedure consigliate per le prestazioni & linee guida'
description: Fornisce procedure consigliate e linee guida per l'archiviazione per ottimizzare le prestazioni del SQL Server in una macchina virtuale (VM) di Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572518"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Archiviazione: procedure consigliate per le prestazioni per SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce procedure consigliate e linee guida per l'archiviazione per ottimizzare le prestazioni per la SQL Server in macchine virtuali (VM) di Azure.

Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questa serie di procedure consigliate per le prestazioni è incentrata su come ottenere le *migliori* prestazioni per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è meno impegnativo, potrebbe non essere necessario ogni ottimizzazione consigliata. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.

Per altre informazioni, vedere gli altri articoli di questa serie: [elenco di controllo delle prestazioni](performance-guidelines-best-practices-checklist.md), [dimensioni della macchina virtuale](performance-guidelines-best-practices-vm-size.md)e [raccolta di base](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Elenco di controllo

Esaminare l'elenco di controllo seguente per una breve panoramica delle procedure consigliate per l'archiviazione descritte più dettagliatamente nel resto dell'articolo: 

- Monitorare l'applicazione e [determinare i requisiti di larghezza di banda e latenza di archiviazione](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) per SQL Server i file di dati, di log e tempdb prima di scegliere il tipo di disco. 
- Per ottimizzare le prestazioni di archiviazione, pianificare le operazioni di i/o al secondo disponibili e usare la memorizzazione nella cache dei dati come funzionalità di prestazioni per le letture dei dati evitando il limite di [macchine virtuali e dischi](../../../virtual-machines/premium-storage-performance.md#throttling).
- Inserire i file di dati, di log e tempdb in unità separate.
    - Per l'unità dati, usare solo i [dischi P30 e P40 Premium](../../../virtual-machines/disks-types.md#premium-ssd) per garantire la disponibilità del supporto per la cache
    - Per il piano di unità di log per la capacità e le prestazioni dei test rispetto al costo durante la valutazione dei [dischi P30-P80 Premium](../../../virtual-machines/disks-types.md#premium-ssd)
      - Se è necessaria la latenza di archiviazione di sottomillisecondi, usare i [dischi ultra di Azure](../../../virtual-machines/disks-types.md#ultra-disk) per il log delle transazioni. 
      - Per le distribuzioni di macchine virtuali della serie M, considerare l' [acceleratore di scrittura](../../../virtual-machines/how-to-enable-write-accelerator.md) sull'uso di dischi Ultra Azure.
    - Inserire [tempdb](/sql/relational-databases/databases/tempdb-database) nell'unità SSD temporanea locale `D:\` per la maggior parte dei carichi di lavoro SQL Server dopo aver scelto le dimensioni ottimali della macchina virtuale. 
      - Se la capacità dell'unità locale non è sufficiente per tempdb, provare a ridimensionare la macchina virtuale. Per ulteriori informazioni, vedere [criteri di memorizzazione nella cache dei file di dati](#data-file-caching-policies) .
- Eseguire lo striping di più dischi dati di Azure usando [spazi di archiviazione](/windows-server/storage/storage-spaces/overview) per aumentare la larghezza di banda di i/O fino ai limiti di velocità effettiva e IOPS della macchina virtuale di destinazione.
- Impostare la [memorizzazione nella cache dell'host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) su sola lettura per i dischi di file di dati.
- Impostare la [memorizzazione nella cache dell'host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) su None per i dischi dei file di log.
    - Non abilitare la memorizzazione nella cache in lettura/scrittura su dischi che contengono file di SQL Server. 
    - Arrestare sempre il servizio SQL Server prima di modificare le impostazioni della cache del disco.
- Per i carichi di lavoro di sviluppo e test e l'archiviazione di backup a lungo termine, è consigliabile usare l'archiviazione standard. Non è consigliabile usare HDD Standard/SDD per i carichi di lavoro di produzione.
- Il servizio di espansione del [disco basato sul credito](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) deve essere considerato solo per carichi di lavoro di sviluppo/test più piccoli e sistemi di reparto.
- Eseguire il provisioning dell'account di archiviazione nella stessa area della macchina virtuale SQL Server. 
- Disabilitare l'archiviazione con ridondanza geografica di Azure (replica geografica) e usare con ridondanza locale (archiviazione con ridondanza locale) nell'account di archiviazione.
- Formattare il disco dati per usare le dimensioni dell'unità di allocazione di 64 KB per tutti i file di dati presenti in un'unità diversa dall' `D:\` unità temporanea (il cui valore predefinito è 4 KB). SQL Server macchine virtuali distribuite tramite Azure Marketplace sono disponibili dischi dati formattati con le dimensioni dell'unità di allocazione e l'interfoliazione per il pool di archiviazione impostato su 64 KB. 

Per confrontare l'elenco di controllo dell'archiviazione con gli altri, vedere l' [elenco di controllo completo delle procedure](performance-guidelines-best-practices-checklist.md)consigliate per le prestazioni. 

## <a name="overview"></a>Panoramica

Per trovare la configurazione più efficace per i carichi di lavoro SQL Server in una macchina virtuale di Azure, iniziare [misurando le prestazioni di archiviazione dell'applicazione aziendale](performance-guidelines-best-practices-collect-baseline.md#storage). Una volta conosciuti i requisiti di archiviazione, selezionare una macchina virtuale che supporta i IOPS e la velocità effettiva necessari con il rapporto tra memoria e vCore appropriato. 

Scegliere una dimensione di macchina virtuale con una scalabilità di archiviazione sufficiente per il carico di lavoro e una combinazione di dischi (in genere in un pool di archiviazione) che soddisfino i requisiti di capacità e prestazioni dell'azienda. 

Il tipo di disco dipende dal tipo di file ospitato sul disco e dai requisiti di prestazioni massimi.

> [!TIP]
> Il provisioning di una macchina virtuale SQL Server tramite il portale di Azure consente di eseguire il processo di configurazione dell'archiviazione e implementa la maggior parte delle procedure consigliate di archiviazione, ad esempio la creazione di pool di archiviazione separati per i file di dati e di log, la destinazione di tempdb all' `D:\` unità e l'abilitazione dei criteri di memorizzazione nella cache Per altre informazioni sul provisioning e sulla configurazione dell'archiviazione, vedere [configurazione dell'archiviazione delle macchine virtuali SQL](storage-configuration.md). 

## <a name="vm-disk-types"></a>Tipi di dischi per la VM

È possibile scegliere il livello di prestazioni per i dischi. I tipi di dischi gestiti disponibili come archiviazione sottostante (elencati aumentando le capacità di prestazioni) sono unità disco rigido standard (HDD), SSD standard, unità SSD (Solid-State Drive) Premium e dischi Ultra. 

Le prestazioni del disco aumentano con la capacità, raggruppate in base alle [etichette dei dischi Premium](../../../virtual-machines/disks-types.md#premium-ssd) , ad esempio P1 con 4 GiB di spazio e 120 IOPS, a P80 con 32 TIB di archiviazione e 20.000 IOPS. Archiviazione Premium supporta una cache di archiviazione che consente di migliorare le prestazioni di lettura e scrittura per alcuni carichi di lavoro. Per altre informazioni, vedere [Panoramica di Managed disks](../../../virtual-machines/managed-disks-overview.md). 

Esistono anche tre tipi di [dischi](../../../virtual-machines/managed-disks-overview.md#disk-roles) principali da considerare per la SQL Server in una VM di Azure: un disco del sistema operativo, un disco temporaneo e i dischi dati. Scegliere con attenzione gli elementi archiviati nell'unità del sistema operativo `(C:\)` e nell'unità temporanea temporanea `(D:\)` . 

### <a name="operating-system-disk"></a>Disco del sistema operativo

Un disco del sistema operativo è un disco rigido virtuale che può essere avviato e montato come versione in esecuzione di un sistema operativo ed è contrassegnato come `C:\` unità. Quando si crea una macchina virtuale di Azure, la piattaforma collegherà almeno un disco alla macchina virtuale per il disco del sistema operativo. L' `C:\` unità è il percorso predefinito per le installazioni di applicazioni e la configurazione del file. 

Per gli ambienti di produzione SQL Server, non usare il disco del sistema operativo per i file di dati, i file di log e i log degli errori. 

### <a name="temporary-disk"></a>Disco temporaneo

Molte macchine virtuali di Azure contengono un altro tipo di disco denominato disco temporaneo (con etichetta come `D:\` unità). A seconda della serie di macchine virtuali e delle dimensioni, la capacità di questo disco può variare. Il disco temporaneo è effimero, il che significa che l'archiviazione su disco viene ricreata, come in, viene riallocata e allocata di nuovo, quando la macchina virtuale viene riavviata o spostata in un host diverso, ad esempio per la [correzione del servizio](/troubleshoot/azure/virtual-machines/understand-vm-reboot). 

L'unità di archiviazione temporanea non viene salvata in modo permanente nell'archiviazione remota e pertanto non deve archiviare i file del database utente, i file di log delle transazioni o qualsiasi elemento che deve essere mantenuto. 

Posizionare tempdb nell'unità SSD locale `D:\` per SQL Server carichi di lavoro, a meno che l'utilizzo della cache locale non sia un problema. Se si usa una macchina virtuale che [non dispone di un disco temporaneo](../../../virtual-machines/azure-vms-no-temp-disk.md) , è consigliabile posizionare tempdb nel proprio disco isolato o nel pool di archiviazione con la memorizzazione nella cache impostata su sola lettura. Per altre informazioni, vedere [criteri di memorizzazione nella cache dei dati tempdb](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Dischi dati

I dischi dati sono dischi di archiviazione remoti che vengono spesso creati nei [pool di archiviazione](/windows-server/storage/storage-spaces/overview) per superare la capacità e le prestazioni che qualsiasi singolo disco potrebbe offrire alla macchina virtuale.

Alleghi il numero minimo di dischi che soddisfano i requisiti di IOPS, velocità effettiva e capacità del carico di lavoro. Non superare il numero massimo di dischi dati della macchina virtuale più piccola in cui si intende eseguire il ridimensionamento.

Inserire i dati e i file di log nei dischi dati di cui è stato eseguito il provisioning per soddisfare i requisiti di prestazioni 

Formattare il disco dati per usare le dimensioni dell'unità di allocazione di 64 KB per tutti i file di dati presenti in un'unità diversa dall' `D:\` unità temporanea (il cui valore predefinito è 4 KB). SQL Server macchine virtuali distribuite tramite Azure Marketplace sono disponibili dischi dati formattati con le dimensioni dell'unità di allocazione e l'interfoliazione per il pool di archiviazione impostato su 64 KB. 

## <a name="premium-disks"></a>Dischi Premium

Usare dischi SSD Premium per i file di dati e di log per i carichi di lavoro di produzione SQL Server. SSD Premium IOPS e la larghezza di banda variano in base alle [dimensioni e al tipo del disco](../../../virtual-machines/disks-types.md). 

Per i carichi di lavoro di produzione, usare i dischi P30 e/o P40 per i file di dati SQL Server per garantire il supporto della memorizzazione nella cache e usare P30 fino a P80 per SQL Server file di log delle transazioni.  Per il migliore costo totale di proprietà, iniziare con P30s (5000 IOPS/200 MBPS) per i file di dati e di log e scegliere capacità più elevate solo quando è necessario controllare il numero di dischi della macchina virtuale.

Per i carichi di lavoro OLTP, abbinare le operazioni di i/o al secondo per disco (o pool di archiviazione) con i requisiti di prestazioni usando i carichi di lavoro alle ore di punta e i `Disk Reads/sec`  +  `Disk Writes/sec` contatori delle prestazioni Per i carichi di lavoro data warehouse e Reporting, trovare la velocità effettiva di destinazione usando i carichi di lavoro alle ore di punta e `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Usare spazi di archiviazione per ottenere prestazioni ottimali, configurare due pool, uno per i file di log e l'altro per i file di dati. Se non si usa lo striping del disco, usare due dischi SSD Premium mappati a unità separate, dove un'unità contiene il file di log e l'altra contiene i dati.

Le operazioni di i/o al secondo con [provisioning e la velocità effettiva](../../../virtual-machines/disks-types.md#premium-ssd) per disco utilizzate come parte del pool di archiviazione. Le funzionalità di IOPS e velocità effettiva combinate dei dischi sono la capacità massima fino ai limiti di velocità effettiva della macchina virtuale.

La procedura consigliata consiste nell'utilizzare il minor numero di dischi possibile, soddisfacendo al tempo stesso i requisiti minimi per IOPS (e velocità effettiva) e la capacità. Tuttavia, il bilanciamento del prezzo e delle prestazioni tende a essere migliore con un numero elevato di dischi piccoli invece che con un numero ridotto di dischi di grandi dimensioni.

### <a name="scaling-premium-disks"></a>Ridimensionamento di dischi Premium

Quando viene distribuito per la prima volta un disco gestito di Azure, il livello di prestazioni del disco è basato sulle dimensioni del disco di cui è stato effettuato il provisioning. Designare il livello di prestazioni in fase di distribuzione o modificarlo in seguito, senza modificare le dimensioni del disco. Se la domanda aumenta, è possibile aumentare il livello di prestazioni per soddisfare le esigenze aziendali. 

La modifica del livello di prestazioni consente agli amministratori di prepararsi e soddisfare una domanda più elevata senza basarsi sull'espansione del [disco](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Usare le prestazioni più elevate per tutto il tempo necessario quando la fatturazione è progettata per soddisfare il livello di prestazioni di archiviazione. Aggiornare il livello in base ai requisiti di prestazioni senza aumentare la capacità. Tornare al livello originale quando le prestazioni aggiuntive non sono più necessarie.

Questa espansione economica e temporanea delle prestazioni è un caso d'uso forte per gli eventi di destinazione, ad esempio acquisti, test delle prestazioni, eventi di formazione e altre brevi finestre, in cui sono necessarie prestazioni più elevate solo per un breve periodo di tempo. 

Per altre informazioni, vedere [livelli di prestazioni per Managed disks](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Disco Ultra di Azure

Se è necessario un tempo di risposta di submillisecondo con latenza ridotta, provare a usare [Azure ultra disk](../../../virtual-machines/disks-types.md#ultra-disk) per l'unità di log SQL Server o anche l'unità dati per le applicazioni che sono estremamente sensibili alla latenza di I/O. 

È possibile configurare il disco ultra in cui la capacità e il IOPS possono essere ridimensionati in modo indipendente. Con gli amministratori del disco Ultra è possibile effettuare il provisioning di un disco con requisiti di capacità, IOPS e velocità effettiva in base alle esigenze dell'applicazione. 

Il disco Ultra non è supportato in tutte le serie di macchine virtuali e presenta altre limitazioni, ad esempio disponibilità delle aree, ridondanza e supporto per backup di Azure. Per altre informazioni, vedere [uso di Azure ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md) per un elenco completo di limitazioni. 

## <a name="standard-hdds-and-ssds"></a>HDD e SSD standard

I [dischi rigidi](../../../virtual-machines/disks-types.md#standard-hdd) e le unità SSD standard hanno latenze e larghezza di banda variabili e sono consigliati solo per i carichi di lavoro di sviluppo/test. Per i carichi di lavoro di produzione si consiglia di usare le unità SSD Premium. Se si usa SDD Standard (scenari di sviluppo/test), è consigliabile aggiungere il numero massimo di dischi dati supportati dalle [dimensioni della macchina virtuale](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) e usare lo striping del disco con spazi di archiviazione per ottenere prestazioni ottimali.

## <a name="caching"></a>Memorizzazione nella cache

Le macchine virtuali che supportano la memorizzazione nella cache di archiviazione Premium possono sfruttare una funzionalità aggiuntiva denominata Azure BlobCache o la memorizzazione nella cache dell'host per estendere le funzionalità di IOPS e velocità effettiva di una macchina virtuale. Le macchine virtuali abilitate per l'archiviazione Premium e la memorizzazione nella cache di archiviazione Premium hanno questi due limiti di larghezza di banda di archiviazione che possono essere usati insieme per migliorare le prestazioni di archiviazione.

La velocità effettiva di IOPS e MBps senza la memorizzazione nella cache conta sui limiti di velocità effettiva dei dischi non memorizzati nella cache della macchina virtuale. I limiti massimi memorizzati nella cache forniscono un buffer aggiuntivo per le letture che consente di indirizzare la crescita e i picchi imprevisti.

Abilitare la memorizzazione nella cache Premium ogni volta che l'opzione è supportata per migliorare significativamente le prestazioni per le letture sull'unità dati senza costi aggiuntivi. 

Le letture e le scritture nei BlobCache di Azure (IOPS e velocità effettiva memorizzati nella cache) non vengono conteggiate rispetto ai limiti di velocità effettiva e IOPS non memorizzati nella cache della macchina virtuale.

> [!NOTE]
> La memorizzazione nella cache del disco non è supportata per i dischi 4 TiB e maggiori (P50 e superiori). Se alla VM sono collegati più dischi, ogni disco con dimensioni inferiori a 4 TiB supporta la memorizzazione nella cache. Per ulteriori informazioni, vedere [memorizzazione nella cache su disco](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Velocità effettiva non memorizzata nella cache

Il numero massimo di IOPS e velocità effettiva del disco non memorizzato nella cache è il limite massimo di archiviazione remota che la macchina virtuale è in grado di gestire. Questo limite viene definito alla macchina virtuale e non è un limite dell'archiviazione su disco sottostante. Questo limite si applica solo all'I/O alle unità dati in remoto collegate alla macchina virtuale, non all'i/O locale rispetto all'unità temporanea ( `D:\` unità) o all'unità del sistema operativo.

La quantità di IOPS non memorizzata nella cache e la velocità effettiva disponibile per una VM possono essere verificate nella documentazione relativa alla macchina virtuale.

Ad esempio, la documentazione della [serie M](../../../virtual-machines/m-series.md) Mostra che la velocità effettiva massima non memorizzata nella cache per la macchina virtuale Standard_M8ms è 5000 IOPS e 125 Mbps della velocità effettiva del disco non memorizzata nella cache. 

![Screenshot che mostra la documentazione sulla velocità effettiva del disco non memorizzata nella cache della serie M.](./media/performance-guidelines-best-practices/m-series-table.png)

Analogamente, è possibile osservare che il Standard_M32ts supporta 20.000 IOPS del disco non memorizzati nella cache e la velocità effettiva del disco non memorizzata nella cache di 500 MBps. Questo limite è regolato a livello di macchina virtuale, indipendentemente dall'archiviazione su disco Premium sottostante.

Per ulteriori informazioni, vedere la pagina relativa ai limiti non memorizzati [nella cache e memorizzati nella](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)cache.


### <a name="cached-and-temp-storage-throughput"></a>Velocità effettiva di archiviazione temporanea e memorizzata nella cache

Il limite massimo di velocità effettiva cache e archiviazione temporanea è un limite separato dal limite di velocità effettiva non memorizzato nella cache nella macchina virtuale. Azure BlobCache è costituito da una combinazione della memoria ad accesso casuale dell'host della macchina virtuale e dell'unità SSD collegata localmente. L'unità temporanea ( `D:\` unità) all'interno della macchina virtuale è ospitata anche in questa unità SSD locale.

Il limite massimo di velocità effettiva cache e archiviazione temporanea regola l'i/O sull'unità temporanea locale ( `D:\` unità) e la BlobCache di Azure **solo se** è abilitata la memorizzazione nella cache dell'host. 

Quando la memorizzazione nella cache è abilitata nell'archiviazione Premium, le macchine virtuali possono superare le limitazioni dell'archiviazione remota e dei limiti di velocità effettiva della VM non memorizzati nella cache.  

Solo alcune macchine virtuali supportano sia archiviazione Premium che Caching archiviazione Premium (che devono essere verificate nella documentazione della macchina virtuale). Ad esempio, la documentazione della [serie M](../../../virtual-machines/m-series.md) indica che è supportata sia l'archiviazione Premium sia la memorizzazione nella cache di archiviazione Premium: 

![Screenshot che mostra il supporto per archiviazione Premium serie M.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

I limiti della cache variano in base alle dimensioni della macchina virtuale. Ad esempio, la macchina virtuale Standard_M8ms supporta 10000 IOPS del disco memorizzato nella cache e velocità effettiva del disco memorizzata nella cache di 1000 MBps con una dimensione totale della cache di 793 GiB. Analogamente, la macchina virtuale Standard_M32ts supporta 40000 IOPS del disco memorizzato nella cache e velocità effettiva del disco memorizzata nella cache di 400 MBps con una dimensione totale della cache di 3174 GiB 

![Screenshot che mostra la documentazione sulla velocità effettiva del disco memorizzato nella cache della serie M.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

È possibile abilitare manualmente la memorizzazione nella cache dell'host in una macchina virtuale esistente. Arrestare tutti i carichi di lavoro dell'applicazione e i servizi SQL Server prima che vengano apportate modifiche ai criteri di memorizzazione nella cache della macchina virtuale. La modifica delle impostazioni della cache della macchina virtuale comporta lo scollegamento e il ricollegamento del disco di destinazione dopo l'applicazione delle impostazioni.

### <a name="data-file-caching-policies"></a>Criteri di memorizzazione nella cache dei file di dati

I criteri di memorizzazione nella cache di archiviazione variano a seconda del tipo di file di dati SQL Server ospitati nell'unità. 

Nella tabella seguente viene fornito un riepilogo dei criteri consigliati per la memorizzazione nella cache in base al tipo di dati di SQL Server: 

|Disco SQL Server |Recommendation |
|---------|---------|
| **Disco dati** | Abilitare `Read-only` la memorizzazione nella cache per i dischi che ospitano SQL Server file di dati. <br/> Le letture dalla cache saranno più veloci delle letture non memorizzate nella cache dal disco dati. <br/> Le operazioni di i/o al secondo non memorizzate nella cache e la velocità effettiva e le operazioni di i/o nella cache produrranno le prestazioni totali possibili dalla macchina virtuale entro i limiti delle VM, ma le prestazioni effettive variano in base alla capacità del carico di lavoro di usare la cache (percentuale riscontri cache <br/>|
|**Disco del log delle transazioni**|Impostare i criteri di memorizzazione nella cache su `None` per i dischi che ospitano il log delle transazioni.  L'abilitazione della memorizzazione nella cache per il disco del log delle transazioni non comporta alcun vantaggio a livello di prestazioni e, di fatto, `Read-only` `Read/Write` l'abilitazione o la memorizzazione nella cache dell'unità di log può compromettere le prestazioni delle scritture sull'unità e ridurre la quantità di cache disponibile per le letture nell'unità dati.  |
|**Disco del sistema operativo** | I criteri di memorizzazione nella cache predefiniti possono essere `Read-only` o `Read/write` per l'unità del sistema operativo. <br/> Non è consigliabile modificare il livello di memorizzazione nella cache dell'unità del sistema operativo.  |
| **tempdb**| Se non è possibile inserire tempdb nell'unità temporanea a `D:\` causa di motivi di capacità, ridimensionare la macchina virtuale per ottenere un'unità temporanea di dimensioni maggiori o inserire tempdb in un'unità dati separata con la `Read-only` memorizzazione nella cache configurata. <br/> La cache della macchina virtuale e l'unità temporanea usano entrambe l'unità SSD locale, pertanto tenere presente questo aspetto quando il ridimensionamento come I/O di tempdb viene conteggiato in base ai limiti della macchina virtuale di IOPS e velocità effettiva memorizzati nella cache quando sono ospitati nell'unità temporanea.| 
| | | 


Per altre informazioni, vedere [memorizzazione nella cache su disco](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Striping del disco

Analizzare la velocità effettiva e la larghezza di banda necessaria per i file di dati SQL per determinare il numero di dischi dati, inclusi il file di log e tempdb. I limiti di velocità effettiva e larghezza di banda variano in base alle dimensioni della VM Per altre informazioni, vedere [dimensioni della macchina virtuale](../../../virtual-machines/sizes.md)

Aggiungere altri dischi dati e usare lo striping del disco per una maggiore velocità effettiva. Ad esempio, un'applicazione che richiede 12.000 IOPS e 180 MB/s di velocità effettiva può usare tre dischi P30 con striping per fornire 15.000 IOPS e 600 MB/s di velocità effettiva. 

Per configurare lo striping del disco, vedere [striping del disco](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Limitazione del disco 

Sono previsti limiti di velocità effettiva a livello di disco e di macchina virtuale. I limiti massimi di IOPS per macchina virtuale e per disco sono diversi e sono indipendenti tra loro.

Le applicazioni che utilizzano risorse oltre questi limiti saranno limitate (anche note come limitate). Selezionare una macchina virtuale e le dimensioni del disco in uno striping del disco che soddisfi i requisiti dell'applicazione e non le limitazioni di limitazione. Per risolvere il limite, usare la memorizzazione nella cache o ottimizzare l'applicazione in modo che sia necessaria una velocità effettiva minore.

Ad esempio, un'applicazione che richiede 12.000 IOPS e 180 MB/s può: 
- Usare la [Standard_M32ms](../../../virtual-machines/m-series.md) che ha una velocità effettiva massima di dischi non memorizzati nella cache di 20.000 IOPS e 500 Mbps.
- Eseguire lo striping di tre dischi P30 per fornire 15.000 IOPS e 600 MB/s di velocità effettiva.
- Utilizzare una macchina virtuale [Standard_M16ms](../../../virtual-machines/m-series.md) e utilizzare la memorizzazione nella cache dell'host per utilizzare la cache locale per l'utilizzo della velocità effettiva. 

Le macchine virtuali configurate per la scalabilità verticale durante i periodi di utilizzo elevato dovrebbero effettuare il provisioning dell'archiviazione con un numero sufficiente di IOPS e velocità effettiva per supportare le dimensioni massime della macchina virtuale, mantenendo al tempo stesso il numero complessivo di dischi inferiore o uguale al numero massimo supportato dallo SKU di VM più piccolo destinato all'uso.

Per altre informazioni sulle limitazioni di limitazione del disco e sull'uso della memorizzazione nella cache per evitare la chiusura, vedere il numero di unità di i/o su [disco](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Una limitazione del disco può comunque comportare prestazioni soddisfacenti per gli utenti. ottimizzare e mantenere i carichi di lavoro anziché ridimensionarli in una macchina virtuale più grande per bilanciare la gestione dei costi e delle prestazioni per l'azienda. 


## <a name="write-acceleration"></a>Accelerazione scrittura

L'accelerazione di scrittura è una funzionalità del disco disponibile solo per le macchine virtuali (VM) della [serie M](https://docs.microsoft.com/azure/virtual-machines/m-series) . Lo scopo dell'accelerazione di scrittura è quello di migliorare la latenza di I/O delle Scritture nell'archiviazione Premium di Azure quando è necessaria una latenza di I/O a singola cifra a causa di carichi di lavoro OLTP di importanza strategica per volumi elevati o ambienti data warehouse. 

Usare l'accelerazione di scrittura per migliorare la latenza di scrittura nell'unità che ospita i file di log. Non usare l'accelerazione di scrittura per i file di dati SQL Server. 

Acceleratore di scrittura dischi condividono lo stesso limite di IOPS della macchina virtuale. I dischi collegati non possono superare il limite di IOPS acceleratore di scrittura per una macchina virtuale.  

La tabella seguente illustra il numero di dischi dati e IOPS supportati per macchina virtuale: 

| SKU di VM  | N. acceleratore di scrittura dischi  | acceleratore di scrittura IOPS del disco per macchina virtuale  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Esistono diverse restrizioni per l'uso dell'accelerazione di scrittura. Per altre informazioni, vedere [restrizioni quando si usa acceleratore di scrittura](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Confronto con Azure ultra disk

La differenza principale tra l'accelerazione di scrittura e Azure ultra disks è che l'accelerazione di scrittura è una funzionalità di macchina virtuale disponibile solo per la serie M e Azure ultra disks è un'opzione di archiviazione. L'accelerazione di scrittura è una cache ottimizzata per la scrittura con le proprie limitazioni in base alle dimensioni della macchina virtuale. Azure ultra disks è un'opzione di archiviazione su disco a bassa latenza per le macchine virtuali di Azure. 

Se possibile, usare l'accelerazione di scrittura su dischi ultra per il disco del log delle transazioni. Per le macchine virtuali che non supportano l'accelerazione di scrittura ma che richiedono una bassa latenza per il log delle transazioni, usare Azure ultra Disks. 

## <a name="monitor-storage-performance"></a>Monitorare le prestazioni di archiviazione

Per valutare le esigenze di archiviazione e determinare il modo in cui l'archiviazione è in esecuzione, è necessario capire cosa misurare e quali sono le conseguenze di tali indicatori. 

[IOPS (input/output al secondo)](../../../virtual-machines/premium-storage-performance.md#iops) è il numero di richieste che l'applicazione sta effettuando alla risorsa di archiviazione al secondo. Misurare IOPS utilizzando i contatori di Performance Monitor `Disk Reads/sec` e `Disk Writes/sec` . Per ottenere prestazioni ottimali, le applicazioni [OLTP (Online Transaction Processing)](/azure/architecture/data-guide/relational-data/online-transaction-processing) devono aumentare il numero di IOPS. Le applicazioni, ad esempio i sistemi di elaborazione dei pagamenti, gli acquisti online e i sistemi POS di vendita al dettaglio, sono tutti esempi di applicazioni OLTP.

La [velocità effettiva](../../../virtual-machines/premium-storage-performance.md#throughput) è il volume di dati inviati all'archivio sottostante, spesso misurato da megabyte al secondo. Misurare la velocità effettiva con i contatori di Performance Monitor `Disk Read Bytes/sec` e `Disk Write Bytes/sec` . Il [data warehouse](/azure/architecture/data-guide/relational-data/data-warehousing) è ottimizzato per massimizzare la velocità effettiva rispetto al IOPS. Le applicazioni, ad esempio gli archivi dati per l'analisi, la creazione di report, i flussi di dati ETL e altre destinazioni business intelligence sono tutti esempi di applicazioni di data warehousing.

Le dimensioni delle unità di i/O influiscono sulle funzionalità di IOPS e velocità effettiva, mentre le dimensioni di I/o più piccole producono IOPS maggiori e dimensioni I/O maggiori producono una velocità effettiva maggiore SQL Server sceglie automaticamente le dimensioni di I/O ottimali. Per altre informazioni su, vedere [ottimizzare IOPS, velocità effettiva e latenza per le applicazioni](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Sono disponibili metriche di monitoraggio di Azure specifiche che non sono utili per l'individuazione della copertura a livello di macchina virtuale e di disco, nonché per l'utilizzo e l'integrità della cache AzureBlob. Per identificare i contatori chiave da aggiungere alla soluzione di monitoraggio e portale di Azure Dashboard, vedere [metriche di utilizzo dello spazio di archiviazione](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Monitoraggio di Azure attualmente non offre metriche a livello di disco per l'unità temporanea temporanea `(D:\)` . Le operazioni di i/o nella cache della macchina virtuale e la percentuale di utilizzo della larghezza di banda memorizzata nella cache della VM rifletteranno IOPS e velocità effettiva sia dall'unità temporanea temporanea `(D:\)` che dalla cache host


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle procedure consigliate per le prestazioni, vedere gli altri articoli di questa serie:
- [Elenco di controllo rapido](performance-guidelines-best-practices-checklist.md)
- [Dimensioni macchina virtuale](performance-guidelines-best-practices-vm-size.md)
- [Raccogli Baseline](performance-guidelines-best-practices-collect-baseline.md)

Per le procedure di sicurezza consigliate, vedere [considerazioni sulla sicurezza per SQL Server in macchine virtuali di Azure](security-considerations-best-practices.md).

Per i test dettagliati delle prestazioni SQL Server nelle macchine virtuali di Azure con i benchmark TPC-E e TPC_C, fare riferimento al blog sull'[ottimizzazione delle prestazioni OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).
