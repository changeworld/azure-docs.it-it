---
title: Monitora le distribuzioni SQL con SQL Insights (anteprima)
description: Panoramica di SQL Insights in monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d01f80a803c5b0f9da067dd23ab8cdb4cc591a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609845"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitora le distribuzioni SQL con SQL Insights (anteprima)
SQL Insights monitora le prestazioni e l'integrità delle distribuzioni SQL.  Consente di offrire prestazioni prevedibili e la disponibilità di carichi di lavoro fondamentali creati in base a un back-end SQL identificando i colli di bottiglia e i problemi relativi alle prestazioni. SQL Insights archivia i dati nei [log di monitoraggio di Azure](../logs/data-platform-logs.md), che consentono di distribuire potenti aggregazioni e filtri e analizzare le tendenze dei dati nel tempo. È possibile visualizzare questi dati da monitoraggio di Azure nelle visualizzazioni fornite nell'ambito di questa offerta ed è possibile esaminare direttamente i dati di log per eseguire query e analizzare le tendenze.

SQL Insights non installa nulla nelle distribuzioni di SQL IaaS. USA invece macchine virtuali di monitoraggio dedicate per raccogliere dati in modalità remota per le distribuzioni SQL PaaS e SQL IaaS.  Il profilo di monitoraggio di SQL Insights consente di gestire i set di dati da raccogliere in base al tipo di SQL, tra cui il database SQL di Azure, il Istanza gestita SQL di Azure e SQL Server in esecuzione in una macchina virtuale di Azure.

## <a name="pricing"></a>Prezzi

Non è previsto alcun costo diretto per SQL Insights, ma la relativa attività viene addebitata nell'area di lavoro Log Analytics. In base ai prezzi pubblicati nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), SQL Insights viene fatturato per:

- Dati inseriti dagli agenti e archiviati nell'area di lavoro.
- Regole di avviso basate sui dati del log.
- Notifiche inviate dalle regole di avviso.

Le dimensioni del log variano in base alle lunghezze di stringa dei dati raccolti e possono aumentare con la quantità di attività del database. 

## <a name="supported-versions"></a>Versioni supportate 
SQL Insights supporta le seguenti versioni di SQL Server:

- SQL Server 2012 e versioni successive

SQL Insights supporta SQL Server in esecuzione negli ambienti seguenti:

- Database SQL di Azure
- Istanza gestita di SQL di Azure
- Macchine virtuali SQL di Azure
- Macchine virtuali di Azure

SQL Insights non offre supporto o supporto limitato per gli elementi seguenti:

- SQL Server in esecuzione in macchine virtuali all'esterno di Azure non sono attualmente supportate.
- Pool elastici del database SQL di Azure: supporto limitato durante l'anteprima pubblica. Saranno completamente supportati alla disponibilità generale.  
- Distribuzioni senza server SQL di Azure: come il ripristino di emergenza geografico attivo, gli agenti di monitoraggio attuali impediscono la sospensione della distribuzione senza server. Questo potrebbe causare costi superiori rispetto a quelli previsti dalle distribuzioni senza server.  
- Repliche secondarie leggibili: attualmente sono supportati solo i tipi di distribuzione con un singolo endpoint secondario leggibile (business critical o iperscala). Quando le distribuzioni con iperscalabilità supportano le repliche denominate, sarà possibile supportare più endpoint secondari leggibili per un singolo database logico.  
- Azure Active Directory: attualmente sono supportati solo gli account di accesso SQL per l'agente di monitoraggio. Si prevede di supportare Azure Active Directory in una versione imminente e non si dispone del supporto corrente per l'autenticazione della VM SQL con Active Directory in un controller di dominio personalizzato.  


## <a name="open-sql-insights"></a>Apri SQL Insights
Aprire SQL Insights selezionando **SQL (anteprima)** nella sezione **Insights** del menu di monitoraggio di **Azure** nel portale di Azure. Fare clic su un riquadro per caricare l'esperienza per il tipo di SQL monitorato.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Informazioni dettagliate su SQL in portale di Azure.":::


## <a name="enable-sql-insights"></a>Abilita SQL Insights 
Vedere [abilitare SQL Insights](sql-insights-enable.md) per la procedura dettagliata per abilitare SQL Insights oltre ai passaggi per la risoluzione dei problemi.


## <a name="data-collected-by-sql-insights"></a>Dati raccolti da SQL Insights
Nell'anteprima pubblica, SQL Insights supporta solo il metodo remoto di monitoraggio. L'agente Telegraf non è installato nel SQL Server. Usa il plug-in di input SQL Server per Telegraf e usa i tre gruppi di query per i diversi tipi di monitor it di SQL: database SQL di Azure, Istanza gestita SQL di Azure, SQL Server in esecuzione in una macchina virtuale di Azure. 

Le tabelle seguenti riepilogano quanto segue:

- Nome della query nel plug-in SqlServer per Telegraf
- Viste gestite dinamiche chiamate dalla query
- Spazio dei nomi in cui vengono visualizzati i dati nella tabella *InsighstMetrics*
- Indica se i dati vengono raccolti per impostazione predefinita
- Frequenza con cui vengono raccolti i dati per impostazione predefinita
 
È possibile modificare le query che vengono eseguite e la frequenza di raccolta dati quando si crea il profilo di monitoraggio. 

### <a name="azure-sql-db-data"></a>Dati del database SQL di Azure 

| Nome query | Vista a gestione dinamica | Spazio dei nomi | Abilitato per impostazione predefinita | Frequenza di raccolta predefinita |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | No | N/D |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Sì | 60 secondi |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Sì | 60 secondi |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Sì | 60 secondi |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>sys. database<br>sys. [database_service_objectives] | sqlserver_server_properties | Sì | 60 secondi |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Sì | 60 secondi |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Sì | 60 secondi |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Sì | 60 secondi |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | N/D |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | N/D  |

### <a name="azure-sql-managed-instance-data"></a>Dati dell'istanza gestita di SQL di Azure 

| Nome query | Vista a gestione dinamica | Spazio dei nomi | Abilitato per impostazione predefinita | Frequenza di raccolta predefinita |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Sì | 60 secondi |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Sì | 60 secondi |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Sì | 60 secondi |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Sì | 60 secondi |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Sì | 60 secondi |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Sì | 60 secondi |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Sì | 60 secondi |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | N/D |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | N/D |

### <a name="sql-server-data"></a>Dati di SQL Server

| Nome query | Vista a gestione dinamica | Spazio dei nomi | Abilitato per impostazione predefinita | Frequenza di raccolta predefinita |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Sì | 60 secondi |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Sì | 60 secondi | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Sì | 60 secondi |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Sì | 60 secondi |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Sì | 60 secondi |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | N/D |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | N/D |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Sì | 60 secondi |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Sì | 60 secondi |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 secondi |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 secondi |




## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata per abilitare SQL Insights, vedere [abilitare SQL Insights](sql-insights-enable.md) .
Vedere le [domande](../faq.md#sql-insights-preview) frequenti sulle domande frequenti su SQL Insights.
