---
title: Creare un pool SQL dedicato (in precedenza SQL Data Warehouse) con un modello di Azure Resource Manager
description: Informazioni su come creare un pool SQL di Azure Synapse Analytics con un modello di Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460745"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Avvio rapido: Creare un pool SQL dedicato (in precedenza SQL Data Warehouse) di Azure Synapse Analytics con un modello di Azure Resource Manager

Questo modello di Azure Resource Manager creerà un pool SQL dedicato (in precedenza SQL Data Warehouse) con la funzionalità Transparent Data Encryption abilitata. Per pool SQL dedicato (in precedenza SQL Data Warehouse) si intendono le funzionalità di data warehousing aziendali disponibili a livello generale in Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Il modello definisce una risorsa:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Questo modello crea un pool SQL dedicato (in precedenza SQL Data Warehouse).
   
   [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Immettere o aggiornare i valori seguenti:

   * **Sottoscrizione** Selezionare una sottoscrizione di Azure.
   * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e selezionare **OK**. Un nuovo gruppo di risorse faciliterà la pulizia delle risorse.
   * **Area**: Scegliere un'area,  Ad esempio **Stati Uniti centrali**.
   * **Nome SQL Server**: accettare il nome predefinito o immetterne uno nuovo per SQL Server.
   * **Account di accesso amministratore SQL**: immettere il nome utente dell'amministratore di SQL Server.
   * **Password amministratore SQL**: immettere la password dell'amministratore per SQL Server.
   * **Nome data warehouse**: immettere un nome per il pool SQL dedicato.
   * **Transparent Data Encryption**: accettare l'impostazione predefinita, Abilitata. 
   * **Obiettivo del livello di servizio**: accettare l'impostazione predefinita, DW400c.
   * **Località**: accettare la località predefinita del gruppo di risorse.
   * **Rivedi e crea**: selezionare questa casella.
   * **Creazione**: selezionare questa casella.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Per controllare le risorse distribuite, è possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure o lo script di Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure o con Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un pool SQL dedicato (in precedenza SQL Data Warehouse) usando un modello di Azure Resource Manager e ne è stata convalidata la distribuzione. Per altre informazioni su Azure Synapse Analytics e Azure Resource Manager, vedere gli articoli seguenti.

- Leggere una [panoramica di Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Vedere altre informazioni su [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Creare e distribuire il primo modello di Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
