---
title: 'Archiviazione: procedure consigliate per le prestazioni & linee guida'
description: Fornisce le procedure consigliate e le linee guida per l'archiviazione per ottimizzare le prestazioni SQL Server macchina virtuale di Azure.
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
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389724"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Archiviazione: procedure consigliate per le prestazioni SQL Server nelle macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra le procedure consigliate e le linee guida per l'archiviazione per ottimizzare le SQL Server in macchine virtuali di Azure.

Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questa serie di procedure consigliate  per le prestazioni è incentrata su come ottenere le prestazioni migliori per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è meno impegnativo, potrebbe non essere necessaria ogni ottimizzazione consigliata. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.

Per altre informazioni, vedere gli altri articoli di questa serie: Elenco di controllo [delle](performance-guidelines-best-practices-checklist.md)prestazioni, [Dimensioni della macchina](performance-guidelines-best-practices-vm-size.md)virtuale e Raccolta [baseline.](performance-guidelines-best-practices-collect-baseline.md) 

## <a name="checklist"></a>Elenco di controllo

Esaminare l'elenco di controllo seguente per una breve panoramica delle procedure consigliate per l'archiviazione trattate nel resto dell'articolo in modo più dettagliato: 

- Monitorare l'applicazione e [determinare i requisiti di larghezza](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) di banda e latenza di archiviazione SQL Server file di dati, log e tempdb prima di scegliere il tipo di disco. 
- Per ottimizzare le prestazioni di archiviazione, pianificare le operazioni di I/O al secondo non memorizzate nella cache più elevate disponibili e usare la memorizzazione nella cache dei dati come funzionalità di prestazioni per le operazioni di lettura dei dati evitando che la macchina virtuale e i dischi [limitino](../../../virtual-machines/premium-storage-performance.md#throttling).
- Inserire i file di dati, di log e tempdb in unità separate.
    - Per l'unità dati, usare [solo dischi P30 e P40 Premium](../../../virtual-machines/disks-types.md#premium-ssd) per garantire la disponibilità del supporto della cache
    - Per il piano di unità di log per la capacità e le prestazioni di test rispetto ai costi durante la valutazione dei dischi [Premium P30 - P80](../../../virtual-machines/disks-types.md#premium-ssd)
      - Se è necessaria una latenza di archiviazione di sottomillisecondi, usare [dischi Ultra di Azure](../../../virtual-machines/disks-types.md#ultra-disk) per il log delle transazioni. 
      - Per le distribuzioni di macchine virtuali serie M è [consigliabile usare l'acceleratore](../../../virtual-machines/how-to-enable-write-accelerator.md) di scrittura usando dischi Ultra di Azure.
    - Inserire [tempdb nell'unità](/sql/relational-databases/databases/tempdb-database) SSD temporanea locale per la maggior parte dei carichi di lavoro SQL Server dopo aver scelto le dimensioni `D:\` ottimali della macchina virtuale. 
      - Se la capacità dell'unità locale non è sufficiente per tempdb, provare a ridimensionare la macchina virtuale. Per [altre informazioni, vedere Criteri di memorizzazione nella](#data-file-caching-policies) cache dei file di dati.
- Stripe multiple Azure data disks using Storage Spaces to increase I/O bandwidth up to the target virtual machine's IOPS and throughput limits(Stripe multiple Azure data disks using [Storage Spaces](/windows-server/storage/storage-spaces/overview) to increase I/O bandwidth up to the target virtual machine's IOPS and throughput limits).
- Impostare [la memorizzazione nella cache dell'host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) in sola lettura per i dischi dei file di dati.
- Impostare [la memorizzazione nella cache dell'host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) su nessuno per i dischi dei file di log.
    - Non abilitare la memorizzazione nella cache in lettura/scrittura nei dischi che contengono SQL Server file. 
    - Arrestare sempre il SQL Server prima di modificare le impostazioni della cache del disco.
- Per i carichi di lavoro di sviluppo e test e l'archiviazione di backup a lungo termine è consigliabile usare l'archiviazione standard. Non è consigliabile usare HDD Standard/SDD per i carichi di lavoro di produzione.
- [Il bursting dei dischi](../../../virtual-machines/disk-bursting.md#credit-based-bursting) basato sul credito (P1-P20) deve essere considerato solo per carichi di lavoro di sviluppo/test più piccoli e sistemi di reparto.
- Effettuare il provisioning dell'account di archiviazione nella stessa area SQL Server macchina virtuale. 
- Disabilitare l'archiviazione con ridondanza geografica di Azure (replica geografica) e usare LRS (archiviazione con ridondanza locale) nell'account di archiviazione.
- Formattare il disco dati in modo che usi dimensioni del blocco di 64 KB (dimensioni unità di allocazione) per tutti i file di dati posizionati in un'unità diversa dall'unità temporanea (con un valore predefinito `D:\` di 4 KB). SQL Server le macchine virtuali distribuite tramite Azure Marketplace con dischi dati formattati con dimensioni di blocco e interfoliazione per il pool di archiviazione impostato su 64 KB. 

Per confrontare l'elenco di controllo di archiviazione con gli altri, vedere l'elenco di controllo completo [sulle procedure consigliate per le prestazioni](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Panoramica

Per trovare la configurazione più efficace per i SQL Server in una macchina virtuale di Azure, iniziare misurando le prestazioni di archiviazione [dell'applicazione aziendale.](performance-guidelines-best-practices-collect-baseline.md#storage) Una volta noti i requisiti di archiviazione, selezionare una macchina virtuale che supporti le operazioni di I/O al secondo e la velocità effettiva necessarie con il rapporto memoria-vCore appropriato. 

Scegliere una dimensione di vm con scalabilità di archiviazione sufficiente per il carico di lavoro e una combinazione di dischi (in genere in un pool di archiviazione) che soddisfino i requisiti di capacità e prestazioni dell'azienda. 

Il tipo di disco dipende sia dal tipo di file ospitato nel disco che dai requisiti di prestazioni di picco.

> [!TIP]
> Il provisioning di una macchina virtuale SQL Server tramite il portale di Azure consente di eseguire il processo di configurazione dell'archiviazione e implementa la maggior parte delle procedure consigliate di archiviazione, ad esempio la creazione di pool di archiviazione separati per i file di dati e di log, la destinazione di tempdb per l'unità e l'abilitazione dei criteri di memorizzazione nella `D:\` cache ottimali. Per altre informazioni sul provisioning e la configurazione dell'archiviazione, vedere Configurazione [dell'archiviazione delle macchine virtuali SQL.](storage-configuration.md) 

## <a name="vm-disk-types"></a>Tipi di dischi per la VM

È possibile scegliere il livello di prestazioni per i dischi. I tipi di dischi gestiti disponibili come risorsa di archiviazione sottostante (elencati aumentando le funzionalità di prestazioni) sono le unità disco rigido Standard (HDD), le unità SSD Standard, le unità SSD Premium (SSD) e i dischi Ultra. 

Le prestazioni del disco aumentano con la capacità, raggruppata in base alle etichette dei dischi [Premium,](../../../virtual-machines/disks-types.md#premium-ssd) ad esempio P1 con 4 GiB di spazio e 120 operazioni di I/O al secondo al P80 con 32 TiB di archiviazione e 20.000 operazioni di I/O al secondo. Archiviazione Premium supporta una cache di archiviazione che consente di migliorare le prestazioni di lettura e scrittura per alcuni carichi di lavoro. Per altre informazioni, vedere [Panoramica di Managed Disks.](../../../virtual-machines/managed-disks-overview.md) 

Esistono anche tre tipi di [dischi](../../../virtual-machines/managed-disks-overview.md#disk-roles) principali da considerare per l'SQL Server in una macchina virtuale di Azure: un disco del sistema operativo, un disco temporaneo e i dischi dati. Scegliere con attenzione gli elementi archiviati nell'unità del sistema `(C:\)` operativo e nell'unità temporanea temporanea `(D:\)` . 

### <a name="operating-system-disk"></a>Disco del sistema operativo

Un disco del sistema operativo è un disco rigido virtuale che può essere avviato e montato come versione in esecuzione di un sistema operativo ed etichettato come `C:\` unità. Quando si crea una macchina virtuale di Azure, la piattaforma collega almeno un disco alla macchina virtuale per il disco del sistema operativo. `C:\`L'unità è il percorso predefinito per le installazioni di applicazioni e la configurazione dei file. 

Per gli ambienti SQL Server produzione, non usare il disco del sistema operativo per i file di dati, i file di log e i log degli errori. 

### <a name="temporary-disk"></a>Disco temporaneo

Molte macchine virtuali di Azure contengono un altro tipo di disco denominato disco temporaneo (etichettato come `D:\` unità). A seconda della serie di macchine virtuali e delle dimensioni, la capacità di questo disco varia. Il disco temporaneo è temporaneo, ovvero l'archiviazione su disco viene ricreata (ad esempio, viene deallocata e allocata di [](/troubleshoot/azure/virtual-machines/understand-vm-reboot)nuovo), quando la macchina virtuale viene riavviata o spostata in un host diverso (ad esempio, per la riparazione del servizio). 

L'unità di archiviazione temporanea non viene mantenuta nell'archiviazione remota e pertanto non deve archiviare file di database utente, file di log delle transazioni o qualsiasi elemento che deve essere mantenuto. 

Inserire tempdb nell'unità SSD temporanea locale per SQL Server carichi di lavoro, a meno che l'utilizzo della `D:\` cache locale non sia un problema. Se si usa una [](../../../virtual-machines/azure-vms-no-temp-disk.md) macchina virtuale che non dispone di un disco temporaneo, è consigliabile inserire tempdb nel proprio disco isolato o nel pool di archiviazione con la memorizzazione nella cache impostata su sola lettura. Per altre informazioni, vedere Criteri [di memorizzazione nella cache dei dati tempdb](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Dischi dati

I dischi dati sono dischi di archiviazione remota che vengono spesso creati [nei](/windows-server/storage/storage-spaces/overview) pool di archiviazione per superare la capacità e le prestazioni che un singolo disco potrebbe offrire alla macchina virtuale.

Collegare il numero minimo di dischi che soddisfano i requisiti di iops, velocità effettiva e capacità del carico di lavoro. Non superare il numero massimo di dischi dati della macchina virtuale più piccola in cui si prevede di ridimensionare.

Inserire i file di dati e di log nei dischi dati di cui è stato effettuato il provisioning per soddisfare al meglio i requisiti di prestazioni. 

Formattare il disco dati in modo che usi le dimensioni dell'unità di allocazione di 64 KB per tutti i file di dati posizionati in un'unità diversa dall'unità temporanea (con un valore predefinito `D:\` di 4 KB). SQL Server le macchine virtuali distribuite tramite Azure Marketplace con dischi dati formattati con le dimensioni dell'unità di allocazione e l'interfoliazione per il pool di archiviazione impostato su 64 KB. 

## <a name="premium-disks"></a>Dischi Premium

Usare dischi SSD Premium per i file di dati e di log per i carichi di lavoro SQL Server di produzione. SSD Premium iops e la larghezza di banda variano in base alle dimensioni [del disco e al tipo](../../../virtual-machines/disks-types.md). 

Per i carichi di lavoro di produzione, usare i dischi P30 e/o P40 per i file di dati SQL Server per garantire il supporto della memorizzazione nella cache e usare P30 fino a P80 per i file di log delle transazioni SQL Server.  Per il miglior costo totale di proprietà, iniziare con P30s (5000 IOPS/200 MBPS) per i file di dati e di log e scegliere capacità superiori solo quando è necessario controllare il numero di dischi delle macchine virtuali.

Per i carichi di lavoro OLTP, abbinare le operazioni di I/O al secondo di destinazione per disco (o pool di archiviazione) ai requisiti di prestazioni che usano i carichi di lavoro nelle ore di punta e i contatori `Disk Reads/sec`  +  `Disk Writes/sec` delle prestazioni. Per i data warehouse e i report, associare la velocità effettiva di destinazione usando i carichi di lavoro nei momenti di picco e `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Usare Spazi di archiviazione per ottenere prestazioni ottimali, configurare due pool, uno per i file di log e l'altro per i file di dati. Se non si usa lo striping del disco, usare due dischi SSD Premium mappati a unità separate, dove un'unità contiene il file di log e l'altra contiene i dati.

Le [operazioni di I/O al secondo di](../../../virtual-machines/disks-types.md#premium-ssd) cui è stato effettuato il provisioning e la velocità effettiva per ogni disco usato come parte del pool di archiviazione. Le funzionalità combinate di operazioni di I/O al secondo e velocità effettiva dei dischi sono la capacità massima fino ai limiti di velocità effettiva della macchina virtuale.

La procedura consigliata consiste nell'usare il minor numero possibile di dischi, pur soddisfare i requisiti minimi per le operazioni di I/O al secondo (e la velocità effettiva) e la capacità. Tuttavia, l'equilibrio tra prezzo e prestazioni tende a essere migliore con un numero elevato di dischi di piccole dimensioni anziché con un numero ridotto di dischi di grandi dimensioni.

### <a name="scaling-premium-disks"></a>Ridimensionamento dei dischi Premium

Quando un disco gestito di Azure viene distribuito per la prima volta, il livello di prestazioni per tale disco si basa sulle dimensioni del disco di cui è stato effettuato il provisioning. Designare il livello di prestazioni in fase di distribuzione o modificarlo in un secondo momento, senza modificare le dimensioni del disco. Se la domanda aumenta, è possibile aumentare il livello di prestazioni per soddisfare le esigenze aziendali. 

La modifica del livello di prestazioni consente agli amministratori di prepararsi per soddisfare una domanda più elevata senza basarsi [sul bursting del disco.](../../../virtual-machines/disk-bursting.md#credit-based-bursting) 

Usare le prestazioni superiori per tutto il tempo necessario in cui la fatturazione è progettata per soddisfare il livello di prestazioni di archiviazione. Aggiornare il livello in modo che corrisponda ai requisiti di prestazioni senza aumentare la capacità. Tornare al livello originale quando le prestazioni aggiuntive non sono più necessarie.

Questa espansione economica e temporanea delle prestazioni è un caso d'uso efficace per eventi mirati come acquisti, test delle prestazioni, eventi di training e altre brevi finestre in cui sono necessarie prestazioni maggiori solo a breve termine. 

Per altre informazioni, vedere [Livelli di prestazioni per i dischi gestiti](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Disco ultra di Azure

In caso di tempi di risposta di sottomillisecondi con latenza ridotta, è consigliabile usare il disco Ultra di [Azure](../../../virtual-machines/disks-types.md#ultra-disk) per l'unità di log di SQL Server o anche l'unità dati per le applicazioni estremamente sensibili alla latenza di I/O. 

È possibile configurare un disco Ultra in cui la capacità e le operazioni di I/O al secondo possono essere ridimensionate in modo indipendente. Con gli amministratori ultra disk è possibile effettuare il provisioning di un disco con i requisiti di capacità, operazioni di I/O al secondo e velocità effettiva in base alle esigenze dell'applicazione. 

Il disco Ultra non è supportato in tutte le serie di macchine virtuali e presenta altre limitazioni, ad esempio la disponibilità dell'area, la ridondanza e il supporto per Backup di Azure. Per altre informazioni, vedere [Uso dei dischi Ultra di Azure](../../../virtual-machines/disks-enable-ultra-ssd.md) per un elenco completo delle limitazioni. 

## <a name="standard-hdds-and-ssds"></a>HDD e SSD standard

[I dispositivi HDD](../../../virtual-machines/disks-types.md#standard-hdd) e SSD standard hanno latenze e larghezza di banda variabili e sono consigliati solo per carichi di lavoro di sviluppo/test. Per i carichi di lavoro di produzione si consiglia di usare le unità SSD Premium. Se si usa SSD Standard (scenari di sviluppo/test), è consigliabile aggiungere il numero [](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) massimo di dischi dati supportati dalle dimensioni della macchina virtuale e usare lo striping del disco con Spazi di archiviazione per ottenere prestazioni ottimali.

## <a name="caching"></a>Memorizzazione nella cache

Le macchine virtuali che supportano la memorizzazione nella cache di archiviazione Premium possono sfruttare una funzionalità aggiuntiva denominata BlobCache di Azure o memorizzazione nella cache host per estendere le funzionalità di I/O al secondo e velocità effettiva di una macchina virtuale. Le macchine virtuali abilitate sia per l'archiviazione Premium che per la memorizzazione nella cache di archiviazione Premium hanno questi due diversi limiti di larghezza di banda di archiviazione che possono essere usati insieme per migliorare le prestazioni di archiviazione.

La velocità effettiva di operazioni di I/O al secondo e MBps senza memorizzazione nella cache viene conteggiata rispetto ai limiti di velocità effettiva del disco non memorizzati nella cache di una macchina virtuale. I limiti massimi memorizzati nella cache forniscono un buffer aggiuntivo per le operazioni di lettura che consentono di risolvere la crescita e i picchi imprevisti.

Abilitare la memorizzazione nella cache Premium ogni volta che l'opzione è supportata per migliorare significativamente le prestazioni per le operazioni di lettura sull'unità dati senza costi aggiuntivi. 

Le operazioni di lettura e scrittura in BlobCache di Azure (operazioni di I/O al secondo memorizzate nella cache e velocità effettiva) non vengono conteggiati rispetto ai limiti di I/O al secondo non memorizzati nella cache e della velocità effettiva della macchina virtuale.

> [!NOTE]
> La memorizzazione nella cache del disco non è supportata per i dischi da 4 TiB e superiori (P50 e versioni superiori). Se alla VM sono collegati più dischi, ogni disco con dimensioni inferiori a 4 TiB supporta la memorizzazione nella cache. Per altre informazioni, vedere [Memorizzazione nella cache del disco](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Velocità effettiva non memorizzata nella cache

Il numero massimo di operazioni di I/O al secondo e velocità effettiva del disco non memorizzati nella cache è il limite massimo di archiviazione remota che la macchina virtuale può gestire. Questo limite è definito nella macchina virtuale e non è un limite dell'archiviazione su disco sottostante. Questo limite si applica solo alle unità dati collegate in remoto alla macchina virtuale, non all'I/O locale sull'unità temporanea `D:\` (unità) o sull'unità del sistema operativo.

La quantità di operazioni di I/O al secondo non memorizzate nella cache e velocità effettiva disponibili per una macchina virtuale può essere verificata nella documentazione della macchina virtuale.

Ad esempio, la documentazione della serie [M](../../../virtual-machines/m-series.md) mostra che la velocità effettiva massima non memorizzata nella cache per la macchina virtuale Standard_M8ms è di 5000 operazioni di I/O al secondo e 125 MBps di velocità effettiva del disco non memorizzata nella cache. 

![Screenshot che mostra la documentazione sulla velocità effettiva del disco non memorizzata nella cache della serie M.](./media/performance-guidelines-best-practices/m-series-table.png)

Analogamente, è possibile vedere che il Standard_M32ts supporta 20.000 operazioni di I/O al secondo non memorizzate nella cache e una velocità effettiva del disco non memorizzata nella cache di 500 MBps. Questo limite viene regolato a livello di macchina virtuale indipendentemente dall'archiviazione su disco Premium sottostante.

Per altre informazioni, vedere Limiti non memorizzati nella [cache e memorizzati nella cache](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Velocità effettiva di archiviazione temporanea e memorizzata nella cache

Il limite massimo di velocità effettiva di archiviazione temporanea e memorizzata nella cache è un limite separato dal limite di velocità effettiva non memorizzato nella cache nella macchina virtuale. BlobCache di Azure è costituito da una combinazione di memoria ad accesso casuale dell'host macchina virtuale e unità SSD collegata in locale. Anche l'unità temporanea (unità) all'interno della macchina virtuale è `D:\` ospitata in questo SSD locale.

Il limite massimo di velocità effettiva dell'archiviazione temporanea e memorizzata nella cache regola l'I/O rispetto all'unità temporanea locale (unità) e a BlobCache di Azure solo se è abilitata la memorizzazione nella `D:\` cache dell'host.  

Quando la memorizzazione nella cache è abilitata nell'archiviazione Premium, le macchine virtuali possono superare le limitazioni delle operazioni di I/O al secondo e della velocità effettiva delle macchine virtuali non memorizzate nella cache di archiviazione remota.  

Solo alcune macchine virtuali supportano sia l'archiviazione Premium che la memorizzazione nella cache di archiviazione Premium (che deve essere verificata nella documentazione della macchina virtuale). Ad esempio, la [documentazione della serie M](../../../virtual-machines/m-series.md) indica che sono supportati sia l'archiviazione Premium che la memorizzazione nella cache di archiviazione Premium: 

![Screenshot che mostra il supporto Archiviazione Premium M-Series.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

I limiti della cache variano in base alle dimensioni della macchina virtuale. Ad esempio, la macchina virtuale Standard_M8ms supporta 10000 operazioni di I/O al secondo su disco memorizzate nella cache e 1000 MBps di velocità effettiva del disco memorizzato nella cache con una dimensione totale della cache di 793 GiB. Analogamente, la macchina virtuale Standard_M32ts supporta 40000 operazioni di I/O al secondo su disco memorizzate nella cache e velocità effettiva del disco memorizzata nella cache di 400 MB con una dimensione totale della cache di 3174 GiB. 

![Screenshot che mostra la documentazione sulla velocità effettiva del disco memorizzata nella cache della serie M.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

È possibile abilitare manualmente la memorizzazione nella cache dell'host in una macchina virtuale esistente. Arrestare tutti i carichi di lavoro dell'applicazione e SQL Server prima di apportare modifiche ai criteri di memorizzazione nella cache della macchina virtuale. Se si modificano le impostazioni della cache delle macchine virtuali, il disco di destinazione viene scollegato e ricollegare dopo l'applicazione delle impostazioni.

### <a name="data-file-caching-policies"></a>Criteri di memorizzazione nella cache dei file di dati

I criteri di memorizzazione nella cache variano a seconda del tipo SQL Server file di dati ospitati nell'unità. 

La tabella seguente fornisce un riepilogo dei criteri di memorizzazione nella cache consigliati in base al tipo di SQL Server dati: 

|SQL Server disco |Recommendation |
|---------|---------|
| **Disco dati** | Abilitare `Read-only` la memorizzazione nella cache per i dischi che ospitano SQL Server file di dati. <br/> Le operazioni di lettura dalla cache saranno più veloci rispetto alle operazioni di lettura non memorizzate nella cache dal disco dati. <br/> Le operazioni di I/O al secondo non memorizzate nella cache e la velocità effettiva, oltre alle operazioni di I/O al secondo memorizzate nella cache, producono le prestazioni totali possibili disponibili dalla macchina virtuale entro i limiti delle macchine virtuali, ma le prestazioni effettive variano in base alla capacità del carico di lavoro di usare la cache (percentuale di riscontri cache). <br/>|
|**Disco del log delle transazioni**|Impostare i criteri di memorizzazione nella `None` cache su per i dischi che ospitano il log delle transazioni.  L'abilitazione della memorizzazione nella cache per il disco del log delle transazioni non ha alcun vantaggio in termini di prestazioni e di fatto la memorizzazione nella cache nell'unità di log può ridurre le prestazioni delle operazioni di scrittura sull'unità e ridurre la quantità di cache disponibile per le operazioni di lettura nell'unità `Read-only` `Read/Write` dati.  |
|**Disco del sistema operativo** | I criteri di memorizzazione nella cache predefiniti possono `Read-only` essere o `Read/write` per l'unità del sistema operativo. <br/> Non è consigliabile modificare il livello di memorizzazione nella cache dell'unità del sistema operativo.  |
| **tempdb**| Se tempdb non può essere inserito nell'unità temporanea per motivi di capacità, ridimensionare la macchina virtuale per ottenere un'unità temporanea più grande o inserire tempdb in un'unità dati separata con la memorizzazione nella cache `D:\` `Read-only` configurata. <br/> La cache delle macchine virtuali e l'unità temporanea usano entrambe l'unità SSD locale, quindi tenere presente questo problema quando il dimensionamento come tempdb I/O conterà i limiti delle operazioni di I/O al secondo memorizzate nella cache e della velocità effettiva della macchina virtuale quando sono ospitate nell'unità temporanea.| 
| | | 


Per altre informazioni, vedere [Memorizzazione nella cache del disco.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Striping del disco

Analizzare la velocità effettiva e la larghezza di banda necessarie per i file di dati SQL per determinare il numero di dischi dati, inclusi il file di log e tempdb. I limiti di velocità effettiva e larghezza di banda variano in base alle dimensioni della macchina virtuale. Per altre informazioni, vedere Dimensioni [della macchina virtuale](../../../virtual-machines/sizes.md)

Aggiungere altri dischi dati e usare lo striping del disco per una maggiore velocità effettiva. Ad esempio, un'applicazione che richiede 12.000 operazioni di I/O al secondo e 180 MB/s di velocità effettiva può usare tre dischi P30 con striped per offrire 15.000 iops e 600 MB/s di velocità effettiva. 

Per configurare lo striping del disco, vedere [Striping del disco](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Limite del disco 

Esistono limiti di velocità effettiva sia a livello di disco che di macchina virtuale. I limiti massimi di I/O al secondo per ogni macchina virtuale e per ogni disco sono diversi e sono indipendenti l'uno dall'altro.

Le applicazioni che utilizzano risorse oltre questi limiti verranno limitate (note anche come limiti). Selezionare una macchina virtuale e le dimensioni del disco in uno stripe del disco che soddisfino i requisiti dell'applicazione e non presentino limitazioni di limitazione. Per risolvere il limite, usare la memorizzazione nella cache o ottimizzare l'applicazione in modo che sia necessaria una velocità effettiva inferiore.

Ad esempio, un'applicazione che richiede 12.000 operazioni di I/O al secondo e 180 MB/s può: 
- Usare il [Standard_M32ms](../../../virtual-machines/m-series.md) che ha una velocità effettiva massima del disco non memorizzata nella cache di 20.000 operazioni di I/O al secondo e 500 MBps.
- Stripe tre dischi P30 per offrire 15.000 operazioni di I/O al secondo e 600 MB/s di velocità effettiva.
- Usare una macchina [Standard_M16ms](../../../virtual-machines/m-series.md) macchina virtuale e usare la memorizzazione nella cache host per usare la cache locale rispetto all'utilizzo della velocità effettiva. 

Le macchine virtuali configurate per aumentare le prestazioni durante i periodi di utilizzo elevato devono effettuare il provisioning dell'archiviazione con un numero di operazioni di I/O al secondo e velocità effettiva sufficienti per supportare le dimensioni massime della macchina virtuale mantenendo il numero complessivo di dischi minore o uguale al numero massimo supportato dallo SKU della macchina virtuale più piccolo destinato all'uso.

Per altre informazioni sulle limitazioni di limitazione del disco e sull'uso della memorizzazione nella cache per evitare limiti, vedere Limitazione delle [operazioni di I/O su disco.](../../../virtual-machines/disks-performance.md)

> [!NOTE] 
> Un certo limite del disco può comunque comportare prestazioni soddisfacenti per gli utenti. Ottimizzare e gestire i carichi di lavoro invece di ridimensionarsi in una macchina virtuale più grande per bilanciare la gestione dei costi e delle prestazioni per l'azienda. 


## <a name="write-acceleration"></a>Accelerazione della scrittura

L'accelerazione della scrittura è una funzionalità del disco disponibile solo per le macchine virtuali serie [M.](https://docs.microsoft.com/azure/virtual-machines/m-series) Lo scopo dell'accelerazione della scrittura è migliorare la latenza di I/O delle scritture in Azure Archiviazione Premium quando è necessaria una latenza di I/O a cifra singola a causa di carichi di lavoro OLTP cruciali per volumi elevati o ambienti data warehouse. 

Usare l'accelerazione della scrittura per migliorare la latenza di scrittura nell'unità che ospita i file di log. Non usare l'accelerazione di scrittura per SQL Server file di dati. 

acceleratore di scrittura dischi condividono lo stesso limite di operazioni di I/O al secondo della macchina virtuale. I dischi collegati non possono superare il limite acceleratore di scrittura iops per una macchina virtuale.  

La tabella seguente descrive il numero di dischi dati e di operazioni di I/O al secondo supportati per ogni macchina virtuale: 

| SKU di VM  | N. acceleratore di scrittura dischi  | acceleratore di scrittura operazioni di I/O al secondo del disco per macchina virtuale  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Esistono alcune restrizioni per l'uso dell'accelerazione di scrittura. Per altre informazioni, vedere [Restrizioni quando si usa acceleratore di scrittura](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Confronto con il disco Ultra di Azure

La differenza principale tra l'accelerazione della scrittura e i dischi Ultra di Azure è che l'accelerazione della scrittura è una funzionalità di macchina virtuale disponibile solo per i dischi serie M e Ultra di Azure è un'opzione di archiviazione. L'accelerazione della scrittura è una cache ottimizzata per la scrittura con limitazioni proprie in base alle dimensioni della macchina virtuale. I dischi Ultra di Azure sono un'opzione di archiviazione su disco a bassa latenza per macchine virtuali di Azure. 

Se possibile, usare l'accelerazione della scrittura su dischi Ultra per il disco del log delle transazioni. Per le macchine virtuali che non supportano l'accelerazione della scrittura ma richiedono una bassa latenza per il log delle transazioni, usare i dischi Ultra di Azure. 

## <a name="monitor-storage-performance"></a>Monitorare le prestazioni di archiviazione

Per valutare le esigenze di archiviazione e determinare le prestazioni dell'archiviazione, è necessario comprendere cosa misurare e cosa significano questi indicatori. 

[IOPS (Input/Output al secondo)](../../../virtual-machines/premium-storage-performance.md#iops) è il numero di richieste che l'applicazione sta effettuando per l'archiviazione al secondo. Misurare le operazioni di I/O al secondo usando Monitor prestazioni contatori `Disk Reads/sec` e `Disk Writes/sec` . [Le applicazioni OLTP (Online Transaction Processing)](/azure/architecture/data-guide/relational-data/online-transaction-processing) devono migliorare le operazioni di I/O al secondo per ottenere prestazioni ottimali. Applicazioni come i sistemi di elaborazione dei pagamenti, gli acquisti online e i sistemi di punti vendita al dettaglio sono tutti esempi di applicazioni OLTP.

[La](../../../virtual-machines/premium-storage-performance.md#throughput) velocità effettiva è il volume di dati che viene inviato all'archiviazione sottostante, spesso misurato in megabyte al secondo. Misurare la velocità effettiva con Monitor prestazioni contatori `Disk Read Bytes/sec` e `Disk Write Bytes/sec` . [Il data warehousing è ottimizzato](/azure/architecture/data-guide/relational-data/data-warehousing) per ottimizzare la velocità effettiva rispetto alle operazioni di I/O al secondo. Applicazioni come gli archivi dati per l'analisi, la creazione di report, i workstream ETL e altre destinazioni business intelligence sono tutti esempi di applicazioni di data warehousing.

Le dimensioni delle unità di I/O influiscono sulle funzionalità di I/O al secondo e sulla velocità effettiva, poiché dimensioni di I/O inferiori producono operazioni di I/O più elevate e dimensioni di I/O maggiori producono una velocità effettiva maggiore. SQL Server sceglie automaticamente le dimensioni di I/O ottimali. Per altre informazioni, vedere [Ottimizzare le operazioni di I/O al secondo, la velocità effettiva e la latenza per le applicazioni.](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance) 

Esistono metriche Monitoraggio di Azure che sono molto importanti per l'individuazione del limite a livello di macchina virtuale e disco, nonché per l'utilizzo e l'integrità della cache di AzureBlob. Per identificare i contatori chiave da aggiungere alla soluzione di monitoraggio e al dashboard portale di Azure, vedere Metriche [di utilizzo dell'archiviazione.](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics) 

> [!NOTE]
> Monitoraggio di Azure attualmente non offre metriche a livello di disco per l'unità temporanea temporanea `(D:\)` . Vm Cached IOPS Consumed Percentage and VM Cached Bandwidth Consumed Percentage will reflect IOPS and throughput from both the ephemeral temp drive and `(D:\)` host caching together.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle procedure consigliate per le prestazioni, vedere gli altri articoli di questa serie:
- [Elenco di controllo rapido](performance-guidelines-best-practices-checklist.md)
- [Dimensioni macchina virtuale](performance-guidelines-best-practices-vm-size.md)
- [Raccogliere la baseline](performance-guidelines-best-practices-collect-baseline.md)

Per le procedure consigliate per la sicurezza, vedere [Considerazioni sulla sicurezza SQL Server nelle macchine virtuali di Azure.](security-considerations-best-practices.md)

Per i test dettagliati delle prestazioni SQL Server nelle macchine virtuali di Azure con i benchmark TPC-E e TPC_C, fare riferimento al blog sull'[ottimizzazione delle prestazioni OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).
