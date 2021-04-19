---
title: Novità
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Informazioni sulle nuove funzionalità e sui miglioramenti della documentazione per database SQL di Azure & SQL Istanza gestita.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2021
ms.author: sstein
ms.openlocfilehash: d0522fe2c8b6d6b623903a720e6c8e760bd6aa92
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602089"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Novità di SQL database SQL di Azure & sql Istanza gestita?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questo articolo elenca database SQL di Azure e Istanza gestita di SQL di Azure funzionalità attualmente disponibili in anteprima pubblica. Per aggiornamenti e miglioramenti del database SQL Istanza gestita SQL, vedere Aggiornamenti del servizio & [SQL Istanza gestita](https://azure.microsoft.com/updates/?product=sql-database)SQL . Per gli aggiornamenti e i miglioramenti apportati ad altri servizi di Azure, vedere [Aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Novità

La documentazione per database SQL di Azure e Istanza gestita di SQL di Azure è stata suddivisa in sezioni separate. È stato anche aggiornato il modo in cui si fa riferimento a un'istanza gestita *da database SQL di Azure gestita* a *Istanza gestita di SQL di Azure*.

Questa operazione è stata eseguita perché alcune funzionalità variano notevolmente tra un database singolo e un'istanza gestita ed è diventato sempre più difficile spiegare le complesse sfumature tra database SQL di Azure e Istanza gestita di SQL di Azure nei singoli articoli condivisi.

Questo chiarimento tra i diversi prodotti Azure SQL dovrebbe semplificare e semplificare il processo di utilizzo del motore di database di SQL Server in Azure, sia che si tratta di un singolo database gestito in database SQL di Azure, di un'istanza gestita completamente funzionante che ospita più database in Istanza gestita di SQL di Azure o del noto prodotto SQL Server locale ospitato in una macchina virtuale in Azure.

Si consideri che si tratta di un lavoro in corso e che non tutti gli articoli sono stati ancora aggiornati. La documentazione per istruzioni Transact-SQL (T-SQL), stored procedure e molte funzionalità condivise tra database SQL di Azure e Istanza gestita di SQL di Azure, ad esempio, non è ancora completa, quindi l'utente è grata per la sua grande disapprezione, in quanto continua a chiarire il contenuto. 

Questa tabella offre un rapido confronto per la modifica della terminologia: 


|**Nuovo termine**  | **Termine precedente**  |**Spiegazione** |
|---------|---------|---------|
|**Istanza gestita di database SQL di Azure** | database SQL di Azure *gestita*| Istanza gestita di SQL di Azure è il proprio prodotto all'interno della famiglia Azure SQL, anziché solo un'opzione di distribuzione all'interno database SQL di Azure. | 
|**Database SQL di Azure**|database SQL di Azure database *singolo*| Se non diversamente specificato in modo esplicito, il nome del database SQL di Azure include sia database singoli che database distribuiti in un pool elastico. |
|**Database SQL di Azure**|database SQL di Azure pool *elastico*| Se non diversamente specificato in modo esplicito, il nome del database SQL di Azure include sia database singoli che database distribuiti in un pool elastico.  |
|**Database SQL di Azure** |Database SQL di Azure | Anche se il termine rimane lo stesso, ora si applica solo alle distribuzioni di database singolo e pool elastico e non include l'istanza gestita. |
| **SQL di Azure**| N/D | Si riferisce alla famiglia di prodotti SQL Server motore di database disponibili in Azure: database SQL di Azure, Istanza gestita di SQL di Azure e SQL Server nelle macchine virtuali di Azure. | 

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

