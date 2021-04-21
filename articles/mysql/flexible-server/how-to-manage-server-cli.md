---
title: Gestire il server - Interfaccia della riga di comando di Azure - Server flessibile di Database di Azure per MySQL
description: Informazioni su come gestire un server flessibile di Database di Azure per MySQL dall'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4ef1408d5f7afc3b78ab021cdd25eedd75110849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776933"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Gestire un server flessibile di Database di Azure per MySQL (anteprima) usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo illustra come gestire il server flessibile (anteprima) distribuito in Azure. Le attività di gestione includono il ridimensionamento delle risorse di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

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

> [!Important]
> Se non è ancora stato creato un server flessibile, crearne uno per iniziare a usare questa guida.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

È possibile aumentare facilmente il livello di calcolo, i vCore e l'archiviazione usando il comando seguente. È possibile visualizzare tutte le operazioni del server che è possibile eseguire [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Ecco i dettagli per gli argomenti indicati:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server di Database di Azure per MySQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|Standard_D4ds_v4|Immettere il nome e le dimensioni del livello di calcolo. Segue la convenzione Standard_{vm size} in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](../concepts-pricing-tiers.md).
storage-size | 6144 | Capacità di archiviazione del server (l'unità è MB). Minimo 5120 e incrementi di 1024.

> [!Important]
> - L'archiviazione può essere ridimensionata(tuttavia, non è possibile ridimensionare lo spazio di archiviazione)


## <a name="manage-mysql-databases-on-a-server"></a>Gestire i database MySQL in un server.
È possibile usare uno di questi comandi per creare, eliminare, elencare e visualizzare le proprietà di un database nel server

| Cmdlet | Utilizzo| Descrizione |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Crea un database|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Eliminare il database dal server. Questo comando non elimina il server. |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|elenca tutti i database nel server|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra altri dettagli del database|

## <a name="update-admin-password"></a>Aggiornare la password amministratore
È possibile modificare la password del ruolo amministratore con questo comando
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Assicurarsi che la password sia di almeno 8 caratteri e un massimo di 128 caratteri.
> La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici.

## <a name="delete-a-server"></a>Eliminazione di un server
Se si vuole semplicemente eliminare il server flessibile MySQL, è possibile eseguire [il comando az mysql flexible-server delete.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come avviare o arrestare un server](how-to-stop-start-server-portal.md)
- [Informazioni su come gestire una rete virtuale](how-to-manage-virtual-network-cli.md)
- [Risolvere i problemi di connessione](how-to-troubleshoot-common-connection-issues.md)
- [Creare e gestire il firewall](how-to-manage-firewall-cli.md)