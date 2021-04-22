---
title: Usare Visual Studio con .NET e C# per eseguire query
description: Usare Visual Studio per creare un'app C# che si connette a un database nel database SQL di Azure o nell'istanza gestita di SQL di Azure ed esegue query.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1fe39e0ff9acc0c092199ab2dd199cf396f67d01
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874766"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Avvio rapido: Usare .NET e C# in Visual Studio per connettersi a un database ed eseguire query
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Questa guida di avvio rapido illustra come usare il codice [.NET Framework](https://dotnet.microsoft.com) e C# in Visual Studio per eseguire query su un database in Azure SQL o Synapse SQL con istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional o Enterprise Edition.
- Un database in cui sia possibile eseguire una query.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Creare il codice per eseguire query sul database nel database SQL di Azure

1. In Visual Studio creare un nuovo progetto. 
   
1. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#** , **App console (.NET Framework)** .
   
1. Immettere *sqltest* come nome di progetto e quindi selezionare **OK**. Viene creato il nuovo progetto. 
   
1. Selezionare **Progetto** > **Gestisci pacchetti NuGet**. 
   
1. In **Gestisci pacchetti NuGet**, selezionare la scheda **Sfoglia**, quindi cercare e selezionare **Microsoft.Data.SqlClient**.
   
1. Nella pagina **Microsoft.Data.SqlClient** selezionare **Installa**. 
   - Se richiesto, selezionare **OK** per continuare l'installazione. 
   - Se viene visualizzata una finestra **Accettazione della licenza** selezionare **Accetto**.
   
1. Al termine dell'installazione, è possibile chiudere **Gestisci pacchetti NuGet**. 
   
1. Nell'editor di codice, sostituire i contenuti **Program.cs** con il codice seguente. Sostituire con i valori per `<your_server>`, `<your_username>`, `<your_password>` e `<your_database>`.
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Eseguire il codice

1. Per eseguire l'app, selezionare **Debug** > **Avvia debug**, o selezionare **Avvia** sulla barra degli strumenti oppure premere **F5**.
1. Verificare che vengano restituiti i nomi dei database e le regole di confronto, quindi chiudere la finestra dell'app.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [connettersi ed eseguire query su un database nel database SQL di Azure usando .NET Core](connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Informazioni su come [iniziare a usare .NET Core in Windows/Linux/macOS con la riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [progettare il primo database nel database SQL di Azure con SSMS](design-first-database-tutorial.md) o su come [progettare il primo database nel database SQL di Azure con .NET](design-first-database-csharp-tutorial.md).
- Per altre informazioni su .NET, vedere la [documentazione di .NET](/dotnet/).
- Esempio di logica di ripetizione dei tentativi: [Connettersi in modo resiliente ad Azure SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
