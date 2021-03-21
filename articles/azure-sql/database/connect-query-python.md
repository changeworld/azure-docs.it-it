---
title: Usare Python per eseguire query su un database
description: Questo argomento illustra come usare Python per creare un programma che si connette a un database nel database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491963"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Avvio rapido: Usare Python per eseguire query su un database

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

In questa guida di avvio rapido viene usato Python per connettersi a un database SQL di Azure, a un'istanza gestita di SQL di Azure o a un database di Synapse SQL e vengono usate le istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Un database in cui verrà eseguita una query.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 e software correlato
    

    |**Azione**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Installare il driver ODBC, SQLCMD e il driver Python per SQL Server|Usare i passaggi **1,2**, **1,3** e **2,1** in [creare app Python usando SQL Server in MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Verrà inoltre installato Install homebrew e Python.       |[Configurare un ambiente per lo sviluppo di pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Configurare un ambiente per lo sviluppo Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Installare Python e altri pacchetti necessari|    |Usare `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Altre informazioni|[Driver Microsoft ODBC in macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Driver Microsoft ODBC in Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Driver Microsoft ODBC in Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Per esplorare ulteriormente Python e il database nel database SQL di Azure, vedere [Librerie del database SQL di Azure per Python](/python/api/overview/azure/sql), il [repository di modelli pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e un [esempio di modelli pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Creare il codice per eseguire query sul database 

1. Nell'editor di testo, creare un nuovo file chiamato *sqltest.py*.  
   
1. Aggiungere il codice seguente. Ottenere le informazioni sulla connessione dalla sezione dei prerequisiti e sostituire \<server>, \<database>, \<username> e \<password> con i valori effettivi.
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi, eseguire il seguente comando:

   ```cmd
   python sqltest.py
   ```

1. Verificare che vengano restituiti i database e le relative regole di confronto, quindi chiudere la finestra di comando.

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database nel database SQL di Azure](design-first-database-tutorial.md)
- [Driver Microsoft Python per SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/?v=17.23h)
