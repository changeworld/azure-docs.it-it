---
title: Ripristino temporato (PITR)
titleSuffix: Azure SQL Managed Instance
description: Ripristinare un database in Istanza gestita di SQL di Azure a un momento precedente.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787167"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Ripristinare un database in Istanza gestita di SQL di Azure a un momento precedente
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Usare il ripristino tempoR (PITR) per creare un database come copia di un altro database da un certo tempo nel passato. Questo articolo descrive come eseguire un ripristino temporato di un database in Istanza gestita di SQL di Azure.

Il ripristino tempormente utile in scenari di ripristino, ad esempio eventi imprevisti causati da errori, dati caricati in modo errato o eliminazione di dati cruciali. È anche possibile usarlo semplicemente per il test o il controllo. I file di backup vengono conservati per 7-35 giorni, a seconda delle impostazioni del database.

Il ripristino tempormente può ripristinare un database:

- da un database esistente.
- da un database eliminato.
- allo stesso Istanza gestita SQL o a un altro Istanza gestita SQL. 

## <a name="limitations"></a>Limitazioni

Il ripristino temporato in SQL Istanza gestita presenta le limitazioni seguenti:

- Quando si esegue il ripristino da un'istanza di SQL Istanza gestita a un'altra, entrambe le istanze devono essere nella stessa sottoscrizione e nella stessa area. Il ripristino tra aree e tra sottoscrizioni non è attualmente supportato.
- Il ripristino temporato di un intero database SQL Istanza gestita non è possibile. Questo articolo illustra solo le operazioni possibili: ripristino temporato di un database ospitato in SQL Istanza gestita.

> [!WARNING]
> Tenere presente le dimensioni di archiviazione dell'istanza di SQL Istanza gestita. A seconda delle dimensioni dei dati da ripristinare, è possibile che lo spazio di archiviazione dell'istanza sia insufficiente. Se non è disponibile spazio sufficiente per i dati ripristinati, usare un approccio diverso.

La tabella seguente illustra gli scenari di ripristino tempor Istanza gestita:

|           |Ripristinare il database esistente nella stessa istanza di SQL Istanza gestita| Ripristinare il database esistente in un altro database SQL Istanza gestita|Ripristinare il database eliminato nello stesso database SQL Istanza gestita|Ripristinare un database eliminato in un altro database SQL Istanza gestita|
|:----------|:----------|:----------|:----------|:----------|
|**Portale di Azure**| Sì|No |Sì|No|
|**Interfaccia della riga di comando di Azure**|Sì |Sì |No|No|
|**PowerShell**| Sì|Sì |Sì|Sì|

## <a name="restore-an-existing-database"></a>Ripristinare un database esistente

Ripristinare un database esistente nello stesso database SQL Istanza gestita usando portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per ripristinare un database in un altro Istanza gestita SQL, usare PowerShell o l'interfaccia della riga di comando di Azure in modo da poter specificare le proprietà per il gruppo di risorse e Istanza gestita SQL di destinazione. Se non si specificano questi parametri, per impostazione predefinita il database verrà ripristinato nel database SQL Istanza gestita esistente. Il portale di Azure attualmente non supporta il ripristino in un'altra istanza di SQL Istanza gestita.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Passare al database SQL Istanza gestita selezionare il database da ripristinare.
3. Selezionare **Ripristina** nella pagina del database:

    ![Ripristinare un database usando il portale di Azure](./media/point-in-time-restore/restore-database-to-mi.png)

