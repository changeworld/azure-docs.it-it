---
title: Ottenere l'attività dei metadati in Azure Data Factory
description: Informazioni su come usare l'attività Ottieni metadati in una pipeline Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: jingwang
ms.openlocfilehash: bd8fc3383d6d9a0afb7733cb94643623e6879d23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178542"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Ottenere l'attività dei metadati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È possibile utilizzare l'attività Recupera metadati per recuperare i metadati di tutti i dati in Azure Data Factory. È possibile utilizzare l'output dell'attività Ottieni metadati nelle espressioni condizionali per eseguire la convalida oppure utilizzare i metadati nelle attività successive.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'attività Ottieni metadati accetta un set di dati come input e restituisce informazioni sui metadati come output. Attualmente sono supportati i seguenti connettori e i metadati recuperabili corrispondenti. La dimensione massima dei metadati restituiti è **4 MB**.

### <a name="supported-connectors"></a>Connettori supportati

**Archiviazione file**

| Connettore/Metadati | itemName<br>(file/cartella) | itemType<br>(file/cartella) | size<br>(file) | created<br>(file/cartella) | lastModified<sup>1</sup><br>(file/cartella) |childItems<br>(cartella) |contentMD5<br>(file) | struttura<sup>2</sup><br/>(file) | columnCount<sup>2</sup><br>(file) | esistente<sup>3</sup><br>(file/cartella) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Archivio BLOB di Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [File di Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [File system](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> metadati `lastModified` :
- Per Amazon S3 e Google Cloud Storage, `lastModified` si applica al bucket e alla chiave ma non alla cartella virtuale e `exists` si applica al bucket e alla chiave, ma non al prefisso o alla cartella virtuale. 
- Per l'archiviazione BLOB di Azure, `lastModified` si applica al contenitore e al BLOB, ma non alla cartella virtuale.

<sup>2</sup> `structure` i metadati e `columnCount` non sono supportati quando si recuperano metadati da file binari, JSON o XML.

<sup>3</sup> metadati `exists` : per Amazon S3 e Google Cloud Storage, `exists` si applica al bucket e alla chiave, ma non al prefisso o alla cartella virtuale.

Tenere presente quanto segue:

- Quando si usa l'attività Ottieni metadati in una cartella, assicurarsi di disporre dell'autorizzazione di elenco/esecuzione per la cartella specificata.
- Il filtro con caratteri jolly per le cartelle o i file non è supportato per l'attività Ottieni metadati.
- `modifiedDatetimeStart` e `modifiedDatetimeEnd` filtro impostati sul connettore:

    - Queste due proprietà vengono utilizzate per filtrare gli elementi figlio durante il recupero dei metadati da una cartella. Non si applica quando si recuperano i metadati da un file.
    - Quando si usa tale filtro, nell' `childItems` output sono inclusi solo i file modificati nell'intervallo specificato ma non nelle cartelle.
    - Per applicare tale filtro, l'attività GetMetadata enumera tutti i file nella cartella specificata e controlla l'ora modificata. Evitare di puntare a una cartella con un numero elevato di file anche se il numero di file completo previsto è ridotto. 

**Database relazionale**

| Connettore/Metadati | structure | columnCount | esiste |
|:--- |:--- |:--- |:--- |
| [Database SQL di Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Istanza gestita di database SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opzioni dei metadati

È possibile specificare i tipi di metadati seguenti nell'elenco dei campi attività Recupera metadati per recuperare le informazioni corrispondenti:

| Tipo di metadati | Descrizione |
|:--- |:--- |
| itemName | Nome del file o della cartella. |
| itemType | Tipo di file o di cartella. Il valore restituito è `File` o `Folder` . |
| size | Dimensioni del file, in byte. Applicabile solo ai file. |
| created | Data/ora di creazione del file o della cartella. |
| LastModified | Data/ora dell'ultima modifica del file o della cartella. |
| childItems | Elenco di sottocartelle e file nella cartella specificata. Applicabile solo alle cartelle. Il valore restituito è un elenco del nome e del tipo di ogni elemento figlio. |
| contentMD5 | MD5 del file. Applicabile solo ai file. |
| structure | Struttura dei dati del file o della tabella di database relazionale. Il valore restituito è un elenco di nomi di colonna e di colonne. |
| columnCount | Numero di colonne nel file o nella tabella relazionale. |
| esiste| Indica se esiste un file, una cartella o una tabella. Se `exists` viene specificato nell'elenco dei campi Get Metadata, l'attività non avrà esito negativo anche se il file, la cartella o la tabella non esiste. Viene invece `exists: false` restituito nell'output. |

> [!TIP]
> Quando si desidera convalidare l'esistenza di un file, una cartella o una tabella, specificare `exists` nell'elenco dei campi attività Ottieni metadati. È quindi possibile controllare il `exists: true/false` risultato nell'output dell'attività. Se `exists` non è specificato nell'elenco dei campi, l'attività Ottieni metadati avrà esito negativo se l'oggetto non viene trovato.

> [!NOTE]
> Quando si ottengono metadati da archivi di file e si configura `modifiedDatetimeStart` o `modifiedDatetimeEnd` , nell' `childItems` output sono inclusi solo i file nel percorso specificato con l'ora dell'Ultima modifica nell'intervallo specificato. Gli elementi nelle sottocartelle non sono inclusi.

> [!NOTE]
> Affinché l'elenco dei campi della **struttura** fornisca la struttura dei dati effettiva per i set di dati in formato testo delimitato ed Excel, è necessario abilitare la `First Row as Header` proprietà, supportata solo per queste origini dati.

## <a name="syntax"></a>Sintassi

**Attività Ottieni metadati**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Set di dati**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Proprietà del tipo

Attualmente, l'attività Ottieni metadati può restituire i seguenti tipi di informazioni sui metadati:

Proprietà | Descrizione | Obbligatoria
-------- | ----------- | --------
fieldList | Tipi di informazioni sui metadati necessari. Per informazioni dettagliate sui metadati supportati, vedere la sezione opzioni per i [metadati](#metadata-options) di questo articolo. | Sì 
dataset | Set di dati di riferimento i cui metadati devono essere recuperati dall'attività Recupera metadati. Vedere la sezione [funzionalità](#supported-capabilities) per informazioni sui connettori supportati. Per informazioni dettagliate sulla sintassi dei set di dati, vedere gli argomenti del connettore specifici. | Sì
formatSettings | Applicare quando si usa il tipo di formato DataSet. | No
storeSettings | Applicare quando si usa il tipo di formato DataSet. | No

## <a name="sample-output"></a>Output di esempio

I risultati di Get Metadata vengono visualizzati nell'output dell'attività. Di seguito sono riportati due esempi che mostrano opzioni di metadati estese. Per usare i risultati in un'attività successiva, usare questo modello: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Ottenere i metadati di un file

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Ottenere i metadati di una cartella

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle altre attività del flusso di controllo supportate da Data Factory:

- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
