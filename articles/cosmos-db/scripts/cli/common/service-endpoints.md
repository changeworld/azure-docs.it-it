---
title: Creare un account Azure Cosmos con endpoint servizio di rete virtuale
description: Creare un account Azure Cosmos con endpoint servizio di rete virtuale
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6aa8da221818f807c29310f0b124b58ae70b853e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770737"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Creare un account Azure Cosmos con endpoint servizio di rete virtuale tramite l'interfaccia della riga di comando di Azure
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.9.1 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

Questo esempio crea una nuova rete virtuale con una subnet front-end e back-end e abilita gli endpoint di servizio per `Microsoft.AzureCosmosDB`. Recupera quindi l'ID risorsa per questa subnet, lo applica all'account Azure Cosmos e quindi abilita gli endpoint di servizio per l'account.

> [!NOTE]
> Questo esempio mostra l'uso di un account API Core (SQL). Per usare questo esempio per altre API, applicare i parametri `enable-virtual-network` e `virtual-network-rules` dello script seguente allo script specifico dell'API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Crea una rete virtuale di Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crea una subnet per una rete virtuale di Azure. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Restituisce una subnet per una rete virtuale di Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crea un account Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure Cosmos DB, vedere la relativa [documentazione](/cli/azure/cosmosdb).

Tutti gli esempi di script dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili nel [repository GitHub dell'interfaccia della riga di comando di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
