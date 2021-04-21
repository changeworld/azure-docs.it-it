---
title: Regole del firewall IP
description: Configurare le regole del firewall IP a livello di server per un database database SQL di Azure o Azure Synapse Analytics firewall. Gestire l'accesso e configurare le regole del firewall IP a livello di database per il database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: 200df14e7d18c4bdfb903bef46c169f6f7bf5ca5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781776"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>database SQL di Azure e Azure Synapse del firewall IP
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Quando si crea un nuovo server in database SQL di Azure o Azure Synapse Analytics denominato *mysqlserver*, ad esempio, un firewall a livello di server blocca tutti gli accessi all'endpoint pubblico per il server (accessibile all'indirizzo *mysqlserver.database.windows.net*). Per semplicità, *il database SQL* viene usato per fare riferimento sia al database SQL che Azure Synapse Analytics.

> [!IMPORTANT]
> Questo articolo *non* si applica a *Istanza gestita di SQL di Azure*. Per informazioni sulla configurazione di rete, vedere [Connettere l'applicazione a Istanza gestita di SQL di Azure](../managed-instance/connect-application-instance.md).
>
> Azure Synapse supporta solo le regole del firewall IP a livello di server. Non supporta le regole del firewall IP a livello di database.

## <a name="how-the-firewall-works"></a>Funzionamento del firewall

I tentativi di connessione da Internet e Azure devono passare attraverso il firewall prima che raggiungano il server o il database, come illustrato nel diagramma seguente.

   ![Diagramma di configurazione del firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server

consentono ai client di accedere all'intero server ovvero a tutti i database gestiti dal server. Le regole vengono archiviate nel database *master.* Per un server è possibile avere un massimo di 128 regole del firewall IP a livello di server. Se l'impostazione Consenti alle risorse **e ai servizi** di Azure di accedere a questo server è abilitata, viene conteggiato come una singola regola del firewall per il server.
  
È possibile configurare le regole del firewall IP a livello di server usando le istruzioni portale di Azure, PowerShell o Transact-SQL.

- Per usare il portale o PowerShell, è necessario essere il proprietario della sottoscrizione o un collaboratore della sottoscrizione.
- Per usare Transact-SQL, è necessario connettersi al database *master* come account di accesso dell'entità di livello server o come amministratore Azure Active Directory database. Una regola del firewall IP a livello di server deve essere prima creata da un utente con autorizzazioni a livello di Azure.

> [!NOTE]
> Per impostazione predefinita, durante la creazione di un nuovo server SQL logico dal portale di Azure, l'impostazione Consenti alle risorse e ai servizi di Azure di accedere a questo **server** è impostata su **No.**

### <a name="database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di database

Le regole del firewall IP a livello di database consentono ai client di accedere a determinati database (sicuri). Le regole vengono create per ogni database (incluso il database *master)* e vengono archiviate nel singolo database.
  
- È possibile creare e gestire le regole del firewall IP a livello di database per i database master e utente solo tramite istruzioni Transact-SQL e solo dopo aver configurato il primo firewall a livello di server.
- Se si specifica un intervallo di indirizzi IP nella regola del firewall IP a livello di database non compreso nell'intervallo nella regola del firewall IP a livello di server, solo i client che dispongono di indirizzi IP nell'intervallo a livello di database possono accedere al database.
- Per un database è possibile avere un massimo di 128 regole del firewall IP a livello di database. Per altre informazioni sulla configurazione delle regole del firewall IP a livello di database, vedere l'esempio più avanti in questo articolo e vedere sp_set_database_firewall_rule [(database SQL di Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Raccomandazioni su come impostare le regole del firewall

È consigliabile usare le regole del firewall IP a livello di database quando possibile. Questa pratica migliora la sicurezza e rende più portabile il database. Usare le regole del firewall IP a livello di server per gli amministratori. Usarli anche quando si dispone di molti database con gli stessi requisiti di accesso e non si vuole configurare ogni database singolarmente.

> [!NOTE]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server e a livello di database a confronto

*Gli utenti di un database devono essere completamente isolati da un altro database?*

Se *sì,* usare le regole del firewall IP a livello di database per concedere l'accesso. Questo metodo evita l'uso di regole del firewall IP a livello di server, che consentono l'accesso tramite il firewall a tutti i database. Ciò ridurrebbe la profondità delle difese.

*Gli utenti degli indirizzi IP devono accedere a tutti i database?*

In *caso affermativa,* usare le regole del firewall IP a livello di server per ridurre il numero di volte in cui è necessario configurare le regole del firewall IP.

*La persona o il team che configura le regole del firewall IP ha accesso solo tramite portale di Azure, PowerShell o l'API REST?*

In tal caso, è necessario usare le regole del firewall IP a livello di server. Le regole del firewall IP a livello di database possono essere configurate solo tramite Transact-SQL.  

*La persona o il team che configura le regole del firewall IP non dispone di autorizzazioni di alto livello a livello di database?*

In tal caso, usare le regole del firewall IP a livello di server. È necessaria almeno *l'autorizzazione CONTROL DATABASE* a livello di database per configurare le regole del firewall IP a livello di database tramite Transact-SQL.  

*La persona o il team che configura o controlla le regole del firewall IP gestisce centralmente le regole del firewall IP per molti (forse centinaia) database?*

In questo scenario, le procedure consigliate sono determinate dalle esigenze e dall'ambiente. Le regole del firewall IP a livello di server potrebbero essere più facili da configurare, tuttavia lo scripting consente di configurare le regole a livello di database. E anche se si usano regole del firewall IP a livello di server, potrebbe essere necessario controllare le regole del firewall IP a livello di database per verificare se gli utenti con l'autorizzazione *CONTROL* per il database creano regole del firewall IP a livello di database.

*È possibile usare una combinazione di regole del firewall IP a livello di server e di database?*

Sì. Alcuni utenti, ad esempio gli amministratori, potrebbero richiedere regole del firewall IP a livello di server. Altri utenti, ad esempio gli utenti di un'applicazione di database, potrebbero aver bisogno di regole del firewall IP a livello di database.

### <a name="connections-from-the-internet"></a>Connessioni da Internet

Quando un computer tenta di connettersi al server da Internet, il firewall verifica innanzitutto l'indirizzo IP di origine della richiesta rispetto alle regole del firewall IP a livello di database per il database richiesto dalla connessione.

- Se l'indirizzo è compreso in un intervallo specificato nelle regole del firewall IP a livello di database, la connessione viene concessa al database che contiene la regola.
- Se l'indirizzo non è compreso in un intervallo nelle regole del firewall IP a livello di database, il firewall controlla le regole del firewall IP a livello di server. Se l'indirizzo è compreso in un intervallo compreso nelle regole del firewall IP a livello di server, la connessione viene concessa. Le regole del firewall IP a livello di server si applicano a tutti i database gestiti dal server.  
- Se l'indirizzo non è compreso in un intervallo compreso in una delle regole del firewall IP a livello di database o di server, la richiesta di connessione ha esito negativo.

> [!NOTE]
> Per accedere database SQL di Azure dal computer locale, assicurarsi che il firewall nella rete e nel computer locale consenta la comunicazione in uscita sulla porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Connessioni dall'interno di Azure

Per consentire alle applicazioni ospitate in Azure di connettersi al server SQL, è necessario che le connessioni di Azure siano abilitate. Per abilitare le connessioni di Azure, deve essere presente una regola del firewall con indirizzi IP iniziali e finali impostati su 0.0.0.0.

Quando un'applicazione di Azure tenta di connettersi al server, il firewall verifica che le connessioni di Azure siano consentite verificando l'esistenza di questa regola del firewall. Questa opzione può essere attivata direttamente dal pannello portale di Azure impostando l'opzione Consenti alle risorse e ai servizi di Azure di accedere al **server** su **ATTIVAta** nelle impostazioni Firewall e **reti** virtuali. Impostando su ON viene creata una regola del firewall in ingresso per l'indirizzo IP 0.0.0.0 - 0.0.0.0 denominato **AllowAllWindowsIP.** Usare PowerShell o l'interfaccia della riga di comando di Azure per creare una regola del firewall con indirizzi IP iniziale e finale impostati su 0.0.0.0 se non si usa il portale. 

> [!IMPORTANT]
> Questa opzione configura il firewall per consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Se si seleziona questa opzione, assicurarsi che le autorizzazioni di accesso e utente limitino l'accesso solo agli utenti autorizzati.

## <a name="permissions"></a>Autorizzazioni

Per poter creare e gestire le regole del firewall IP per SQL Server di Azure, sarà necessario avere:

- nel ruolo [SQL Server collaboratore](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- nel ruolo [Gestione sicurezza SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- proprietario della risorsa che contiene il server Azure SQL

## <a name="create-and-manage-ip-firewall-rules"></a>Creare e gestire le regole del firewall IP

È possibile creare la prima impostazione [](https://portal.azure.com/) del firewall a livello di server usando il portale di Azure o a livello di codice usando Azure PowerShell [,](/powershell/module/az.sql)l'interfaccia della riga di comando di [Azure](/cli/azure/sql/server/firewall-rule)o un'API [REST di](/rest/api/sql/firewallrules/createorupdate)Azure. È possibile creare e gestire regole aggiuntive del firewall IP a livello di server usando questi metodi o Transact-SQL.

> [!IMPORTANT]
> Le regole del firewall IP a livello di database possono essere create e gestite solo tramite Transact-SQL.

Per migliorare le prestazioni, le regole del firewall IP a livello di server vengono temporaneamente memorizzate nella cache a livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> È possibile usare il [controllo del database per](../../azure-sql/database/auditing-overview.md) controllare le modifiche del firewall a livello di server e di database.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Usare il portale di Azure per gestire le regole del firewall IP a livello di server

Per impostare una regola del firewall IP a livello di server nel portale di Azure, passare alla pagina di panoramica per il database o il server.

> [!TIP]
> Per un'esercitazione, [vedere Creare un database usando il portale di Azure](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Dalla pagina di panoramica del database

1. Per impostare una regola del firewall IP a livello di server dalla pagina di panoramica del database, selezionare **Imposta firewall server** sulla barra degli strumenti, come illustrato nell'immagine seguente.

    ![Regola del firewall IP del server](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Verrà visualizzata la pagina **Impostazioni del firewall** per il server.

2. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer in uso e quindi selezionare **Salva**. Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

    ![Impostare la regola del firewall IP a livello di server](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Dalla pagina di panoramica del server

Verrà visualizzata la pagina di panoramica per il server. Mostra il nome completo del server (ad esempio *mynewserver20170403.database.windows.net*) e fornisce opzioni per un'ulteriore configurazione.

1. Per impostare una regola a livello di server da questa pagina, selezionare **Firewall** dal menu **Impostazioni** sul lato sinistro.

2. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer in uso e quindi selezionare **Salva**. Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Usare Transact-SQL per gestire le regole del firewall IP

| Visualizzazione del catalogo o stored procedure | Level | Descrizione |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Server |Rimuove regole del firewall IP a livello di server |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Database |Visualizza le regole del firewall IP a livello di database correnti |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Database |Crea o aggiorna regole del firewall IP a livello di database |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Database |Rimuove le regole del firewall IP a livello di database |

L'esempio seguente esamina le regole esistenti, abilita un intervallo di indirizzi IP nel server *Contoso* ed elimina una regola del firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Aggiungere quindi una regola del firewall IP a livello di server.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Per eliminare una regola del firewall IP a livello di server, eseguire la *sp_delete_firewall_rule* stored procedure. L'esempio seguente elimina la regola *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Usare PowerShell per gestire le regole del firewall IP a livello di server

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager PowerShell è ancora supportato da database SQL di Azure, ma tutto lo sviluppo è ora per il modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

| Cmdlet | Level | Descrizione |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Restituisce le regole del firewall a livello di server correnti |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Crea una nuova regola del firewall a livello di server |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Rimuove regole del firewall a livello di server |

L'esempio seguente usa PowerShell per impostare una regola del firewall IP a livello di server:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Ad $servername specificare il nome del server e non il nome DNS completo, ad esempio **specificare mysqldbserver** **anziché** mysqldbserver.database.windows.net
>
> Per esempi di PowerShell nel contesto di una guida introduttiva, vedere Creare un database [- PowerShell](powershell-script-content-guide.md) e Creare un database singolo e configurare una regola del firewall IP a livello di [server usando PowerShell.](scripts/create-and-configure-database-powershell.md)

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Usare l'interfaccia della riga di comando per gestire le regole del firewall IP a livello di server

| Cmdlet | Level | Descrizione |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Server|Crea una regola del firewall IP del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Server|Elenca le regole del firewall IP in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Server|Mostra i dettagli di una regola del firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Server|Aggiorna una regola del firewall IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Server|Elimina una regola del firewall IP|

L'esempio seguente usa l'interfaccia della riga di comando per impostare una regola del firewall IP a livello di server:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Ad $servername specificare il nome del server e non il nome DNS completo, ad esempio **specificare mysqldbserver** **anziché mysqldbserver.database.windows.net**
>
> Per un esempio dell'interfaccia della riga di comando nel contesto di una guida introduttiva, vedere Creare un database - Interfaccia della riga di comando di [Azure](az-cli-script-samples-content-guide.md) e Creare un database singolo e configurare una regola del firewall IP a livello di server usando l'interfaccia della riga di [comando di Azure.](scripts/create-and-configure-database-cli.md)

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Usare un'API REST per gestire le regole del firewall IP a livello di server

| API | Level | Descrizione |
| --- | --- | --- |
| [Elencare le regole del firewall](/rest/api/sql/firewallrules/listbyserver) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [Creare o aggiornare le regole del firewall](/rest/api/sql/firewallrules/createorupdate) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [Eliminare le regole del firewall](/rest/api/sql/firewallrules/delete) |Server |Rimuove regole del firewall IP a livello di server |
| [Ottenere le regole del firewall](/rest/api/sql/firewallrules/get) | Server | Ottiene regole del firewall IP a livello di server |

## <a name="troubleshoot-the-database-firewall"></a>Eseguire la risoluzione dei problemi del firewall del database

Quando l'accesso a database SQL di Azure non si comporta come previsto, tenere presente quanto segue.

- **Configurazione del firewall locale:**

  Per consentire al computer di accedere al database SQL di Azure, è necessario creare un'eccezione del firewall sul computer per la porta TCP 1433. Per creare connessioni all'interno del limite del cloud di Azure, potrebbe essere necessario aprire porte aggiuntive. Per altre informazioni, vedere la sezione "Database SQL: esterno rispetto all'interno" di Porte oltre [la 1433 per ADO.NET 4.5 e database SQL di Azure](adonet-v12-develop-direct-route-ports.md).

- **Network Address Translation:**

  A causa di NAT (Network Address Translation), l'indirizzo IP usato dal computer per connettersi a database SQL di Azure può essere diverso dall'indirizzo IP nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP utilizzato dal computer per connettersi ad Azure:
    1. Accedere al portale.
    1. Passare alla **scheda Configura** nel server che ospita il database.
    1. **L'indirizzo IP del** client corrente viene visualizzato nella sezione Indirizzi IP **consentiti.** Selezionare **Aggiungi** per **Indirizzi IP consentiti** per consentire al computer di accedere al server.

- **Le modifiche all'elenco elementi consentiti non sono ancora state applicate:**

  Potrebbe verificarsi un ritardo massimo di cinque minuti per l'applicazione delle modifiche database SQL di Azure configurazione del firewall.

- **L'account di accesso non è autorizzato o è stata usata una password non corretta:**

  Se un account di accesso non dispone delle autorizzazioni per il server o la password non è corretta, la connessione al server viene negata. La creazione di un'impostazione del firewall offre *solo* ai client la possibilità di provare a connettersi al server. Il client deve comunque fornire le credenziali di sicurezza necessarie. Per altre informazioni sulla preparazione degli account di accesso, vedere [Controllo e concessione dell'accesso al database.](logins-create-manage.md)

- **Indirizzo IP dinamico:**

  Se si dispone di una connessione Internet che usa l'indirizzamento IP dinamico e si verificano problemi di accesso attraverso il firewall, provare una delle soluzioni seguenti:
  
  - Chiedere al provider di servizi Internet l'intervallo di indirizzi IP assegnato ai computer client che accedono al server. Aggiungere tale intervallo di indirizzi IP come regola del firewall IP.
  - Ottenere invece indirizzi IP statici per i computer client. Aggiungere gli indirizzi IP come regole del firewall IP.

## <a name="next-steps"></a>Passaggi successivi

- Verificare che l'ambiente di rete aziendale consenta la comunicazione in ingresso dagli intervalli di indirizzi IP di calcolo (inclusi gli intervalli SQL) usati dai data center di Azure. Potrebbe essere necessario aggiungere tali indirizzi IP all'elenco degli indirizzi consentiti. Vedere Microsoft Azure [ip del data center.](https://www.microsoft.com/download/details.aspx?id=41653)  
- Vedere la guida introduttiva sulla [creazione di un database singolo in database SQL di Azure](single-database-create-quickstart.md).
- Per informazioni sulla connessione a un database in database SQL di Azure da applicazioni open source o di terze parti, vedere Esempi di codice di avvio rapido client [per](connect-query-content-reference-guide.md#libraries)database SQL di Azure .
- Per informazioni sulle porte aggiuntive che potrebbe essere necessario aprire, vedere la sezione "Database SQL: esterno rispetto all'interno" di Porte oltre [la 1433 per ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md) e database SQL
- Per una panoramica della sicurezza database SQL di Azure sicurezza, vedere [Protezione del database](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
