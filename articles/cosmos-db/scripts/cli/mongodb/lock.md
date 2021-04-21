---
title: Creare un blocco di risorse per un database e una raccolta per l'API MongoDB per Azure Cosmos DB
description: Creare un blocco di risorse per un database e una raccolta per l'API MongoDB per Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: ee298b33736ae25b365cc54ee1bb5ec9f38bfb62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763607"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Creare un blocco di risorse per l'API di Azure Cosmos DB per MongoDB usando l'interfaccia della riga di comando di Azure
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.9.1 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

> [!IMPORTANT]
> I blocchi delle risorse non funzionano per le modifiche apportate dagli utenti che si connettono tramite MongoDB SDK, Mongoshell, strumenti o il portale di Azure, a meno che l'account Cosmos DB non venga inizialmente bloccato con la proprietà `disableKeyBasedMetadataWriteAccess` abilitata. Per altre informazioni su come abilitare questa proprietà, vedere [Impedire modifiche dagli SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | Crea un blocco. |
| [az lock list](/cli/azure/lock#az_lock_list) | Elenca le informazioni del blocco. |
| [az lock show](/cli/azure/lock#az_lock_show) | Mostra le proprietà di un blocco. |
| [az lock delete](/cli/azure/lock#az_lock_delete) | Elimina un blocco. |

## <a name="next-steps"></a>Passaggi successivi

- [Bloccare le risorse per impedire modifiche impreviste](../../../../azure-resource-manager/management/lock-resources.md)

- [Documentazione sull'interfaccia della riga di comando di Azure Cosmos DB](/cli/azure/cosmosdb).

- [Repository GitHub per l'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
