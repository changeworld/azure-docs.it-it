---
title: Copiare un database
description: Creare una copia coerente dal punto di vista transazionale di un database database SQL di Azure nello stesso server o in un server diverso.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: b7084ef045d14b9715c41bb9ffa483d1f2f7bedf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865150"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Copiare una copia coerente dal punto di vista transazionale di un database in database SQL di Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

database SQL di Azure offre diversi metodi per la creazione di una copia di [un database esistente](single-database-overview.md) nello stesso server o in un server diverso. È possibile copiare un database usando portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o T-SQL.

## <a name="overview"></a>Panoramica

Una copia del database è uno snapshot coerente dal punto di vista transazionale del database di origine a un momento successivo all'avvio della richiesta di copia. È possibile selezionare lo stesso server o un server diverso per la copia. È anche possibile scegliere di mantenere la ridondanza del backup, il livello di servizio e le dimensioni di calcolo del database di origine oppure usare una ridondanza di archiviazione di backup e/o dimensioni di calcolo diverse all'interno dello stesso livello di servizio o di un livello di servizio diverso. Al termine del processo di copia, questa diventa un database indipendente e completamente funzionante. Gli account di accesso, gli utenti e le autorizzazioni nel database copiato vengono gestiti in modo indipendente dal database di origine. La copia viene creata usando la tecnologia di replica geografica. Al termine del seeding della replica, il collegamento di replica geografica viene terminato automaticamente. All'operazione di copia del database si applicano tutti i requisiti per l'uso della replica geografica. Per [informazioni dettagliate, vedere Panoramica della replica geografica](active-geo-replication-overview.md) attiva.

> [!NOTE]
> database SQL di Azure ridondanza dell'archiviazione di backup configurabile è attualmente disponibile in anteprima pubblica in Brasile meridionale e disponibile a livello generale solo nell'area asia sud-orientale di Azure. Nell'anteprima, se il database di origine viene creato con ridondanza locale o con ridondanza della zona, la copia del database in un server in un'area di Azure diversa non è supportata. 

## <a name="logins-in-the-database-copy"></a>Account di accesso nella copia del database

Quando si copia un database nello stesso server, è possibile utilizzare gli stessi account di accesso in entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database nel nuovo database.

Quando si copia un database in un server diverso, l'entità di sicurezza che ha avviato l'operazione di copia nel server di destinazione diventa il proprietario del nuovo database.

