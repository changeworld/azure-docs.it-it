---
title: Usare .NET Core per connettersi a un database ed eseguire query
description: Questo argomento illustra come usare .NET Core per creare un programma che si connette a un database nel database SQL di Azure o nell'istanza gestita di SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 221b69d428556b031efd3bd91e16d12cfeb71393
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874802"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Avvio rapido: Usare .NET Core (C#) per eseguire query su un database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

In questa guida di avvio rapido si userà [.NET Core](https://dotnet.microsoft.com) e il codice C# per la connessione a un database. Si eseguirà quindi un'istruzione Transact-SQL per eseguire query sui dati.

> [!TIP]
> Il modulo di Microsoft Learn gratuito seguente contiene informazioni su come [sviluppare e configurare un'applicazione ASP.NET che esegue query su un database nel database SQL di Azure](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [.NET Core SDK per il sistema operativo](https://dotnet.microsoft.com/download) installato.
- Un database in cui sia possibile eseguire la query. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Creare un nuovo progetto .NET Core

1. Aprire un prompt dei comandi e creare una cartella denominata **sqltest**. Passare a questa cartella ed eseguire questo comando.

    ```cmd
    dotnet new console
    ```

    Vengono creati nuovi file di progetto dell'app, inclusi un iniziale file di codice C# (**Program.cs**), un file di configurazione XML (**sqltest. csproj**) e i file binari necessari.

2. In un editor di testo, aprire **sqltest. csproj** e incollare il codice XML seguente tra i tag `<Project>`. In questo modo viene aggiunto `System.Data.SqlClient` come dipendenza.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Inserire il codice per eseguire query sul database nel database SQL di Azure

1. Aprire **Program.cs** in un editor di testo.

2. Sostituire il contenuto con il codice seguente e aggiungere i valori appropriati per il server, il database, il nome utente e la password.

> [!NOTE]
> Per usare una stringa di connessione ADO.NET, sostituire le 4 righe di codice impostando server, database, nome utente e password con la riga seguente. Immettere il proprio nome utente e la password.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt eseguire i seguenti comandi.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Verificare che le righe vengano restituite.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Scegliere **INVIO** per chiudere la finestra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione all'uso di .NET Core su Windows/Linux/macOS dalla riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [connettersi ed eseguire query su un database SQL di Azure o un'istanza gestita di SQL di Azure usando .NET Framework e Visual Studio](connect-query-dotnet-visual-studio.md).  
- Informazioni su come [Progettare il primo database con SSMS](design-first-database-tutorial.md) o su come [Progettare un database e connettersi con C# e ADO.NET](design-first-database-csharp-tutorial.md).
- Per altre informazioni su .NET, vedere la [documentazione di .NET](/dotnet/).
