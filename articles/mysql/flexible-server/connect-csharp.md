---
title: 'Guida introduttiva: connettersi con C#-server flessibile per database di Azure per MySQL'
description: Questa Guida introduttiva fornisce un esempio di codice C# (.NET) che è possibile usare per connettersi ed eseguire query sui dati da un server flessibile di database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: c13b8392a766dc29d8c83f9986d0b43271caabbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98605991"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Guida introduttiva: usare .NET (C#) per connettersi ed eseguire query sui dati nel database di Azure per MySQL-server flessibile

Questa guida introduttiva illustra come connettersi a un database di Azure per MySQL usando un'applicazione C#. Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database.

## <a name="prerequisites"></a>Prerequisiti
Per questa guida di avvio rapido, è necessario:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
- Creare un server flessibile per database di Azure per MySQL usando [portale di Azure](./quickstart-create-server-portal.md) <br/> o in alternativa l'[interfaccia della riga di comando di Azure](./quickstart-create-server-cli.md).
- A seconda che si usi l'accesso pubblico o privato, completare **UNA** delle azioni seguenti per abilitare la connettività.
- [Creare un database e un utente non amministratore](../howto-create-users.md)

[Problemi? Segnalarli](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="create-a-c-project"></a>Creare un progetto C#
Al prompt dei comandi eseguire:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Dal menu a sinistra nel portale di Azure scegliere **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Selezionare il nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="Nome del server del database di Azure per MySQL":::

## <a name="step-1-connect-and-insert-data"></a>Passaggio 1: Connettersi e inserire i dati
Usare il codice seguente per connettersi e caricare i dati usando le istruzioni SQL `CREATE TABLE` e `INSERT INTO`. Il codice usa i metodi della classe `MySqlConnection`:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) per stabilire una connessione a MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) per impostare la proprietà CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) per eseguire i comandi di database.

Sostituire i parametri `Server`, `Database`, `UserID` e `Password` con i valori specificati al momento della creazione del server e del database.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Si verificano problemi? Informare l'it.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>Passaggio 2: Leggere i dati

Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL `SELECT`. Il codice usa la classe `MySqlConnection` con i metodi:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) per stabilire una connessione a MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) per impostare la proprietà CommandText.
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync) per eseguire i comandi di database.
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) per passare ai record nei risultati. Il codice usa quindi GetInt32 e GetString per analizzare i valori nel record.


Sostituire i parametri `Server`, `Database`, `UserID` e `Password` con i valori specificati al momento della creazione del server e del database.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Problemi? Segnalarli](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-3-update-data"></a>Passaggio 3: Aggiornare i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL `UPDATE`. Il codice usa la classe `MySqlConnection` con i metodi:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) per stabilire una connessione a MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) per impostare la proprietà CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) per eseguire i comandi di database.


Sostituire i parametri `Server`, `Database`, `UserID` e `Password` con i valori specificati al momento della creazione del server e del database.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="step-4-delete-data"></a>Passaggio 4: Eliminare i dati
Usare il codice seguente per connettersi ed eliminare i dati usando un'istruzione SQL `DELETE`.

Il codice usa la classe `MySqlConnection` con i metodi:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) per stabilire una connessione a MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) per impostare la proprietà CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) per eseguire i comandi di database.


Sostituire i parametri `Server`, `Database`, `UserID` e `Password` con i valori specificati al momento della creazione del server e del database.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire tutte le risorse usate in questo argomento di avvio rapido, eliminare il gruppo di risorse con il comando seguente:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Gestire il server di Database di Azure per MySQL usando il portale](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gestire il server di Database di Azure per MySQL usando l'interfaccia della riga di comando](./how-to-manage-server-cli.md)


