---
title: Creare un blocco delle risorse per una tabella dell'API Tabella di Azure Cosmos DB
description: Creare un blocco delle risorse per una tabella dell'API Tabella di Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 04f8d541fc534a60550ba77d9775b340571a504f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788949"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Creare un blocco delle risorse per una tabella dell'API Tabella di Azure Cosmos DB con l'interfaccia della riga di comando di Azure
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.9.1 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

> [!IMPORTANT]
> I blocchi delle risorse non funzionano per le modifiche apportate da utenti che si connettono con Cosmos DB Table SDK, Azure Storage Table SDK, strumenti che si connettono mediante chiavi dell'account o il portale di Azure, a meno che l'account Cosmos DB non venga inizialmente bloccato con la proprietà `disableKeyBasedMetadataWriteAccess` abilitata. Per altre informazioni su come abilitare questa proprietà, vedere [Impedire modifiche dagli SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

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
