---
title: Monitora le distribuzioni SQL con SQL Insights (anteprima)
description: Panoramica di SQL Insights in monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567832"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitora le distribuzioni SQL con SQL Insights (anteprima)
SQL Insights è una soluzione completa per il monitoraggio di tutti i prodotti della [famiglia SQL di Azure](../../azure-sql/index.yml). SQL Insights usa le [viste a gestione dinamica](../../azure-sql/database/monitoring-with-dmvs.md) per esporre i dati necessari per monitorare l'integrità, diagnosticare i problemi e ottimizzare le prestazioni.  

SQL Insights esegue tutti i monitoraggi in remoto. Gli agenti di monitoraggio sulle macchine virtuali dedicate si connettono alle risorse SQL e raccolgono i dati in modalità remota. I dati raccolti vengono archiviati nei [log di monitoraggio di Azure](../logs/data-platform-logs.md), consentendo un'aggregazione semplice, un filtro e un'analisi delle tendenze. È possibile visualizzare i dati raccolti dal [modello di cartella di lavoro](../visualize/workbooks-overview.md)di SQL Insights oppure è possibile approfondire direttamente i dati usando le query di [log](../logs/get-started-queries.md).

## <a name="pricing"></a>Prezzi
Non è previsto alcun costo diretto per SQL Insights. Tutti i costi sono sostenuti dalle macchine virtuali che raccolgono i dati, dalle aree di lavoro Log Analytics in cui sono archiviati i dati e da eventuali regole di avviso configurate sui dati. 

**Macchine virtuali**

