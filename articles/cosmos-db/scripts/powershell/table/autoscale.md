---
title: Script di PowerShell per creare una tabella con scalabilità automatica nell'API Tabella di Azure Cosmos DB
description: Script di PowerShell per creare una tabella con scalabilità automatica nell'API Tabella di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 023e75549265a0784db2ea49ab9b5af83ca1acd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674661"
---
# <a name="create-a-table-with-autoscale-for-azure-cosmos-db---table-api"></a>Creare una tabella con scalabilità automatica per Azure Cosmos DB: API Tabella
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Per questo esempio è necessario Azure PowerShell AZ 5.4.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per determinare le versioni installate.
Se è necessario installarlo, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

Eseguire [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) per accedere ad Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-autoscale.ps1 "Create a table with autoscale for Table API")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Crea un account Cosmos DB. |
| [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) | Crea o una tabella dell'API Tabella. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).
