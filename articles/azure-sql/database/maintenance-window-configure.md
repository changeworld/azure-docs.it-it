---
title: Configura finestra di manutenzione (anteprima)
description: Informazioni su come impostare il momento in cui deve essere eseguita la manutenzione pianificata nei database SQL di Azure, i pool elastici e i database di istanze gestite.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: 210f0c52a2b27492bfa2181473043df3537157d2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183200"
---
# <a name="configure-maintenance-window-preview"></a>Configura finestra di manutenzione (anteprima)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Configurare la [finestra di manutenzione (anteprima)](maintenance-window.md) per un database SQL di Azure, un pool elastico o un database di azure SQL istanza gestita durante la creazione delle risorse o in qualsiasi momento dopo la creazione di una risorsa. 

La finestra di manutenzione *predefinita del sistema* è la 17 alle 8 al giorno (ora locale dell'area di Azure in cui si trova la risorsa) per evitare interruzioni massime dell'orario di ufficio. Se la finestra di manutenzione *predefinita del sistema* non è il momento migliore, selezionare una delle altre finestre di manutenzione disponibili.

La possibilità di passare a una finestra di manutenzione diversa non è disponibile per ogni livello di servizio o in ogni area. Per informazioni dettagliate sulla disponibilità, vedere [disponibilità della finestra di manutenzione](maintenance-window.md#availability).

> [!Important]
> La configurazione della finestra di manutenzione è un'operazione asincrona a esecuzione prolungata, simile alla modifica del livello di servizio della risorsa SQL di Azure. La risorsa è disponibile durante l'operazione, ad eccezione di un breve failover che si verifica alla fine dell'operazione e in genere dura fino a 8 secondi anche in caso di transazioni a esecuzione prolungata interrotte. Per ridurre al minimo l'effetto del failover, è necessario eseguire l'operazione al di fuori delle ore di punta.

## <a name="configure-maintenance-window-during-database-creation"></a>Configura finestra di manutenzione durante la creazione del database 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per configurare la finestra di manutenzione quando si crea un database, un pool elastico o un'istanza gestita, impostare la **finestra di manutenzione** desiderata nella pagina **Impostazioni aggiuntive** . 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Impostare la finestra di manutenzione durante la creazione di un singolo database o di un pool elastico

Per informazioni dettagliate sulla creazione di un nuovo database o pool, vedere [creare un database singolo di database SQL di Azure](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Scheda Crea impostazioni aggiuntive del database":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Impostare la finestra di manutenzione durante la creazione di un'istanza gestita

Per informazioni dettagliate sulla creazione di una nuova istanza gestita, vedere [creare un istanza gestita SQL di Azure](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Scheda Crea impostazioni aggiuntive istanza gestita":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Negli esempi seguenti viene illustrato come configurare la finestra di manutenzione utilizzando Azure PowerShell. È possibile [installare Azure PowerShell](/powershell/azure/install-az-ps)o usare il Azure cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.


## <a name="discover-available-maintenance-windows"></a>Individuare le finestre di manutenzione disponibili

Quando si imposta la finestra di manutenzione, ogni area ha le proprie opzioni della finestra di manutenzione che corrispondono al fuso orario per l'area in cui si trova il database o il pool. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Individuare le finestre di manutenzione del database SQL e del pool elastico 

Nell'esempio seguente vengono restituite le finestre di manutenzione disponibili per l'area *eastus2* utilizzando il cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Per i database e i pool elastici, impostare `MaintenanceScope` su `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Individuare le finestre di manutenzione di SQL Istanza gestita 

Nell'esempio seguente vengono restituite le finestre di manutenzione disponibili per l'area *eastus2* utilizzando il cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Per le istanze gestite, impostare `MaintenanceScope` su `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Impostare la finestra di manutenzione durante la creazione di un database singolo

Nell'esempio seguente viene creato un nuovo database e viene impostata la finestra di manutenzione utilizzando il cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) . L'oggetto `-MaintenanceConfigurationId` deve essere impostato su un valore valido per l'area del database. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).


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

L'esempio seguente crea un nuovo pool elastico e imposta la finestra di manutenzione usando il cmdlet [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) . La finestra di manutenzione è impostata sul pool elastico, quindi tutti i database nel pool hanno la pianificazione della finestra di manutenzione del pool. L'oggetto `-MaintenanceConfigurationId` deve essere impostato su un valore valido per l'area del pool. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).


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

Nell'esempio seguente viene creata una nuova istanza gestita e viene impostata la finestra di manutenzione utilizzando il cmdlet [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) . La finestra di manutenzione è impostata sull'istanza di, pertanto tutti i database nell'istanza di hanno la pianificazione della finestra di manutenzione dell'istanza. Per `-MaintenanceConfigurationId` , *MaintenanceConfigName* deve essere un valore valido per l'area dell'istanza. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Gli esempi seguenti illustrano come configurare la finestra di manutenzione usando l'interfaccia della riga di comando di Azure. È possibile [installare l'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure o usare la Azure cloud Shell. 

La configurazione della finestra di manutenzione con l'interfaccia della riga di comando di Azure è disponibile solo per SQL Istanza gestita.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/cli](https://shell.azure.com/cli). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="discover-available-maintenance-windows"></a>Individuare le finestre di manutenzione disponibili

Quando si imposta la finestra di manutenzione, ogni area ha le proprie opzioni della finestra di manutenzione che corrispondono al fuso orario per l'area in cui si trova il database o il pool.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Individuare le finestre di manutenzione del database SQL e del pool elastico

Nell'esempio seguente vengono restituite le finestre di manutenzione disponibili per l'area *eastus2* usando il comando [AZ Maintenance public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Per i database e i pool elastici, impostare `maintenanceScope` su `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Individuare le finestre di manutenzione di SQL Istanza gestita

Nell'esempio seguente vengono restituite le finestre di manutenzione disponibili per l'area *eastus2* usando il comando [AZ Maintenance public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Per le istanze gestite, impostare `maintenanceScope` su `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Impostare la finestra di manutenzione durante la creazione di un database singolo

Nell'esempio seguente viene creato un nuovo database e viene impostata la finestra di manutenzione tramite il comando [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) . `--maint-config-id`(O `-m` ) deve essere impostato su un valore valido per l'area del database. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).


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

L'esempio seguente crea un nuovo pool elastico e imposta la finestra di manutenzione usando il cmdlet [AZ SQL Elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) . La finestra di manutenzione è impostata sul pool elastico, quindi tutti i database nel pool hanno la pianificazione della finestra di manutenzione del pool. `--maint-config-id`(O `-m` ) deve essere impostato su un valore valido per l'area del pool. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).


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

Nell'esempio seguente viene creata una nuova istanza gestita e viene impostata la finestra di manutenzione con [AZ SQL mi create](/cli/azure/sql/mi#az_sql_mi_create). La finestra di manutenzione è impostata sull'istanza di, pertanto tutti i database nell'istanza di hanno la pianificazione della finestra di manutenzione dell'istanza. *MaintenanceConfigName* deve essere un valore valido per l'area dell'istanza. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Configura la finestra di manutenzione per i database esistenti


Quando si applica una selezione della finestra di manutenzione a un database, è possibile che in alcuni casi venga rilevato un breve failover (alcuni secondi), in quanto Azure applica le modifiche necessarie.

# <a name="portal"></a>[Portale](#tab/azure-portal)

La procedura seguente imposta la finestra di manutenzione su un database esistente, un pool elastico o un'istanza gestita usando il portale di Azure:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Impostare la finestra di manutenzione per un database o un pool elastico esistente

1. Passare al database SQL o al pool elastico per cui si vuole impostare la finestra di manutenzione.
1. Nel menu **Impostazioni** selezionare **manutenzione**, quindi selezionare la finestra di manutenzione desiderata.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Pagina manutenzione database SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Impostare la finestra di manutenzione per un'istanza gestita esistente

1. Passare all'istanza gestita per la quale si desidera impostare la finestra di manutenzione.
1. Nel menu **Impostazioni** selezionare **manutenzione**, quindi selezionare la finestra di manutenzione desiderata.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Pagina di manutenzione dell'istanza gestita di SQL":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Impostare la finestra di manutenzione per un database esistente

Nell'esempio seguente viene impostata la finestra di manutenzione di un database esistente usando il cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . L'oggetto `-MaintenanceConfigurationId` deve essere impostato su un valore valido per l'area del database. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).

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

Nell'esempio seguente viene impostata la finestra di manutenzione in un pool elastico esistente usando il cmdlet [set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) . È importante assicurarsi che il `$maintenanceConfig` valore sia un valore valido per l'area del pool.  Per ottenere valori validi per un'area, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).

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

Nell'esempio seguente viene impostata la finestra di manutenzione in un'istanza gestita esistente usando il cmdlet [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) . È importante assicurarsi che il `$maintenanceConfig` valore deve essere un valore valido per l'area dell'istanza.  Per ottenere valori validi per un'area, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Gli esempi seguenti illustrano come configurare la finestra di manutenzione usando l'interfaccia della riga di comando di Azure. È possibile [installare l'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure o usare la Azure cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Impostare la finestra di manutenzione per un database esistente

Nell'esempio seguente viene impostata la finestra di manutenzione di un database esistente utilizzando il comando [AZ SQL DB Update](/cli/azure/sql/db#az_sql_db_update) . `--maint-config-id`(O `-m` ) deve essere impostato su un valore valido per l'area del database. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).

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

L'esempio seguente imposta la finestra di manutenzione in un pool elastico esistente usando il comando [AZ SQL Elastic-Pool Update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) . È importante assicurarsi che il `maintenanceConfig` valore sia un valore valido per l'area del pool.  Per ottenere valori validi per un'area, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).

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

Nell'esempio seguente viene impostata la finestra di manutenzione con [AZ SQL mi Update](/cli/azure/sql/mi#az_sql_mi_update). La finestra di manutenzione è impostata sull'istanza di, pertanto tutti i database nell'istanza di hanno la pianificazione della finestra di manutenzione dell'istanza. Per `-MaintenanceConfigurationId` , *MaintenanceConfigName* deve essere un valore valido per l'area dell'istanza. Per ottenere valori validi per l'area geografica, vedere [individuare le finestre di manutenzione disponibili](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Risorse di pulizia

Assicurarsi di eliminare le risorse non necessarie dopo averle finite per evitare addebiti superflui.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare al database SQL o al pool elastico che non è più necessario.
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

- Per altre informazioni sulla finestra di manutenzione, vedere [finestra di manutenzione (anteprima)](maintenance-window.md).
- Per altre informazioni, vedere [domande frequenti sulla finestra di manutenzione](maintenance-window-faq.yml).
- Per informazioni sull'ottimizzazione delle prestazioni, vedere [monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure e istanza gestita SQL di Azure](monitor-tune-overview.md).
