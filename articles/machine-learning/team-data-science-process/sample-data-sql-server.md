---
title: Campionare dati in SQL Server in Azure - Processo di data science per i team
description: Campionare dati archiviati in SQL Server in Azure usando SQL o il linguaggio di programmazione Python e quindi spostarli in Azure Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321873"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Dati di esempio in SQL Server in Azure

Questo articolo illustra come campionare dati archiviati in SQL Server su Azure usando SQL o il linguaggio di programmazione Python. Viene inoltre illustrato come spostare i dati campionati in Azure Machine Learning salvandoli in un file, caricandoli in un BLOB di Azure e quindi leggendoli in Azure Machine Learning Studio.

Il campionamento di Python usa la libreria ODBC [pyodbc](https://code.google.com/p/pyodbc/) per connettersi al server SQL in Azure e la libreria [Pandas](https://pandas.pydata.org/) per creare il campionamento.

> [!NOTE]
> Il codice SQL di esempio riportato in questo documento presuppone che i dati si trovino in un server SQL in Azure. In caso contrario, fare riferimento all'articolo [Spostamento dei dati in SQL Server in una macchina virtuale di Azure](move-sql-server-virtual-machine.md) per istruzioni su come spostare i dati in SQL Server su Azure.
> 
> 

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Il campionamento semplifica la comprensione dei dati, l'esplorazione e la progettazione delle funzionalità. Il suo ruolo nel [Processo di analisi scientifica dei dati per i team (TDSP)](./index.yml) consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli di Machine Learning.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Uso di SQL
In questa sezione vengono descritti alcuni metodi di utilizzo di SQL per eseguire il campionamento casuale semplice dei dati all'interno del database. Scegliere il metodo in base alla dimensione dei dati e alla loro distribuzione.

I due elementi seguenti mostrano come usare `newid` in SQL Server per effettuare il campionamento. Il metodo scelto dipende dal modo in cui si desidera che il campione sia (pk_id nel codice di esempio seguente si presuppone una chiave primaria generata automaticamente).

1. Campionamento casuale meno rigoroso

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Campionamento più casuale 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

È possibile usare la clausola Tablesample anche per il campionamento dei dati. Questa opzione può essere un approccio migliore se le dimensioni dei dati sono elevate (presupponendo che i dati in pagine diverse non siano correlati) e che la query venga completata in un tempo ragionevole.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> È possibile esplorare e generare le funzionalità da questi dati campionati archiviandoli in una nuova tabella
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Connessione ad Azure Machine Learning
È possibile usare direttamente le query di esempio riportate sopra nel modulo [Import Data][import-data] (Importazione dati) di Azure Machine Learning per sottocampionare i dati in modo immediato e inserirli in un esperimento di Azure Machine Learning. Di seguito è riportata una schermata relativa all'uso del modulo Reader per leggere i dati campionati:

![lettore sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Utilizzo del linguaggio di programmazione Python
In questa sezione viene mostrato l'uso della [libreria pyodbc](https://code.google.com/p/pyodbc/) per stabilire un collegamento ODBC al database di un server SQL in Python. La stringa di connessione del database è la seguente: (sostituire ServerName, dbname, username e password con la configurazione):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

La libreria [Pandas](https://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati nella programmazione in Python. Il codice seguente legge un campione 0,1% dei dati di una tabella nel database SQL di Azure in un dato Pandas:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

È ora possibile lavorare con i dati campionati nel frame di dati Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Connessione ad Azure Machine Learning
È possibile utilizzare il codice di esempio seguente per salvare i dati ricampionati in un file e caricarli in un BLOB di Azure. I dati del BLOB possono essere letti direttamente in un esperimento di Azure Machine Learning con il modulo [Import Data][import-data] (Importazione dati). Attenersi alla procedura seguente: 

1. Scrivere il frame di dati Pandas in un file locale

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Caricare il file locale nel BLOB di Azure

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Leggere i dati nel BLOB di Azure usando il modulo [Import Data][import-data] (Importazione dati) di Azure Machine Learning come illustrato nella schermata seguente:

![lettore BLOB][2]

## <a name="the-team-data-science-process-in-action-example"></a>Esempio del Processo di analisi scientifica dei dati per i team
Per esaminare un esempio del processo di Data Science per i team usando un set di dati pubblico, vedere processo di analisi [scientifica dei dati per i team in azione: uso di SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data