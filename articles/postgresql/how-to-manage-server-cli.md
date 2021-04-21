---
title: Gestire il server - Interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL
description: Informazioni su come gestire un server di Database di Azure per PostgreSQL dall'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 36151a9afda0bb23ee244ee778a30f30a41243ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791464"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Gestire un server singolo di Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come gestire i server singoli distribuiti in Azure. Le attività di gestione includono il ridimensionamento delle risorse di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az_login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

Se non è già stato creato un server, fare riferimento a questa [guida introduttiva](quickstart-create-server-database-azure-cli.md) per crearne uno.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

È possibile aumentare facilmente il piano tariffario, il calcolo e l'archiviazione usando il comando seguente. È possibile visualizzare tutte le operazioni del server che è possibile eseguire [az postgres server overview](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Ecco i dettagli per gli argomenti indicati:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server di Database di Azure per PostgreSQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|GP_Gen5_2|Immettere il nome del piano tariffario e della configurazione delle risorse di calcolo. Segue la convenzione {piano tariffario} _{generazione di calcolo}_ {Vcore} in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](./concepts-pricing-tiers.md).
storage-size | 6144 | Capacità di archiviazione del server (l'unità è MB). Minimo 5120 e incrementi di 1024.

> [!Important]
> - Lo spazio di archiviazione può essere ridimensionato(tuttavia, non è possibile ridimensionare lo spazio di archiviazione)
> - La scalabilità verticale dal piano tariffario Basic a Per utilizzo generico o Ottimizzata per la memoria non è supportata. È possibile aumentare manualmente le dimensioni usando  [uno script Bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) o [PostgreSQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>Gestire i database PostgreSQL in un server.
È possibile usare uno di questi comandi per creare, eliminare, elencare e visualizzare le proprietà di un database nel server

| Cmdlet | Utilizzo| Descrizione |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az_mysql_db_create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Crea un database|
|[az postgres db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Eliminare il database dal server. Questo comando non elimina il server. |
|[az postgres db list](/cli/azure/sql/db#az_mysql_db_list)|```az postgres db list -g myresourcegroup -s mydemoserver```|elenca tutti i database nel server|
|[az postgres db show](/cli/azure/sql/db#az_mysql_db_show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra altri dettagli del database|

## <a name="update-admin-password"></a>Aggiornare la password amministratore
È possibile modificare la password del ruolo amministratore con questo comando
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Assicurarsi che la password sia di almeno 8 caratteri e un massimo di 128 caratteri.
> La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.

## <a name="delete-a-server"></a>Eliminazione di un server
Se si vuole eliminare il server singolo PostgreSQL, è possibile eseguire [il comando az postgres server delete.](/cli/azure/mysql/server#az_mysql_server_delete)

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- [Riavviare un server](howto-restart-server-cli.md)
- [Ripristinare un server in uno stato non valido](howto-restore-server-cli.md)
- [Monitorare e ottimizzare il server](concepts-monitoring.md)
