---
title: "Guida introduttiva: connettersi usando l'interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server"
description: Questa Guida introduttiva offre diversi modi per connettersi con l'interfaccia della riga di comando di Azure con database di Azure per PostgreSQL-server flessibile.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f4eec89aadee1966271286b9280916af973e4b1c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614344"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Guida introduttiva: connettersi ed eseguire query con l'interfaccia della riga di comando di Azure con database di Azure per PostgreSQL

> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è attualmente disponibile in anteprima pubblica.

Questa Guida introduttiva illustra come connettersi a un server flessibile di database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure con il ```az postgres flexible-server connect``` comando. Questo comando consente di testare la connettività al server di database ed eseguire query. È anche possibile eseguire più query usando la modalità interattiva. 

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure. Se non è disponibile, [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Installare l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) più recente (2.20.0 o versione successiva)
- Accedere usando l'interfaccia della riga di comando di Azure con ```az login``` comando 
- Attivare la persistenza dei parametri con ```az config param-persist on``` . La persistenza dei parametri consentirà di usare il contesto locale senza dover ripetere una grande quantità di argomenti, ad esempio il gruppo di risorse o la località.

## <a name="create-an-postgresql-flexible-server"></a>Creare un server PostgreSQL flessibile

Il primo componente che verrà creato è un server PostgreSQL gestito. In [Azure cloud Shell](https://shell.azure.com/)eseguire lo script seguente e prendere nota del **nome del server**, del nome **utente** e della  **password** generati da questo comando.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
È possibile specificare altri argomenti per questo comando per personalizzarlo. Vedere tutti gli argomenti per [AZ Postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Visualizza tutti gli argomenti
È possibile visualizzare tutti gli argomenti per questo comando con l' ```--help``` argomento. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testare la connessione al server di database
È possibile verificare e convalidare la connessione al database dall'ambiente di sviluppo tramite il comando.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Esempio:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Se la connessione ha avuto esito positivo, viene visualizzato l'output.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Se la connessione non è riuscita, provare le soluzioni seguenti:
- Controllare se la porta 5432 è aperta nel computer client.
- Se il nome utente e la password dell'amministratore del server sono corretti
- Se è stata configurata la regola del firewall per il computer client
- Se il server è stato configurato con accesso privato in rete virtuale, assicurarsi che il computer client si trovi nella stessa rete virtuale.

## <a name="run-single-query"></a>Esegui query singola
È possibile eseguire una singola query con il comando utilizzando l' ```--querytext``` argomento ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Esempio:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Viene visualizzato un output come illustrato di seguito:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Esempio:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Viene visualizzata l'esperienza shell di **PSQL** , come illustrato di seguito:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il server](./how-to-manage-server-cli.md)
