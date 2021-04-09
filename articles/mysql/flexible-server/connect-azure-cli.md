---
title: "Guida introduttiva: connettersi usando l'interfaccia della riga di comando di Azure-database di Azure per MySQL-server"
description: Questa Guida introduttiva offre diversi modi per connettersi con l'interfaccia della riga di comando di Azure con database di Azure per MySQL-server flessibile.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: b28c4457129985a1d5c47d251873eaa52a253f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607969"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Guida introduttiva: connettersi ed eseguire query con l'interfaccia della riga di comando di Azure con database di Azure per MySQL

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questa Guida introduttiva illustra come connettersi a un server flessibile di database di Azure per MySQL usando l'interfaccia della riga di comando di Azure con il ```az mysql flexible-server connect``` comando. Questo comando consente di testare la connettività al server di database ed eseguire query direttamente sul server.  È anche possibile usare Esegui il comando in modalità interattiva per l'esecuzione di più query.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non è disponibile, [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Installare l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) più recente (2.20.0 o versione successiva)
- Accedere usando l'interfaccia della riga di comando di Azure con ```az login``` comando 
- Attivare la persistenza dei parametri con ```az config param-persist on``` . La persistenza dei parametri consente di usare il contesto locale senza dover ripetere una grande quantità di argomenti, ad esempio il gruppo di risorse o la posizione e così via.

## <a name="create-an-mysql-flexible-server"></a>Creare un server MySQL flessibile

Il primo componente che verrà creato è un server MySQL gestito. In [Azure cloud Shell](https://shell.azure.com/)eseguire lo script seguente e prendere nota del **nome del server**, del nome **utente** e della  **password** generati da questo comando.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

È possibile specificare altri argomenti per questo comando per personalizzarlo. Vedere tutti gli argomenti per [AZ MySQL flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Creazione di un database
Eseguire il comando seguente per creare un database, **newDatabase** se non ne è già stato creato uno.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Visualizza tutti gli argomenti
È possibile visualizzare tutti gli argomenti per questo comando con l' ```--help``` argomento. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testare la connessione al server di database
Eseguire lo script seguente per verificare e convalidare la connessione al database dall'ambiente di sviluppo.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Esempio:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Per la corretta connessione dovrebbe essere visualizzato l'output seguente:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Se la connessione non è riuscita, provare le soluzioni seguenti:
- Controllare se la porta 3306 è aperta nel computer client.
- Se il nome utente e la password dell'amministratore del server sono corretti
- Se è stata configurata la regola del firewall per il computer client
- Se il server è stato configurato con accesso privato in rete virtuale, assicurarsi che il computer client si trovi nella stessa rete virtuale.

## <a name="run-single-query"></a>Esegui query singola
Eseguire il comando seguente per eseguire una singola query usando l' ```--querytext``` argomento ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Esempio:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Viene visualizzato un output come illustrato di seguito:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Eseguire più query usando la modalità interattiva
È possibile eseguire più query usando la modalità **interattiva** . Per abilitare la modalità interattiva, eseguire il comando seguente

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Esempio:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

L'esperienza di **MySQL** Shell viene visualizzata come illustrato di seguito:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il server](./how-to-manage-server-cli.md)

