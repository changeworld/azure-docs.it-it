---
title: Differenze T-SQL tra SQL Server & Istanza gestita di SQL di Azure
description: Questo articolo illustra le differenze Transact-SQL (T-SQL) tra un Istanza gestita di SQL di Azure e SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: f744b718919a6da75b2064efdc163ef4618b5a7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815901"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Differenze T-SQL tra SQL Server & Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo riepiloga e illustra le differenze di sintassi e comportamento tra Istanza gestita di SQL di Azure e SQL Server. 


Sql Istanza gestita offre una compatibilità elevata con il motore di database SQL Server e la maggior parte delle funzionalità è supportata in un Istanza gestita SQL.

![Migrazione semplice da SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Esistono alcune limitazioni PaaS introdotte in SQL Istanza gestita e alcune modifiche del comportamento rispetto alle SQL Server. Le differenze sono suddivise nelle categorie seguenti: <a name="Differences"></a>

- [La](#availability) disponibilità include le differenze nei [Always On di disponibilità](#always-on-availability-groups) e nei [backup.](#backup)
- [La](#security) sicurezza include le differenze [nel](#auditing)controllo [,](#certificates)i [certificati,](#credential)le [credenziali,](#cryptographic-providers)i provider di crittografia, gli [account](#logins-and-users)di accesso e gli utenti e la chiave master del servizio [e](#service-key-and-service-master-key).
- [La](#configuration) configurazione include le differenze nell'estensione del pool di [buffer,](#buffer-pool-extension)nelle regole di [confronto,](#collation) [nei](#compatibility-levels)livelli di compatibilità, nel mirroring del [database,](#database-mirroring)nelle opzioni di [database,](#database-options) [SQL Server Agent](#sql-server-agent)e nelle opzioni [di tabella](#tables).
- [](#functionalities) Le funzionalità includono [BULK INSERT/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc)transazioni [distribuite,](#distributed-transactions)eventi [estesi,](#extended-events)librerie [esterne,](#external-libraries)filestream e [FileTable,](#filestream-and-filetable) [ricerca semantica full-text,](#full-text-semantic-search)server [collegati,](#linked-servers) [PolyBase,](#polybase) [Replica,](#replication) [RESTORE,](#restore-statement) [Service Broker,](#service-broker) [stored procedure,](#stored-procedures-functions-and-triggers)funzioni e trigger.
- [Impostazioni dell'ambiente,](#Environment) ad esempio reti virtuali e configurazioni di subnet.

La maggior parte di queste funzionalità sono vincoli di architettura e rappresentano le funzionalità del servizio.

I problemi noti temporanei individuati in SQL Istanza gestita e verranno risolti in futuro sono descritti nella pagina delle note [sulla versione](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Disponibilità

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Gruppi di disponibilità Always On

[La disponibilità](../database/high-availability-sla.md) elevata è incorporata in SQL Istanza gestita e non può essere controllata dagli utenti. Le istruzioni seguenti non sono supportate:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Clausola [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) dell'istruzione [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Istanza gestita backup automatici, in modo che gli utenti possano creare backup completi `COPY_ONLY` del database. I backup differenziali, di log e di snapshot di file non sono supportati.

- Con un'istanza Istanza gestita SQL è possibile eseguire il backup di un database dell'istanza solo in un account di archiviazione BLOB di Azure:
  - È supportato solo `BACKUP TO URL`.
  - `FILE`I `TAPE` dispositivi di backup , e non sono supportati.
- La maggior parte delle `WITH` opzioni generali è supportata.
  - `COPY_ONLY` è obbligatorio.
  - `FILE_SNAPSHOT` non è supportata.
  - Le opzioni `REWIND` nastro, `NOREWIND` , , e non sono `UNLOAD` `NOUNLOAD` supportate.
  - Le opzioni specifiche del `NORECOVERY` log: `STANDBY` , e non `NO_TRUNCATE` sono supportate.

Limitazioni 

- Con un Istanza gestita SQL, è possibile eseguire il backup di un database dell'istanza in un backup con un massimo di 32 stripe, sufficiente per i database fino a 4 TB se si usa la compressione dei backup.
- Non è possibile eseguire in un database crittografato con `BACKUP DATABASE ... WITH COPY_ONLY` TDE (Service Managed Transparent Data Encryption). La crittografia TDE gestita dal servizio forza la crittografia dei backup con una chiave TDE interna. La chiave non può essere esportata, quindi non è possibile ripristinare il backup. Usare i backup automatici e il ripristino tempormente oppure usare la funzionalità TDE gestita dal cliente [(BYOK).](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) È anche possibile disabilitare la crittografia nel database.
- I backup nativi ese Istanza gestita non possono essere ripristinati in un SQL Server. Ciò è dovuto Istanza gestita ha una versione interna del database più elevata rispetto a qualsiasi versione di SQL Server.
- La dimensione massima dello stripe di backup usando il comando `BACKUP` in SQL Istanza gestita è di 195 GB, ovvero la dimensione massima del BLOB. Aumentare il numero di strisce nel comando di backup per ridurre la dimensione delle singole strisce e rimanere entro questo limite.

    > [!TIP]
    > Per risolvere questa limitazione, quando si esegue il backup di un database da SQL Server in un ambiente locale o in una macchina virtuale, è possibile:
    >
    > - Eseguire il backup in `DISK` invece di eseguire il backup in `URL` .
    > - Caricare i file di backup nell'archivio BLOB.
    > - Eseguire il ripristino in sql Istanza gestita.
    >
    > Il comando in SQL Istanza gestita supporta dimensioni BLOB maggiori nei file di backup perché viene usato un tipo di BLOB diverso per l'archiviazione dei `Restore` file di backup caricati.

Per informazioni sui backup con T-SQL, vedere [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Security

### <a name="auditing"></a>Controllo

Le differenze principali tra il controllo in Microsoft Azure SQL e in SQL Server sono:

- Con sql Istanza gestita, il controllo funziona a livello di server. I `.xel` file di log vengono archiviati nell'archivio BLOB di Azure.
- Con database SQL di Azure, il controllo funziona a livello di database. I `.xel` file di log vengono archiviati nell'archivio BLOB di Azure.
- Con SQL Server, in locale o nelle macchine virtuali, il controllo funziona a livello di server. Gli eventi vengono archiviati file system o nei log eventi di Windows.
 
Il controllo XEvent in SQL Istanza gestita supporta le destinazioni di archiviazione BLOB di Azure. I log di File e Windows non sono supportati.

Le principali differenze nella sintassi `CREATE AUDIT` per il controllo in Archivio BLOB di Azure sono le seguenti:

- Viene fornita una nuova sintassi che è possibile usare per specificare l'URL del contenitore di archiviazione BLOB di `TO URL` Azure in cui vengono `.xel` inseriti i file.
- La `TO FILE` sintassi non è supportata perché sql Istanza gestita non può accedere alle condivisioni file di Windows.

Per altre informazioni, vedere: 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controllo](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificati

Sql Istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, quindi si applicano i vincoli seguenti:

- Il `CREATE FROM` / `BACKUP TO` file non è supportato per i certificati.
- Il `CREATE` / `BACKUP` certificato da `FILE` / `ASSEMBLY` non è supportato. I file di chiavi private non possono essere usati. 

Vedere [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) e [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Soluzione** alternativa: anziché creare il backup del certificato e ripristinare il backup, ottenere il contenuto binario del certificato e la chiave privata, archiviarlo come file con estensione sql e creare [da file binario:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credenziale

Sono supportati solo l'insieme di credenziali delle chiavi di Azure e le identità `SHARED ACCESS SIGNATURE`. Gli utenti di Windows non sono supportati.

Vedere [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provider del servizio di crittografia

Sql Istanza gestita non può accedere ai file, quindi i provider del servizio di crittografia non possono essere creati:

- `CREATE CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` non è supportata. Vedere [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Account di accesso e utenti

- Sono supportati gli account di accesso SQL creati `FROM CERTIFICATE` `FROM ASYMMETRIC KEY` con , e `FROM SID` . Vedere [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory sono supportate Azure AD entità server (account di accesso) create con la sintassi [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) o CREATE USER FROM [LOGIN [Azure AD Login].](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) Questi account di accesso vengono creati a livello di server.

    SQL Istanza gestita supporta le Azure AD di database con la sintassi `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Questa funzionalità è nota anche come Azure AD utenti di database indipendente.

- Gli account di accesso di Windows creati `CREATE LOGIN ... FROM WINDOWS` con la sintassi non sono supportati. Usare gli accessi e gli utenti di Azure Active Directory.
- L Azure AD'utente che ha creato l'istanza [ha privilegi di amministratore senza restrizioni](../database/logins-create-manage.md).
- Gli utenti Azure AD a livello di database possono essere creati usando la `CREATE USER ... FROM EXTERNAL PROVIDER` sintassi . Vedere [CREATE USER ... FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD le entità server (account di accesso) supportano solo le funzionalità SQL all'interno di Istanza gestita SQL. Le funzionalità che richiedono l'interazione tra istanze, indipendentemente dal fatto che siano nello stesso tenant Azure AD o in tenant diversi, non sono supportate per gli Azure AD utenti. Tra queste funzionalità sono incluse:

  - Replica transazionale SQL.
  - Server di collegamento.

- L'impostazione di un account di accesso di Azure AD mappato a un gruppo di Azure AD come proprietario del database non è supportata.
- La rappresentazione Azure AD entità a livello di server tramite altre entità Azure AD è supportata, ad esempio la [clausola EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) Le limitazioni di EXECUTE AS sono:

  - EXECUTE AS USER non è supportato per gli utenti Azure AD quando il nome è diverso dal nome dell'account di accesso. Ad esempio, quando l'utente viene creato tramite la sintassi CREATE USER [myAadUser] FROM LOGIN [ ] e viene tentata la rappresentazione tramite john@contoso.com EXEC AS USER = _myAadUser_. Quando si crea un **utente** da un'entità server Azure AD (account di accesso), specificare il user_name come lo stesso nome login_name **login**.
  - Solo le SQL Server di livello superiore (account di accesso) che fanno parte del ruolo possono eseguire le operazioni seguenti che hanno come destinazione Azure AD `sysadmin` principali:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Per rappresentare un utente con l'istruzione EXECUTE AS, è necessario eseguire il mapping dell'utente direttamente Azure AD'entità server (account di accesso). Gli utenti membri di gruppi Azure AD mappati Azure AD entità server non possono essere rappresentati in modo efficace con l'istruzione EXECUTE AS, anche se il chiamante dispone delle autorizzazioni di rappresentazione per il nome utente specificato.

- L'esportazione/importazione di database con file bacpac è supportata per gli utenti Azure AD in SQL Istanza gestita usando [SSMS V18.4](/sql/ssms/download-sql-server-management-studio-ssms)o versione successiva [ oSQLPackage.exe](/sql/tools/sqlpackage-download).
  - Le configurazioni seguenti sono supportate tramite il file bacpac del database: 
    - Esportare/importare un database tra istanze di gestione diverse all'interno dello stesso Azure AD dominio.
    - Esportare un database da SQL Istanza gestita importare nel database SQL all'interno dello stesso Azure AD dominio. 
    - Esportare un database dal database SQL e importare in sql Istanza gestita all'interno dello stesso Azure AD dominio.
    - Esportare un database da SQL Istanza gestita importare in SQL Server (versione 2012 o successiva).
      - In questa configurazione, tutti Azure AD utenti vengono creati come entità SQL Server database (utenti) senza account di accesso. Il tipo di utenti è elencato come `SQL` ed è visibile come in `SQL_USER` sys.database_principals). Le autorizzazioni e i ruoli rimangono nei SQL Server del database e possono essere usati per la rappresentazione. Tuttavia, non possono essere usati per accedere e accedere al SQL Server usando le proprie credenziali.

- Solo l'account di accesso dell'entità a livello di server, creato dal processo di provisioning di SQL Istanza gestita, i membri dei ruoli del server, ad esempio o , o altri account di accesso con l'autorizzazione ALTER ANY LOGIN Azure AD a livello di server possono creare entità server (account di accesso) nel database master per `securityadmin` `sysadmin` SQL Istanza gestita.
- Se l'account di accesso è un'entità di sicurezza SQL, solo gli account di accesso che fanno parte del ruolo `sysadmin` possono usare il comando per creare gli account di accesso per un account Azure AD.
- L Azure AD login deve essere membro di un Azure AD all'interno della stessa directory usata per l'Istanza gestita di SQL di Azure.
- Azure AD le entità server (account di accesso) sono visibili Esplora oggetti a partire da SQL Server Management Studio 18.0 Preview 5.
- È consentita la sovrapposizione di entità server (account di accesso) di Azure AD con un account amministratore di Azure AD. Azure AD le entità server (account di accesso) hanno la precedenza sull'amministratore di Azure AD quando si risolve l'entità e si applicano le autorizzazioni a SQL Istanza gestita.
- Durante l'autenticazione, viene applicata la sequenza seguente per risolvere l'entità di autenticazione:

    1. Se l'account Azure AD esiste come direttamente mappato all'entità server Azure AD (account di accesso), presente in sys.server_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'entità server Azure AD (account di accesso).
    2. Se l'account Azure AD è membro di un gruppo Azure AD mappato all'entità server (account di accesso) di Azure AD, presente in sys.server_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo Azure AD.
    3. Se l'account Azure AD è uno speciale amministratore di Azure AD configurato nel portale per SQL Istanza gestita, che non esiste nelle viste di sistema di SQL Istanza gestita, applicare speciali autorizzazioni fisse dell'amministratore di Azure AD per SQL Istanza gestita (modalità legacy).
    4. Se l'account Azure AD esiste come direttamente mappato a un utente di Azure AD in un database, presente in sys.database_principals come tipo "E", concedere l'accesso e applicare le autorizzazioni dell'utente del database Azure AD.
    5. Se l'account Azure AD è membro di un gruppo Azure AD mappato a un utente di Azure AD in un database, presente in sys.database_principals come tipo "X", concedere l'accesso e applicare le autorizzazioni dell'account di accesso del gruppo Azure AD.
    6. Se è presente un account di accesso Azure AD mappato a un account utente di Azure AD o a un account di gruppo Azure AD, che viene risolto nell'utente che sta effettuando l'autenticazione, vengono applicate tutte le autorizzazioni di questo account di accesso Azure AD.

### <a name="service-key-and-service-master-key"></a>Chiave del servizio e chiave master del servizio

- [Il backup della chiave](/sql/t-sql/statements/backup-master-key-transact-sql) master non è supportato (gestito dal servizio database SQL).
- [Il ripristino della chiave](/sql/t-sql/statements/restore-master-key-transact-sql) master non è supportato (gestito dal servizio database SQL).
- [Il backup della chiave master](/sql/t-sql/statements/backup-service-master-key-transact-sql) del servizio non è supportato (gestito dal servizio di database SQL).
- [Il ripristino della chiave master](/sql/t-sql/statements/restore-service-master-key-transact-sql) del servizio non è supportato (gestito dal servizio di database SQL).

## <a name="configuration"></a>Configurazione

### <a name="buffer-pool-extension"></a>Estensione del pool di buffer

- [L'estensione del pool](/sql/database-engine/configure-windows/buffer-pool-extension) di buffer non è supportata.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` non è supportata. Vedere [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Regole di confronto

Le regole di confronto di istanza predefinita sono `SQL_Latin1_General_CP1_CI_AS` e possono essere specificate come un parametro di creazione. Vedere [Regole di confronto](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Livelli di compatibilità

- I livelli di compatibilità supportati sono 100, 110, 120, 130, 140 e 150.
- I livelli di compatibilità inferiori a 100 non sono supportati.
- Il livello di compatibilità predefinito per i nuovi database è 140. Per i database ripristinati, il livello di compatibilità rimane invariato se era 100 e versioni successive.

Vedere [Livello di compatibilità ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mirroring del database

Il mirroring del database non è supportato.

- Le opzioni `ALTER DATABASE SET PARTNER` e `SET WITNESS` non sono supportate.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` non è supportata.

Per altre informazioni, vedere [ALTER DATABASE SET PARTNER e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opzioni di database

- I file di log multipli non sono supportati.
- Gli oggetti in memoria non sono supportati nel livello di servizio per utilizzo generico. 
- Esiste un limite di 280 file per ogni per utilizzo generico, che implica un massimo di 280 file per ogni database. Sia i file di dati che i file di log per utilizzo generico vengono conteggiati in base a questo limite. [Il business critical supporta 32.767 file per database](./resource-limits.md#service-tier-characteristics).
- Il database non può contenere filegroup che contengono dati filestream. Il ripristino non riesce se .bak contiene `FILESTREAM` dati. 
- Ogni file viene inserito in Archiviazione BLOB di Azure. L'I/O e la velocità effettiva per file dipendono dalle dimensioni di ogni singolo file.

#### <a name="create-database-statement"></a>Istruzione CREATE DATABASE

Le limitazioni seguenti si applicano a `CREATE DATABASE` :

- Non possono essere definiti file e filegroup. 
- `CONTAINMENT`L'opzione non è supportata. 
- `WITH` Le opzioni non sono supportate. 
   > [!TIP]
   > Come soluzione alternativa, usare `ALTER DATABASE` dopo per impostare le opzioni del database per aggiungere file o per impostare il `CREATE DATABASE` contenimento. 

- `FOR ATTACH`L'opzione non è supportata.
- `AS SNAPSHOT OF`L'opzione non è supportata.

Per altre informazioni, vedere [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE - istruzione

Alcune proprietà di file non possono essere impostate o modificate:

- Non è possibile specificare un percorso di file `ALTER DATABASE ADD FILE (FILENAME='path')` nell'istruzione T-SQL. Rimuovere `FILENAME` dallo script perché SQL Istanza gestita automaticamente i file. 
- Un nome file non può essere modificato usando `ALTER DATABASE` l'istruzione .

Le opzioni seguenti sono attive per impostazione predefinita e non possono essere modificate:

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

Le opzioni seguenti non possono essere modificate:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Alcune `ALTER DATABASE` istruzioni , ad esempio SET [CONTAINMENT,](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)potrebbero avere esito negativo temporaneo, ad esempio durante il backup automatico del database o subito dopo la creazione di un database. In questo caso `ALTER DATABASE` l'istruzione deve essere ritentata. Per altre informazioni sui messaggi di errore correlati, vedere [la sezione Osservazioni](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2).

Per altre informazioni, vedere [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- L'abilitazione e la disabilitazione di SQL Server Agent non sono attualmente supportate nell'istanza gestita di SQL. SQL Agent è sempre in esecuzione.
- Il trigger di pianificazione del processo basato su una CPU inattiva non è supportato.
- SQL Server Agent impostazioni sono di sola lettura. La procedura `sp_set_agent_properties` non è supportata in SQL Istanza gestita. 
- Processi
  - I passaggi dei processi T-SQL sono supportati.
  - I processi di replica seguenti sono supportati:
    - Lettore di log delle transazioni
    - Snapshot
    - Database di distribuzione
  - Sono supportati i passaggi del processo SSIS.
  - Altri tipi di passaggi di processo non sono attualmente supportati:
    - Il passaggio del processo di replica di tipo merge non è supportato. 
    - La lettura della coda non è supportata. 
    - La shell dei comandi non è ancora supportata.
  - Sql Istanza gestita non può accedere a risorse esterne, ad esempio condivisioni di rete tramite robocopy. 
  - SQL Server Analysis Services non è supportato.
- Le notifiche sono supportate in modo parziale.
- La notifica tramite posta elettronica è supportata, anche se richiede la configurazione di un Posta elettronica database personalizzato. SQL Server Agent possibile usare un solo Posta elettronica database profilo e deve essere chiamato `AzureManagedInstance_dbmail_profile` . 
  - Pager non è supportato.
  - NetSend non è supportato.
  - Gli avvisi non sono ancora supportati.
  - I proxy non sono supportati.
- EventLog non è supportato.
- È necessario eseguire direttamente il mapping dell'utente Azure AD'entità server (account di accesso) per creare, modificare o eseguire SQL Agent processi. Gli utenti di cui non è stato eseguito il mapping diretto, ad esempio gli utenti che appartengono a un gruppo Azure AD che dispone dei diritti per creare, modificare o eseguire processi SQL Agent, non saranno in grado di eseguire tali azioni. Ciò è dovuto alla rappresentazione Istanza gestita e [alle limitazioni di EXECUTE AS](#logins-and-users).
- La funzionalità Amministrazione multi server per i processi master/di destinazione (MSX/TSX) non è supportata.

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelle

I tipi di tabella seguenti non sono supportati:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [Filetable](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (non supportato solo nel livello per utilizzo generico)

Per informazioni su come creare e modificare tabelle, vedere [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funzionalità

### <a name="bulk-insert--openrowset"></a>Inserimento bulk/OPENROWSET

SQL Istanza gestita non può accedere alle condivisioni file e alle cartelle di Windows, quindi i file devono essere importati dall'archivio BLOB di Azure:

- `DATASOURCE` è obbligatorio nel `BULK INSERT` comando durante l'importazione di file da Archiviazione BLOB di Azure. Vedere [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` è obbligatorio nella `OPENROWSET` funzione quando si legge il contenuto di un file dall'archivio BLOB di Azure. Vedere [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` può essere usato per leggere dati da database SQL di Azure, Istanza gestita di SQL di Azure o SQL Server istanze. Altre origini, ad esempio database Oracle o file di Excel, non sono supportate.

### <a name="clr"></a>CLR

Un Istanza gestita SQL non può accedere alle condivisioni file e alle cartelle di Windows, quindi si applicano i vincoli seguenti:

- È supportato solo `CREATE ASSEMBLY FROM BINARY`. Vedere [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` non è supportata. Vedere [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` non può fare riferimento a file. Vedere [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Posta elettronica database (db_mail)
 - `sp_send_dbmail` non può inviare allegati usando il @file_attachments parametro . Le file system e le condivisioni o Archiviazione BLOB di Azure esterne non sono accessibili da questa procedura.
 - Vedere i problemi noti relativi al parametro `@query` e all'autenticazione.
 
### <a name="dbcc"></a>DBCC

Le istruzioni DBCC non documentate abilitate SQL Server non sono supportate in SQL Istanza gestita.

- È supportato solo un numero limitato di flag di traccia globali. A livello `Trace flags` di sessione non sono supportati. Vedere [Flag di traccia.](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)
- [DBCC TRACEOFF e](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funzionano con il numero limitato di flag di traccia globali.
- Non è possibile usare [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) con REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD perché non è possibile impostare il database in modalità . Vedere Differenze `SINGLE_USER` di ALTER [DATABASE](#alter-database-statement). Il potenziale danneggiamento del database viene gestito supporto tecnico di Azure team. Contattare supporto tecnico di Azure se sono presenti indicazioni di danneggiamento del database.

### <a name="distributed-transactions"></a>Transazioni distribuite

Il supporto parziale [per le transazioni distribuite](../database/elastic-transactions-overview.md) è attualmente disponibile in anteprima pubblica. Le transazioni distribuite sono supportate nelle condizioni seguenti (tutte devono essere soddisfatte):
* tutti i partecipanti alla transazione Azure SQL istanze gestite che fanno parte del gruppo [di trust server](./server-trust-group-overview.md).
* Le transazioni vengono avviate da .NET (classe TransactionScope) o Transact-SQL.

Istanza gestita di SQL di Azure attualmente non supporta altri scenari che sono regolarmente supportati da MSDTC in locale o in Macchine virtuali di Azure.

### <a name="extended-events"></a>Eventi estesi

Alcune destinazioni specifiche di Windows per gli eventi estesi (XEvent) non sono supportate:

- La `etw_classic_sync` destinazione non è supportata. Archiviare `.xel` i file nell'archivio BLOB di Azure. Vedere [Destinazione etw_classic_sync_target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- La `event_file` destinazione non è supportata. Archiviare `.xel` i file nell'archivio BLOB di Azure. Vedere [Destinazione event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Librerie esterne

Le librerie esterne R e Python nel database sono supportate in anteprima pubblica limitata. Vedere [Machine Learning Services in Istanza gestita di SQL di Azure (anteprima).](machine-learning-services-overview.md)

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- I dati filestream non sono supportati.
- Il database non può contenere filegroup con `FILESTREAM` dati.
- `FILETABLE` non è supportata.
- Le tabelle non possono contenere tipi `FILESTREAM`.
- Le funzioni seguenti non sono supportate:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Per altre informazioni, vedere [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTable](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Ricerca semantica full-text

La [ricerca semantica](/sql/relational-databases/search/semantic-search-sql-server) non è supportata.

### <a name="linked-servers"></a>Server collegati

I server collegati in SQL Istanza gestita un numero limitato di destinazioni:

- Le destinazioni supportate sono sql Istanza gestita, database SQL, Azure Synapse pool sql [serverless](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) e dedicati e SQL Server istanze. 
- Le transazioni scrivibili distribuite sono possibili solo tra le istanze gestite. Per altre informazioni, vedere [Transazioni distribuite](../database/elastic-transactions-overview.md). Tuttavia, MS DTC non è supportato.
- Le destinazioni non supportate sono file, Analysis Services e altri servizi RDBMS. Provare a usare l'importazione CSV nativa da Archiviazione BLOB di Azure o come alternativa per l'importazione di file o caricare file usando un `BULK INSERT` `OPENROWSET` pool SQL [serverless in Azure Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Operazioni: 

- [Le transazioni di](../database/elastic-transactions-overview.md) scrittura tra istanze sono supportate solo per le istanze gestite.
- `sp_dropserver` è supportato per l'eliminazione di un server collegato. Vedere [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La `OPENROWSET` funzione può essere usata per eseguire query solo SQL Server istanze. Possono essere gestiti, in locale o in macchine virtuali. Vedere [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- La `OPENDATASOURCE` funzione può essere usata per eseguire query solo SQL Server istanze. Possono essere gestiti, in locale o in macchine virtuali. Come provider sono supportati solo i valori `SQLNCLI` `SQLNCLI11` , e `SQLOLEDB` . Un esempio è `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Vedere [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- I server collegati non possono essere usati per leggere file (Excel, CSV) dalle condivisioni di rete. Provare a [usare BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) che legge i file CSV da Archiviazione BLOB di Azure o un server collegato che fa riferimento a un [pool SQL serverless in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Tenere traccia di queste richieste [in SQL Istanza gestita Feedback](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

L'unico tipo disponibile di origine esterna è RDBMS (in anteprima pubblica) per Azure SQL database, Azure SQL istanza gestita e Azure Synapse pool. È possibile usare una tabella esterna che fa riferimento a un [pool SQL serverless in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) come soluzione alternativa per le tabelle esterne polybase che leggono direttamente dall'archiviazione di Azure. In Azure SQL istanza gestita è possibile usare server collegati a un [pool SQL serverless in](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) Synapse Analytics o SQL Server per leggere i dati di archiviazione di Azure.
Per informazioni su PolyBase, vedere [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replica

- Sono supportati i tipi di replica snapshot e bidirezionale. La replica di tipo merge, la replica peer-to-peer e le sottoscrizioni aggiornabili non sono supportate.
- [La replica transazionale](replication-transactional-overview.md) è disponibile per l'anteprima pubblica in SQL Istanza gestita con alcuni vincoli:
    - Tutti i tipi di partecipanti alla replica (server di pubblicazione, server di distribuzione, Sottoscrittore pull e Sottoscrittore push) possono essere inseriti in SQL Istanza gestita, ma il server di pubblicazione e il server di distribuzione devono essere entrambi nel cloud o entrambi in locale.
    - Sql Istanza gestita può comunicare con le versioni recenti di SQL Server. Per altre [informazioni, vedere la matrice](replication-transactional-overview.md#supportability-matrix) delle versioni supportate.
    - La replica transazionale presenta alcuni [requisiti di rete aggiuntivi.](replication-transactional-overview.md#requirements)

Per altre informazioni sulla configurazione della replica transazionale, vedere le esercitazioni seguenti:
- [Replica tra un server di pubblicazione dell'istanza del server di gestione sql e un sottoscrittore dell'istanza istanza di Sql Server](replication-between-two-instances-configure-tutorial.md)
- [Replica tra un server di pubblicazione dell'istanza del server di gestione delle istanze di SQL, un server di distribuzione dell'istanza del server di distribuzione sql e un sottoscrittore SQL Server database](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>Istruzione RESTORE 

- Sintassi supportata:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintassi non supportata:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origine: 
  - `FROM URL` (Archiviazione BLOB di Azure) è l'unica opzione supportata.
  - `FROM DISK`/`TAPE`/dispositivo di backup non è supportata.
  - I set di backup non sono supportati.
- `WITH` Le opzioni non sono supportate. I tentativi di `WITH` `DIFFERENTIAL` ripristino, ad `STATS` esempio , , e così `REPLACE` via, avranno esito negativo.
- `ASYNC RESTORE`: il ripristino continua anche se la connessione client si interrompe. Se la connessione viene interrotta, è possibile controllare lo stato di un'operazione di ripristino nella vista e per un `sys.dm_operation_status` database CREATE e DROP. Vedere [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Le opzioni di database seguenti vengono impostate o sottoposte a override e non possono essere modificate in un secondo momento: 

- `NEW_BROKER` se il broker non è abilitato nel file con estensione bak. 
- `ENABLE_BROKER` se il broker non è abilitato nel file con estensione bak. 
- `AUTO_CLOSE=OFF` se un database nel file con estensione bak contiene `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` se un database nel file con estensione bak ha `SIMPLE` la modalità di ripristino o `BULK_LOGGED` .
- Viene aggiunto un filegroup ottimizzato per la memoria denominato XTP se non si trova nel file con estensione bak di origine. 
- Qualsiasi filegroup ottimizzato per la memoria esistente viene rinominato in XTP. 
- `SINGLE_USER` Le `RESTRICTED_USER` opzioni e vengono convertite in `MULTI_USER` .

Limitazioni 

- I backup dei database danneggiati potrebbero essere ripristinati a seconda del tipo di danneggiamento, ma i backup automatizzati non verranno evasi fino a quando non viene risolto il danneggiamento. Assicurarsi di eseguire nel database SQL di Istanza gestita `DBCC CHECKDB` e usare il backup per evitare questo `WITH CHECKSUM` problema.
- Il ripristino di un file di un database che contiene eventuali limitazioni descritte in questo documento (ad esempio, o oggetti) non può essere ripristinato in `.BAK` `FILESTREAM` SQL `FILETABLE` Istanza gestita.
- `.BAK` I file che contengono più set di backup non possono essere ripristinati. 
- `.BAK` I file che contengono più file di log non possono essere ripristinati.
- I backup contenenti database di dimensioni superiori a 8 TB, oggetti OLTP attivi in memoria o numero di file che superano 280 file per istanza non possono essere ripristinati in un'istanza per utilizzo generico. 
- I backup che contengono database superiori a 4 TB o oggetti OLTP in memoria con dimensioni totali superiori a quelle descritte [nei](resource-limits.md) limiti delle risorse non possono essere ripristinati in un'istanza business critical memoria.
Per informazioni sulle istruzioni di ripristino, vedere [Istruzioni RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Le stesse limitazioni si applicano all'operazione di ripristino temporificato incorporata. Ad esempio, non per utilizzo generico possibile ripristinare un database di dimensioni superiori a 4 TB business critical istanza. business critical database con file OLTP in memoria o più di 280 file non può essere ripristinato per utilizzo generico istanza.

### <a name="service-broker"></a>Service Broker

Lo scambio di messaggi tra istanze di Service Broker è supportato solo tra istanze Azure SQL gestite:

- `CREATE ROUTE`: non è possibile usare `CREATE ROUTE` con un nome DIVERSO da o DNS di `ADDRESS` un'altra istanza di SQL `LOCAL` Istanza gestita.
- `ALTER ROUTE`: non è possibile usare `ALTER ROUTE` con un nome DIVERSO da o DNS di `ADDRESS` un'altra istanza di SQL `LOCAL` Istanza gestita.

La sicurezza del trasporto è supportata, la sicurezza del dialogo non è:
- `CREATE REMOTE SERVICE BINDING` non è supportato.

Service Broker è abilitato per impostazione predefinita e non può essere disabilitato. Le opzioni ALTER DATABSE seguenti non sono supportate:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Stored procedure, funzioni e trigger

- `NATIVE_COMPILATION` non è supportato nel livello per utilizzo generico servizio.
- Le opzioni [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) seguenti non sono supportate: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` non è supportata. Vedere [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` non è supportata. Vedere [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` non sono supportati e include `sp_addextendedproc` e `sp_dropextendedproc` . Questa funzionalità non sarà supportata perché si tratta di un percorso deprecato per SQL Server. Per altre informazioni, vedere [Stored procedure estese.](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)
- `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db` non sono supportate. Vedere [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funzioni e variabili di sistema

Le variabili, funzioni e viste seguenti restituiscono risultati diversi:

- `SERVERPROPERTY('EngineEdition')` restituisce il valore 8. Questa proprietà identifica in modo univoco un Istanza gestita SQL. Vedere [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` restituisce NULL perché il concetto di istanza esistente per SQL Server non si applica a SQL Istanza gestita. Vedere [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` restituisce un nome DNS completo "collegabile", ad esempio my-managed-instance.wcus17662feb9ce98.database.windows.net. Vedere [ @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` restituisce un nome DNS "collegabile" completo, ad esempio per le `myinstance.domain.database.windows.net` proprietà "name" e "data_source". Vedere [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` restituisce NULL perché il concetto di servizio esistente per SQL Server non si applica a SQL Istanza gestita. Vedere [ @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` è supportato. Restituisce NULL se l'Azure AD di accesso non è in sys.sysaccesso. Vedere [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` non è supportata. Vengono restituiti dati non validi, che è un problema noto temporaneo. Vedere [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Vincoli di ambiente

### <a name="subnet"></a>Subnet
-  Non è possibile inserire altre risorse, ad esempio macchine virtuali, nella subnet in cui è stato distribuito il database SQL Istanza gestita. Distribuire queste risorse usando una subnet diversa.
- La subnet deve avere un numero sufficiente [di indirizzi IP disponibili.](connectivity-architecture-overview.md#network-requirements) Almeno deve avere almeno 32 indirizzi IP nella subnet.
- Il numero di vCore e tipi di istanze che è possibile distribuire in un'area ha alcuni [vincoli e limiti.](resource-limits.md#regional-resource-limitations)
- Nella subnet [deve essere](connectivity-architecture-overview.md#network-requirements) applicata una configurazione di rete.

### <a name="vnet"></a>Rete virtuale
- La rete virtuale può essere distribuita usando il modello di risorsa: il modello classico per la rete virtuale non è supportato.
- Dopo aver creato un Istanza gestita SQL, lo spostamento del Istanza gestita SQL o della rete virtuale in un altro gruppo di risorse o sottoscrizione non è supportato.
- Per le istanze gestite di SQL ospitate in cluster virtuali creati prima del 22/9/2020 il [peering](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) globale non è supportato. È possibile connettersi a queste risorse tramite ExpressRoute o da rete virtuale a rete virtuale tramite gateway di rete virtuale.

### <a name="failover-groups"></a>Gruppi di failover
I database di sistema non vengono replicati nell'istanza secondaria in un gruppo di failover. Pertanto, gli scenari che dipendono dagli oggetti dei database di sistema saranno impossibili nell'istanza secondaria, a meno che gli oggetti non vengano creati manualmente nel database secondario.

### <a name="tempdb"></a>Tempdb
- Le dimensioni massime del file di non possono essere maggiori di `tempdb` 24 GB per core in un per utilizzo generico livello. Le dimensioni massime in un business critical di archiviazione sono limitate dalle dimensioni di `tempdb` archiviazione Istanza gestita SQL. `Tempdb` Le dimensioni del file di log sono limitate a 120 GB per utilizzo generico livello. Alcune query potrebbero restituire un errore se sono necessari più di 24 GB per core o se producono più di `tempdb` 120 GB di dati di log.
- `Tempdb` è sempre suddiviso in 12 file di dati: 1 primario, denominato anche master, file di dati e 11 file di dati non primari. La struttura del file non può essere modificata e non è possibile aggiungere nuovi file a `tempdb` . 
- [I metadati ottimizzati `tempdb` per la](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata)memoria, una nuova SQL Server di database in memoria 2019, non sono supportati.
- Gli oggetti creati nel database modello non possono essere creati automaticamente in dopo un riavvio o un failover perché non ottiene l'elenco di oggetti `tempdb` `tempdb` iniziale dal database modello. È necessario creare oggetti in `tempdb` manualmente dopo ogni riavvio o failover.

### <a name="msdb"></a>MSDB

Gli schemi MSDB seguenti in SQL Istanza gestita devono essere di proprietà dei rispettivi ruoli predefiniti:

- Ruoli generali
  - Ruolo TargetServersRole
- [Ruoli predefiniti del database](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Ruoli di Posta elettronica database:](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile)
  - Databasemailuserrole
- [Ruoli di Integration Services:](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> La modifica dei nomi di ruolo, dei nomi di schema e dei proprietari dello schema predefiniti da parte dei clienti inciderà sul normale funzionamento del servizio. Tutte le modifiche apportate a queste modifiche verranno ripristinate ai valori predefiniti non appena vengono rilevati o al successivo aggiornamento del servizio al più recente per garantire il normale funzionamento del servizio.

### <a name="error-logs"></a>Log degli errori

SQL Istanza gestita inserisce informazioni dettagliate nei log degli errori. Nel log degli errori vengono registrati molti eventi di sistema interni. Usare una procedura personalizzata per leggere i log degli errori che filtrano alcune voci irrilevanti. Per altre informazioni, vedere [SQL Istanza gestita – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) o ESTENSIONE SQL Istanza gestita [(anteprima)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) per Azure Data Studio.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su SQL Istanza gestita, vedere [Informazioni su SQL Istanza gestita?](sql-managed-instance-paas-overview.md)
- Per un elenco di confronto e delle funzionalità, [Istanza gestita di SQL di Azure confronto delle funzionalità.](../database/features-comparison.md)
- Per gli aggiornamenti della versione e lo stato dei problemi noti, vedere le note [sulla versione Istanza gestita SQL](../database/doc-changes-updates-release-notes.md)
- Per una guida introduttiva che illustra come creare un nuovo Istanza gestita SQL, vedere [Creare un'istanza di SQL Istanza gestita](instance-create-quickstart.md).
