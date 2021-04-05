---
title: Mapping delle colonne del set di dati in Azure Data Factory
description: Informazioni su come eseguire il mapping delle colonne di origine alle colonne di destinazione.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371125"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati di destinazione
> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. 

Il mapping delle colonne può essere utilizzato per specificare come le colonne specificate nella "struttura" della tabella di origine vengono mappate alle colonne specificate nella "struttura" della tabella di sink. La proprietà **columnMapping** è disponibile nella sezione **typeProperties** dell'attività di copia.

Il mapping di colonne supporta gli scenari seguenti:

* Tutte le colonne della struttura del set di dati di origine vengono mappate a tutte le colonne della struttura del set di dati del sink.
* Un sottoinsieme delle colonne nella struttura del set di dati di origine viene mappato a tutte le colonne della struttura del set di dati del sink.

Le seguenti sono condizioni di errore che generano un'eccezione:

* Un numero minore di colonne o più colonne nella "struttura" della tabella di sink rispetto a quanto specificato nel mapping.
* Mapping duplicato.
* Il risultato della query SQL non ha un nome colonna specificato nel mapping.

> [!NOTE]
> Gli esempi che seguono sono per SQL Azure e Azure Blob, ma sono applicabili per qualsiasi archivio dati che supporti set di dati rettangolari. Modificare i set di dati e le definizioni dei servizi collegati negli esempi per puntare a dati nell'origine dati pertinente.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Esempio 1: mapping di colonne da Azure SQL al BLOB di Azure
In questo esempio, la tabella di input ha una struttura che punta a una tabella SQL nel database SQL di Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

In questo esempio, la tabella di output ha una struttura che punta a un BLOB in un'archiviazione BLOB di Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Il codice JSON seguente definisce un'attività di copia in una pipeline. Le colonne dell'origine vengono mappate alle colonne del sink (**columnMappings**) usando la proprietà **Translator**.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Flusso di mapping colonne:**

![Flusso del mapping di colonne](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Esempio 2: mapping di colonne con query SQL da Azure SQL al BLOB di Azure
In questo esempio viene usata una query SQL per estrarre i dati da Azure SQL invece di specificare semplicemente il nome della tabella e i nomi delle colonne nella sezione "Structure". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
In questo caso, i risultati della query vengono prima mappati alle colonne specificate nella "struttura" di origine. Successivamente, viene eseguito il mapping delle colonne dell'origine "Structure" alle colonne nel sink "Structure" con le regole specificate in columnMappings.  Si supponga che la query restituisca 5 colonne, altre due colonne rispetto a quelle specificate nella "struttura" di origine.

**Flusso del mapping di colonne**

![Flusso del mapping di colonne-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo per un'esercitazione sull'uso dell'attività di copia: 

- [Copiare dati da un archivio BLOB a un database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
