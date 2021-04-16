---
title: "Guida introduttiva: Connettersi usando l'interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - Server flessibile"
description: Questa guida introduttiva offre diversi modi per connettersi all'interfaccia della riga di comando di Azure con il server flessibile di Database di Azure per PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479237"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Guida introduttiva: Connettersi ed eseguire query con l'interfaccia della riga di comando di Azure con database di Azure per PostgreSQL - Server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è attualmente disponibile in anteprima pubblica.

Questa guida introduttiva illustra come connettersi a un server flessibile di Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure con ```az postgres flexible-server connect``` il comando . Questo comando consente di testare la connettività al server di database ed eseguire query. È anche possibile eseguire più query usando la modalità interattiva. 

## <a name="prerequisites"></a>Prerequisiti
- Un account Azure. Se non è disponibile, [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Installare la [versione più recente dell'interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure (2.20.0 o versione successiva)
- Accedere con l'interfaccia della riga di comando di Azure con ```az login``` il comando 
- Attivare la persistenza dei parametri con ```az config param-persist on``` . La persistenza dei parametri consente di usare il contesto locale senza dover ripetere molti argomenti, ad esempio il gruppo di risorse o la posizione.

## <a name="create-an-postgresql-flexible-server"></a>Creare un server flessibile PostgreSQL

Il primo componente che verrà creato è un server PostgreSQL gestito. In [Azure Cloud Shell](https://shell.azure.com/)eseguire lo script seguente e prendere nota del nome **del server**, del nome **utente** e della  **password** generati da questo comando.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
È possibile specificare argomenti aggiuntivi per questo comando per personalizzarlo. Vedere tutti gli argomenti [per az postgres flexible-server create.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)

## <a name="view-all-the-arguments"></a>Visualizza tutto gli argomenti
È possibile visualizzare tutti gli argomenti per questo comando con ```--help``` l'argomento . 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testare la connessione al server di database
È possibile testare e convalidare la connessione al database dall'ambiente di sviluppo usando il comando .

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Esempio:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Se la connessione ha avuto esito positivo, verrà visualizzato l'output.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Se la connessione non è riuscita, provare queste soluzioni:
- Controllare se la porta 5432 è aperta nel computer client.
- se il nome utente e la password dell'amministratore del server sono corretti
- se è stata configurata la regola del firewall per il computer client
- se il server è stato configurato con accesso privato nella rete virtuale, assicurarsi che il computer client si trova nella stessa rete virtuale.

## <a name="run-single-query"></a>Eseguire una singola query
È possibile eseguire una singola query con il comando usando ```--querytext``` l'argomento ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Esempio:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Verrà visualizzato un output come illustrato di seguito:

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
È possibile eseguire più query usando la **modalità** interattiva . Per abilitare la modalità interattiva, eseguire il comando seguente

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Esempio:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Verrà visualizzata **l'esperienza della** shell psql come illustrato di seguito:

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
