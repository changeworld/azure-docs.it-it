---
title: Che cos'è un server database SQL di Azure e Azure Synapse Analytics?
titleSuffix: ''
description: Informazioni sui server SQL logici usati database SQL di Azure e Azure Synapse Analytics e su come gestirli.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c76d3ae78bf2b9b4a71d9520f7f1c6c2c322483b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784516"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Che cos'è un server SQL logico in database SQL di Azure e Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In database SQL di Azure e Azure Synapse Analytics, un server è un costrutto logico che funge da punto amministrativo centrale per una raccolta di database. A livello di server, è possibile amministrare account [](threat-detection-configure.md) [di](logins-create-manage.md)accesso, regole del [firewall,](firewall-configure.md)regole di [controllo,](../../azure-sql/database/auditing-overview.md)criteri di rilevamento delle minacce e gruppi di failover [automatico.](auto-failover-group-overview.md) Un server può essere in un'area diversa rispetto al relativo gruppo di risorse. Il server deve esistere prima di poter creare un database in database SQL di Azure o un database data warehouse in Azure Synapse Analytics. Tutti i database gestiti da un singolo server vengono creati nella stessa area del server.

Questo server è diverso da SQL Server istanza di che si ha familiarità con l'ambiente locale. In particolare, non vi sono garanzie relative alla posizione dei database o data warehouse database in relazione al server che li gestisce. Inoltre, né database SQL di Azure né Azure Synapse esporre funzionalità o accessi a livello di istanza. Al contrario, i database dell'istanza in un'istanza gestita si trovano tutti fisicamente in una stessa posizione, nello stesso modo in cui si ha familiarità con SQL Server nell'ambiente locale o della macchina virtuale.

Quando si crea un server, si forniscono un account di accesso al server e una password con diritti amministrativi per il database master in tale server e per tutti i database creati in tale server. Questo account iniziale è un account di accesso SQL. database SQL di Azure e Azure Synapse Analytics supportano l'autenticazione SQL e Azure Active Directory per l'autenticazione. Per informazioni su account di accesso e autenticazione, vedere [Gestione di database e account di accesso nel database SQL di Azure](logins-create-manage.md). L'autenticazione Windows non è supportata.

Un server nel database SQL e in Azure Synapse:

