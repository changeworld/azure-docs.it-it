---
title: 'Elenco di controllo: procedure consigliate sulle prestazioni & linee guida'
description: Fornisce un elenco di controllo rapido per esaminare le procedure consigliate e le linee guida per ottimizzare le prestazioni della SQL Server in una macchina virtuale (VM) di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 84f2f4f679de80cd9b5fc986d40e084bae8a4cad
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313760"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Elenco di controllo: procedure consigliate per le prestazioni per SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce un elenco di controllo rapido come una serie di procedure consigliate e linee guida per ottimizzare le prestazioni della SQL Server in macchine virtuali (VM) di Azure. 

Per informazioni dettagliate, vedere gli altri articoli di questa serie: [dimensioni della macchina virtuale](performance-guidelines-best-practices-vm-size.md), [archiviazione](performance-guidelines-best-practices-storage.md), [Raccogli Baseline](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Panoramica

Durante l'esecuzione di SQL Server in macchine virtuali di Azure, continuare a usare le stesse opzioni di ottimizzazione delle prestazioni del database applicabili ai SQL Server in ambienti server locali. Tuttavia, le prestazioni di un database relazionale in un cloud pubblico dipendono da molti fattori, ad esempio le dimensioni di una macchina virtuale e la configurazione dei dischi dati.

Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questa serie di procedure consigliate per le prestazioni è incentrata su come ottenere le *migliori* prestazioni per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è meno impegnativo, potrebbe non essere necessario ogni ottimizzazione consigliata. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.

## <a name="vm-size"></a>Dimensioni macchina virtuale

Di seguito è riportato un elenco rapido delle procedure consigliate per le dimensioni delle VM per l'esecuzione di SQL Server in una macchina virtuale di Azure: 

- Usare le dimensioni delle macchine virtuali con 4 o più vCPU come [Standard_M8-4ms](../../../virtual-machines/m-series.md), il [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)o il [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) o superiore. 
- Usare le dimensioni delle macchine virtuali con ottimizzazione per la [memoria](../../../virtual-machines/sizes-memory.md) per ottimizzare le prestazioni dei carichi di lavoro SQL Server. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), serie [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , [M-](../../../virtual-machines/m-series.md)e la serie [Mv2](../../../virtual-machines/mv2-series.md) offrono il rapporto ottimale tra memoria e vCore richiesto per i carichi di lavoro OLTP. Entrambe le VM serie M offrono il rapporto massimo tra memoria e vCore richiesto per i carichi di lavoro mission-critical e sono ideali anche per carichi di lavoro data warehouse. 
- Prendere in considerazione un rapporto più elevato tra memoria e vCore per carichi di lavoro mission-critical e data warehouse. 
- Usare le immagini del Marketplace per le macchine virtuali di Azure perché le impostazioni di SQL Server e le opzioni di archiviazione sono configurate per prestazioni SQL Server ottimali. 
- Raccogliere le caratteristiche di prestazioni del carico di lavoro di destinazione e usarle per determinare le dimensioni appropriate della macchina virtuale per l'azienda.

Per altre informazioni, vedere le procedure consigliate per le [dimensioni delle VM](performance-guidelines-best-practices-vm-size.md)complete. 

## <a name="storage"></a>Archiviazione

Di seguito è riportato un rapido elenco di controllo delle procedure consigliate per la configurazione dell'archiviazione per l'esecuzione di SQL Server nella VM di Azure: 

- Monitorare l'applicazione e [determinare i requisiti di larghezza di banda e latenza di archiviazione](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) per SQL Server i file di dati, di log e tempdb prima di scegliere il tipo di disco. 
- Per ottimizzare le prestazioni di archiviazione, pianificare le operazioni di i/o al secondo disponibili e usare la memorizzazione nella cache dei dati come funzionalità di prestazioni per le letture dei dati, evitando al contempo la [limitazione o la limitazione dei dischi e delle macchine virtuali](../../../virtual-machines/premium-storage-performance.md#throttling).
- Inserire i file di dati, di log e tempdb in unità separate.
    - Per l'unità dati, usare solo i [dischi P30 e P40 Premium](../../../virtual-machines/disks-types.md#premium-ssd) per garantire la disponibilità del supporto per la cache
    - Per il piano di unità di log per la capacità e le prestazioni di test rispetto al costo durante la valutazione dei [dischi P30-P80 Premium](../../../virtual-machines/disks-types.md#premium-ssd).
      - Se è necessaria la latenza di archiviazione di sottomillisecondi, usare i [dischi ultra di Azure](../../../virtual-machines/disks-types.md#ultra-disk) per il log delle transazioni. 
      - Per le distribuzioni di macchine virtuali della serie M, considerare [acceleratore di scrittura](../../../virtual-machines/how-to-enable-write-accelerator.md) sull'uso di dischi ultra di Azure.
    - Inserire [tempdb](/sql/relational-databases/databases/tempdb-database) nell'unità SSD temporanea locale `D:\` per la maggior parte dei carichi di lavoro SQL Server dopo aver scelto le dimensioni ottimali della macchina virtuale. 
      - Se la capacità dell'unità locale non è sufficiente per tempdb, provare a ridimensionare la macchina virtuale. Per ulteriori informazioni, vedere [criteri di memorizzazione nella cache dei file di dati](performance-guidelines-best-practices-storage.md#data-file-caching-policies) .
- Eseguire lo striping di più dischi dati di Azure usando [spazi di archiviazione](/windows-server/storage/storage-spaces/overview) per aumentare la larghezza di banda di i/O fino ai limiti di velocità effettiva e IOPS della macchina virtuale di destinazione.
- Impostare la [memorizzazione nella cache dell'host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) su sola lettura per i dischi di file di dati.
- Impostare la [memorizzazione nella cache dell'host](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) su None per i dischi dei file di log.
    - Non abilitare la memorizzazione nella cache in lettura/scrittura su dischi che contengono file di SQL Server. 
    - Arrestare sempre il servizio SQL Server prima di modificare le impostazioni della cache del disco.
- Per i carichi di lavoro di sviluppo e test, provare a usare l'archiviazione standard. Non è consigliabile usare HDD Standard/SDD per i carichi di lavoro di produzione.
- Il servizio di espansione del [disco basato sul credito](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) deve essere considerato solo per carichi di lavoro di sviluppo/test più piccoli e sistemi di reparto.
- Eseguire il provisioning dell'account di archiviazione nella stessa area della macchina virtuale SQL Server. 
- Disabilitare l'archiviazione con ridondanza geografica di Azure (replica geografica) e usare con ridondanza locale (archiviazione con ridondanza locale) nell'account di archiviazione.
- Formattare il disco dati per usare le dimensioni dell'unità di allocazione di 64 KB per tutti i file di dati presenti in un'unità diversa dall' `D:\` unità temporanea (il cui valore predefinito è 4 KB). SQL Server macchine virtuali distribuite tramite Azure Marketplace sono disponibili dischi dati formattati con le dimensioni dell'unità di allocazione e l'interfoliazione per il pool di archiviazione impostato su 64 KB. 

Per altre informazioni, vedere le [procedure consigliate](performance-guidelines-best-practices-storage.md)per l'archiviazione completa. 


## <a name="azure--sql-feature-specific"></a>Funzionalità specifiche di Azure & SQL

Di seguito è riportato un rapido elenco di controllo delle procedure consigliate per la SQL Server e le configurazioni specifiche di Azure durante l'esecuzione del SQL Server nella macchina virtuale di Azure: 

- Eseguire la registrazione con l' [estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) per sbloccare diversi [vantaggi della funzionalità](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Abilitare la compressione di pagina di database.
- Abilitare l'inizializzazione immediata dei file per i file di dati.
- Limitare l'aumento automatico del database.
- Disabilitare la compattazione automatica del database.
- Spostare tutti i database su dischi dati, inclusi i database di sistema.
- Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati.
- Configurare i percorsi predefiniti per i file di database e di backup.
- [Abilitare pagine bloccate in memoria](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Valutare e applicare gli [aggiornamenti cumulativi più recenti](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) per la versione installata di SQL Server.
- Eseguire il backup direttamente nell'archivio BLOB di Azure.
- Usare più file [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) , 1 file per core, fino a 8 file.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli altri articoli di questa serie:
- [Dimensioni macchina virtuale](performance-guidelines-best-practices-vm-size.md)
- [Archiviazione](performance-guidelines-best-practices-storage.md)
- [Raccogli Baseline](performance-guidelines-best-practices-collect-baseline.md)

Per le procedure di sicurezza consigliate, vedere [considerazioni sulla sicurezza per SQL Server in macchine virtuali di Azure](security-considerations-best-practices.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).
