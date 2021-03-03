---
title: 'Database SQL di Azure: gestire la conservazione dei backup a lungo termine'
description: Informazioni su come archiviare e ripristinare i backup automatici per il database SQL di Azure in archiviazione di Azure (per un massimo di 10 anni) usando il portale di Azure e PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690558"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gestire la conservazione a lungo termine dei backup del database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Con il database SQL di Azure è possibile impostare un criterio di [conservazione dei backup a lungo termine](long-term-retention-overview.md) per conservare automaticamente i backup in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. È quindi possibile ripristinare un database usando questi backup tramite il portale di Azure o PowerShell. I criteri di conservazione a lungo termine sono supportati anche per [istanza gestita SQL di Azure](../managed-instance/long-term-backup-retention-configure.md).

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Le sezioni seguenti illustrano come usare la portale di Azure per impostare i criteri di conservazione a lungo termine, gestire i backup di conservazione a lungo termine disponibili e ripristinare da un backup disponibile.

### <a name="configure-long-term-retention-policies"></a>Configurare i criteri di conservazione a lungo termine

È possibile configurare il database SQL per [conservare i backup automatizzati](long-term-retention-overview.md) per un periodo più lungo rispetto al periodo di conservazione associato al livello di servizio.

1. Nella portale di Azure passare al server e quindi selezionare **backup**. Selezionare la scheda **criteri di conservazione** per modificare le impostazioni di conservazione dei backup.

   ![esperienza dei criteri di conservazione](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Nella scheda criteri di conservazione Selezionare i database per i quali si desidera impostare o modificare i criteri di conservazione dei backup a lungo termine. I database non selezionati non saranno interessati.

   ![Selezionare il database per configurare i criteri di conservazione dei backup](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. Nel riquadro **Configura criteri** specificare il periodo di memorizzazione desiderato per i backup settimanali, mensili o annuali. Scegliere un periodo di memorizzazione di ' 0' per indicare che non è necessario impostare la conservazione dei backup a lungo termine.

   ![riquadro Configura criteri](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Selezionare **applica** per applicare le impostazioni di conservazione scelte a tutti i database selezionati.

> [!IMPORTANT]
> Quando si Abilita un criterio di conservazione dei backup a lungo termine, potrebbe essere necessario attendere fino a 7 giorni prima che il primo backup diventi visibile e disponibile per il ripristino. Per informazioni dettagliate sul backup Cadance di LTR, vedere [conservazione dei backup a lungo termine](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Visualizzare i backup e il ripristino da un backup

Visualizzare i backup conservati per un database specifico con un criterio LTR e ripristinare da tali backup.

1. Nella portale di Azure passare al server e quindi selezionare **backup**. Per visualizzare i backup di LTR disponibili per un database specifico, selezionare **Gestisci** nella colonna backup di LTR disponibili. Verrà visualizzato un riquadro con un elenco dei backup di LTR disponibili per il database selezionato.

   ![esperienza backup disponibili](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. Nel riquadro **backup di LTR disponibili** visualizzato esaminare i backup disponibili. È possibile selezionare un backup da cui eseguire il ripristino o da eliminare.

   ![visualizzare i backup di LTR disponibili](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Per eseguire il ripristino da un backup di LTR disponibile, selezionare il backup da cui si vuole eseguire il ripristino e quindi selezionare **Ripristina**.

   ![ripristino da un backup di LTR disponibile](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Scegliere un nome per il nuovo database, quindi selezionare **Verifica + crea** per esaminare i dettagli del ripristino. Selezionare **Crea** per ripristinare il database dal backup scelto.

   ![configurare i dettagli del ripristino](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Sulla barra degli strumenti selezionare l'icona di notifica per visualizzare lo stato del processo di ripristino.

   ![avanzamento processo di ripristino](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Al termine del processo di ripristino, aprire la pagina **Database SQL** per visualizzare il database appena ripristinato.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per [estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Utilizzo di PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione dei backup a lungo termine, visualizzare i backup in archiviazione di Azure e ripristinare da un backup in archiviazione di Azure.

### <a name="azure-roles-to-manage-long-term-retention"></a>Ruoli di Azure per gestire la conservazione a lungo termine

Per **Get-AzSqlDatabaseLongTermRetentionBackup** e **Restore-AzSqlDatabase** è necessario disporre di uno dei ruoli seguenti:

- Ruolo proprietario sottoscrizione o
- SQL Server ruolo Collaboratore o
- Ruolo personalizzato con le autorizzazioni seguenti:

   Microsoft. SQL/locations/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read

Per **Remove-AzSqlDatabaseLongTermRetentionBackup** è necessario disporre di uno dei ruoli seguenti:

- Ruolo proprietario sottoscrizione o
- Ruolo personalizzato con l'autorizzazione seguente:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Il ruolo Collaboratore SQL Server non dispone dell'autorizzazione per eliminare i backup di LTR.

È possibile concedere le autorizzazioni RBAC di Azure nell'ambito di una *sottoscrizione* o di un *gruppo di risorse* . Tuttavia, per accedere ai backup di LTR che appartengono a un server eliminato, l'autorizzazione deve essere concessa nell'ambito della *sottoscrizione* di tale server.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Creare i criteri di conservazione a lungo termine

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visualizzare i criteri di conservazione a lungo termine

Questo esempio illustra come elencare i criteri di conservazione a lungo termine in un server

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Cancellare i criteri di conservazione a lungo termine

Questo esempio illustra come cancellare i criteri di conservazione a lungo termine da un database

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visualizzare i backup con conservazione a lungo termine

Questo esempio illustra come elencare i backup con conservazione a lungo termine in un server.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo esempio illustra come eliminare un backup con conservazione a lungo termine dall'elenco di backup.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> L'eliminazione di un backup con conservazione a lungo termine non è reversibile. Per eliminare un backup di LTR dopo che il server è stato eliminato, è necessario disporre dell'autorizzazione per l'ambito della sottoscrizione. È possibile configurare le notifiche relative a ogni eliminazione in monitoraggio di Azure filtrando l'operazione "Elimina un backup di conservazione a lungo termine". Il log attività contiene informazioni su chi ha effettuato la richiesta e sul momento in cui è stata effettuata. Per istruzioni dettagliate, vedere [Creare gli avvisi del log attività](../../azure-monitor/alerts/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Eseguire il ripristino dai backup con conservazione a lungo termine

Questo esempio illustra come eseguire il ripristino da un backup con conservazione a lungo termine. Si noti che questa interfaccia non è stata modificata, ma il parametro Resource ID richiede ora l'ID della risorsa di backup LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Per eseguire il ripristino da un backup di LTR dopo che il server o il gruppo di risorse è stato eliminato, è necessario avere l'ambito delle autorizzazioni per la sottoscrizione del server e che la sottoscrizione sia attiva. È inoltre necessario omettere il parametro facoltativo-ResourceGroupName.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente. Vedere [ripristino temporizzato](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Limitazioni
- Quando si esegue il ripristino da un backup di LTR, la proprietà scala lettura è disabilitata. Per abilitare, leggere la scalabilità nel database ripristinato, aggiornare il database dopo che è stato creato.
- È necessario specificare l'obiettivo del livello di servizio di destinazione, quando si esegue il ripristino da un backup di LTR, che è stato creato quando il database si trovava in un pool elastico. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](automated-backups-overview.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](long-term-retention-overview.md)
