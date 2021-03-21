---
title: 'Istanza gestita SQL di Azure: conservazione dei backup a lungo termine'
description: Informazioni su come archiviare e ripristinare i backup automatici in contenitori di archiviazione BLOB di Azure separati per un Istanza gestita SQL di Azure con PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052027"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Gestire la conservazione dei backup a lungo termine Istanza gestita SQL di Azure (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In Istanza gestita SQL di Azure è possibile configurare un criterio di [conservazione dei backup a lungo termine](../database/long-term-retention-overview.md) come funzionalità di anteprima pubblica. In questo modo è possibile mantenere automaticamente i backup del database in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. È quindi possibile ripristinare un database usando questi backup con PowerShell.

   > [!IMPORTANT]
   > Il LTR per le istanze gestite è attualmente disponibile in anteprima pubblica nelle aree pubbliche di Azure. 

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione a lungo termine dei backup, visualizzare i backup nella risorsa di archiviazione di Azure SQL ed eseguire il ripristino da un backup nella risorsa di archiviazione di Azure SQL.


## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Le sezioni seguenti illustrano come usare la portale di Azure per impostare i criteri di conservazione a lungo termine, gestire i backup di conservazione a lungo termine disponibili e ripristinare da un backup disponibile.

### <a name="configure-long-term-retention-policies"></a>Configurare i criteri di conservazione a lungo termine

È possibile configurare SQL Istanza gestita per [conservare i backup automatici](../database/long-term-retention-overview.md) per un periodo più lungo rispetto al periodo di conservazione per il livello di servizio.

1. Nella portale di Azure selezionare l'istanza gestita e quindi fare clic su **backup**. Nella scheda **criteri di conservazione** selezionare i database per i quali si desidera impostare o modificare i criteri di conservazione dei backup a lungo termine. Le modifiche non verranno applicate a nessun database lasciato deselezionato. 

   ![collegamento di gestione backup](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. Nel riquadro **Configura criteri** specificare il periodo di memorizzazione desiderato per i backup settimanali, mensili o annuali. Scegliere un periodo di memorizzazione di ' 0' per indicare che non è necessario impostare la conservazione dei backup a lungo termine.

   ![Configurare criteri](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Al termine, fare clic su **Applica**.

> [!IMPORTANT]
> Quando si Abilita un criterio di conservazione dei backup a lungo termine, potrebbe essere necessario attendere fino a 7 giorni prima che il primo backup diventi visibile e disponibile per il ripristino. Per informazioni dettagliate sul backup Cadance di LTR, vedere [conservazione dei backup a lungo termine](../database/long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Visualizzare i backup e il ripristino da un backup

Visualizzare i backup conservati per un database specifico con un criterio LTR e ripristinare da tali backup.

1. Nella portale di Azure selezionare l'istanza gestita e quindi fare clic su **backup**. Nella scheda **Available backups** (Backup disponibili) selezionare il database per cui si vuole visualizzare i backup disponibili. Fare clic su **Manage**.

   ![Selezionare il database](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Nel riquadro **Gestisci backup** esaminare i backup disponibili.

   ![Visualizzare i backup](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selezionare il backup da cui si desidera eseguire il ripristino, fare clic su **Ripristina**, quindi nella pagina Ripristina specificare il nome del nuovo database. Il backup e l'origine saranno precompilati in questa pagina. 

   ![Selezionare Backup per il ripristino](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![ripristinare](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Fare clic su **Verifica + crea** per esaminare i dettagli del ripristino. Fare quindi clic su **Crea** per ripristinare il database dal backup scelto.

1. Sulla barra degli strumenti fare clic sull'icona di notifica per visualizzare lo stato del processo di ripristino.

   ![avanzamento processo di ripristino](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Al termine del processo di ripristino, aprire la pagina **panoramica istanza gestita** per visualizzare il database appena ripristinato.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per [estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente](../database/recovery-using-backups.md#point-in-time-restore).


## <a name="using-powershell"></a>Utilizzo di PowerShell
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure. Tuttavia, lo sviluppo futuro verrà eseguito nel modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione dei backup a lungo termine, visualizzare i backup in archiviazione di Azure e ripristinare da un backup in archiviazione di Azure.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>Ruoli RBAC di Azure per gestire la conservazione a lungo termine

Per **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** e **Restore-AzSqlInstanceDatabase** è necessario disporre di uno dei ruoli seguenti:

- Ruolo proprietario sottoscrizione o
- Istanza gestita ruolo Collaboratore o
- Ruolo personalizzato con le autorizzazioni seguenti:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Per **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** è necessario disporre di uno dei ruoli seguenti:

- Ruolo proprietario sottoscrizione o
- Ruolo personalizzato con l'autorizzazione seguente:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Il ruolo Collaboratore Istanza gestita non dispone dell'autorizzazione per eliminare i backup di LTR.

È possibile concedere le autorizzazioni RBAC di Azure nell'ambito di una *sottoscrizione* o di un *gruppo di risorse* . Tuttavia, per accedere ai backup di LTR che appartengono a un'istanza eliminata, l'autorizzazione deve essere concessa nell'ambito della *sottoscrizione* di tale istanza.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>Creare i criteri di conservazione a lungo termine

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>Visualizzare i criteri di conservazione a lungo termine

Questo esempio illustra come elencare i criteri di LTR all'interno di un'istanza per un singolo database

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Questo esempio illustra come elencare i criteri di LTR per tutti i database in un'istanza

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>Cancellare i criteri di conservazione a lungo termine

Questo esempio illustra come cancellare i criteri di conservazione a lungo termine da un database

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>Visualizzare i backup con conservazione a lungo termine

Questo esempio illustra come elencare i backup di LTR all'interno di un'istanza di.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo esempio illustra come eliminare un backup con conservazione a lungo termine dall'elenco di backup.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> L'eliminazione di un backup con conservazione a lungo termine non è reversibile. Per eliminare un backup di LTR dopo l'eliminazione dell'istanza, è necessario disporre dell'autorizzazione per l'ambito della sottoscrizione. È possibile configurare le notifiche relative a ogni eliminazione in monitoraggio di Azure filtrando l'operazione "Elimina un backup di conservazione a lungo termine". Il log attività contiene informazioni su chi ha effettuato la richiesta e sul momento in cui è stata effettuata. Per istruzioni dettagliate, vedere [Creare gli avvisi del log attività](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Eseguire il ripristino dai backup con conservazione a lungo termine

Questo esempio illustra come eseguire il ripristino da un backup con conservazione a lungo termine. Si noti che questa interfaccia non è stata modificata, ma il parametro Resource ID richiede ora l'ID della risorsa di backup LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Per eseguire il ripristino da un backup di LTR dopo che l'istanza è stata eliminata, è necessario disporre dell'ambito delle autorizzazioni per la sottoscrizione dell'istanza di e la sottoscrizione deve essere attiva. È inoltre necessario omettere il parametro facoltativo-ResourceGroupName.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente. Vedere [ripristino temporizzato](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](../database/automated-backups-overview.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](../database/long-term-retention-overview.md)
