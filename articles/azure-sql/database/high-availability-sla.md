---
title: Disponibilità elevata
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Informazioni sulle funzionalità e sulle funzionalità database SQL di Azure disponibilità elevata Istanza gestita SQL Istanza gestita
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 21ac73b461ebcb171f48621aa27a16dfc0e8c936
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781740"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Disponibilità elevata per database SQL di Azure e SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

L'obiettivo dell'architettura a disponibilità elevata in database SQL di Azure e SQL Istanza gestita è garantire che il database sia operativo almeno il 99,99% del tempo. Per altre informazioni sul contratto di servizio specifico per livelli diversi, vedere Contratto di servizio per database SQL di Azure e [SQL Istanza gestita](https://azure.microsoft.com/support/legal/sla/sql-database/)), senza preoccuparsi dell'impatto delle operazioni di manutenzione e delle interruzioni. Azure gestisce automaticamente le attività di manutenzione critiche, ad esempio l'applicazione di patch, i backup, gli aggiornamenti di Windows e Azure SQL, nonché gli eventi non pianificati, ad esempio gli errori hardware, software o di rete sottostanti.  Quando al database sottostante in database SQL di Azure viene eseguita una patch o viene eseguito [](develop-overview.md#resiliency) il fails over, il tempo di inattività non è evidente se si usa la logica di ripetizione dei tentativi nell'app. Il database SQL e il Istanza gestita SQL possono essere ripristinati rapidamente anche nelle circostanze più critiche assicurando che i dati sono sempre disponibili.

La soluzione a disponibilità elevata è progettata per garantire che i dati di cui è stato eseguito il commit non andranno mai persi a causa di errori, che le operazioni di manutenzione non influiscano sul carico di lavoro e che il database non sia un singolo punto di guasto nell'architettura software. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione.

Esistono due modelli architetturali a disponibilità elevata:

- **Modello di disponibilità standard** basato su una separazione tra calcolo e archiviazione.  Si basa sulla disponibilità elevata e sull'affidabilità del livello di archiviazione remota. Questa architettura è destinata alle applicazioni aziendali orientate al budget che possono tollerare una riduzione delle prestazioni durante le attività di manutenzione.
- **Modello di disponibilità Premium** basato su un cluster di processi del motore di database. Si basa sul fatto che esiste sempre un quorum di nodi del motore di database disponibili. Questa architettura è destinata ad applicazioni cruciali con prestazioni di I/O elevate, velocità di transazione elevata e garantisce un impatto minimo sulle prestazioni del carico di lavoro durante le attività di manutenzione.

Il database SQL e SQL Istanza gestita eseguono entrambi la versione stabile più recente del motore di database SQL Server e del sistema operativo Windows e la maggior parte degli utenti non noterebbe che gli aggiornamenti vengono eseguiti in modo continuo.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Disponibilità con ridondanza locale per utilizzo generico livello di servizio basic, standard e locale

I livelli di servizio Basic, Standard e per utilizzo generico sfruttano l'architettura di disponibilità standard sia per il calcolo serverless che per il provisioning. La figura seguente illustra quattro nodi diversi con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](./media/high-availability-sla/general-purpose-service-tier.png)

Il modello di disponibilità standard include due livelli:

- Livello di calcolo senza stato che esegue il processo e contiene solo dati temporanei e memorizzati nella cache, ad esempio TempDB, database modello nel disco SSD collegato e cache dei piani, pool di buffer e `sqlservr.exe` pool columnstore in memoria. Questo nodo senza stato viene gestito da Azure Service Fabric inizializza , controlla l'integrità del nodo ed esegue il failover in un `sqlservr.exe` altro nodo, se necessario.
- Livello dati con stato con i file di database (con estensione mdf/.ldf) archiviati nell'archivio BLOB di Azure. L'archiviazione BLOB di Azure include funzionalità di disponibilità e ridondanza dei dati incorporate. Garantisce che ogni record nel file di log o nella pagina nel file di dati verrà mantenuto anche in caso di arresto `sqlservr.exe` anomalo del processo.

Ogni volta che il motore di database o il sistema operativo viene aggiornato o viene rilevato un errore, Azure Service Fabric sposterà il processo senza stato in un altro nodo di calcolo senza stato con capacità disponibile `sqlservr.exe` sufficiente. I dati nell'archivio BLOB di Azure non sono interessati dallo spostamento e i file di dati/log vengono collegati al processo appena `sqlservr.exe` inizializzato. Questo processo garantisce una disponibilità del 99,99%, ma un carico di lavoro elevato potrebbe verificarsi una riduzione delle prestazioni durante la transizione poiché il nuovo processo `sqlservr.exe` inizia con la cache a freddo.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>per utilizzo generico disponibilità con ridondanza della zona del livello di servizio (anteprima)

La configurazione con ridondanza della zona per il livello di servizio per utilizzo generico è disponibile sia per le risorse di calcolo serverless che per le risorse di calcolo con provisioning. Questa configurazione [](../../availability-zones/az-overview.md) usa zone di disponibilità di Azure replicare i database in più posizioni fisiche all'interno di   un'area di Azure.Selezionando la ridondanza della zona, è possibile rendere i database singoli e i pool elastici nuovi ed esistenti serverless e con provisioning per utilizzo generico resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del data center, senza apportare modifiche alla logica dell'applicazione.

La configurazione con ridondanza della zona per il livello utilizzo generico ha due livelli:  

- Un livello dati con stato con i file di database (con estensione mdf/ldf) archiviati in ZRS (archiviazione con ridondanza della zona). Usando [l'archiviazione con](../../storage/common/storage-redundancy.md) accesso in lettura, i file di dati e di log vengono copiati in modo sincrono in tre zone di disponibilità di Azure isolate fisicamente.
- Livello di calcolo senza stato che esegue il processo sqlservr.exe e contiene solo dati temporanei e memorizzati nella cache, ad esempio TempDB, database modello nell'unità SSD collegata e cache dei piani, pool di buffer e pool columnstore in memoria. Questo nodo senza stato è gestito da Azure Service Fabric che inizializza sqlservr.exe, controlla l'integrità del nodo ed esegue il failover in un altro nodo, se necessario. Per i database per utilizzo generico serverless e con provisioning con ridondanza della zona, i nodi con capacità di riserva sono immediatamente disponibili in altri zone di disponibilità per il failover.

La versione con ridondanza della zona dell'architettura a disponibilità elevata per il livello di servizio per utilizzo generico è illustrata nel diagramma seguente:

![Configurazione con ridondanza della zona per utilizzo generico](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> La configurazione con ridondanza della zona è disponibile solo quando è selezionato l'hardware di calcolo Gen5. Questa funzionalità non è disponibile in SQL Istanza gestita. La configurazione con ridondanza della zona per il livello di utilizzo generico serverless e con provisioning è disponibile solo nelle aree seguenti: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale, Asia sud-orientale, Australia orientale, Giappone orientale, Regno Unito meridionale e Francia centrale.

> [!NOTE]
> per utilizzo generico database con dimensioni di 80 vcore possono verificarsi una riduzione delle prestazioni con la configurazione con ridondanza della zona. Inoltre, operazioni come il backup, il ripristino, la copia del database, la configurazione delle relazioni di ripristino geografico e il downgrade di un database con ridondanza della zona da business critical a per utilizzo generico possono verificarsi prestazioni più lente per i singoli database di dimensioni superiori a 1 TB. Per altre informazioni, [vedere la documentazione sulla latenza sul ridimensionamento](single-database-scale.md) di un database.
> 
> [!NOTE]
> L'anteprima non è trattata in Istanza riservata

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Disponibilità con ridondanza locale business critical Premium e livello di servizio

I livelli di servizio Premium e business critical sfruttano il modello di disponibilità Premium, che integra le risorse di calcolo (processo) e l'archiviazione `sqlservr.exe` (unità SSD collegata localmente) in un singolo nodo. La disponibilità elevata viene ottenuta replicando sia il calcolo che l'archiviazione in nodi aggiuntivi creando un cluster da tre a quattro nodi.

![Cluster di nodi di motore di database](./media/high-availability-sla/business-critical-service-tier.png)

I file di database sottostanti (con estensione mdf/.ldf) vengono inseriti nell'archiviazione SSD collegata per fornire operazioni di I/O a bassa latenza al carico di lavoro. La disponibilità elevata viene implementata usando una tecnologia simile a SQL Server [Gruppi di disponibilità Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Il cluster include una singola replica primaria accessibile per i carichi di lavoro dei clienti in lettura/scrittura e fino a tre repliche secondarie (calcolo e archiviazione) contenenti copie dei dati. Il nodo primario esegue costantemente il push delle modifiche ai nodi secondari in ordine e garantisce che i dati siano sincronizzati con almeno una replica secondaria prima di eseguire il commit di ogni transazione. Questo processo garantisce che se il nodo primario si arresta in modo anomalo per qualsiasi motivo, esiste sempre un nodo completamente sincronizzato su cui eseguire il failover. Il failover viene avviato dall'istanza di Azure Service Fabric. Quando la replica secondaria diventa il nuovo nodo primario, viene creata un'altra replica secondaria per garantire che il cluster abbia un numero sufficiente di nodi (set di quorum). Al termine del failover, Azure SQL connessioni vengono reindirizzate automaticamente al nuovo nodo primario.

Come vantaggio aggiuntivo, il modello di disponibilità Premium include la possibilità di reindirizzare le connessioni di sola Azure SQL a una delle repliche secondarie. Questa funzionalità è denominata [Scalabilità in lettura.](read-scale-out.md) Offre una capacità di calcolo aggiuntiva del 100% senza costi aggiuntivi per le operazioni di sola lettura off-load, ad esempio carichi di lavoro analitici, dalla replica primaria.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Disponibilità con ridondanza della zona del livello di servizio Premium e business critical servizio 

Per impostazione predefinita, il cluster di nodi per il modello di disponibilità Premium viene creato nello stesso data center. Con l'introduzione [zone di disponibilità di Azure](../../availability-zones/az-overview.md), il database SQL può inserire repliche diverse del database business critical in zone di disponibilità diverse nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md). Poiché la configurazione con ridondanza della zona nei livelli di servizio Premium o business critical non crea ridondanza aggiuntiva del database, è possibile abilitarla senza costi aggiuntivi. Selezionando una configurazione con ridondanza della zona, è possibile rendere i database Premium o business critical resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del data center, senza apportare modifiche alla logica dell'applicazione. È anche possibile convertire qualsiasi pool o database premium o business critical alla configurazione con ridondanza della zona.

Poiché i database con ridondanza della zona hanno repliche in data center diversi con una certa distanza tra di essi, l'aumento della latenza di rete può aumentare il tempo di commit e quindi influire sulle prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione online simile al normale aggiornamento del livello di servizio. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> Quando si usa il livello business critical, la configurazione con ridondanza della zona è disponibile solo quando è selezionato l'hardware di calcolo Gen5. Per informazioni aggiornate sulle aree che supportano i database con ridondanza della zona, vedere Supporto [dei servizi in base all'area.](../../availability-zones/az-region.md)

> [!NOTE]
> Questa funzionalità non è disponibile in SQL Istanza gestita.

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:

![architettura a disponibilità elevata con ridondanza della zona](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Disponibilità del livello di servizio Hyperscale

L'architettura del livello di servizio Hyperscale è descritta in [Architettura](./service-tier-hyperscale.md#distributed-functions-architecture) delle funzioni distribuite ed è attualmente disponibile solo per il database SQL, non per sql Istanza gestita.

![Architettura funzionale hyperscale](./media/high-availability-sla/hyperscale-architecture.png)

Il modello di disponibilità in Hyperscale include quattro livelli:

- Livello di calcolo senza stato che esegue i processi e contiene solo dati temporanei e memorizzati nella cache, ad esempio `sqlservr.exe` cache RBPEX non coperta, TempDB, database modello e così via, nel database SSD collegato e nella cache dei piani, nel pool di buffer e nel pool columnstore in memoria. Questo livello senza stato include la replica di calcolo primaria e, facoltativamente, una serie di repliche di calcolo secondarie che possono fungere da destinazioni di failover.
- Livello di archiviazione senza stato formato dai server di pagina. Questo livello è il motore di archiviazione distribuito per `sqlservr.exe` i processi in esecuzione nelle repliche di calcolo. Ogni server di pagine contiene solo dati temporanei e memorizzati nella cache, ad esempio la cache RBPEX nel disco SSD collegato e le pagine di dati memorizzate nella cache in memoria. Ogni server di pagine ha un server di pagine associato in una configurazione attivo-attivo per fornire bilanciamento del carico, ridondanza e disponibilità elevata.
- Livello di archiviazione con stato del log delle transazioni formato dal nodo di calcolo che esegue il processo del servizio di log, dalla zona di destinazione del log delle transazioni e dall'archiviazione a lungo termine del log delle transazioni. La zona di destinazione e l'archiviazione a [](../../storage/common/storage-redundancy.md) lungo termine usano Archiviazione di Azure, che offre disponibilità e ridondanza per il log delle transazioni, garantendo la durabilità dei dati per le transazioni di cui è stato eseguito il commit.
- Livello di archiviazione dati con stato con i file di database (con estensione mdf/.ndf) archiviati in Archiviazione di Azure e aggiornati dai server di pagina. Questo livello usa la disponibilità dei dati [e le funzionalità di ridondanza](../../storage/common/storage-redundancy.md) Archiviazione di Azure. Garantisce che ogni pagina in un file di dati verrà mantenuta anche se i processi in altri livelli dell'architettura Hyperscale si arrestano in modo anomalo o se i nodi di calcolo hanno esito negativo.

I nodi di calcolo in tutti i livelli Hyperscale vengono eseguiti in Azure Service Fabric, che controlla l'integrità di ogni nodo ed esegue i failover nei nodi integri disponibili in base alle esigenze.

Per altre informazioni sulla disponibilità elevata in Hyperscale, vedere [Disponibilità elevata del database in Hyperscale.](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)


## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database

[ripristino accelerato del database (ADR)](../accelerated-database-recovery.md) è una nuova funzionalità del motore di database che migliora notevolmente la disponibilità del database, soprattutto in presenza di transazioni a esecuzione elevata. AdR è attualmente disponibile per database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Test della resilienza degli errori dell'applicazione

La disponibilità elevata è una parte fondamentale del database SQL e della piattaforma Istanza gestita di SQL che funziona in modo trasparente per l'applicazione del database. Tuttavia, è possibile che si desideri testare il modo in cui le operazioni di failover automatico avviate durante gli eventi pianificati o non pianificati influiranno su un'applicazione prima di distribuirla nella produzione. È possibile attivare manualmente un failover chiamando un'API speciale per riavviare un database, un pool elastico o un'istanza gestita. Nel caso di un database elastico o un database elastico con ridondanza della zona senza per utilizzo generico server o con provisioning, la chiamata API comporta il reindirizzamento delle connessioni client al nuovo database primario in una zona di disponibilità diversa dalla zona di disponibilità del database primario precedente. Oltre a testare l'impatto del failover sulle sessioni di database esistenti, è anche possibile verificare se modifica le prestazioni end-to-end a causa di modifiche nella latenza di rete. Poiché l'operazione di riavvio è intrusiva e un numero elevato di questi potrebbe stressare la piattaforma, è consentita una sola chiamata di failover ogni 15 minuti per ogni database, pool elastico o istanza gestita.

Un failover può essere avviato tramite PowerShell, l'API REST o l'interfaccia della riga di comando di Azure:

|Tipo di distribuzione|PowerShell|API REST| Interfaccia della riga di comando di Azure|
|:---|:---|:---|:---|
|Database|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Failover del database](/rest/api/sql/databases/failover)|[Az rest può essere](/cli/azure/reference-index#az_rest) usato per richiamare una chiamata API REST dall'interfaccia della riga di comando di Azure|
|Pool elastico|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Failover del pool elastico](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[Az rest può essere](/cli/azure/reference-index#az_rest) usato per richiamare una chiamata API REST dall'interfaccia della riga di comando di Azure|
|database SQL|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Istanze gestite - Failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> Il comando Failover non è disponibile per le repliche secondarie leggibili dei database Hyperscale.

## <a name="conclusion"></a>Conclusione

database SQL di Azure e Istanza gestita di SQL di Azure una soluzione a disponibilità elevata predefinita, completamente integrata con la piattaforma Azure. Dipende dal Service Fabric per il rilevamento e il ripristino degli errori, dall'archiviazione BLOB di Azure per la protezione dei dati e da zone di disponibilità per una tolleranza di errore più elevata (come accennato in precedenza nel documento non applicabile Istanza gestita di SQL di Azure ancora). Inoltre, il database SQL e SQL Istanza gestita sfruttare Always On tecnologia del gruppo di disponibilità dall'istanza SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di realizzare appieno i vantaggi di un modello di archiviazione mista e di supportare i contratti di servizio più impegnativi.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../../availability-zones/az-overview.md)
- Informazioni sulle [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md)
- Informazioni [su come avviare un failover manuale in SQL Istanza gestita](../managed-instance/user-initiated-failover.md)
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md).
