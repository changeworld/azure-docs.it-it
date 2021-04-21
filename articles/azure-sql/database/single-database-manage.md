---
title: Creare & gestire server e database singoli
description: Informazioni sulla creazione e sulla gestione di server e database singoli in database SQL di Azure usando portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, Transact-SQL (T-SQL) e Rest-API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781596"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Creare e gestire server e database singoli in database SQL di Azure

È possibile creare e gestire server e database singoli in database SQL di Azure usando portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e Transact-SQL.

## <a name="the-azure-portal"></a>Portale di Azure

È possibile creare il gruppo di risorse per database SQL di Azure in anticipo o durante la creazione del server stesso.

### <a name="create-a-server"></a>Creare un server

Per creare un server usando il [portale di Azure](https://portal.azure.com), creare una nuova [risorsa server](logical-servers.md) da Azure Marketplace. In alternativa, è possibile creare il server quando si distribuisce un database SQL di Azure.

  ![creare un server](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Creare un database vuoto o di esempio

Per creare una singola database SQL di Azure usando il [portale di Azure](https://portal.azure.com), scegliere la risorsa database SQL di Azure in Azure Marketplace. È possibile creare il gruppo di risorse e il server in anticipo o durante la creazione del database singolo. È possibile creare un database vuoto o creare un database di esempio basato su Adventure Works LT.

  ![Creare il database 1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Per informazioni sulla selezione del piano tariffario per il database, vedere [modello di acquisto basato su DTU](service-tiers-dtu.md) e [modello di acquisto basato su vCore](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Gestire un server esistente

Per gestire un server esistente, passare al server usando diversi metodi, ad esempio da una pagina di database specifica, dalla pagina **Server SQL** o dalla pagina **Tutte le** risorse.

Per gestire un database esistente, passare alla pagina **Database SQL** e selezionare il database da gestire. Lo screenshot seguente mostra come iniziare a impostare un firewall di livello server per un database dalla pagina **Informazioni generali** per un database.

   ![Regola del firewall del server](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Per configurare le proprietà delle prestazioni di un database, vedere [modello di acquisto basato su DTU](service-tiers-dtu.md) e [modello di acquisto basato su vCore](service-tiers-vcore.md).
> [!TIP]
> Per un portale di Azure introduttiva, vedere [Creare un database nel database SQL nella portale di Azure](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Per creare e gestire server, database singoli e in pool e firewall a livello di server con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Per gli script di esempio di [PowerShell,](scripts/create-and-configure-database-powershell.md) vedere Usare PowerShell per creare un database nel database SQL e configurare una regola del firewall a livello di server e Monitorare e ridimensionare un [database nel database SQL usando PowerShell.](scripts/monitor-and-scale-database-powershell.md)

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

## <a name="the-azure-cli"></a>Interfaccia della riga di comando di Azure

Per creare e gestire i server, i database e i firewall con l'interfaccia della riga di comando di [Azure,](/cli/azure)usare i comandi seguenti dell'interfaccia della [riga di comando di Azure.](/cli/azure/sql/db) Usare [Cloud Shell](../../cloud-shell/overview.md) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. Per creare e gestire i pool elastici, vedere [Pool elastici](elastic-pool-overview.md).

> [!TIP]
> Per una guida introduttiva all'interfaccia della riga di comando di Azure, vedere Creare un database SQL di Azure [usando l'interfaccia della riga di comando di Azure.](az-cli-script-samples-content-guide.md) Per gli script di esempio dell'interfaccia della riga di comando di [Azure,](scripts/create-and-configure-database-cli.md) vedere Usare l'interfaccia della riga di comando per creare un database in database SQL di Azure e configurare una regola del firewall del database SQL e Usare l'interfaccia della riga di comando per monitorare e ridimensionare [un database in database SQL di Azure](scripts/monitor-and-scale-database-cli.md).
>

| Cmdlet | Descrizione |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Crea un database|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Elenca tutti i database e i data warehouse in un server o tutti i database in un pool elastico|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Elenca gli obiettivi di servizio e i limiti di archiviazione disponibili|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Restituisce gli utilizzi del database|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Recupera un database o un data warehouse|
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

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Per creare e gestire server, database e firewall con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)o qualsiasi altro programma in grado di connettersi a un server nel database SQL e passare i comandi Transact-SQL. Per la gestione dei pool elastici, vedere [Pool elastici](elastic-pool-overview.md).

> [!TIP]
> Per una guida introduttiva per SQL Server Management Studio in Microsoft Windows, vedere [Database SQL di Azure: usare SQL Server Management Studio per connettersi ed eseguire query sui dati](connect-query-ssms.md). Per una guida introduttiva per Visual Studio Code in macOS, Linux o Windows, vedere [Database SQL di Azure: Usare Visual Studio Code per connettersi ai dati ed eseguire query](connect-query-vscode.md).
> [!IMPORTANT]
> Non è possibile creare o eliminare un server con Transact-SQL.

| Comando | Descrizione |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Crea un nuovo database singolo. È necessario essere connessi al database master per creare un nuovo database.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modifica un database o un pool elastico. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Questo comando elimina un database.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, per database SQL di Azure o un pool SQL dedicato in Azure Synapse Analytics. Se si è connessi al database master in un server nel database SQL, restituisce informazioni su tutti i database. Per Azure Synapse Analytics, è necessario essere connessi al database master.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Restituisce l'utilizzo di CPU, I/O e memoria per un database in database SQL di Azure. Esiste una riga ogni 15 secondi, anche se non è presente alcuna attività nel database.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Restituisce i dati di archiviazione e utilizzo della CPU per un database in database SQL di Azure. I dati vengono raccolti e aggregati in intervalli di cinque minuti.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contiene statistiche per gli eventi di connettività del database SQL, che offrono una panoramica degli errori e delle connessioni al database. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Restituisce connessioni database SQL di Azure, errori di connessione e deadlock. È possibile usare queste informazioni per rilevare o risolvere i problemi dell'attività del database SQL.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crea o aggiorna le impostazioni del firewall a livello di server per il server. Questa stored procedure è disponibile solo nel database master all'account di accesso principale di livello server. Una regola del firewall a livello di server può essere creata tramite Transact-SQL solo dopo la creazione della prima regola del firewall a livello di server da parte di un utente con autorizzazioni a livello di Azure|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Restituisce informazioni sulle impostazioni del firewall a livello di server associate al database in database SQL di Azure.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Rimuove le impostazioni del firewall a livello di server dal server. Questa stored procedure è disponibile solo nel database master all'account di accesso principale di livello server.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crea o aggiorna le regole del firewall a livello di database per il database in database SQL di Azure. Le regole firewall del database possono essere configurate per il database master e per i database utente nel database SQL. Le regole del firewall del database sono utili quando si usano utenti di database indipendenti. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Restituisce informazioni sulle impostazioni del firewall a livello di database associate al database in database SQL di Azure. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Rimuove l'impostazione del firewall a livello di database da un database. |

## <a name="rest-api"></a>API REST

Per creare e gestire server, database e firewall, usare queste richieste API REST.

| Comando | Descrizione |
| --- | --- |
|[Server - Creare o aggiornare](/rest/api/sql/servers/createorupdate)|Crea o aggiorna un nuovo server.|
|[Servers - Delete](/rest/api/sql/servers/delete)|Elimina un server SQL.|
|[Servers - Get](/rest/api/sql/servers/get)|Ottiene un server.|
|[Servers - List](/rest/api/sql/servers/list)|Restituisce un elenco di server in una sottoscrizione.|
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
|[Regole del firewall - Get](/rest/api/sql/firewallrules/get)|Ottiene una regola del firewall.|
|[Regole del firewall - Elenco per server](/rest/api/sql/firewallrules/listbyserver)|Restituisce un elenco di regole firewall.|

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione di un database SQL Server in Azure, vedere [Migrazione al database SQL di Azure](migrate-to-database-from-sql-server.md).
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](features-comparison.md) (Funzioni del database SQL di Azure).
