---
title: Configurare la finestra di manutenzione (anteprima)
description: Informazioni su come impostare l'ora in cui deve essere eseguita la manutenzione pianificata nei database Azure SQL, nei pool elastici e nei database dell'istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 9771c68dda6f457586f27ea45fbc52aa118e8006
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874784"
---
# <a name="configure-maintenance-window-preview"></a>Configurare la finestra di manutenzione (anteprima)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Configurare la [finestra di manutenzione (anteprima)](maintenance-window.md) per un database Azure SQL, un pool elastico o un database Istanza gestita di SQL di Azure durante la creazione delle risorse o in qualsiasi momento dopo la creazione di una risorsa. 

La *finestra di manutenzione* predefinita del sistema è dalle 17:00 alle 8:00 ogni giorno (ora locale dell'area di Azure in cui si trova la risorsa) per evitare interruzioni nelle ore lavorative di punta. Se la *finestra di manutenzione* predefinita del sistema non è il momento migliore, selezionare una delle altre finestre di manutenzione disponibili.

La possibilità di passare a una finestra di manutenzione diversa non è disponibile per ogni livello di servizio o in ogni area. Per informazioni dettagliate sulla disponibilità, vedere [Disponibilità della finestra di manutenzione.](maintenance-window.md#availability)

> [!Important]
> La configurazione della finestra di manutenzione è un'operazione asincrona a esecuzione lunga, simile alla modifica del livello di servizio della Azure SQL risorsa. La risorsa è disponibile durante l'operazione, ad eccezione di una breve riconfigurazione che si verifica alla fine dell'operazione e in genere dura fino a 8 secondi anche in caso di transazioni interrotte a esecuzione lunga. Per ridurre al minimo l'impatto della riconfigurazione, è necessario eseguire l'operazione al di fuori delle ore di punta.

## <a name="configure-maintenance-window-during-database-creation"></a>Configurare la finestra di manutenzione durante la creazione del database 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per configurare la finestra di manutenzione quando si crea un database, un pool elastico o un'istanza gestita, impostare la finestra di **manutenzione** desiderata nella **pagina Impostazioni** aggiuntive. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Impostare la finestra di manutenzione durante la creazione di un database singolo o di un pool elastico

Per informazioni dettagliate sulla creazione di un nuovo database o pool, vedere Creare un database [database SQL di Azure database singolo](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Scheda Crea impostazioni aggiuntive del database":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Impostare la finestra di manutenzione durante la creazione di un'istanza gestita

Per informazioni dettagliate sulla creazione di una nuova istanza gestita, vedere [Creare un'istanza Istanza gestita di SQL di Azure](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Scheda Crea impostazioni aggiuntive dell'istanza gestita":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gli esempi seguenti illustrano come configurare la finestra di manutenzione usando Azure PowerShell. È possibile [installare Azure PowerShell](/powershell/azure/install-az-ps)o usare il Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.


## <a name="discover-available-maintenance-windows"></a>Individuare le finestre di manutenzione disponibili

Quando si imposta la finestra di manutenzione, ogni area ha le proprie opzioni della finestra di manutenzione che corrispondono al fuso orario per l'area in cui si trova il database o il pool. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Individuare le finestre di manutenzione del database SQL e del pool elastico 

Nell'esempio seguente vengono restituite le finestre di manutenzione disponibili per l'area *eastus2* usando il cmdlet [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) Per i database e i pool elastici, impostare `MaintenanceScope` su `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Individuare le finestre di Istanza gestita SQL 

Nell'esempio seguente vengono restituite le finestre di manutenzione disponibili per l'area *eastus2* usando il cmdlet [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) Per le istanze gestite, impostare `MaintenanceScope` su `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Impostare la finestra di manutenzione durante la creazione di un database singolo

L'esempio seguente crea un nuovo database e imposta la finestra di manutenzione usando il cmdlet [New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase) Deve `-MaintenanceConfigurationId` essere impostato su un valore valido per l'area del database. Per ottenere valori validi per l'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Impostare la finestra di manutenzione durante la creazione di un pool elastico

L'esempio seguente crea un nuovo pool elastico e imposta la finestra di manutenzione usando il cmdlet [New-AzSqlElasticPool.](/powershell/module/az.sql/new-azsqlelasticpool) La finestra di manutenzione viene impostata nel pool elastico, quindi tutti i database nel pool hanno la pianificazione della finestra di manutenzione del pool. Deve `-MaintenanceConfigurationId` essere impostato su un valore valido per l'area del pool. Per ottenere valori validi per la propria area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Impostare la finestra di manutenzione durante la creazione di un'istanza gestita

L'esempio seguente crea una nuova istanza gestita e imposta la finestra di manutenzione usando il cmdlet [New-AzSqlInstance.](/powershell/module/az.sql/new-azsqlinstance) La finestra di manutenzione viene impostata nell'istanza, quindi tutti i database nell'istanza hanno la pianificazione della finestra di manutenzione dell'istanza. Per `-MaintenanceConfigurationId` , *MaintenanceConfigName deve* essere un valore valido per l'area dell'istanza. Per ottenere valori validi per la propria area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Gli esempi seguenti illustrano come configurare la finestra di manutenzione usando l'interfaccia della riga di comando di Azure. È possibile installare [l'interfaccia della riga di](/cli/azure/install-azure-cli)comando di Azure o usare il Azure Cloud Shell. 

La configurazione della finestra di manutenzione con l'interfaccia della riga di comando di Azure è disponibile solo per sql Istanza gestita.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/cli](https://shell.azure.com/cli). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="discover-available-maintenance-windows"></a>Individuare le finestre di manutenzione disponibili

Quando si imposta la finestra di manutenzione, ogni area dispone di opzioni della finestra di manutenzione che corrispondono al fuso orario per l'area in cui si trova il database o il pool.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Individuare le finestre di manutenzione del database SQL e del pool elastico

L'esempio seguente restituisce le finestre di manutenzione disponibili per *l'area eastus2* usando [il comando az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) Per i database e i pool elastici, impostare `maintenanceScope` su `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Individuare le finestre di manutenzione Istanza gestita SQL

L'esempio seguente restituisce le finestre di manutenzione disponibili per *l'area eastus2* usando [il comando az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) Per le istanze gestite, impostare `maintenanceScope` su `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Impostare la finestra di manutenzione durante la creazione di un database singolo

L'esempio seguente crea un nuovo database e imposta la finestra di manutenzione usando [il comando az sql db create.](/cli/azure/sql/db#az_sql_db_create) (o `--maint-config-id` ) deve essere impostato su un valore valido per `-m` l'area del database. Per ottenere valori validi per l'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Impostare la finestra di manutenzione durante la creazione di un pool elastico

L'esempio seguente crea un nuovo pool elastico e imposta la finestra di manutenzione usando il cmdlet [az sql elastic-pool create.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) La finestra di manutenzione viene impostata nel pool elastico, quindi tutti i database nel pool hanno la pianificazione della finestra di manutenzione del pool. (o `--maint-config-id` ) deve essere impostato su un valore valido per `-m` l'area del pool. Per ottenere valori validi per l'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Impostare la finestra di manutenzione durante la creazione di un'istanza gestita

L'esempio seguente crea una nuova istanza gestita e imposta la finestra di manutenzione usando [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create). La finestra di manutenzione viene impostata nell'istanza, quindi tutti i database nell'istanza hanno la pianificazione della finestra di manutenzione dell'istanza. *MaintenanceConfigName* deve essere un valore valido per l'area dell'istanza. Per ottenere valori validi per l'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Configurare la finestra di manutenzione per i database esistenti


Quando si applica una selezione della finestra di manutenzione a un database, in alcuni casi può verificarsi una breve riconfigurazione (alcuni secondi) quando Azure applica le modifiche necessarie.

# <a name="portal"></a>[Portale](#tab/azure-portal)

I passaggi seguenti impostano la finestra di manutenzione in un database, un pool elastico o un'istanza gestita esistente usando il portale di Azure:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Impostare la finestra di manutenzione per un database o un pool elastico esistente

1. Passare al database SQL o al pool elastico per cui si vuole impostare la finestra di manutenzione.
1. Nel menu **Impostazioni** selezionare **Manutenzione** e quindi selezionare la finestra di manutenzione desiderata.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Pagina Manutenzione database SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Impostare la finestra di manutenzione per un'istanza gestita esistente

1. Passare all'istanza gestita per cui si vuole impostare la finestra di manutenzione.
1. Nel menu **Impostazioni** selezionare **Manutenzione** e quindi selezionare la finestra di manutenzione desiderata.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Pagina Manutenzione dell'istanza gestita di SQL":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Impostare la finestra di manutenzione per un database esistente

L'esempio seguente imposta la finestra di manutenzione in un database esistente usando il cmdlet [Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) Deve `-MaintenanceConfigurationId` essere impostato su un valore valido per l'area del database. Per ottenere valori validi per la propria area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Impostare la finestra di manutenzione in un pool elastico esistente

L'esempio seguente imposta la finestra di manutenzione in un pool elastico esistente usando il cmdlet [Set-AzSqlElasticPool.](/powershell/module/az.sql/set-azsqlelasticpool) È importante assicurarsi che il valore sia `$maintenanceConfig` un valore valido per l'area del pool.  Per ottenere valori validi per un'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Impostare la finestra di manutenzione in un'istanza gestita esistente

L'esempio seguente imposta la finestra di manutenzione in un'istanza gestita esistente usando il cmdlet [Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance) È importante assicurarsi che il valore sia un valore valido per `$maintenanceConfig` l'area dell'istanza.  Per ottenere valori validi per un'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Gli esempi seguenti illustrano come configurare la finestra di manutenzione usando l'interfaccia della riga di comando di Azure. È possibile installare [l'interfaccia della riga di](/cli/azure/install-azure-cli)comando di Azure o usare il Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Impostare la finestra di manutenzione per un database esistente

L'esempio seguente imposta la finestra di manutenzione in un database esistente usando [il comando az sql db update.](/cli/azure/sql/db#az_sql_db_update) (o `--maint-config-id` ) deve essere impostato su un valore valido per `-m` l'area del database. Per ottenere valori validi per la propria area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Impostare la finestra di manutenzione in un pool elastico esistente

L'esempio seguente imposta la finestra di manutenzione in un pool elastico esistente usando il [comando az sql elastic-pool update.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) È importante assicurarsi che il valore sia `maintenanceConfig` un valore valido per l'area del pool.  Per ottenere valori validi per un'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Impostare la finestra di manutenzione in un'istanza gestita esistente

L'esempio seguente imposta la finestra di manutenzione [usando az sql mi update](/cli/azure/sql/mi#az_sql_mi_update). La finestra di manutenzione viene impostata nell'istanza, quindi tutti i database nell'istanza hanno la pianificazione della finestra di manutenzione dell'istanza. Per `-MaintenanceConfigurationId` , *MaintenanceConfigName* deve essere un valore valido per l'area dell'istanza. Per ottenere valori validi per l'area, vedere [Individuare le finestre di manutenzione disponibili.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Risorse di pulizia

Assicurarsi di eliminare le risorse non necessarie dopo averle completate per evitare addebiti non necessari.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare al database SQL o al pool elastico non più necessario.
1. Nel menu **Panoramica** selezionare l'opzione per eliminare la risorsa.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla finestra di manutenzione, vedere [Finestra di manutenzione (anteprima).](maintenance-window.md)
- Per altre informazioni, vedere Domande [frequenti sulla finestra di manutenzione](maintenance-window-faq.yml).
- Per informazioni sull'ottimizzazione delle prestazioni, vedere Monitoraggio e [ottimizzazione delle prestazioni in database SQL di Azure e Istanza gestita di SQL di Azure](monitor-tune-overview.md).
