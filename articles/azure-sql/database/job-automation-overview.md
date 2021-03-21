---
title: Panoramica di automazione dei processi con processi elastici
description: Usare i processi elastici per l'automazione dei processi per l'esecuzione di script Transact-SQL (T-SQL) in un set di uno o più database
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 1f4bd28d2b95aeebe07fcad84d757327622d51f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690431"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatizzare le attività di gestione usando processi elastici (anteprima)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

È possibile creare e pianificare processi elastici che possono essere eseguiti periodicamente su uno o più database SQL di Azure per eseguire query Transact-SQL (T-SQL) ed eseguire attività di manutenzione. 

È possibile definire il database o i gruppi di database di destinazione in cui verrà eseguito il processo, nonché le pianificazioni per l'esecuzione.
Un processo gestisce l'attività di accesso al database di destinazione. È anche possibile definire, gestire e mantenere script Transact-SQL da eseguire su un gruppo di database.

Ogni processo registra lo stato di esecuzione e ripete automaticamente le operazioni se si verificano errori.

## <a name="when-to-use-elastic-jobs"></a>Quando usare processi elastici

Esistono diversi scenari in cui è possibile usare l'automazione di processi elastici:

- Automatizzare le attività di gestione e quindi pianificare l'esecuzione in ogni giorno feriale, fuori orario lavorativo e così via.
  - Distribuire le modifiche dello schema, la gestione delle credenziali, la raccolta dei dati sulle prestazioni o la raccolta dei dati di telemetria del tenant (cliente).
  - Aggiornare i dati di riferimento (ossia le informazioni comuni tra tutti i database) e caricare dati dall'archivio BLOB di Azure.
- Configurare i processi per l'esecuzione in una raccolta di database su base periodica, ad esempio durante le fasce orarie non di punta.
  - Raccogliere i risultati di query da un set di database in una tabella centrale su base costante. Le query di prestazione possono essere eseguite continuamente e configurate per l'esecuzione di attività aggiuntive di trigger.
- Raccogliere i dati per i report
  - Aggregare i dati di una raccolta di database in una singola tabella di destinazione.
  - Eseguire query di elaborazione dei dati più lunghe per una vasta serie di database, ad esempio la raccolta della telemetria del cliente. I risultati vengono raccolti in una tabella di destinazione singola per ulteriori analisi.
- Spostare dati 

### <a name="automation-on-other-platforms"></a>Automazione su altre piattaforme

Prendere in considerazione le seguenti tecnologie di pianificazione dei processi su piattaforme diverse:

- I **processi elastici** sono servizi di pianificazione dei processi che eseguono processi personalizzati in uno o più database nel database SQL di Azure.
- I **processi di SQL Agent** vengono eseguiti dal servizio SQL Agent che continua a essere usato per l'automazione delle attività in SQL Server ed è incluso anche nelle istanze gestite di SQL di Azure. I processi di SQL Agent non sono disponibili in Database SQL di Azure.

I processi elastici possono avere come destinazione [database SQL](sql-database-paas-overview.md)di Azure, [pool elastici del database SQL di Azure](elastic-pool-overview.md)e database SQL di Azure nelle [mappe di partizionamento](elastic-scale-shard-map-management.md).

Per l'automazione dei processi di script T-SQL in SQL Server e Istanza gestita SQL di Azure, prendere in considerazione [SQL Agent](job-automation-managed-instances.md). 

Per l'automazione dei processi di script T-SQL in Azure sinapsi Analytics, prendere in considerazione [le pipeline con trigger ricorrenti](../../synapse-analytics/data-integration/concepts-data-factory-differences.md), [basate su Azure Data Factory](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

Vale la pena notare le differenze tra SQL Agent (disponibile in SQL Server e come parte di SQL Istanza gestita) e l'agente processo elastico di database (che può eseguire T-SQL su database o database SQL di Azure in SQL Server e Azure SQL Istanza gestita, Azure sinapsi Analytics).