Per le macchine virtuali, i costi vengono addebitati in base ai prezzi pubblicati nella [pagina dei prezzi delle macchine virtuali](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/). Il numero di macchine virtuali necessarie può variare in base al numero di stringhe di connessione che si desidera monitorare. Si consiglia di allocare una macchina virtuale di dimensioni Standard_B2s per ogni stringa di connessione 100. Per altri dettagli, vedere [requisiti della macchina virtuale di Azure](sql-insights-enable.md#azure-virtual-machine-requirements) .

**Aree di lavoro di Log Analytics**

Per le aree di lavoro Log Analytics, i costi vengono addebitati in base ai prezzi pubblicati nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Le aree di lavoro Log Analytics utilizzate da SQL Insights comportano costi per l'inserimento dei dati, la conservazione dei dati e, facoltativamente, l'esportazione dei dati. Gli addebiti esatti variano in base alla quantità di dati inseriti, conservati ed esportati. La quantità di questi dati varia a seconda dell'attività del database e delle impostazioni di raccolta definite nei [profili di monitoraggio](sql-insights-enable.md#create-sql-monitoring-profile).

**Regole di avviso**

Per le regole di avviso in monitoraggio di Azure, i costi vengono addebitati in base ai prezzi pubblicati nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Se si sceglie di [creare avvisi con SQL Insights](sql-insights-alerts.md), vengono addebitate tutte le regole di avviso create e tutte le notifiche inviate.

## <a name="supported-versions"></a>Versioni supportate 
SQL Insights supporta le seguenti versioni di SQL Server:
- SQL Server 2012 e versioni successive

SQL Insights supporta SQL Server in esecuzione negli ambienti seguenti:
- Database SQL di Azure
- Istanza gestita di SQL di Azure
- SQL Server in macchine virtuali di Azure (SQL Server in esecuzione su macchine virtuali registrate con il provider di [macchine virtuali SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )
- VM di Azure (SQL Server in esecuzione in macchine virtuali non registrate con il provider di [macchine virtuali SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )

SQL Insights non offre supporto o supporto limitato per gli elementi seguenti:
- **Istanze non di Azure**: SQL Server in esecuzione in macchine virtuali esterne ad Azure non sono supportate
- **Pool elastici del database SQL di Azure**: non è possibile raccogliere le metriche per i pool elastici. Non è possibile raccogliere le metriche per i database nei pool elastici.
- **Livelli di servizio bassi del database SQL di Azure**: non è possibile raccogliere le metriche per i database nei [livelli di servizio](../../azure-sql/database/resource-limits-dtu-single-databases.md) Basic, S0, S1 e S2
- **Livello senza server del database SQL di Azure**: le metriche possono essere raccolte per i database che usano il livello di calcolo senza server. Tuttavia, il processo di raccolta delle metriche Reimposta il timer di ritardo di sospensione automatica, evitando che il database entri in uno stato di sospensione automatica
- **Repliche secondarie**: le metriche possono essere raccolte solo per una singola replica secondaria per database. Se un database dispone di più di una replica secondaria, è possibile monitorare solo 1.
- **Autenticazione con Azure Active Directory**: l'unico metodo di [autenticazione](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) supportato per il monitoraggio è l'autenticazione SQL. Per SQL Server in una macchina virtuale di Azure, l'autenticazione con Active Directory in un controller di dominio personalizzato non è supportata.  

## <a name="open-sql-insights"></a>Apri SQL Insights
Aprire SQL Insights selezionando **SQL (anteprima)** nella sezione **Insights** del menu di monitoraggio di **Azure** nel portale di Azure. Fare clic su un riquadro per caricare l'esperienza per il tipo di SQL monitorato.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Informazioni dettagliate su SQL in portale di Azure.":::

## <a name="enable-sql-insights"></a>Abilita SQL Insights 
Per istruzioni sull'abilitazione di SQL Insights, vedere [Enable SQL Insights](sql-insights-enable.md) .

## <a name="troubleshoot-sql-insights"></a>Risolvere i problemi relativi a SQL Insights 
Per istruzioni sulla risoluzione dei problemi relativi a SQL Insights, vedere [risoluzione dei problemi di SQL Insights](sql-insights-troubleshoot.md) .

## <a name="data-collected-by-sql-insights"></a>Dati raccolti da SQL Insights
SQL Insights esegue tutti i monitoraggi in remoto. Gli agenti non vengono installati nelle macchine virtuali che eseguono SQL Server. 

SQL Insights usa macchine virtuali di monitoraggio dedicate per raccogliere i dati dalle risorse SQL in modalità remota. Ogni macchina virtuale di monitoraggio avrà l' [agente di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) e l'estensione WLI (workload Insights) installata. L'estensione WLI include l' [agente Telegraf](https://www.influxdata.com/time-series-platform/telegraf/)open source. SQL Insights USA [regole di raccolta dati](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) per specificare le impostazioni di raccolta dati per il plug-in [SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/)di Telegraf.

Sono disponibili diversi set di dati per il database SQL di Azure, Azure SQL Istanza gestita e SQL Server. Le tabelle seguenti descrivono i dati disponibili. È possibile personalizzare i set di dati da raccogliere e la frequenza di raccolta quando si [Crea un profilo di monitoraggio](sql-insights-enable.md#create-sql-monitoring-profile).

Le tabelle seguenti includono le colonne seguenti:
- **Nome descrittivo**: nome della query, come illustrato nella portale di Azure durante la creazione di un profilo di monitoraggio
- **Nome configurazione**: nome della query, come illustrato nella portale di Azure durante la modifica di un profilo di monitoraggio
- **Namespace**: nome della query come disponibile in un'area di lavoro log Analytics. Questo identificatore viene visualizzato nella tabella **InsighstMetrics** della `Namespace` proprietà nella `Tags` colonna
- **DMV**: viste gestite dinamiche utilizzate per produrre il set di dati
- **Abilitata per impostazione predefinita**: indica se i dati vengono raccolti per impostazione predefinita
- **Frequenza di raccolta predefinita**: con quale frequenza vengono raccolti i dati per impostazione predefinita

### <a name="data-for-azure-sql-database"></a>Dati per il database SQL di Azure 
| Nome descrittivo | Nome della configurazione | Spazio dei nomi | DMV | Abilitato per impostazione predefinita | Frequenza di raccolta predefinita |
|:---|:---|:---|:---|:---|:---|
| Statistiche di attesa del database | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | N/D |
| Statistiche di attesa DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Sì | 60 secondi |
| Clerk di memoria | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Sì | 60 secondi |
| I/O database | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Sì | 60 secondi |
| Proprietà server | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys. database<br>sys. [database_service_objectives] | Sì | 60 secondi |
| Contatori delle prestazioni | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Sì | 60 secondi |
| Statistiche delle risorse | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Sì | 60 secondi |
| Governance delle risorse | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Sì | 60 secondi |
| Requests | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | N/D |
| Pianificazione| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | N/D  |

### <a name="data-for-azure-sql-managed-instance"></a>Dati per Istanza gestita SQL di Azure 

| Nome descrittivo | Nome della configurazione | Spazio dei nomi | DMV | Abilitato per impostazione predefinita | Frequenza di raccolta predefinita |
|:---|:---|:---|:---|:---|:---|
| Statistiche di attesa | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Sì | 60 secondi |
| Clerk di memoria | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Sì | 60 secondi |
| I/O database | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Sì | 60 secondi |
| Proprietà server | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Sì | 60 secondi |
| Contatori delle prestazioni | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Sì | 60 secondi |
| Statistiche delle risorse | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Sì | 60 secondi |
| Governance delle risorse | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Sì | 60 secondi |
| Requests | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | N/D |
| Pianificazione | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | N/D |

### <a name="data-for-sql-server"></a>Dati per SQL Server

| Nome descrittivo | Nome della configurazione | Spazio dei nomi | DMV | Abilitato per impostazione predefinita | Frequenza di raccolta predefinita |
|:---|:---|:---|:---|:---|:---|
| Statistiche di attesa | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Sì | 60 secondi | 
| Clerk di memoria | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Sì | 60 secondi |
| I/O database | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Sì | 60 secondi |
| Proprietà server | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Sì | 60 secondi |
| Contatori delle prestazioni | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Sì | 60 secondi |
| Spazio del volume | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Sì | 60 secondi |
| CPU SQL Server | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Sì | 60 secondi |
| Pianificazione | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | N/D |
| Requests | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | N/D |
| Stati replica di disponibilità | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 secondi |
| Repliche di database di disponibilità | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 secondi |

## <a name="next-steps"></a>Passaggi successivi

- Per istruzioni sull'abilitazione di SQL Insights, vedere [abilitare SQL Insights](sql-insights-enable.md)
- Vedere le [domande](../faq.md#sql-insights-preview) frequenti sulle domande frequenti su SQL Insights
