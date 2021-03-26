---
title: 'Raccolta Baseline: procedure consigliate per le prestazioni & linee guida'
description: Viene descritta la procedura per raccogliere i dati di riferimento per le prestazioni come linee guida per ottimizzare le prestazioni della SQL Server in una macchina virtuale (VM) di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572381"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Raccolta Baseline: procedure consigliate per le prestazioni per SQL Server in una macchina virtuale di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce informazioni per raccogliere i dati di riferimento relativi alle prestazioni come una serie di procedure consigliate e linee guida per ottimizzare le prestazioni per la SQL Server in macchine virtuali (VM) di Azure.

Vi è in genere un compromesso tra l'ottimizzazione per i costi e l'ottimizzazione per le prestazioni. Questa serie di procedure consigliate per le prestazioni è incentrata su come ottenere le *migliori* prestazioni per SQL Server in macchine virtuali di Azure. Se il carico di lavoro è meno impegnativo, potrebbe non essere necessario ogni ottimizzazione consigliata. Prendere in considerazione le esigenze di prestazione, i costi e i modelli di carico di lavoro durante la valutazione di questi elementi consigliati.

## <a name="overview"></a>Panoramica

Per un approccio prescrittivo, raccogliere i contatori delle prestazioni usando PerfMon/LogMan e acquisire SQL Server le statistiche di attesa per comprendere meglio le pressioni generali e i potenziali colli di bottiglia dell'ambiente di origine. 

Per iniziare, raccogliere la CPU, la [memoria, le](../../../virtual-machines/premium-storage-performance.md#iops)operazioni di i/o al secondo, la [velocità effettiva](../../../virtual-machines/premium-storage-performance.md#throughput)e [la](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) [latenza](../../../virtual-machines/premium-storage-performance.md#latency) del carico di lavoro di origine in momenti di picco successivi a 

Raccogliere i dati durante le ore di picco, ad esempio i carichi di lavoro durante la giornata lavorativa tipica, ma anche altri processi di carico elevato, ad esempio l'elaborazione di fine giornata e i carichi di lavoro ETL del fine settimana. Prendere in considerazione la scalabilità verticale delle risorse per carichi di lavoro in modo atipico, ad esempio l'elaborazione di fine trimestre, quindi eseguire il ridimensionamento dopo il completamento del carico di lavoro. 

Usare l'analisi delle prestazioni per selezionare le [dimensioni della VM](../../../virtual-machines/sizes-memory.md) che possono essere ridimensionate in modo da soddisfare i requisiti di prestazioni del carico di lavoro


## <a name="storage"></a>Archiviazione

SQL Server prestazioni dipende molto dal sottosistema di I/O e le prestazioni di archiviazione vengono misurate in base a IOPS e velocità effettiva. A meno che il database non si inserisca nella memoria fisica, SQL Server porta costantemente le pagine del database all'interno e all'esterno del pool di buffer. I file di dati per SQL Server devono essere trattati in modo diverso. L'accesso ai file di log è sequenziale tranne quando è necessario eseguire il rollback di una transazione in cui è possibile accedere in modo casuale ai file di dati, incluso il database tempdb. Se si dispone di un sottosistema di I/O lento, gli utenti potrebbero riscontrare problemi di prestazioni, ad esempio tempi di risposta lenti e attività che non vengono completate a causa di timeout. 

Per impostazione predefinita, le macchine virtuali di Azure Marketplace hanno file di log in un disco fisico separato dai file di dati. Il numero e le dimensioni dei file di dati tempdb soddisfano le procedure consigliate e sono assegnati all' `D:\` unità temporanea. 

I contatori PerfMon seguenti consentono di convalidare la velocità effettiva di i/o richiesta dal SQL Server: 
* **\LogicalDisk\Disk letture/sec** (IOPS lettura)
* **Scritture \LogicalDisk\Disk/sec** (IOPS scrittura) 
* **Byte letti da \LogicalDisk\Disk/sec** (requisiti della velocità effettiva di lettura per i file di dati, log e tempdb)
* **\LogicalDisk\Disk byte scritti/sec** (requisiti di velocità effettiva di scrittura per i file di dati, di log e tempdb)

Usando i requisiti di IOPS e velocità effettiva ai livelli di picco, valutare le dimensioni delle VM che corrispondono alla capacità delle misurazioni. 

Se il carico di lavoro richiede 20.000 letture IOPS e 10.000 operazioni di i/o al secondo, è possibile scegliere E16s_v3 (con 32K memorizzato nella cache e 25600 IOPS non memorizzati nella cache) o M16_s (con un massimo di 20.000 memorizzati nella cache e 10.000 IOPS non memorizzati nella cache) con 2 dischi P30 con striping con spazi di archiviazione. 

Assicurarsi di comprendere i requisiti di velocità effettiva e IOPS del carico di lavoro perché le macchine virtuali hanno limiti di scalabilità diversi per IOPS e velocità effettiva.

## <a name="memory"></a>Memoria

Tenere traccia della memoria esterna usata dal sistema operativo e della memoria utilizzata internamente da SQL Server. L'identificazione della pressione per uno dei due componenti consente di ridimensionare le macchine virtuali e identificare le opportunità di ottimizzazione. 

I contatori PerfMon seguenti consentono di convalidare l'integrità della memoria di una macchina virtuale SQL Server: 
* [\Memoria\MByte disponibili](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: memoria memoria server Manager\Target (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: memoria memoria server Manager\Total (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy scritture/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\letture permanenza presunta](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Calcolo

Il calcolo in Azure viene gestito in modo diverso rispetto a quello locale. I server locali sono stati compilati per durare diversi anni senza un aggiornamento a causa del sovraccarico di gestione e dei costi di acquisizione di nuovi componenti hardware. La virtualizzazione mitiga alcuni di questi problemi, ma le applicazioni sono ottimizzate per sfruttare al meglio l'hardware sottostante, ovvero qualsiasi modifica significativa all'utilizzo delle risorse richiede il ribilanciamento dell'intero ambiente fisico. 

Non si tratta di una sfida in Azure, in cui una nuova macchina virtuale in una serie diversa di hardware e anche in un'area diversa è facile da realizzare. 

In Azure, si desidera sfruttare il maggior numero possibile di risorse delle macchine virtuali, pertanto le macchine virtuali di Azure devono essere configurate in modo da garantire la media della CPU più elevata possibile senza alcun effetto sul carico di lavoro. 

I contatori PerfMon seguenti consentono di convalidare l'integrità di calcolo di una macchina virtuale SQL Server:
* **Tempo processore informazioni \Processor (_Total) \%**
* **Tempo processore \Processo (sqlservr) \%**

> [!NOTE] 
> Idealmente, provare a puntare all'uso del 80% del calcolo, con picchi superiori al 90% ma che non raggiungano il 100% per un periodo di tempo prolungato. Fondamentalmente, è sufficiente eseguire il provisioning delle risorse di calcolo necessarie per l'applicazione e quindi pianificare la scalabilità verticale o verticale in base alle esigenze aziendali. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli altri articoli di questa serie:
- [Elenco di controllo rapido](performance-guidelines-best-practices-checklist.md)
- [Dimensioni macchina virtuale](performance-guidelines-best-practices-vm-size.md)
- [Archiviazione](performance-guidelines-best-practices-storage.md)


Per le procedure di sicurezza consigliate, vedere [considerazioni sulla sicurezza per SQL Server in macchine virtuali di Azure](security-considerations-best-practices.md).

Esaminare altri articoli relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).
