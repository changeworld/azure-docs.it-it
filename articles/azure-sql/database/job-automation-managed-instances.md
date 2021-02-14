---
title: Automazione dei processi con processi di SQL Agent in Azure SQL Istanza gestita
description: Opzioni di automazione per l'esecuzione di script Transact-SQL (T-SQL) in Azure SQL Istanza gestita
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: beb82f8435aea817a074ce83fddc6a5417b86c26
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417635"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatizzare le attività di gestione usando i processi di SQL Agent in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Utilizzando [SQL Server Agent](/sql/ssms/agent/sql-server-agent) in SQL Server e [SQL istanza gestita](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), è possibile creare e pianificare processi che possono essere eseguiti periodicamente su uno o più database per eseguire query Transact-SQL (T-SQL) ed eseguire attività di manutenzione. In questo articolo è stato introdotto SQL Agent per SQL Istanza gestita.

> [!Note]
> SQL Agent non è disponibile nel database SQL di Azure o in Azure sinapsi Analytics. Si consiglia invece l' [automazione dei processi con processi elastici](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Limitazioni del processo di SQL Agent nell'istanza gestita di SQL di Azure

Vale la pena notare le differenze tra SQL Agent disponibile in SQL Server e come parte di SQL Istanza gestita. Per altre informazioni sulle differenze tra le funzionalità supportate tra SQL Server e SQL Istanza gestita, vedere [differenze di sql istanza gestita T-SQL di Azure da SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Alcune delle funzionalità di SQL Agent disponibili in SQL Server non sono supportate in SQL Istanza gestita:

- Le impostazioni dell'agente SQL sono di sola lettura. 
    - Il stored procedure `sp_set_agent_properties` di sistema non è supportato in SQL istanza gestita.
- L'abilitazione/disabilitazione di SQL Agent non è attualmente supportata in SQL Istanza gestita. SQL Agent è sempre in esecuzione.
- Le notifiche sono parzialmente supportate:
  - Il cercapersone non è supportato.
  - NetSend non è supportato.
  - Gli avvisi non sono supportati.
- I proxy non sono supportati.
- EventLog non è supportato.
- Il trigger di pianificazione del processo basato su una CPU inattiva non è supportato.

## <a name="when-to-use-sql-agent-jobs"></a>Quando utilizzare i processi di SQL Agent 

Esistono diversi scenari in cui è possibile utilizzare i processi di SQL Agent:

- Automatizzare le attività di gestione e quindi pianificare l'esecuzione in ogni giorno feriale, fuori orario lavorativo e così via.
  - Distribuire le modifiche dello schema, la gestione delle credenziali, la raccolta dei dati sulle prestazioni o la raccolta dei dati di telemetria del tenant (cliente).
  - Aggiornare i dati di riferimento (ossia le informazioni comuni tra tutti i database) e caricare dati dall'archivio BLOB di Azure.
  - Attività di manutenzione comuni che includono DBCC CHECKDB per garantire l'integrità dei dati o la manutenzione degli indici per migliorare le prestazioni delle query. Configurare i processi per l'esecuzione in una raccolta di database su base periodica, ad esempio durante le fasce orarie non di punta.
  - Raccogliere i risultati di query da un set di database in una tabella centrale su base costante. Le query di prestazione possono essere eseguite continuamente e configurate per l'esecuzione di attività aggiuntive di trigger.
- Raccogliere i dati per i report
  - Aggregare i dati di una raccolta di database in una singola tabella di destinazione.
  - Eseguire query di elaborazione dei dati più lunghe per una vasta serie di database, ad esempio la raccolta della telemetria del cliente. I risultati vengono raccolti in una tabella di destinazione singola per ulteriori analisi.
- Spostare dati
  - Creare processi che replicano le modifiche apportate ai database in altri database o raccolgono gli aggiornamenti effettuati in database remoti e applicano le modifiche nel database.
  - Creare processi che caricano dati da o verso i database usando SQL Server Integration Services (SSIS).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Processi di SQL Agent nell'istanza gestita di SQL di Azure

I processi di SQL Agent vengono eseguiti dal servizio SQL Agent che continua a essere utilizzato per l'automazione delle attività in SQL Server e SQL Istanza gestita. 

I processi di SQL Agent sono una serie specificata di script T-SQL sul database. Usare i processi per definire un'attività amministrativa eseguibile una o più volte e monitorabile per verificarne l'esito positivo o negativo. 

Un processo può essere eseguito in un server locale o in più server remoti. I processi di SQL Agent sono un componente motore di database interno eseguito all'interno del servizio SQL Istanza gestita.

Di seguito sono riportati i concetti chiave dei processi di SQL Agent:

- I **passaggi di processo** sono set di uno o più passaggi da eseguire all'interno del processo. Per ogni passaggio di processo è possibile definire la strategia di ripetizione dei tentativi e l'azione da eseguire in caso di esito positivo o negativo del passaggio.
- Le **pianificazioni** definiscono quando deve essere eseguito il processo.
- Le **notifiche** consentono di definire le regole da usare per inviare notifiche agli operatori tramite posta elettronica al termine del processo.

### <a name="sql-agent-job-steps"></a>Passaggi del processo di SQL Agent

I passaggi di processo di SQL Agent sono sequenze di azioni che devono essere eseguite da SQL Agent. Ogni passaggio include il passaggio successivo da eseguire in caso di esito positivo o negativo e il numero di tentativi in caso di errore.

SQL Agent consente di creare diversi tipi di passaggi di processo, ad esempio i passaggi del processo Transact-SQL che eseguono un singolo batch Transact-SQL per il database o i passaggi del comando/PowerShell del sistema operativo in grado di eseguire script del sistema operativo personalizzati, i [passaggi di processo SSIS](/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent) che consentono di caricare i dati tramite SSIS Runtime o passaggi di [replica](../managed-instance/replication-transactional-overview.md) che possono pubblicare le modifiche dal database ad altri

> [!Note]
> Per altre informazioni sull'uso del Integration Runtime SSIS di Azure con SSISDB ospitato da Istanza gestita SQL di Azure, vedere [usare istanza gestita SQL di Azure con SQL Server Integration Services (SSIS) in Azure Data Factory](/../azure/data-factory/how-to-use-sql-managed-instance-with-ir.md).

La [replica transazionale](../managed-instance/replication-transactional-overview.md) può replicare le modifiche dalle tabelle in altri database in azure SQL istanza gestita, nel database SQL di Azure o in SQL Server. Per informazioni, vedere [configurare la replica in Azure SQL istanza gestita](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Altri tipi di passaggi di processo non sono attualmente supportati in SQL Istanza gestita, tra cui:

- Il passaggio del processo di replica di tipo merge non è supportato.
- La lettura coda non è supportata.
- Analysis Services non è supportato.
 
### <a name="sql-agent-job-schedules"></a>Pianificazioni processi di SQL Agent

Una pianificazione specifica quando un processo viene eseguito. La stessa pianificazione può includere l'esecuzione di più processi e allo stesso processo possono essere applicate più pianificazioni.

Per quanto riguarda quando un processo deve essere eseguito, una pianificazione può definire le condizioni seguenti:

- Ogni volta che SQL Server Agent viene avviato. Il processo viene attivato dopo ogni failover.
- Una sola volta in una data e un'ora specifiche. Questa opzione è utile per l'esecuzione posticipata di alcuni processi.
- In base a una pianificazione ricorrente.

> [!Note]
> SQL Istanza gestita attualmente non consente di avviare un processo quando la CPU è inattiva.

### <a name="sql-agent-job-notifications"></a>Notifiche del processo di SQL Agent

I processi di SQL Agent consentono di ricevere notifiche quando il processo viene completato o non riesce. È possibile ricevere le notifiche tramite posta elettronica.

Se non è già abilitato, prima di tutto è necessario configurare [la funzionalità posta elettronica database](/sql/relational-databases/database-mail/database-mail) in Azure SQL istanza gestita:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Come esercizio di esempio, configurare l'account di posta elettronica che verrà usato per inviare le notifiche tramite posta elettronica. Assegnare l'account al profilo di posta elettronica denominato `AzureManagedInstance_dbmail_profile` . Per inviare messaggi di posta elettronica utilizzando processi di SQL Agent in SQL Istanza gestita, deve essere presente un profilo che deve essere chiamato `AzureManagedInstance_dbmail_profile` . In caso contrario, SQL Istanza gestita non sarà in grado di inviare messaggi di posta elettronica tramite SQL Agent. Vedere l'esempio seguente:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Testare la configurazione di Posta elettronica database tramite T-SQL usando il sistema di [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) stored procedure:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

È possibile notificare all'operatore che si sono verificati determinati eventi nei processi di SQL Agent. Un operatore definisce le informazioni di contatto per la persona responsabile della manutenzione di una o più istanze in Istanza gestita di SQL. Le responsabilità di operatore vengono talvolta assegnate a una sola persona.

Nei sistemi con più istanze in Istanza gestita di SQL o in SQL Server, possono essere condivise da diverse persone. Un operatore non include informazioni di sicurezza e non definisce un'entità di sicurezza. Idealmente, un operatore non è un individuo le cui responsabilità possono cambiare, ma un gruppo di distribuzione di posta elettronica.   

È possibile [creare operatori](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) usando SQL Server Management Studio (SSMS) o lo script Transact-SQL illustrato nell'esempio seguente:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Verificare l'esito positivo o negativo del messaggio di posta elettronica tramite il [posta elettronica database log](/sql/relational-databases/database-mail/database-mail-log-and-audits) in SSMS.

È quindi possibile [modificare qualsiasi processo di SQL Agent](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) e assegnare gli operatori che riceveranno una notifica tramite posta elettronica se il processo viene completato, ha esito negativo o ha esito positivo utilizzando SSMS o lo script Transact-SQL seguente:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Cronologia processo di SQL Agent

Il Istanza gestita SQL di Azure attualmente non consente di modificare le proprietà di SQL Agent perché sono archiviate nei valori del registro di sistema sottostanti. Ciò significa che le opzioni per la modifica dei criteri di conservazione degli agenti per i record della cronologia processo sono fisse al valore predefinito di 1000 record totali e al massimo 100 record di cronologia per processo.

### <a name="sql-agent-fixed-database-role-membership"></a>Appartenenza al ruolo predefinito del database di SQL Agent

Se gli utenti collegati a account di accesso non sysadmin vengono aggiunti a uno dei tre ruoli predefiniti del database di SQL Agent nel database di sistema msdb, esiste un problema per cui è necessario concedere le autorizzazioni EXECUTE esplicite alle stored procedure master per il funzionamento di questi account di accesso. Se si verifica questo problema, si riceve l'errore "The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229) [L'autorizzazione EXECUTE è stata negata per l'oggetto <object_name> (Microsoft SQL Server, Error: 229)]". 

Quando si aggiungono utenti a un ruolo predefinito del database di SQL Agent (SQLAgentUserRole, SQLAgentReaderRole o SQLAgentOperatorRole) in msdb, per ognuno degli account di accesso dell'utente aggiunti a questi ruoli, eseguire lo script T-SQL seguente per concedere esplicitamente le autorizzazioni EXECUTE alle stored procedure di sistema elencate. In questo esempio si presuppone che il nome utente e il nome dell'account di accesso siano uguali. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Altre informazioni

- [Che cos'è Istanza gestita di SQL di Azure?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Novità del database SQL di Azure & SQL Istanza gestita](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Differenze T-SQL di SQL Istanza gestita di Azure da SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Confronto tra le funzionalità: database SQL di Azure e Istanza gestita SQL di Azure](../../azure-sql/database/features-comparison.md)