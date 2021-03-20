---
title: Script di PowerShell per ottenere le operazioni relative alla chiave e alla stringa di connessione per un account Azure Cosmos DB
description: Esempio di script di Azure PowerShell - Operazioni relative alla chiave dell'account e alla stringa di connessione per un account di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: eb84684201ddf79f2a73fd1bcba1a0f69899bea8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684318"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>Operazioni relative alla chiave dell'account e alla stringa di connessione per un account Azure Cosmos DB con PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Per questo esempio è necessario Azure PowerShell AZ 5.4.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per determinare le versioni installate.
Se è necessario installarlo, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

Eseguire [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) per accedere ad Azure.

## <a name="sample-script"></a>Script di esempio

> [!NOTE]
> Questo esempio mostra l'utilizzo di un account per l'API SQL. Per usare questo esempio per altre API, copiare le proprietà correlate e applicarle allo script specifico dell'API

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

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
| [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | Ottiene la stringa di connessione o la chiave (lettura/scrittura o sola lettura) per un account Cosmos DB. |
| [New-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | Rigenera la chiave specificata per un account Cosmos DB. |
|**Gruppi di risorse di Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).