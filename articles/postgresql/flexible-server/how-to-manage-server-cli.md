---
title: Gestire un server - Interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - Server flessibile
description: Informazioni su come gestire un server flessibile di Database di Azure per PostgreSQL dall'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778500"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Gestire un server flessibile di Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima.

Questo articolo illustra come gestire il server flessibile distribuito in Azure. Le attività di gestione includono il ridimensionamento delle risorse di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0 o successiva in locale. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Accedere al proprio account con il [comando az login.](/cli/azure/reference-index#az_login) 

```azurecli-interactive
az login
```

Selezionare la sottoscrizione usando il [comando az account set.](/cli/azure/account) Prendere nota del valore **id** dell'output **di az login** da usare come valore per l'argomento **subscription** nel comando seguente. Se si hanno più sottoscrizioni, scegliere la sottoscrizione a cui deve essere fatturata la risorsa. Per identificare tutte le sottoscrizioni, usare il [comando az account list.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se non è ancora stato creato un server flessibile, è necessario farlo per seguire questa guida alle procedure.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

È possibile aumentare facilmente il livello di calcolo, i vCore e l'archiviazione usando il comando seguente. Per un elenco di tutte le operazioni del server che è possibile eseguire, vedere la panoramica [di az postgres flexible-server.](/cli/azure/postgres/flexible-server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Di seguito sono riportati i dettagli per gli argomenti nel codice precedente:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere da 3 a 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|Standard_D4ds_v3|Immettere il nome e le dimensioni del livello di calcolo. Il valore segue la *convenzione Standard_{vm size}* in forma abbreviata. Per altre informazioni, vedere i [piani tariffari](../concepts-pricing-tiers.md).
storage-size | 6144 | Immettere la capacità di archiviazione del server in megabyte. Il valore minimo è 5120, con incrementi di 1024.

> [!IMPORTANT]
> Non è possibile ridimensionare lo spazio di archiviazione. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gestire i database PostgreSQL in un server

Sono diverse le applicazioni che è possibile usare per connettere il database di Azure per il server PostgreSQL. Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Si userà ora lo strumento da riga di comando psql per connettersi al server di Database di Azure per PostgreSQL.

1. Eseguire il comando **psql** seguente:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente si connette al database predefinito denominato **postgres** nel **server** PostgreSQL mydemoserver.postgres.database.azure.com le credenziali di accesso. Quando richiesto, immettere il `<server_admin_password>` valore scelto.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Dopo la connessione, lo strumento psql visualizza un prompt **postgres** in cui è possibile immettere i comandi SQL. Verrà visualizzato un avviso nell'output della connessione iniziale se la versione di psql in uso è diversa dalla versione nel server di Database di Azure per PostgreSQL.

   Output di psql esempio:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se il firewall non è configurato per accettare l'indirizzo IP del client, verrà visualizzato l'errore seguente:
   >
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Specificare le opzioni SSL e riprovare."
   >
   > Verificare che l'indirizzo IP del client sia consentito nelle regole del firewall.

2. Creare un database vuoto denominato **postgresdb** digitando il comando seguente al prompt:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Al prompt dei comandi eseguire il comando seguente per cambiare le connessioni al database appena creato **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Digitare  `\q` e selezionare INVIO per uscire da psql.

In questa sezione si è connessi al server di Database di Azure per PostgreSQL tramite psql e è stato creato un database utente vuoto.

## <a name="reset-the-admin-password"></a>Reimpostare la password amministratore

È possibile modificare la password del ruolo amministratore con il comando seguente:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Scegliere una password con un minimo di 8 caratteri e un massimo di 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: 
> - Lettere maiuscole
> - Lettere minuscole
> - Numeri
> - Caratteri non alfanumerici

## <a name="delete-a-server"></a>Eliminazione di un server

Per eliminare il server flessibile di Database di Azure per PostgreSQL, eseguire [il comando az postgres flexible-server delete.](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete)

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti di backup e ripristino](concepts-backup-restore.md)
- [Ottimizzare e monitorare il server](concepts-monitoring.md)