4. Nella pagina **Ripristina** selezionare il punto per la data e l'ora in cui si vuole ripristinare il database.
5. Selezionare **Conferma** per ripristinare il database. Questa azione avvia il processo di ripristino, che crea un nuovo database e lo popola con i dati del database originale nel momento specificato. Per altre informazioni sul processo di ripristino, vedere [Tempo di ripristino](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se non è già stato installato Azure PowerShell, vedere [Installare il](/powershell/azure/install-az-ps)modulo Azure PowerShell .

Per ripristinare il database tramite PowerShell, specificare i valori per i parametri nel comando seguente. Eseguire quindi il comando :

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Per ripristinare il database in un altro Istanza gestita SQL, specificare anche i nomi del gruppo di risorse di destinazione e di SQL Istanza gestita:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Per informazioni dettagliate, [vedere Restore-AzSqlInstanceDatabase.](/powershell/module/az.sql/restore-azsqlinstancedatabase)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se l'interfaccia della riga di comando di Azure non è già installata, vedere [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)

Per ripristinare il database usando l'interfaccia della riga di comando di Azure, specificare i valori per i parametri nel comando seguente. Eseguire quindi il comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Per ripristinare il database in un altro Istanza gestita SQL, specificare anche i nomi del gruppo di risorse di destinazione e di SQL Istanza gestita:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Per una spiegazione dettagliata dei parametri disponibili, vedere la documentazione dell'interfaccia della riga di comando per il ripristino di [un database in sql Istanza gestita](/cli/azure/sql/midb#az_sql_midb_restore).

---

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato

Il ripristino di un database eliminato può essere eseguito tramite PowerShell o portale di Azure. Per ripristinare un database eliminato nella stessa istanza, usare il portale di Azure o PowerShell. Per ripristinare un database eliminato in un'altra istanza, usare PowerShell. 

### <a name="portal"></a>Portale 


Per ripristinare un database gestito usando il portale di Azure, aprire la pagina di panoramica di SQL Istanza gestita e selezionare **Database eliminati**. Scegliere un database eliminato da ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino del database dell'Azure SQL eliminato](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Per ripristinare un database nella stessa istanza, aggiornare i valori dei parametri e quindi eseguire il comando powershell seguente: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Per ripristinare il database in un altro Istanza gestita SQL, specificare anche i nomi del gruppo di risorse di destinazione e di SQL Istanza gestita:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Sovrascrivere un database esistente

Per sovrascrivere un database esistente, è necessario:

1. Eliminare il database esistente che si desidera sovrascrivere.
2. Rinominare il database ripristinato tempormente con il nome del database eliminato.

### <a name="drop-the-original-database"></a>Eliminare il database originale

È possibile eliminare il database usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

È anche possibile eliminare il database connettendosi direttamente a SQL Istanza gestita, avviando SQL Server Management Studio (SSMS) e quindi eseguendo il comando Transact-SQL (T-SQL) seguente:

```sql
DROP DATABASE WorldWideImporters;
```

Usare uno dei metodi seguenti per connettersi al database in SQL Istanza gestita:

- [SSMS/Azure Data Studio tramite una macchina virtuale di Azure](./connect-vm-instance-configure.md)
- [Da punto a sito](./point-to-site-p2s-configure.md)
- [Endpoint pubblico](./public-endpoint-configure.md)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'portale di Azure selezionare il database nell'Istanza gestita SQL e quindi **selezionare Elimina**.

   ![Eliminare un database usando il portale di Azure](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare il comando di PowerShell seguente per eliminare un database esistente da un database SQL Istanza gestita:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando seguente dell'interfaccia della riga di comando di Azure per eliminare un database esistente da un database SQL Istanza gestita:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Modificare il nome del nuovo database in modo che corrisponda al nome del database originale

Connettersi direttamente all'istanza di SQL Istanza gestita e avviare SQL Server Management Studio. Eseguire quindi la query Transact-SQL (T-SQL) seguente. La query modificherà il nome del database ripristinato in quello del database eliminato che si intende sovrascrivere.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Usare uno dei metodi seguenti per connettersi al database in SQL Istanza gestita:

- [Macchina virtuale di Azure](./connect-vm-instance-configure.md)
- [Da punto a sito](./point-to-site-p2s-configure.md)
- [Endpoint pubblico](./public-endpoint-configure.md)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [backup automatizzati.](../database/automated-backups-overview.md)
