---
title: 'Avvio rapido: Creare un database Azure Cosmos DB e un contenitore usando un modello di Azure Resource Manager'
description: Questo argomento di avvio rapido illustra come creare un database Azure Cosmos DB e un contenitore usando un modello di Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339666"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Avvio rapido: Creare un database Azure Cosmos DB e un contenitore con un modello di Resource Manager
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB è il database NoSQL veloce di Microsoft con API aperte per qualsiasi scala. È possibile usare Azure Cosmos DB per creare rapidamente database di chiavi/valori, di documenti e a grafo ed eseguire query su di essi. Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Azure Resource Manager (modello ARM) per creare un database Azure Cosmos DB e un contenitore al suo interno. Sarà quindi possibile archiviare dati in questo contenitore.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure o un account di prova gratuito di Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Nel modello sono definite tre risorse di Azure:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): crea un account Azure Cosmos DB.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): crea un database Azure Cosmos DB.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): crea un contenitore Azure Cosmos DB.

Altri esempi di modelli di Azure Cosmos DB sono disponibili nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un account, un database e un contenitore Azure Cosmos DB.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuzione in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="Modello di Resource Manager: integrazione di Azure Cosmos DB, portale per la distribuzione":::

    Se non specificati, usare i valori predefiniti per creare le risorse di Azure Cosmos DB.

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    * **Località**: selezionare una località.  Ad esempio **Stati Uniti centrali**.
    * **Nome account**: immettere un nome per l'account Azure Cosmos DB. Deve essere univoco a livello globale.
    * **Località**: immettere una località in cui creare l'account Azure Cosmos DB. Può essere la stessa località in cui si trova il gruppo di risorse.
    * **Area primaria**: area di replica primaria per l'account Azure Cosmos DB.
    * **Area secondaria**: area di replica secondaria per l'account Azure Cosmos DB.
    * **Livello di coerenza predefinito**: livello di coerenza predefinito dell'account Azure Cosmos.
    * **Prefisso di decadimento massimo**: numero massimo di richieste non aggiornate. Obbligatorio per BoundedStaleness.
    * **Intervallo massimo in secondi**: Tempo di ritardo massimo. Obbligatorio per BoundedStaleness.
    * **Nome database**: nome del database Azure Cosmos DB.
    * **Nome contenitore**: nome del contenitore Azure Cosmos DB.
    * **Velocità effettiva**:  velocità effettiva del contenitore. Il valore minimo è 400 UR/sec.
    * **Accetto le condizioni riportate sopra**: selezionare questa casella.

3. Selezionare **Acquisto**. Al termine della distribuzione dell'account Azure Cosmos DB, si riceverà una notifica:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="Modello di Resource Manager: integrazione di Cosmos DB, notifica nel portale per la distribuzione":::

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

È possibile usare il portale di Azure per controllare l'account, il database e il contenitore Azure Cosmos DB oppure usare lo script dell'interfaccia della riga di comando di Azure o di Azure PowerShell seguente per elencare il segreto creato.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'account Azure Cosmos DB e le risorse correlate. Per eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure oppure con Azure PowerShell:

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

In questo argomento di avvio rapido sono stati creati un account, un database e un contenitore Azure Cosmos DB usando un modello di Resource Manager ed è stata convalidata la distribuzione. Per altre informazioni su Azure Cosmos DB e Azure Resource Manager, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Cosmos DB](introduction.md)
- Vedere altre informazioni su [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Ottenere altri [modelli di Azure Resource Manager per Azure Cosmos DB](./templates-samples-sql.md)