- Viene creato all'interno di una sottoscrizione di Azure, ma può essere spostato con le risorse contenute in un'altra sottoscrizione
- È la risorsa padre per database, pool elastici e data warehouse
- Fornisce uno spazio dei nomi per database, pool elastici e data warehouse database
- È un contenitore logico con semantica di durata forte: elimina un server ed elimina i database, i pool elastici e i pool SQK
- Partecipa al controllo degli [accessi in](../../role-based-access-control/overview.md) base al ruolo di Azure: database, pool elastici e database data warehouse all'interno di un server ereditano i diritti di accesso dal server
- Elemento di ordine elevato dell'identità di database, pool elastici e database data warehouse per la gestione delle risorse di Azure (vedere lo schema URL per database e pool)
- Colloca risorse in un'area
- Fornisce un endpoint di connessione per l'accesso ai database (`<serverName>`.database.windows.net)
- Fornisce accesso ai metadati riguardanti le risorse contenute tramite DMV, connettendosi a un database master
- Fornisce l'ambito per i criteri di gestione che si applicano ai database: account di accesso, firewall, controllo, rilevamento minacce e così via
- È limitato da una quota nella sottoscrizione padre: sei server per sottoscrizione per impostazione predefinita - [vedere i limiti relativi alle sottoscrizioni qui](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Fornisce l'ambito per la quota database e la quota DTU o vCore per le risorse in esso contenute, ad esempio 45.000 DTU
- Rappresenta l'ambito di controllo delle versioni per le funzionalità abilitate sulle risorse contenute
- Gli account di accesso all'entità a livello di server possono gestire tutti i database in un server
- Può contenere account di accesso simili a quelli nelle istanze di SQL Server nell'ambiente locale a cui viene concesso l'accesso a uno o più database nel server e a cui possono essere concessi diritti amministrativi limitati. Per altre informazioni, vedere [Autenticazione e autorizzazione per database SQL: concessione dell'accesso](logins-create-manage.md).
- Le regole di confronto predefinite per tutti i database creati in un server sono , dove è inglese (Stati Uniti), è la tabella codici `SQL_LATIN1_GENERAL_CP1_CI_AS` `LATIN1_GENERAL` `CP1` 1252, non fa distinzione tra maiuscole e minuscole e fa distinzione tra maiuscole e `CI` `AS` minuscole.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Gestire server, database e firewall usando il portale di Azure

È possibile creare il gruppo di risorse per un server in anticipo o durante la creazione del server stesso. Esistono diversi metodi per accedere al modulo per la creazione di un nuovo server SQL Server, creando un nuovo server SQL Server o nell'ambito della procedura per creare un nuovo database.

### <a name="create-a-blank-server"></a>Creare un server vuoto

Per creare un server (senza un database, un pool elastico o un database [data warehouse)](https://portal.azure.com)usando il portale di Azure , passare a un modulo vuoto di SQL Server (sql server logico).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Creare un database vuoto o di esempio in database SQL di Azure

Per creare un database nel database SQL usando il portale di Azure [,](https://portal.azure.com)passare a un modulo di database SQL vuoto e specificare le informazioni richieste. È possibile creare il gruppo di risorse e il server in anticipo o durante la creazione del database stesso. È possibile creare un database vuoto o creare un database di esempio basato su Adventure Works LT.

  ![Creare il database 1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Per informazioni sulla selezione del piano tariffario per il database, vedere [modello di acquisto basato su DTU](service-tiers-dtu.md) e [modello di acquisto basato su vCore](service-tiers-vcore.md).

Per creare un'istanza gestita, vedere [Creare un'istanza gestita](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Gestire un server esistente

Per gestire un server esistente, passare al server usando diversi metodi, ad esempio dalla pagina del database specifico, dalla pagina **Server SQL** o dalla pagina **Tutte le** risorse.

Per gestire un database esistente, passare alla pagina **Database SQL** e fare clic sul database che si desidera gestire. Lo screenshot seguente mostra come iniziare a impostare un firewall di livello server per un database dalla pagina **Informazioni generali** per un database.

   ![Regola del firewall del server](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Per configurare le proprietà delle prestazioni di un database, vedere [modello di acquisto basato su DTU](service-tiers-dtu.md) e [modello di acquisto basato su vCore](service-tiers-vcore.md).
> [!TIP]
> Per un portale di Azure introduttiva, vedere [Creare un database nel database SQL nella portale di Azure](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Gestire server, database e firewall con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager PowerShell è ancora supportato, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Per creare e gestire server, database e firewall con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Per creare e gestire i pool elastici, vedere [Pool elastici](elastic-pool-overview.md).

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Crea un database |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Recupera uno o più database|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Rimuove un database|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Crea un gruppo di risorse|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Crea un server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Restituisce informazioni sui server|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Modifica le proprietà di un server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Rimuove un server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Crea una regola del firewall a livello di server |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Ottiene le regole del firewall per un server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modifica una regola del firewall in un server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Elimina una regola del firewall da un server|
| New-AzSqlServerVirtualNetworkRule | Crea una [*regola di rete virtuale*](vnet-service-endpoint-rule-overview.md), in base a una subnet che è un endpoint servizio di Rete virtuale. |

> [!TIP]
> Per una guida introduttiva di PowerShell, vedere [Creare un database in database SQL di Azure usando PowerShell.](single-database-create-quickstart.md) Per gli script di esempio di [PowerShell,](scripts/create-and-configure-database-powershell.md) vedere Usare PowerShell per creare un database in database SQL di Azure e configurare una regola del firewall e Monitorare e ridimensionare un [database database SQL di Azure tramite PowerShell.](scripts/monitor-and-scale-database-powershell.md)
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Gestire server, database e firewall usando l'interfaccia della riga di comando di Azure

Per creare e gestire server, database e firewall con l'interfaccia della riga di comando di [Azure,](/cli/azure)usare i comandi seguenti per il database SQL dell'interfaccia della riga di comando [di Azure.](/cli/azure/sql/db) Usare [Cloud Shell](../../cloud-shell/overview.md) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. Per creare e gestire i pool elastici, vedere [Pool elastici](elastic-pool-overview.md).

| Cmdlet | Descrizione |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Crea un database|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Elenca tutti i database gestiti da un server o tutti i database in un pool elastico|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Elenca gli obiettivi di servizio e i limiti di archiviazione disponibili|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Restituisce gli utilizzi del database|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Ottiene un database
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Aggiorna un database|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Rimuove un database|
|[az group create](/cli/azure/group#az_group_create)|Crea un gruppo di risorse|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Crea un server|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Elenca i server|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Restituisce gli utilizzi del server|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Ottiene un server|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Aggiorna un server|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Consente di eliminare un server|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Crea una regola del firewall del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Elenca le regole del firewall in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Mostra i dettagli di una regola del firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aggiorna una regola del firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina una regola del firewall|

> [!TIP]
> Per una guida introduttiva all'interfaccia della riga di comando di Azure, vedere Creare un [database in database SQL di Azure'interfaccia della riga di comando di Azure.](az-cli-script-samples-content-guide.md) Per gli script di esempio dell'interfaccia della riga di comando di [Azure,](scripts/create-and-configure-database-cli.md) vedere Usare l'interfaccia della riga di comando per creare un database in database SQL di Azure e configurare una regola del firewall e Usare l'interfaccia della riga di comando per monitorare e ridimensionare [un database in database SQL di Azure](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Gestire server, database e firewall con Transact-SQL

Per creare e gestire server, database e firewall con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)o qualsiasi altro programma in grado di connettersi a un server e passare comandi Transact-SQL. Per la gestione dei pool elastici, vedere [Pool elastici](elastic-pool-overview.md).

> [!IMPORTANT]
> Non è possibile creare o eliminare un server con Transact-SQL.

| Comando | Descrizione |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Crea un nuovo database nel database SQL di Azure. È necessario essere connessi al database master per creare un nuovo database.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Crea un nuovo database data warehouse in Azure Synapse. È necessario essere connessi al database master per creare un nuovo database.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modifica il database o il pool elastico. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Modifica un database data warehouse in Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Questo comando elimina un database.|
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, per un database. Se si è connessi al database master per un server, restituisce informazioni su tutti i database. Per Azure Synapse, è necessario essere connessi al database master.|
|[sys.dm_db_resource_stats (Database SQL di Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Restituisce l'utilizzo di CPU, I/O e memoria per un database in database SQL di Azure. È presente una riga per ogni 15 secondi, anche se non esiste alcuna attività nel database.|
|[sys.resource_stats (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Restituisce i dati di utilizzo e archiviazione della CPU per un database in database SQL di Azure. I dati vengono raccolti e aggregati in intervalli di cinque minuti.|
|[sys.database_connection_stats (Database di SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contiene statistiche per gli eventi di connettività del database per database SQL di Azure, fornendo una panoramica degli errori e delle connessioni al database. |
|[sys.event_log (Database di SQL Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Restituisce le database SQL di Azure di database, gli errori di connessione e i deadlock per database SQL di Azure. È possibile usare queste informazioni per tenere traccia o risolvere i problemi relativi all'attività del database.|
|[sp_set_firewall_rule (Database di SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crea o aggiorna le impostazioni del firewall a livello di server per il server. Questa stored procedure è disponibile solo nel database master all'account di accesso principale di livello server. Una regola del firewall a livello di server può essere creata tramite Transact-SQL solo dopo la creazione della prima regola del firewall a livello di server da parte di un utente con autorizzazioni a livello di Azure|
|[sys.firewall_rules (Database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Restituisce informazioni sulle impostazioni del firewall a livello di server associate a un server.|
|[sp_delete_firewall_rule (Database di SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Rimuove le impostazioni del firewall a livello di server da un server. Questa stored procedure è disponibile solo nel database master all'account di accesso principale di livello server.|
|[sp_set_database_firewall_rule (Database di SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crea o aggiorna le regole del firewall a livello di database per un database in database SQL di Azure. Le regole del firewall del database possono essere configurate per il database master e per i database utente nel database SQL. Le regole del firewall del database sono utili quando si usano utenti di database indipendenti. Le regole del firewall del database non sono supportate in Azure Synapse.|
|[sys.database_firewall_rules (Database di SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Restituisce informazioni sulle impostazioni del firewall a livello di database per un database in database SQL di Azure. |
|[sp_delete_database_firewall_rule (Database di SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Rimuove l'impostazione del firewall a livello di database per un database in database SQL di Azure. |

> [!TIP]
> Per una guida introduttiva per SQL Server Management Studio in Microsoft Windows, vedere [Database SQL di Azure: usare SQL Server Management Studio per connettersi ed eseguire query sui dati](connect-query-ssms.md). Per una guida introduttiva per Visual Studio Code in macOS, Linux o Windows, vedere [Database SQL di Azure: Usare Visual Studio Code per connettersi ai dati ed eseguire query](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Gestire server, database e firewall con l'API REST

Per creare e gestire server, database e firewall, usare queste richieste API REST.

| Comando | Descrizione |
| --- | --- |
|[Server - Creare o aggiornare](/rest/api/sql/servers/createorupdate)|Crea o aggiorna un nuovo server.|
|[Servers - Delete](/rest/api/sql/servers/delete)|Elimina un server.|
|[Servers - Get](/rest/api/sql/servers/get)|Ottiene un server.|
|[Servers - List](/rest/api/sql/servers/list)|Restituisce un elenco di server.|
|[Servers - List by resource group](/rest/api/sql/servers/listbyresourcegroup)|Restituisce un elenco di server in un gruppo di risorse.|
|[Servers - Update](/rest/api/sql/servers/update)|Aggiorna un server esistente.|
|[Databases - Create or update](/rest/api/sql/databases/createorupdate)|Crea un nuovo database o ne aggiorna uno esistente.|
|[Databases - Delete](/rest/api/sql/databases/delete)|Questo comando elimina un database.|
|[Databases - Get](/rest/api/sql/databases/get)|Ottiene un database.|
|[Database - Elenco per pool elastico](/rest/api/sql/databases/listbyelasticpool)|Restituisce un elenco di database in un pool elastico.|
|[Database - Elenco per server](/rest/api/sql/databases/listbyserver)|Restituisce un elenco di database in un server.|
|[Databases - Update](/rest/api/sql/databases/update)|Aggiorna un database esistente.|
|[Regole del firewall - Creare o aggiornare](/rest/api/sql/firewallrules/createorupdate)|Crea o aggiorna una regola del firewall.|
|[Regole del firewall - Elimina](/rest/api/sql/firewallrules/delete)|Elimina una regola del firewall.|
|[Regole del firewall - Ottieni](/rest/api/sql/firewallrules/get)|Ottiene una regola del firewall.|
|[Regole del firewall - Elenco per server](/rest/api/sql/firewallrules/listbyserver)|Restituisce un elenco di regole firewall.|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla migrazione di un database SQL Server a database SQL di Azure, vedere [Eseguire la migrazione a database SQL di Azure](migrate-to-database-from-sql-server.md).
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](features-comparison.md) (Funzioni del database SQL di Azure).