| |Processi elastici |SQL Agent |
|---------|---------|---------|
|**Ambito** | Qualsiasi numero di database di Database SQL di Azure e/o di data warehouse nello stesso cloud di Azure dell'agente di processo. Le destinazioni possono trovarsi in server, sottoscrizioni e/o aree differenti. <br><br>I gruppi di destinazione possono essere costituiti da singoli database o data warehouse o da tutti i database in un server, un pool o una mappa partizioni (enumerati dinamicamente al runtime del processo). | Qualsiasi database singolo nella stessa istanza di SQL Agent. La funzionalità di amministrazione multiserver di SQL Server Agent consente alle istanze master/di destinazione di coordinare l'esecuzione del processo, anche se questa funzionalità non è disponibile nell'istanza gestita di SQL. |
|**API e strumenti supportati** | Portale, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Destinazioni processi elastici

I **processi elastici** offrono la possibilità di eseguire uno o più script T-SQL in parallelo, in un numero elevato di database, in base a una pianificazione o su richiesta.

È possibile eseguire processi pianificati in qualsiasi combinazione di database: uno o più database singoli, tutti i database in un server, tutti i database in un pool elastico o una mappa partizioni, con la flessibilità aggiuntiva per includere o escludere qualsiasi database specifico. I processi possono essere eseguiti in più server, in più pool e anche in database di sottoscrizioni differenti. I server e i pool vengono enumerati in modo dinamico in fase di esecuzione, quindi i processi vengono eseguiti su tutti i database esistenti nel gruppo di destinazione al momento dell'esecuzione.

La figura seguente mostra un agente di processo che esegue processi tra diversi tipi di gruppi di destinazione:

