---
title: Script di PowerShell per le operazioni di velocità effettiva (UR/s) per l'API Tabella di Azure Cosmos DB
description: Script di PowerShell per le operazioni di velocità effettiva (UR/s) per l'API Tabella di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 061bd06c0fa54497d8f00db0e5d4a808e949c2d3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679201"
---
# <a name="throughput-rus-operations-with-powershell-for-a-table-for-azure-cosmos-db---table-api"></a>Operazioni di velocità effettiva (UR/s) con PowerShell per una tabella per l'API Tabella di Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Per questo esempio è necessario Azure PowerShell AZ 5.4.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per determinare le versioni installate.
Se è necessario installarlo, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

Eseguire [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) per accedere ad Azure.

## <a name="get-throughput"></a>Misurare la velocità effettiva

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-get.ps1 "Get throughput on a table for Table API")]

## <a name="update-throughput"></a>Aggiornare la velocità effettiva

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

## <a name="migrate-throughput"></a>Eseguire la migrazione della velocità effettiva

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a table for Table API")]

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
| [Get-AzCosmosDBTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | Ottiene il valore della velocità effettiva della tabella dell'API Tabella specificata. |
| [Update-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | Aggiorna il valore della velocità effettiva della tabella dell'API Tabella. |
| [Invoke-AzCosmosDBTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbtablethroughputmigration) | Esegue la migrazione della velocità effettiva di una tabella dell'API Tabella. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).