### <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Processi di database elastici (anteprima) | Per informazioni, vedere [Creare, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastiche](elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](elastic-transactions-overview.md). |
| Editor di query nel portale di Azure |Per informazioni, vedere [Usare l'editor di query SQL del portale di Azure per connettersi ed eseguire query sui dati](connect-query-portal.md).|
|Analitica SQL|Per informazioni, vedere [Analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Istanza gestita di database SQL di Azure](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| [Transazioni distribuite](/azure/azure-sql/database/elastic-transactions-overview) | Transazioni distribuite tra istanze gestite. |
| [Pool di istanze](/azure/sql-database/sql-database-instance-pools) | Soluzione comoda ed economica per eseguire la migrazione di istanze SQL di dimensioni ridotte nel cloud. |
| [Entità di sicurezza server (accessi) di Azure AD a livello di istanza](/sql/t-sql/statements/create-login-transact-sql) | Creare account di accesso a livello di istanza usando [un'istruzione CREATE LOGIN FROM EXTERNAL PROVIDER.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) |
| [Replica transazionale](../managed-instance/replication-transactional-overview.md) | Replicare le modifiche dalle tabelle in altri database in sql Istanza gestita, database SQL o SQL Server. Oppure aggiornare le tabelle quando alcune righe vengono modificate in altre istanze di SQL Istanza gestita o SQL Server. Per informazioni, vedere [Configurare la replica in Istanza gestita di SQL di Azure](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Rilevamento delle minacce |Per informazioni, vedere [Configurare il rilevamento delle minacce in Istanza gestita di SQL di Azure](../managed-instance/threat-detection-configure.md).|
| Conservazione del backup a lungo termine | Per informazioni, vedere [Configurare la conservazione del backup](../managed-instance/long-term-backup-retention-configure.md)a lungo termine in Istanza gestita di SQL di Azure , attualmente disponibile in anteprima pubblica limitata. |

---

## <a name="new-features"></a>Nuove funzionalità

### <a name="sql-managed-instance-h2-2019-updates"></a>Aggiornamenti di SQL Istanza gestita H2 2019

- [La configurazione della subnet](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) con supporto del servizio è un modo sicuro e pratico per gestire la configurazione della subnet in cui è possibile controllare il traffico dati mentre SQL Istanza gestita garantisce il flusso ininterrotto del traffico di gestione.
- [Transparent Data Encryption (TDE) con Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) consente uno scenario BYOK (Bring Your Own Key) per la protezione dei dati in pausa e consente alle organizzazioni di separare i compiti di gestione per chiavi e dati.
- I [Gruppi di failover automatico](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) consentono di replicare tutti i database dall'istanza primaria a un'istanza secondaria in un'altra area.
- [I flag di traccia](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) globali consentono di configurare il comportamento Istanza gestita SQL.

### <a name="sql-managed-instance-h1-2019-updates"></a>Aggiornamenti di SQL Istanza gestita H1 2019

Nel modello di distribuzione sql Istanza gestita 2019 sono abilitate le funzionalità seguenti:
  - Supporto per le sottoscrizioni con <a href="/azure/azure-sql/managed-instance/resource-limits"> credito mensile di Azure per i sottoscrittori di Visual Studio </a> e più [limiti a livello di area](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Supporto per <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Creare un'istanza gestita con <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">regole di confronto</a> a livello di istanza e un <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso</a> orario di propria scelta.
  - Le istanze gestite ora sono protette con un [firewall predefinito](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Configurare sql Istanza gestita per l'uso di [endpoint](../managed-instance/public-endpoint-configure.md)pubblici, connessione di [sostituzione](connectivity-architecture.md#connection-policy) proxy per ottenere prestazioni di rete migliori, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vCore</a> per la generazione di hardware gen5 o configurare la conservazione dei backup fino a <a href="/azure/azure-sql/database/automated-backups-overview">35</a> giorni per il ripristino temporizzazione. [La conservazione dei backup a lungo](long-term-retention-overview.md) termine (fino a 10 anni) è attualmente disponibile in anteprima pubblica.  
  - Le nuove funzionalità consentono di <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">eseguire il ripristino geografico del database in un altro data center tramite PowerShell</a>, [rinominare il database](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [eliminare il cluster virtuale](../managed-instance/virtual-cluster-delete.md).
  - Il nuovo [ruolo Collaboratore Istanza](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) predefinito abilita la conformità della separazione dei compiti con i principi di sicurezza e la conformità agli standard aziendali.
  - SQL Istanza gestita è disponibile nelle aree Azure per enti pubblici seguenti per la disponibilità a disponibilità US Gov Texas (US Gov Arizona) e in Cina settentrionale 2 e Cina orientale 2. È disponibile anche nelle aree pubbliche seguenti: Australia centrale, Australia centrale 2, Brasile meridionale, Francia meridionale, Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale, Sudafrica occidentale.

## <a name="known-issues"></a>Problemi noti

|Problema  |Data individuata  |Stato  |Data risolta  |
|---------|---------|---------|---------|
|[La modifica del tipo di connessione non influisce sulle connessioni tramite l'endpoint del gruppo di failover](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|Gennaio 2021|Ha una soluzione alternativa||
|[L'sp_send_dbmail della procedura può avere esito negativo temporaneo @query quando si usa il parametro](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Gennaio 2021|Ha una soluzione alternativa||
|[Le transazioni distribuite possono essere eseguite dopo la rimozione di Istanza gestita dal gruppo di trust del server](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Ottobre 2020|Ha una soluzione alternativa||
|[Le transazioni distribuite non possono essere eseguite dopo l'Istanza gestita di ridimensionamento](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Ottobre 2020|Ha una soluzione alternativa||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [OPENROWSET nell'Azure SQL](/sql/t-sql/functions/openrowset-transact-sql) `BACKUP` / `RESTORE` e nell'istruzione in Istanza gestita non è possibile Azure AD gestire l'identità per l'autenticazione in Archiviazione di Azure|Settembre 2020|Ha una soluzione alternativa||
|[L'entità servizio non può accedere Azure AD e AKV](#service-principal-cannot-access-azure-ad-and-akv)|Agosto 2020|Ha una soluzione alternativa||
|[Il ripristino del backup manuale senza CHECKSUM potrebbe avere esito negativo](#restoring-manual-backup-without-checksum-might-fail)|Maggio 2020|Risolto|Giugno 2020|
|[Agent non risponde dopo la modifica, la disabilitazione o l'abilitazione di processi esistenti](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Maggio 2020|Risolto|Giugno 2020|
|[Autorizzazioni per il gruppo di risorse non applicate a SQL Istanza gestita](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Febbraio 2020|Risolto|Novembre 2020|
|[Limitazione del failover manuale tramite il portale per i gruppi di failover](#limitation-of-manual-failover-via-portal-for-failover-groups)|Gennaio 2020|Ha una soluzione alternativa||
|[Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli accessi diversi da sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dicembre 2019|Ha una soluzione alternativa||
|[I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agente](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dicembre 2019|Risolto|Marzo 2020|
|[Azure AD gli account di accesso e gli utenti non sono supportati in SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Novembre 2019|Nessuna soluzione alternativa||
|[I limiti di memoria di OLTP in memoria non vengono applicati](#in-memory-oltp-memory-limits-are-not-applied)|Ottobre 2019|Ha una soluzione alternativa||
|[Errore sbagliato restituito durante il tentativo di rimozione di un file non vuoto](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Ottobre 2019|Ha una soluzione alternativa||
|[Le operazioni che consentono di modificare il livello di servizio e creare istanze sono bloccate dal ripristino di database in corso](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Settembre 2019|Ha una soluzione alternativa||
|[Potrebbe essere necessario riconfigurare Resource Governor sul livello di servizio business critical dopo il failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Settembre 2019|Ha una soluzione alternativa||
|[Le finestre di dialogo Service Broker tra database devono essere reinizializzare dopo l'aggiornamento del livello di servizio](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Agosto 2019|Ha una soluzione alternativa||
|[La rappresentazione dei Azure AD di accesso non è supportata](#impersonation-of-azure-ad-login-types-is-not-supported)|Luglio 2019|Nessuna soluzione alternativa||
|[Parametro @query non supportato in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Risolto|Gennaio 2021|
|[È necessario riconfigurare la replica transazionale dopo il failover geografico](#transactional-replication-must-be-reconfigured-after-geo-failover)|Marzo 2019|Nessuna soluzione alternativa||
|[Il database temporaneo viene usato durante l'operazione RESTORE](#temporary-database-is-used-during-restore-operation)||Ha una soluzione alternativa||
|[La struttura e il contenuto di TEMPDB vengono ricreati](#tempdb-structure-and-content-is-re-created)||Nessuna soluzione alternativa||
|[Superamento dello spazio di archiviazione con file di database di piccole dimensioni](#exceeding-storage-space-with-small-database-files)||Ha una soluzione alternativa||
|[Valori GUID visualizzati al posto dei nomi di database](#guid-values-shown-instead-of-database-names)||Ha una soluzione alternativa||
|[I log degli errori non sono permanenti](#error-logs-arent-persisted)||Nessuna soluzione alternativa||
|[L'ambito della transazione in due database nella stessa istanza non è supportato](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Ha una soluzione alternativa|Marzo 2020|
|[I moduli CLR e i server collegati a volte non riescono a fare riferimento all'indirizzo IP locale](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Ha una soluzione alternativa||
|Coerenza del database non verificata usando DBCC CHECKDB dopo il ripristino del database dall'Archiviazione BLOB di Azure.||Risolto|Novembre 2019|
|Il ripristino temporizzato di database dal livello business critical al livello utilizzo generico non avrà esito positivo se il database di origine contiene oggetti OLTP in memoria.||Risolto|Ottobre 2019|
|Funzionalità posta elettronica database con server di posta esterni (non Azure) che usano una connessione sicura||Risolto|Ottobre 2019|
|Database indipendenti non supportati in SQL Istanza gestita||Risolto|Agosto 2019|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>La modifica del tipo di connessione non influisce sulle connessioni tramite l'endpoint del gruppo di failover

Se un'istanza fa parte di un gruppo di [](https://docs.microsoft.com/azure/azure-sql/managed-instance/connection-types-overview) [failover automatico,](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)la modifica del tipo di connessione dell'istanza non ha effetto per le connessioni stabilite tramite l'endpoint listener del gruppo di failover.

**Soluzione** alternativa: eliminare e ricreare il gruppo di failover automatico afer modificando il tipo di connessione.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>La sp_send_dbmail può avere esito negativo temporaneo quando @query viene usato il parametro

La sp_send_dbmail può avere esito negativo temporaneo quando `@query` viene usato il parametro . Quando si verifica questo problema, ogni secondo esecuzione della procedura sp_send_dbmail ha esito negativo con errore `Msg 22050, Level 16, State 1` e messaggio `Failed to initialize sqlcmd library with error number -2147467259` . Per poter visualizzare correttamente questo errore, la procedura deve essere chiamata con il valore predefinito 0 per il parametro , in caso contrario l'errore `@exclude_query_output` non verrà propagato.
Questo problema è causato da un bug noto correlato al modo in cui sp_send_dbmail la rappresentazione e il pool di connessioni.
Per risolvere questo problema, eseguire il wrapping del codice per l'invio di posta elettronica in una logica di ripetizione dei tentativi basata sul parametro di output `@mailitem_id` . Se l'esecuzione ha esito negativo, il valore del parametro sarà NULL, che sp_send_dbmail deve essere chiamato ancora una volta per inviare correttamente un messaggio di posta elettronica. Ecco un esempio di questa logica di ripetizione dei tentativi.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Le transazioni distribuite possono essere eseguite dopo la rimozione di Istanza gestita dal gruppo di trust del server

[I gruppi di trust](../managed-instance/server-trust-group-overview.md) del server vengono usati per stabilire l'attendibilità tra istanze gestite che è prerequisito per l'esecuzione [di transazioni distribuite.](./elastic-transactions-overview.md) Dopo aver rimosso Istanza gestita dal gruppo di trust del server o aver eliminato il gruppo, è comunque possibile eseguire transazioni distribuite. È possibile applicare una soluzione alternativa per assicurarsi che le transazioni distribuite siano disabilitate e che si tratta di [un failover](../managed-instance/user-initiated-failover.md) manuale avviato dall'utente Istanza gestita.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Le transazioni distribuite non possono essere eseguite dopo l'Istanza gestita di ridimensionamento

Istanza gestita operazioni di ridimensionamento che includono la modifica del livello di servizio o del numero di vCore reimposta le impostazioni del gruppo di attendibilità del server nel back-end e disabilita l'esecuzione [delle transazioni distribuite.](./elastic-transactions-overview.md) Come soluzione alternativa, eliminare e creare un nuovo gruppo di [trust del](../managed-instance/server-trust-group-overview.md) server portale di Azure.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT e BACKUP/RESTORE non possono usare l'identità gestita per accedere ad Archiviazione di Azure

Le istruzioni di inserimento bulk, BACKUP e RESTORE e la funzione OPENROWSET non possono usare con l'identità gestita `DATABASE SCOPED CREDENTIAL` per l'autenticazione in Archiviazione di Azure. Come soluzione alternativa, passare all'autenticazione CON FIRMA DI ACCESSO CONDIVISO. L'esempio seguente non funziona in Azure SQL (database e Istanza gestita):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Soluzione** alternativa: usare la [firma di accesso condiviso per eseguire l'autenticazione nell'archiviazione.](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>L'entità servizio non può accedere Azure AD e AKV

In alcune circostanze potrebbe verificarsi un problema con l'entità servizio usata per accedere Azure AD e Azure Key Vault (AKV). Di conseguenza, questo problema influisce sull'utilizzo dell'autenticazione Azure AD e transparent database encryption (TDE) con SQL Istanza gestita. Può trattarsi di un problema di connettività intermittente o di non essere in grado di eseguire istruzioni come CREATE LOGIN/USER FROM EXTERNAL PROVIDER o EXECUTE AS LOGIN/USER. Anche la configurazione di TDE con una chiave gestita dal cliente in un nuovo Istanza gestita di SQL di Azure potrebbe non funzionare in alcune circostanze.

**Soluzione** alternativa: per evitare che questo problema si verifichi nel Istanza gestita SQL prima di eseguire comandi di aggiornamento o nel caso in cui il problema sia già stato riscontrato dopo i comandi di aggiornamento, passare a portale di Azure, accedere al pannello di amministrazione di SQL Istanza gestita [Active Directory.](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal) Verificare se è possibile visualizzare il messaggio di errore "Istanza gestita richiede un'entità servizio per accedere Azure Active Directory. Fare clic qui per creare un'entità servizio". Nel caso in cui sia stato visualizzato questo messaggio di errore, fare clic su di esso e seguire le istruzioni dettagliate fornite fino a quando l'errore non viene risolto.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Il ripristino del backup manuale senza CHECKSUM potrebbe avere esito negativo

In alcuni casi il backup manuale dei database evasi in un'istanza gestita senza CHECKSUM potrebbe non essere ripristinato. In questi casi, ripetere il ripristino del backup fino a quando non si è riusciti.

**Soluzione** alternativa: eseguire backup manuali dei database in istanze gestite con CHECKSUM abilitato.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>L'agente non risponde alla modifica, alla disabilitazione o all'abilitazione di processi esistenti

In determinate circostanze, la modifica, la disabilitazione o l'abilitazione di un processo esistente possono causare la non risposta dell'agente. Il problema viene risolto automaticamente al rilevamento, determinando un riavvio del processo dell'agente.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Autorizzazioni per il gruppo di risorse non applicate a SQL Istanza gestita

Quando il ruolo di Azure Collaboratore Istanza gestita sql Istanza gestita viene applicato a un gruppo di risorse (RG), non viene applicato a SQL Istanza gestita e non ha alcun effetto.

**Soluzione** alternativa: configurare un ruolo collaboratore Istanza gestita sql server per gli utenti a livello di sottoscrizione.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Limitazione del failover manuale tramite il portale per i gruppi di failover

Se un gruppo di failover si estende tra istanze in sottoscrizioni o gruppi di risorse di Azure diversi, non è possibile avviare il failover manuale dall'istanza primaria nel gruppo di failover.

**Soluzione** alternativa: avviare il failover tramite il portale dall'istanza geografica secondaria.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Per i ruoli SQL Agent sono necessarie autorizzazioni EXECUTE esplicite per gli accessi diversi da sysadmin

Se gli account di accesso non sysadmin vengono aggiunti SQL Agent qualsiasi ruolo predefinito del database , esiste un problema [per](/sql/ssms/agent/sql-server-agent-fixed-database-roles)cui è necessario concedere autorizzazioni EXECUTE esplicite alle stored procedure master per il funzionamento di questi account di accesso. Se si verifica questo problema, si riceve l'errore "The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229) [L'autorizzazione EXECUTE è stata negata per l'oggetto <object_name> (Microsoft SQL Server, Error: 229)]".

**Soluzione** alternativa: dopo aver aggiunto account di accesso a un ruolo predefinito del database SQL Agent (SQLAgentUserRole, SQLAgentReaderRole o SQLAgentOperatorRole), per ognuno degli account di accesso aggiunti a questi ruoli, eseguire lo script T-SQL seguente per concedere esplicitamente le autorizzazioni EXECUTE alle stored procedure elencate.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>I processi di SQL Agent possono essere interrotti dal riavvio del processo dell'agente

**(Risolto a marzo 2020)** SQL Agent crea una nuova sessione ogni volta che viene avviato un processo, aumentando gradualmente il consumo di memoria. Per evitare di raggiungere il limite di memoria interno, che blocca l'esecuzione dei processi pianificati, il processo di Agent verrà riavviato quando il consumo di memoria raggiunge la soglia. Potrebbe causare l'interruzione dei processi in esecuzione al momento del riavvio.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>I limiti di memoria di OLTP in memoria non vengono applicati

In business critical livello di servizio non verranno applicati correttamente i limiti massimi di memoria per gli oggetti [ottimizzati per](../managed-instance/resource-limits.md#in-memory-oltp-available-space) la memoria. Sql Istanza gestita può consentire al carico di lavoro di usare più memoria per le operazioni OLTP in memoria, che possono influire sulla disponibilità e la stabilità dell'istanza. Le query di OLTP in memoria che raggiungono i limiti potrebbero non avere subito esito negativo. Questo problema verrà risolto a breve. Le query che usano più memoria OLTP in memoria avranno esito negativo prima se raggiungono i [limiti](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Soluzione** alternativa: [monitorare l'utilizzo dello](../in-memory-oltp-monitor-space.md) spazio di archiviazione OLTP in memoria [usando SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) per assicurarsi che il carico di lavoro non utilizzi più della memoria disponibile. Aumentare i limiti di memoria che dipendono dal numero di vCore oppure ottimizzare il carico di lavoro per usare meno memoria.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Errore sbagliato restituito durante il tentativo di rimozione di un file non vuoto

SQL Server e SQL Istanza gestita consentono a un utente di eliminare [un file non vuoto.](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites) Se si tenta di rimuovere un file di dati non vuoto usando un'istruzione `ALTER DATABASE REMOVE FILE` , `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` l'errore non verrà restituito immediatamente. Sql Istanza gestita continuerà a tentare di eliminare il file e l'operazione avrà esito negativo dopo 30 minuti con `Internal server error` .

**Soluzione** alternativa: rimuovere il contenuto del file usando il `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` comando . Se questo è l'unico file nel file group, è necessario eliminare i dati dalla tabella o dalla partizione associata a questo file group prima di compattare il file e facoltativamente caricare i dati in un'altra tabella/partizione.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Le operazioni che consentono di modificare il livello di servizio e creare istanze sono bloccate dal ripristino di database in corso

L'istruzione in corso, il processo di migrazione del Servizio Migrazione dei dati e il ripristino temporizzazione predefinito bloccano l'aggiornamento di un livello di servizio o il ridimensionamento dell'istanza esistente e la creazione di nuove istanze fino al completamento del processo di `RESTORE` ripristino. 

Il processo di ripristino bloczzerà queste operazioni nelle istanze gestite e nei pool di istanze nella stessa subnet in cui è in esecuzione il processo di ripristino. Le istanze nei pool di istanze non sono interessate. Le operazioni di creazione o modifica del livello di servizio non avranno esito negativo o si oseranno timeout. Si procederà dopo il completamento o l'annullamento del processo di ripristino.

**Soluzione** alternativa: attendere il completamento del processo di ripristino o annullare il processo di ripristino se la creazione o l'operazione update-service-tier ha priorità più alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Potrebbe essere necessario riconfigurare Resource Governor sul livello di servizio business critical dopo il failover

La [funzionalità Resource Governor](/sql/relational-databases/resource-governor/resource-governor) che consente di limitare le risorse assegnate al carico di lavoro utente potrebbe classificare erroneamente un carico di lavoro utente dopo il failover o una modifica del livello di servizio avviata dall'utente, ad esempio la modifica delle dimensioni di archiviazione max vCore o max instance.

**Soluzione** alternativa: eseguire periodicamente o come parte di un processo SQL Agent che esegue l'attività SQL all'avvio dell'istanza se si usa `ALTER RESOURCE GOVERNOR RECONFIGURE` [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Le finestre di dialogo Service Broker tra database devono essere reinizializzare dopo l'aggiornamento del livello di servizio

Le finestre di dialogo Service Broker tra database interromperanno l'invio di messaggi ai servizi di altri database dopo l'operazione di modifica del livello di servizio. I messaggi non *vengono persi* e sono disponibili nella coda del mittente. Qualsiasi modifica di vCore o delle dimensioni di archiviazione dell'istanza in SQL Istanza gestita causerà la modifica di un valore nella vista `service_broke_guid` [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) per tutti i database. Qualsiasi `DIALOG` elemento creato usando [un'istruzione BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) che fa riferimento a Service Brokers in un altro database interromperà il recapito dei messaggi al servizio di destinazione.

**Soluzione** alternativa: arrestare qualsiasi attività che usa conversazioni tra database Service Broker dialogo prima di aggiornare un livello di servizio e reinizializzarle in un secondo momento. Se sono presenti messaggi rimanenti non recapitati dopo una modifica del livello di servizio, leggere i messaggi dalla coda di origine e inviarli di nuovo alla coda di destinazione.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>La rappresentazione dei Azure AD di accesso non è supportata

La rappresentazione `EXECUTE AS USER` con o delle seguenti Azure Active Directory `EXECUTE AS LOGIN` (Azure AD) non è supportata:
-   Utenti con Azure AD alias. In questo caso viene restituito l'errore seguente: `15517` .
- Azure AD gli account di accesso e gli utenti in base Azure AD applicazioni o entità servizio. In questo caso vengono restituiti gli errori `15517` seguenti: e `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>Parametro @query non supportato in sp_send_db_mail

Il parametro `@query` nella procedura [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) non funziona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>È necessario riconfigurare la replica transazionale dopo il failover geografico

Se la replica transazionale è abilitata in un database in un gruppo di failover automatico, l'amministratore di SQL Istanza gestita deve pulire tutte le pubblicazioni nel database primario precedente e riconfigurarle nel nuovo database primario dopo il failover in un'altra area. Per altre informazioni, vedere [Replica.](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD gli account di accesso e gli utenti non sono supportati in SSDT

SQL Server Data Tools non supportano completamente gli Azure AD e gli utenti.

### <a name="temporary-database-is-used-during-restore-operation"></a>Il database temporaneo viene usato durante l'operazione RESTORE

Quando un database viene ripristinato in SQL Istanza gestita, il servizio di ripristino creerà innanzitutto un database vuoto con il nome desiderato per allocare il nome nell'istanza. Dopo un certo periodo di tempo, il database verrà eliminato e verrà avviato il ripristino del database effettivo. 

Il database in stato *Di* ripristino avrà temporaneamente un valore GUID casuale anziché un nome. Il nome temporaneo verrà modificato nel nome desiderato specificato nell'istruzione `RESTORE` al termine del processo di ripristino. 

Nella fase iniziale, un utente può accedere al database vuoto e persino creare tabelle o caricare dati in questo database. Questo database temporaneo verrà rimosso quando il servizio di ripristino avvierà la seconda fase.

**Soluzione alternativa**: non accedere al database in fase di ripristino fino al completamento dell'operazione.

### <a name="tempdb-structure-and-content-is-re-created"></a>La struttura e il contenuto di TEMPDB vengono ricreati

Il `tempdb` database è sempre suddiviso in 12 file di dati e la struttura del file non può essere modificata. Le dimensioni massime per file non possono essere modificate e non è possibile aggiungere nuovi file a `tempdb`. `Tempdb` viene sempre ricreato come database vuoto quando l'istanza viene avviata o si effettua il failover e tutte le modifiche apportate in `tempdb` non verranno mantenute.

### <a name="exceeding-storage-space-with-small-database-files"></a>Superamento dello spazio di archiviazione con file di database di piccole dimensioni

Le istruzioni `CREATE DATABASE`, `ALTER DATABASE ADD FILE`e `RESTORE DATABASE` potrebbero avere esito negativo perché l'istanza può raggiungere il limite di Archiviazione di Azure.

Ogni per utilizzo generico istanza di SQL Istanza gestita ha fino a 35 TB di spazio di archiviazione riservato per lo spazio su disco Premium di Azure. Ogni file di database si trova in un disco fisico separato. I dischi possono essere da 128 GB, 256 GB, 512 GB, 1 TB o 4 TB. Lo spazio inutilizzato sul disco non viene conteggiato, ma la somma delle dimensioni dei dischi Premium di Azure non può superare 35 TB. In alcuni casi, un'istanza gestita che non necessita di 8 TB in totale può superare il limite di Azure di 35 TB per le dimensioni delle risorse di archiviazione a causa della frammentazione interna.

Ad esempio, un'per utilizzo generico di SQL Istanza gestita potrebbe avere un file di grandi dimensioni di 1,2 TB posizionato su un disco da 4 TB. Può anche contenere 248 file da 1 GB ciascuno e inseriti in dischi da 128 GB separati. Esempio:

- la dimensione totale della risorsa di archiviazione sul disco allocato è 1 x 4 TB + 248 x 128 GB = 35 TB.
- Lo spazio totale riservato per i database nell'istanza è 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Questo esempio illustra che in determinate circostanze, a causa di una distribuzione specifica dei file, un'istanza di SQL Istanza gestita potrebbe raggiungere il limite di 35 TB riservato a un disco Premium di Azure collegato quando non è previsto.

In questo esempio, i database esistenti continuano a funzionare e possono crescere senza alcun problema fino a quando non vengono aggiunti nuovi file. Non è possibile creare o ripristinare nuovi database a causa dello spazio insufficiente per le nuove unità disco, anche se le dimensioni totali di tutti i database non raggiungono il limite di dimensioni dell'istanza. L'errore restituito in questo caso potrebbe non essere chiaro.

È possibile [identificare il numero di file rimanenti](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando le viste di sistema. Se si raggiunge questo limite, provare a [svuotare ed eliminare alcuni dei file più piccoli usando l'istruzione DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) o passare al [livello di servizio business critical, che non ha questo limite](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valori GUID visualizzati al posto dei nomi di database

In numerose viste di sistema, contatori delle prestazioni, messaggi di errore, XEvent e voci del log degli errori sono visualizzati gli identificatori GUID dei database anziché i nomi effettivi. Non fare affidamento su questi identificatori GUID, in quanto vengono sostituiti dai nomi effettivi dei database in futuro.

**Soluzione** alternativa: usare la vista sys.databases per risolvere il nome effettivo del database dal nome del database fisico, specificato sotto forma di identificatori di database GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>I log degli errori non sono permanenti

I log degli errori disponibili in SQL Istanza gestita non vengono resi persistenti e le relative dimensioni non sono incluse nel limite massimo di archiviazione. I log degli errori potrebbero essere cancellati automaticamente se si esegue il failover. La cronologia del log degli errori potrebbe non essere presente perché sql Istanza gestita è stato spostato più volte in più macchine virtuali.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L'ambito della transazione in due database nella stessa istanza non è supportato

**(Risolto a marzo 2020)** La classe `TransactionScope` in .NET non funziona se vengono inviate due query ai due database nella stessa istanza all'interno del medesimo ambito della transazione:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Soluzione alternativa (non necessaria a partire da marzo **2020):** usare [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) per usare un altro database in un contesto di connessione anziché due connessioni.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>I moduli CLR e i server collegati a volte non riescono a fare riferimento all'indirizzo IP locale

I moduli CLR in sql Istanza gestita server collegati o query distribuite che fanno riferimento a un'istanza corrente talvolta non possono risolvere l'INDIRIZZO IP di un'istanza locale. Questo errore è un problema temporaneo.

**Soluzione** alternativa: usare connessioni di contesto in un modulo CLR, se possibile.

## <a name="updates"></a>Aggiornamenti

Per un elenco di aggiornamenti e miglioramenti del database SQL, vedere [Aggiornamenti del servizio database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per gli aggiornamenti e i miglioramenti apportati a tutti i servizi di Azure, vedere [Aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuire al contenuto

Per contribuire alla documentazione Azure SQL, vedere l'guida per i collaboratori di Docs [.](/contribute/)
