---
title: Script dell'interfaccia della riga di comando - Ridimensionare un server - Database di Azure per MariaDB
description: Questo esempio di script dell'interfaccia della riga di comando di Azure consente di ridimensionare un server Database di Azure per MariaDB a un livello di prestazioni diverso dopo l'esecuzione di query sulle metriche.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3a099f1a3ab16c33baa8037e727d26f107a593a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785544"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorare e ridimensionare un server Database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure
Questo esempio di script dell'interfaccia della riga di comando ridimensiona le risorse di calcolo e archiviazione per un singolo database di Azure per il server MariaDB dopo le query sulle metriche. È possibile aumentare o ridurre le prestazioni di calcolo. Le prestazioni di archiviazione possono solo essere aumentate.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio
Aggiornare lo script con l'ID sottoscrizione.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Crea un server MariaDB che ospita i database. |
| [az mariadb server update](/cli/azure/mariadb/server#az_mariadb_server_update) | Aggiorna le proprietà del server MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Elencare il valore metrico per le risorse. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [risorse di calcolo e archiviazione del database di Azure per MariaDB](../concepts-pricing-tiers.md)
- Provare altri script: [Esempi dell'interfaccia della riga di comando di Azure per Database di Azure per MariaDB](../sample-scripts-azure-cli.md)
- Altre informazioni sull'[interfaccia della riga di comando di Azure](/cli/azure)
