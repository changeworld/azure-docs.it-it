---
title: 'PowerShell: Eseguire la migrazione offline dal database MySQL a Database di Azure per MySQL con servizio Migrazione del database'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione di un database MySQL locale a Database di Azure per MySQL usando Servizio Migrazione del database di Azure tramite script di PowerShell.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819687"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>Eseguire la migrazione offline di MySQL a Database di Azure per MySQL con PowerShell & Servizio Migrazione del database di Azure

In questo articolo si esegue la migrazione di un database MySQL ripristinato in un'istanza locale in Database di Azure per MySQL usando la funzionalità di migrazione offline di Servizio Migrazione del database di Azure tramite Microsoft Azure PowerShell. L'articolo illustra una raccolta di script di PowerShell che possono essere eseguiti in sequenza per eseguire la migrazione offline del database MySQL ad Azure.

> [!NOTE]
> Attualmente non è possibile eseguire la migrazione completa del database usando il modulo Az.DataMigration. Nel frattempo, lo script di PowerShell di esempio viene fornito "così come è" che usa [l'API REST](https://docs.microsoft.com/rest/api/datamigration/tasks/get) del Servizio Migrazione del database e consente di automatizzare la migrazione. Questo script verrà modificato o deprecato, una volta aggiunto il supporto ufficiale nel modulo Az.DataMigration e nell'interfaccia della riga di comando di Azure. 

> [!IMPORTANT]
> Lo scenario di migrazione online "Da MySQL a Database di Azure per MySQL" verrà sostituito con uno scenario di migrazione offline parallelizzato e ad alta prestazioni a partire dal 1° giugno 2021. Per le migrazioni online, è possibile usare questa nuova offerta insieme alla [replica dei dati in ingresso.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) In alternativa, usare strumenti open source come [MyDumper/MyLoader](https://centminmod.com/mydumper.html) con la replica dei dati in ingresso per le migrazioni online. 

L'articolo consente di automatizzare lo scenario in cui i nomi dei database di origine e di destinazione possono essere uguali o diversi e come parte della migrazione è necessario eseguire la migrazione di tutte o alcune delle tabelle nel database di destinazione che hanno lo stesso nome e la stessa struttura di tabella. Anche se gli articoli presuppongono che l'origine sia un'istanza di database MySQL e che la destinazione sia Database di Azure per MySQL, può essere usata per eseguire la migrazione da un Database di Azure per MySQL a un altro semplicemente modificando il nome e le credenziali del server di origine. È supportata anche la migrazione da server MySQL di versione inferiore (v5.6 e versioni successive) a versioni successive.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema del database.
> * Creare un gruppo di risorse.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione in un'istanza del Servizio Migrazione del database di Azure.
> * Configurare il progetto di migrazione per usare la funzionalità di migrazione offline per MySQL.
> * Eseguire la migrazione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* Avere a disposizione un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
* Avere un database MySQL locale con la versione 5.6 o successiva. In caso contrario, scaricare e installare [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 o versione precedente.
* [Creare un'istanza in Database di Azure per MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Per informazioni dettagliate su come connettersi e creare un database usando l'applicazione Workbench, vedere l'articolo Usare [MySQL Workbench](../mysql/connect-workbench.md) per connettersi ed eseguire query sui dati. La versione di Database di Azure per MySQL deve essere uguale o superiore a quella di MySQL locale. Ad esempio, MySQL 5.7 può eseguire la migrazione a Database di Azure per MySQL 5.7 o aggiornato a 8.  
* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](../virtual-network/index.yml) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtualeNet, se si usa ExpressRoute con peering di rete a Microsoft, aggiungere [l'endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) del servizio *Microsoft.Sql* alla subnet in cui verrà effettuato il provisioning del servizio. Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Assicurarsi che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino la porta in uscita 443 di ServiceTag per Archiviazione e AzureMonitor. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Aprire Windows Firewall per consentire alle connessioni dalla rete virtuale Servizio Migrazione del database di Azure accedere al server MySQL di origine, che per impostazione predefinita è la porta TCP 3306.
* Quando si usa un'appliance firewall davanti ai database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire alle connessioni dalla rete virtuale per Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una regola del firewall a livello di [server](../azure-sql/database/firewall-configure.md) o configurare gli [endpoint](../mysql/howto-manage-vnet-using-portal.md) di servizio della rete virtuale per Database di Azure per MySQL di destinazione per consentire alla rete virtuale Servizio Migrazione del database di Azure l'accesso ai database di destinazione.
* L'origine MySQL deve essere in un'Edizione MySQL Community supportata. Per determinare la versione dell'istanza di MySQL, eseguire il comando seguente nell'utilità MySQL o in MySQL Workbench:

    ```
    SELECT @@version;
    ```

* Database di Azure per MySQL supporta solo le tabelle InnoDB. Per convertire le tabelle MyISAM in InnoDB, vedere l'articolo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Conversione di tabelle da MyISAM a InnoDB).
* L'utente deve avere i privilegi per leggere i dati nel database di origine.
* La guida usa PowerShell v7.1 con PSEdition Core che può essere installato in base alla [guida all'installazione](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)
* Scaricare e installare i moduli seguenti dal PowerShell Gallery usando [il cmdlet Install-Module di PowerShell](/powershell/module/powershellget/Install-Module). Assicurarsi di aprire la finestra di comando di PowerShell usando Esegui come amministratore:
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Eseguire la migrazione dello schema del database

Per trasferire tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database di destinazione. Per estrarre lo schema, è possibile usare mysqldump con il parametro `--no-data`. A tale scopo è necessario un computer in grado di connettersi sia al database MySQL di origine che al database di Azure per MySQL di destinazione.

Per esportare lo schema usando mysqldump, eseguire il comando seguente:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Ad esempio:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Per importare lo schema nel database di Azure per MySQL di destinazione, eseguire il comando seguente:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Ad esempio:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Se sono presenti chiavi esterne nello schema, il caricamento parallelo dei dati durante la migrazione verrà gestito dall'attività di migrazione. Non è necessario eliminare le chiavi esterne durante la migrazione dello schema.

Se sono presenti trigger nel database, verrà applicata l'integrità dei dati nella destinazione prima della migrazione completa dei dati dall'origine. È consigliabile disabilitare i trigger in tutte le tabelle nella destinazione durante la migrazione e quindi abilitare i trigger al termine della migrazione.

Eseguire lo script seguente in MySQL Workbench nel database di destinazione per estrarre lo script drop trigger e aggiungere lo script del trigger.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Eseguire la query di drop trigger generata (colonna DropQuery) nel risultato per eliminare i trigger nel database di destinazione. La query di aggiunta trigger può essere salvata, da usare dopo il completamento della migrazione dei dati.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure

Usare il [comando Connect-AzAccount di PowerShell](/powershell/module/az.accounts/connect-azaccount) per accedere alla sottoscrizione di Azure usando PowerShell, come illustrato nell'articolo Accedere [con Azure PowerShell](/powershell/azure/authenticate-azureps).

Lo script seguente imposta la sottoscrizione predefinita per il post-accesso alla sessione di PowerShell e crea una funzione di registrazione helper per i log della console formattati.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

La registrazione del provider di risorse deve essere eseguita in ogni sottoscrizione di Azure una sola volta. Senza la registrazione, non sarà possibile creare un'istanza di **Servizio Migrazione del database di Azure**.

Registrare il provider di risorse usando [il comando Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider) Lo script seguente registra il provider di risorse necessario per **Servizio Migrazione del database di Azure**

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse prima di creare le risorse del Servizio Gestione dei dati.

Creare un gruppo di risorse usando [il comando New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

Nell'esempio seguente viene creato un gruppo di risorse *denominato myResourceGroup* nell'area Stati Uniti occidentali *2* nella sottoscrizione *predefinita mySubscription*.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Creare un'istanza del Servizio Migrazione del database di Azure

È possibile creare una nuova istanza di Servizio Migrazione del database di Azure usando il [comando New-AzDataMigrationService.](/powershell/module/az.datamigration/new-azdatamigrationservice) Questo comando prevede i parametri obbligatori seguenti:
* *Nome del gruppo di risorse di Azure.* È possibile usare [il comando New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) per creare un gruppo di risorse di Azure come illustrato in precedenza e specificarne il nome come parametro.
* *Nome del servizio*. Stringa che corrisponde al nome di servizio univoco desiderato per il Servizio Migrazione del database di Azure 
* *Località*. Specifica il percorso del servizio. Specificare un percorso di data center di Azure, ad esempio Stati Uniti occidentali o Asia sud-orientale
* *Sku*. Questo parametro corrisponde al nome Sku DMS. Il nome dello Sku attualmente *supportato è Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores*, *Premium_4vCores*.
* *Identificatore della subnet virtuale*. È possibile usare [il comando Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) per ottenere le informazioni di una subnet. 

Lo script seguente prevede che la rete virtuale *myVirtualNetwork* esista con una subnet denominata *default* e quindi crea un Servizio Migrazione del database con il nome *myDmService* nel gruppo di risorse creato nel passaggio **3** e nella stessa area.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'Servizio Migrazione del database di Azure, si creerà un progetto di migrazione. Un progetto di migrazione specifica il tipo di migrazione da eseguire.

Lo script seguente crea un progetto di migrazione denominato *myfirstmysqlofflineproject* per la migrazione offline da MySQL a Database di Azure per MySQL nell'istanza del Servizio Migrazione del database creata nel passaggio **4** e nella stessa area.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Creare un oggetto informazioni di connessione del database per le connessioni di origine e di destinazione

Dopo aver creato il progetto di migrazione, verranno create le informazioni di connessione al database. Queste informazioni di connessione verranno usate per connettersi ai server di origine e di destinazione durante il processo di migrazione.

Lo script seguente accetta il nome del server, il nome utente e la password per le istanze di MySQL di origine e di destinazione e crea gli oggetti informazioni di connessione. Lo script richiede all'utente di immettere la password per le istanze di MySQL di origine e di destinazione. Per gli script invisibile all'utente, le credenziali possono essere recuperate da Azure Key Vault. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Estrarre l'elenco dei nomi di tabella dal database di destinazione

L'elenco delle tabelle di database può essere estratto usando un'attività di migrazione e le informazioni di connessione. L'elenco di tabelle verrà estratto sia dal database di origine che dal database di destinazione in modo che sia possibile eseguire il mapping e la convalida appropriate. 

Lo script seguente accetta i nomi dei database di origine e di destinazione e quindi estrae l'elenco di tabelle dai database usando l'attività di migrazione *GetUserTablesMySql.* 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Compilare il mapping delle tabelle in base alla configurazione utente

Durante la configurazione dell'attività di migrazione, verrà creato un mapping tra le tabelle di origine e di destinazione. Il mapping è a livello di nome di tabella, ma si presuppone che la struttura della tabella (numero di colonne, nomi di colonna, tipi di dati e così via) delle tabelle mappate sia esattamente la stessa.

Lo script seguente crea un mapping basato sull'elenco delle tabelle di destinazione e di origine estratto **nel passaggio 7.** Per il caricamento parziale dei dati, l'utente può fornire un elenco di tabelle per filtrare le tabelle. Se non viene specificato alcun input utente, viene eseguito il mapping di tutte le tabelle di destinazione. Lo script controlla anche se nell'origine esiste o meno una tabella con lo stesso nome. Se il nome della tabella non esiste nell'origine, la tabella di destinazione viene ignorata per la migrazione. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Creare e configurare gli input dell'attività di migrazione

Dopo aver creato il mapping della tabella, si creeranno gli input per l'attività di migrazione di tipo *Migrate.MySql.AzureDbForMySql* e si configureranno le proprietà.

Lo script seguente crea l'attività di migrazione e imposta le connessioni, i nomi di database e il mapping delle tabelle.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Configurare i parametri di ottimizzazione delle prestazioni

A seconda del modulo PowerShell, sono disponibili pochi parametri facoltativi, che possono essere ottimizzati in base all'ambiente. Questi parametri possono essere usati per migliorare le prestazioni dell'attività di migrazione. Tutti questi parametri sono facoltativi e il relativo valore predefinito è NULL.

> [!NOTE]
> Le configurazioni delle prestazioni seguenti hanno mostrato una maggiore velocità effettiva durante la migrazione nello SKU Premium.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 secondi
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

Lo script seguente accetta i valori utente dei parametri e imposta i parametri nelle proprietà dell'attività di migrazione.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Creazione ed esecuzione dell'attività di migrazione

Dopo aver configurato l'input per l'attività, l'attività verrà creata ed eseguita nell'agente. Lo script attiva l'esecuzione dell'attività e attende il completamento della migrazione.

Lo script seguente richiama l'attività di migrazione configurata e attende il completamento.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Eliminazione del Servizio Migrazione del database

Lo stesso Servizio Migrazione del database può essere usato per più migrazioni in modo che l'istanza creata possa essere nuovamente usata. Se non si continuerà a usare il Servizio Migrazione del database, è possibile eliminare il servizio usando il [comando Remove-AzDataMigrationService.](/powershell/module/az.datamigration/remove-azdatamigrationservice?)

Lo script seguente elimina l'istanza Servizio Migrazione del database di Azure e i progetti associati.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui problemi noti e sulle limitazioni quando si eseguono migrazioni tramite il Servizio Migrazione del database, vedere l'articolo [Problemi comuni - Servizio Migrazione del database di Azure](./known-issues-troubleshooting-dms.md).
* Per la risoluzione dei problemi di connettività del database di origine durante l'uso del Servizio Gestione dei dati, vedere l'articolo [Problemi di connessione dei database di origine.](./known-issues-troubleshooting-dms-source-connectivity.md)
* Per informazioni su Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](./dms-overview.md).
* Per informazioni sul Database di Azure per MySQL, vedere l'articolo [Database di Azure per MySQL](../mysql/overview.md).
* Per un'esercitazione sull'uso del Servizio Migrazione del database tramite il portale, vedere l'articolo Esercitazione: Eseguire la migrazione di MySQL a Database di [Azure per MySQL offline con Servizio Migrazione del database](./tutorial-mysql-azure-mysql-offline-portal.md)