![Modello concettuale dell'agente di processo elastico](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Componenti dei processi elastici

|Componente | Descrizione (altri dettagli sono disponibili sotto la tabella) |
|---------|---------|
|[**Agente di processo elastico**](#elastic-job-agent) | Risorsa di Azure creata per eseguire e gestire i processi. |
|[**Database di processo**](#elastic-job-database) | Un database di Database SQL di Azure usato dall'agente di processo per archiviare dati, definizioni e così via correlati ai processi. |
|[**Gruppo di destinazione**](#target-group) | Il set di server, pool, database e mappe delle partizioni in cui eseguire un processo. |
|[**Processo**](#elastic-jobs-and-job-steps) | Un processo è un'unità di lavoro costituita da uno o più passaggi. I passaggi del processo specificano lo script T-SQL da eseguire, nonché altri dettagli necessari per eseguirlo. |

#### <a name="elastic-job-agent"></a>Agente processo elastico

Un agente di processo elastico è la risorsa di Azure per la creazione, l'esecuzione e la gestione dei processi. L'agente di processo elastico è una risorsa di Azure che viene creata nel portale (sono anche supportati [PowerShell](elastic-jobs-powershell-create.md) e REST).

La creazione di un **agente di processo elastico** richiede un database esistente in Database SQL di Azure. L'agente configura il database SQL di Azure esistente come [*database dei processi*](#elastic-job-database).

L'agente di processo elastico è gratuito. Il database di processo viene fatturato alla stessa tariffa di qualsiasi database di Database SQL di Azure.

#### <a name="elastic-job-database"></a>Database di processi elastici

Il *database di processo* viene usato per definire i processi e tracciare lo stato e la cronologia delle esecuzioni dei processi. Il *database di processo* viene anche usato per archiviare metadati dell'agente, log, risultati e definizioni dei processi e contiene anche molte utili stored procedure e altri oggetti del database per creare, eseguire e gestire i processi con T-SQL.

Per l'anteprima corrente, per creare un agente di processo elastico, è necessario un database esistente di Database SQL di Azure (S0 o superiore).

Il *database del processo* deve essere un database SQL di Azure pulito, vuoto, S0 o superiore. L'obiettivo di servizio consigliato per il *database di processo* è S1 o superiore, ma la scelta ottimale dipende dalle prestazioni richieste dai processi, ovvero numero di passaggi del processo, numero di obiettivi del processo e frequenza delle esecuzioni. 

Se le operazioni eseguite sul database dei processi sono più lente del previsto, [monitorare](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) le prestazioni del database e l'utilizzo delle risorse nel database dei processi durante i periodi di lentezza usando il portale di Azure o la DMV [sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database). Se l'utilizzo di una risorsa, ad esempio CPU, I/O dati o scrittura log si avvicina al 100% ed è correlato a periodi di lentezza, valutare il ridimensionamento incrementale del database a obiettivi di servizio più elevati (nel [modello DTU](service-tiers-dtu.md) o nel modello [vCore](service-tiers-vcore.md)) finché le prestazioni del database del processo non sono sufficientemente migliorate.

##### <a name="elastic-job-database-permissions"></a>Autorizzazioni del database del processo elastico

Durante la creazione di un agente di processo vengono creati uno schema, tabelle e un ruolo denominato *jobs_reader* nel *database di processo*. Il ruolo viene creato con l'autorizzazione seguente ed è progettato per fornire agli amministratori un controllo di accesso più preciso per il monitoraggio dei processi:

|Nome del ruolo |Autorizzazioni per lo schema "jobs" |Autorizzazioni per lo schema 'jobs_internal' |
|---------|---------|---------|
|**jobs_reader** | SELECT | nessuno |

> [!IMPORTANT]
> Prima di concedere l'accesso al *database di processo* come amministratore del database, considerare le implicazioni per la sicurezza. Un utente malintenzionato con autorizzazioni per la creazione o la modifica di processi potrebbe creare o modificare un processo che utilizza una credenziale archiviata per connettersi a un database con il controllo utente malintenzionato, che potrebbe consentire all'utente malintenzionato di determinare la password della credenziale.

#### <a name="target-group"></a>Gruppo di destinazione

Un *gruppo di destinazione* definisce il set di database sui quali verrà eseguito un passaggio di processo. Un gruppo di destinazione può contenere qualsiasi numero e una combinazione degli elementi seguenti:

- **Server logico di SQL Server**: se è specificato un server, tutti i database presenti al suo interno al momento dell'esecuzione del processo fanno parte del gruppo. È necessario fornire le credenziali del database master in modo che il gruppo possa essere enumerato e aggiornato prima dell'esecuzione del processo. Per altre informazioni sui server logici, vedere [che cos'è un server nel database SQL di Azure e in Azure sinapsi Analytics?](logical-servers.md).
- **Pool elastico**: se è specificato un pool elastico, tutti i database presenti nel pool elastico al momento dell'esecuzione del processo fanno parte del gruppo. Come per un server, è necessario fornire le credenziali del database master in modo che il gruppo possa essere aggiornato prima dell'esecuzione del processo.
- **Database singolo**: specificare uno o più database singoli da includere nel gruppo.
- **Mappa partizione** -database di una mappa partizioni.

> [!TIP]
> Al momento dell'esecuzione del processo, l'*enumerazione dinamica* rivaluta il set dei database nei gruppi di destinazione che includono server o pool. L'enumerazione dinamica assicura che i **processi vengano eseguiti in tutti i database esistenti nel server o nel pool al momento dell'esecuzione**. Rivalutare l'elenco dei database in fase di esecuzione è particolarmente utile per gli scenari in cui l'appartenenza al pool o al server cambia frequentemente.

I pool e i database singoli possono essere specificati come inclusi o esclusi dal gruppo. Ciò consente di creare un gruppo di destinazione con qualsiasi combinazione di database. È ad esempio possibile aggiungere un server a un gruppo di destinazione, ma escludere database specifici di un pool elastico o escludere un intero pool.

Un gruppo di destinazione può includere database in più sottoscrizioni e in più aree. Si noti che le esecuzioni tra più aree hanno una latenza maggiore rispetto alle esecuzioni nella stessa area.

Gli esempi seguenti illustrano come diverse definizioni del gruppo destinazione vengono enumerate in modo dinamico al momento dell'esecuzione del processo per determinare i database che verranno eseguiti:

![Esempi di gruppi di destinazione](./media/job-automation-overview/targetgroup-examples1.png)

L'**esempio 1** mostra un gruppo di destinazione costituito da un elenco di singoli database. Quando un passaggio del processo viene eseguito usando questo gruppo di destinazione, l'azione del passaggio verrà eseguita in ognuno di tali database.<br>
L'**esempio 2** mostra un gruppo di destinazione contenente un server. Quando un passaggio del processo viene eseguito usando questo gruppo di destinazione, il server viene enumerato in modo dinamico per determinare l'elenco dei database attualmente presenti nel server. L'azione del passaggio del processo verrà eseguita in ognuno di tali database.<br>
L'**esempio 3** mostra un gruppo di destinazione simile a quello dell'*esempio 2*, ma con l'esclusione specifica di un singolo database. L'azione del passaggio del processo *non* verrà eseguita nel database escluso.<br>
L'**esempio 4** mostra un gruppo di destinazione contenente un pool elastico come destinazione. Analogamente all'*esempio 2*, il pool verrà enumerato in modo dinamico in fase di esecuzione del processo per determinare l'elenco dei database nel pool.
<br><br>

![Altri esempi di gruppi di destinazione](./media/job-automation-overview/targetgroup-examples2.png)

L'**esempio 5** e l'**esempio 6** mostrano scenari avanzati in cui server, pool elastici e database possono essere combinati usando regole di inclusione e di esclusione.<br>
L'**esempio 7** mostra che in fase di esecuzione del processo possono essere valutate anche le partizioni in una mappa delle partizioni.

> [!NOTE]
> Il database del processo stesso può essere la destinazione di un processo. In questo scenario, il database del processo viene considerato come qualsiasi altro database di destinazione. È necessario aver creato l'utente del processo a cui concedere autorizzazioni sufficienti nel database del processo e nel database del processo devono anche esistere le credenziali con ambito database per l'utente del processo, come per qualsiasi altro database di destinazione.

#### <a name="elastic-jobs-and-job-steps"></a>Processi elastici e passaggi di processo

Un *processo* è un'unità di lavoro che viene eseguita in una pianificazione o come processo occasionale. Un processo è costituito da uno o più *passaggi*.

Ogni passaggio del processo specifica uno script T-SQL da eseguire, uno o più gruppi di destinazione in cui eseguire lo script T-SQL e le credenziali richieste dall'agente di processo per connettersi al database di destinazione. Ogni passaggio del processo ha criteri di timeout e ripetizione personalizzabili e può eventualmente specificare parametri di output.

#### <a name="job-output"></a>Output del processo

Il risultato dei passaggi di un processo in ciascun database di destinazione vengono registrati nei dettagli e l'output dello script può essere acquisito in una tabella specifica. È possibile specificare un database per salvare i dati restituiti da un processo.

#### <a name="job-history"></a>Cronologia dei processi

Visualizzare la cronologia di esecuzione del processo elastico nel *database del processo* eseguendo [una query sulla tabella jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). Un processo di pulizia del sistema elimina la cronologia dei processi eseguiti oltre 45 giorni prima. Per rimuovere la cronologia dei processi eseguiti entro i 45 giorni precedenti, richiamare la stored procedure **sp_purge_history** nel *database di processo*.

#### <a name="job-status"></a>Stato processo

È possibile monitorare le esecuzioni di processi elastici nel *database dei processi* eseguendo [una query sulla tabella jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Prestazioni, capacità e limitazioni degli agenti

I processi elastici usano risorse di calcolo minime in attesa del completamento di processi di lunga durata.

A seconda delle dimensioni del gruppo di database di destinazione e del tempo di esecuzione desiderato per un processo (numero di processi simultanei), l'agente richiede prestazioni e risorse di calcolo differenti per il *database di processo*: maggiore è il numero di destinazioni e di processi, maggiore sarà la quantità di risorse di calcolo necessarie.

Attualmente, il limite è di 100 processi simultanei.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedire ai processi di ridurre le prestazioni del database di destinazione

Per garantire che le risorse non siano sovraccariche quando si eseguono processi sul database in un pool elastico SQL, è possibile configurare i processi in modo da limitare il numero di database in cui un processo può essere eseguito contemporaneamente.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e gestire processi elastici](elastic-jobs-overview.md)
- [Creare e gestire processi elastici usando PowerShell](elastic-jobs-powershell-create.md)
- [Creare e gestire processi elastici usando Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)