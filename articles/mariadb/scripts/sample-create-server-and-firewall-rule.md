---
title: Script dell'interfaccia della riga di comando - Creare un server - Database di Azure per MariaDB
description: Questo script dell'interfaccia della riga di comando di Azure di esempio consente di creare un singolo server Database di Azure per MariaDB e di configurare una regola del firewall a livello di server.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 11/28/2018
ms.openlocfilehash: 1fc02555364beb2288772ae1fbfd0b66a7d96c50
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785686"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creare un server MariaDB e configurare una regola del firewall tramite l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di Azure di esempio consente di creare un singolo server Database di Azure per MariaDB e di configurare una regola del firewall a livello di server. Dopo che lo script è stato eseguito correttamente, il server MariaDB è accessibile da tutti i servizi di Azure e dall'indirizzo IP configurato.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Crea un server MariaDB che ospita i database. |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) | Crea una regola di firewall per consentire l'accesso al server e ai database presenti sul server dall'intervallo di indirizzi IP immesso. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Provare altri script: [Esempi dell'interfaccia della riga di comando di Azure per Database di Azure per MariaDB](../sample-scripts-azure-cli.md)