Indipendentemente dal server di destinazione, tutti gli utenti del database, le relative autorizzazioni e i relativi ID di sicurezza (SID) vengono copiati nella copia del database. [L'uso di](logins-create-manage.md) utenti di database indipendenti per l'accesso ai dati garantisce che il database copiato abbia le stesse credenziali utente, in modo che al termine della copia sia possibile accedervi immediatamente con le stesse credenziali.

Se si usano accessi a livello di server per l'accesso ai dati e si copia il database in un server diverso, l'accesso potrebbe non funzionare. Questo problema può verificarsi perché gli accessi non esistono nel server di destinazione o perché le password e gli ID di sicurezza (SID) sono diversi. Per informazioni sulla gestione degli account di accesso quando si copia un database in un server diverso, vedere Come gestire la sicurezza database SQL di Azure [dopo il ripristino di emergenza.](active-geo-replication-security-configure.md) Al termine dell'operazione di copia in un server diverso e prima che altri utenti siano mappati nuovamente, solo l'account di accesso associato al proprietario del database o l'amministratore del server può accedere al database copiato. Per risolvere gli account di accesso e stabilire l'accesso ai dati al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Copiare usando il portale di Azure

Per copiare un database tramite il portale di Azure, aprire la pagina per il database e quindi fare clic su **Copia**.

   ![Copia del database](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Copiare con PowerShell o l'interfaccia della riga di comando di Azure

Per copiare un database, usare gli esempi seguenti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per PowerShell, usare il cmdlet [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> Il modulo PowerShell Azure Resource Manager (RM) è ancora supportato da database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'accettazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione usando il cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Per uno script di PowerShell di esempio completo, vedere [Copiare un database in un nuovo server](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

La copia del database è un'operazione asincrona, ma il database di destinazione viene creato immediatamente dopo l'accettazione della richiesta. Se è necessario annullare l'operazione di copia mentre è ancora in corso, eliminare il database di destinazione usando [il comando az sql db delete.](/cli/azure/sql/db#az_sql_db_delete)

* * *

## <a name="copy-using-transact-sql"></a>Copia con Transact-SQL

Accedere al database master con l'account di accesso dell'amministratore del server o con l'account di accesso che ha creato il database da copiare. Per l'esito positivo della copia del database, gli account di accesso che non sono l'amministratore del server devono essere membri del `dbmanager` ruolo . Per ulteriori informazioni sugli account di accesso e la connessione al server, vedere [Gestire gli accessi](logins-create-manage.md).

Iniziare a copiare il database di origine con [CREATE DATABASE ... Istruzione AS COPY OF.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) L'istruzione T-SQL continua l'esecuzione fino al completamento dell'operazione di copia del database.

> [!NOTE]
> La chiusura dell'istruzione T-SQL non termina l'operazione di copia del database. Per terminare l'operazione, eliminare il database di destinazione.
>

> [!IMPORTANT]
> Selezione della ridondanza dell'archiviazione di backup quando si usa T-SQL CREATE DATABASE ... Il comando AS COPY OF non è ancora supportato. 

### <a name="copy-to-the-same-server"></a>Copia nello stesso server

Accedere al database master con l'account di accesso dell'amministratore del server o con l'account di accesso che ha creato il database da copiare. Perché la copia del database riesca, gli account di accesso che non sono l'amministratore del server devono essere membri del `dbmanager` ruolo .

Questo comando copia Database1 in un nuovo database denominato Database2 sullo stesso server. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Copiare in un pool elastico

Accedere al database master con l'account di accesso dell'amministratore del server o con l'account di accesso che ha creato il database da copiare. Perché la copia del database riesca, gli account di accesso che non sono l'amministratore del server devono essere membri del `dbmanager` ruolo .

Questo comando copia Database1 in un nuovo database denominato Database2 in un pool elastico denominato pool1. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

Database1 può essere un database singolo o in pool. La copia tra pool di livelli diversi è supportata, ma alcune copie tra livelli non avranno esito positivo. Ad esempio, è possibile copiare un database standard singolo o elastico in un pool per utilizzo generico, ma non è possibile copiare un database elastico standard in un pool Premium. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Copiare in un server diverso

Accedere al database master del server di destinazione in cui deve essere creato il nuovo database. Utilizzare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine nel server di origine. Anche l'account di accesso nel server di destinazione deve essere un membro del ruolo `dbmanager` o l'account di accesso dell'amministratore del server.

Questo comando copia Database1 sul server1 in un nuovo database denominato Database2 sul server2. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> I firewall di entrambi i server devono essere configurati per consentire la connessione in ingresso dall'indirizzo IP del client che emette il comando T-SQL CREATE DATABASE ... As COPY OF comando.

### <a name="copy-to-a-different-subscription"></a>Copiare in un'altra sottoscrizione

È possibile usare la procedura descritta nella sezione Copiare un [database SQL](#copy-to-a-different-server) in un server diverso per copiare il database in un server in una sottoscrizione diversa usando T-SQL. Assicurarsi di usare un account di accesso con lo stesso nome e la stessa password del proprietario del database di origine. Inoltre, l'account di accesso deve essere membro del ruolo o di un amministratore del server, sia nel server `dbmanager` di origine che in quello di destinazione.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> I [portale di Azure](https://portal.azure.com), PowerShell e l'interfaccia della riga di comando di Azure non supportano la copia del database in una sottoscrizione diversa.

> [!TIP]
> La copia del database con T-SQL supporta la copia di un database da una sottoscrizione in un tenant di Azure diverso. Questa opzione è supportata solo quando si usa un account di accesso con autenticazione SQL per accedere al server di destinazione.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorare lo stato dell'operazione di copia

Monitorare il processo di copia tramite query [nelle viste sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Mentre è in corso la copia, la colonna **state_desc** della vista sys.databases per il nuovo database è impostata su **COPYING**.

* Se la copia non riesce, **state_desc** colonna della vista sys.databases per il nuovo database viene impostata su **SUSPECT**. Eseguire l'istruzione DROP sul nuovo database e riprovare in un secondo momento.
* Se la copia ha esito positivo, la colonna **state_desc** della vista sys.databases per il nuovo database viene impostata su **ONLINE.** La copia è stata completata e il nuovo database è un database standard, che può essere modificato indipendentemente dal database di origine.

> [!NOTE]
> Se si decide di annullare il processo di copia mentre è in corso, eseguire l'istruzione [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) nel nuovo database.

> [!IMPORTANT]
> Se è necessario creare una copia con un obiettivo di servizio sostanzialmente inferiore rispetto all'origine, il database di destinazione potrebbe non avere risorse sufficienti per completare il processo di seeding e l'operazione di copia potrebbe non riuscire. In questo scenario usare una richiesta di ripristino geografico per creare una copia in un server diverso e/o in un'area diversa. Per [altre informazioni, vedere Ripristinare database SQL di Azure backup del database.](recovery-using-backups.md#geo-restore)

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Ruoli e autorizzazioni del controllo degli accessi in base al ruolo di Azure per gestire la copia del database

Per creare una copia del database, è necessario avere i ruoli seguenti

* Proprietario della sottoscrizione
* SQL Server ruolo Collaboratore o
* Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Per annullare una copia del database, è necessario avere i ruoli seguenti

* Proprietario della sottoscrizione
* SQL Server collaboratore o
* Ruolo personalizzato nei database di origine e di destinazione con l'autorizzazione seguente:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Per gestire la copia del database portale di Azure, sono necessarie anche le autorizzazioni seguenti:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Per visualizzare le operazioni nelle distribuzioni nel gruppo di risorse nel portale, le operazioni tra più provider di risorse, incluse le operazioni SQL, sono necessarie le autorizzazioni aggiuntive seguenti:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Risolvere gli account di accesso

Quando il nuovo database è online nel server di destinazione, usare l'istruzione [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) per modificare il mapping degli utenti dal nuovo database agli account di accesso nel server di destinazione. Per risolvere gli utenti isolati, vedere [Risolvere i problemi relativi agli utenti isolati (SQL Server)](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Vedere anche [Come gestire la sicurezza database SQL di Azure dopo il ripristino di emergenza.](active-geo-replication-security-configure.md)

Tutti gli utenti nel nuovo database mantengono le autorizzazioni di cui disponevano nel database di origine. L'utente che ha avviato la copia del database diventa il proprietario del nuovo database. Al termine della copia e prima che venga rieffeffeggiato il mapping di altri utenti, solo il proprietario del database può accedere al nuovo database.

Per informazioni sulla gestione di utenti e account di accesso quando si copia un database in un server diverso, vedere Come gestire la sicurezza database SQL di Azure [dopo il ripristino di emergenza.](active-geo-replication-security-configure.md)

## <a name="database-copy-errors"></a>Errori di copia del database

Durante la copia di un database nel database SQL di Azure, possono essere rilevati gli errori seguenti. Per altre informazioni, vedere [Copiare un database SQL di Azure](database-copy.md).

| Codice di errore | Gravità | Descrizione |
| ---:| ---:|:--- |
| 40635 |16 |Il client con indirizzo IP '%.&#x2a;ls' è temporaneamente disabilitato. |
| 40637 |16 |La creazione della copia del database è attualmente disabilitata. |
| 40561 |16 |Copia del database non riuscita. Il database di origine o di destinazione non esiste. |
| 40562 |16 |Copia del database non riuscita. Il database di origine è stato rimosso. |
| 40563 |16 |Copia del database non riuscita. Il database di destinazione è stato rimosso. |
| 40564 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare. |
| 40565 |16 |Copia del database non riuscita. Non è consentita più di una copia simultanea del database dalla stessa origine. Rimuovere il database di destinazione e riprovare in un secondo momento. |
| 40566 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare. |
| 40567 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare. |
| 40568 |16 |Copia del database non riuscita. Il database di origine non è più disponibile. Rimuovere il database di destinazione e riprovare. |
| 40569 |16 |Copia del database non riuscita. Il database di destinazione non è più disponibile. Rimuovere il database di destinazione e riprovare. |
| 40570 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare in un secondo momento. |
| 40571 |16 |Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare in un secondo momento. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sugli account di accesso, vedere [Gestire gli account di accesso](logins-create-manage.md) e Come gestire la sicurezza database SQL di Azure dopo il ripristino di [emergenza.](active-geo-replication-security-configure.md)
* Per esportare un database, vedere [Esportare il database in un file BACPAC.](database-export.md)
