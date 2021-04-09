---
title: 'PowerShell: eseguire la migrazione di SQL Server a SQL Istanza gestita online'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione in linea da SQL Server ad Azure SQL Istanza gestita tramite Azure PowerShell e il servizio migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697797"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Eseguire la migrazione di SQL Server a SQL Istanza gestita online con PowerShell & servizio migrazione del database di Azure

In questo articolo viene eseguita la migrazione in linea del database di **Adventureworks2016** ripristinato a un'istanza locale di SQL Server 2005 o versione successiva a una istanza gestita SQL di Azure sql utilizzando Microsoft Azure PowerShell. È possibile eseguire la migrazione dei database da un'istanza di SQL Server a una Istanza gestita SQL usando il `Az.DataMigration` modulo in Microsoft Azure PowerShell.

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Creare un gruppo di risorse.
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione in un'istanza del servizio migrazione del database di Azure.
> * Eseguire la migrazione in linea.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo illustra la procedura per una migrazione in linea, ma è anche possibile eseguire la migrazione [offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione).
* Una copia locale del database **AdventureWorks2016** , disponibile per il download [qui](/sql/samples/adventureworks-install-configure).
* Per abilitare il protocollo TCP/IP che è disabilitato per impostazione predefinita con l'installazione di SQL Server Express. Abilitare il protocollo TCP/IP seguendo l'articolo [Abilitare o disabilitare un protocollo di rete server](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Per configurare [Windows Firewall per l'accesso al motore di database](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Istanza gestita SQL. È possibile creare un Istanza gestita SQL seguendo le istruzioni riportate nell'articolo [creare un istanza gestita ASQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Per scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) versione 3.3 o successiva.
* Un Rete virtuale di Microsoft Azure creato usando il modello di distribuzione Azure Resource Manager, che fornisce il servizio migrazione del database di Azure con connettività da sito a sito ai server di origine locali tramite [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Una valutazione completata del database locale e della migrazione dello schema usando Data Migration Assistant, come descritto nell'articolo [esecuzione di una valutazione della migrazione del SQL Server](/sql/dma/dma-assesssqlonprem).
* Per scaricare e installare il `Az.DataMigration` modulo (versione 0.7.2 o successiva) dal PowerShell Gallery usando il [cmdlet di PowerShell Install-Module](/powershell/module/powershellget/Install-Module).
* Per assicurarsi che le credenziali utilizzate per connettersi all'istanza di SQL Server di origine dispongano dell'autorizzazione [Control Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Per assicurarsi che le credenziali utilizzate per la connessione a SQL Istanza gestita di destinazione dispongano dell'autorizzazione CONTROL DATABASE sui database SQL Istanza gestita di destinazione.

    > [!IMPORTANT]
    > Per le migrazioni online, è necessario avere già configurato le credenziali Azure Active Directory. Per altre informazioni, vedere l'articolo [usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Per creare un gruppo di risorse, usare il [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando.

Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Creare un'istanza di DMS

È possibile creare una nuova istanza del Servizio Migrazione del database di Azure tramite il cmdlet `New-AzDataMigrationService`.
Questo cmdlet si aspetta i parametri obbligatori seguenti:

* *Nome del gruppo di risorse di Azure*. È possibile usare il [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando per creare un gruppo di risorse di Azure, come illustrato in precedenza, e specificarne il nome come parametro.
* *Nome del servizio*. Stringa che corrisponde al nome del servizio univoco desiderato per il servizio migrazione del database di Azure.
* *Località*. Specifica il percorso del servizio. Specificare una località di Azure data center, ad esempio Stati Uniti occidentali o Asia sud-orientale.
* *SKU*. Questo parametro corrisponde al nome Sku DMS. I nomi degli SKU attualmente supportati sono *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Identificatore della subnet virtuale*. È possibile utilizzare il cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) per creare una subnet.

L'esempio seguente crea un servizio denominato *mydms* nel gruppo di risorse *MyDMSResourceGroup* che si trova nell'area *Stati Uniti orientali* usando una rete *virtuale denominata* *MyVNET* e una subnet denominata subnet.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del Servizio Migrazione del database di Azure, creare un progetto di migrazione. Un progetto Servizio Migrazione del database di Azure richiede informazioni di connessione per entrambe le istanze di origine e di destinazione, nonché un elenco dei database che si desidera migrare come parte del progetto.
Definire le stringhe di connessione di connettività di origine e di destinazione.

Lo script seguente definisce i dettagli della connessione di origine SQL Server: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Lo script seguente definisce i dettagli della connessione SQL Istanza gestita di destinazione: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Definire il mapping del database di origine e di destinazione

Fornire i database di cui eseguire la migrazione in questo progetto di migrazione

Tramite lo script seguente viene eseguito il mapping del database di origine al nuovo database corrispondente nella Istanza gestita SQL di destinazione con il nome fornito.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Per più database, aggiungere l'elenco di database allo script precedente usando il formato seguente:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Creare il progetto del Servizio Migrazione del database di Azure

È possibile creare un progetto di servizio migrazione del database di Azure nell'istanza di DMS.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Creazione di un oggetto FileShare di backup

Creare ora un oggetto FileShare che rappresenta la condivisione di rete SMB locale a cui il servizio migrazione del database di Azure può eseguire i backup del database di origine utilizzando il cmdlet New-AzDmsFileShare.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definire l'archiviazione di Azure 

Selezionare il contenitore di archiviazione di Azure da usare per la migrazione: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Configurare Azure Active Directory app

Specificare i dettagli richiesti per Azure Active Directory per una migrazione in linea di SQL Istanza gestita: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Creare e avviare un'attività di migrazione

Successivamente, creare e avviare un'attività del servizio migrazione del database di Azure. Chiamare l'origine e la destinazione usando le variabili ed elencare le tabelle di database di cui eseguire la migrazione: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

Nell'esempio seguente viene creata e avviata un'attività di migrazione in linea: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Per ulteriori informazioni, vedere [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Monitorare la migrazione

Per monitorare la migrazione, eseguire le operazioni seguenti.

### <a name="check-the-status-of-task"></a>Verificare lo stato dell'attività

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Usare il comando seguente per ottenere l'elenco degli errori:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Esecuzione di cutover 

Con una migrazione in linea, viene eseguito un backup completo e un ripristino dei database e quindi il lavoro continua a ripristinare i log delle transazioni archiviati in BackupFileShare.

Quando il database in un Istanza gestita SQL di Azure viene aggiornato con i dati più recenti ed è sincronizzato con il database di origine, è possibile eseguire un cutover.

Nell'esempio seguente viene completato il cutover\migration. Gli utenti richiamano questo comando a propria discrezione.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Eliminazione dell'istanza del servizio migrazione del database di Azure

Al termine della migrazione, è possibile eliminare l'istanza del servizio migrazione del database di Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Risorse aggiuntive

Per informazioni sugli scenari di migrazione aggiuntivi (coppie di origine/destinazione), vedere la [Guida alla migrazione di database](https://datamigration.microsoft.com/)Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio migrazione del database di Azure, vedere l'articolo [che cos'è il servizio migrazione del database di Azure?](./dms-overview.md).