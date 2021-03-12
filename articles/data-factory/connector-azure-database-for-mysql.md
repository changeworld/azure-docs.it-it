---
title: Copiare e trasformare i dati in database di Azure per MySQL
description: ottenere informazioni su come copiare e trasformare i dati in database di Azure per MySQL usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012607"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Copiare e trasformare i dati in database di Azure per MySQL usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in database di Azure per MySQL e usare il flusso di dati per trasformare i dati in database di Azure per MySQL. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

Questo connettore è specializzato per il [servizio database di Azure per MySQL](../mysql/overview.md). Per copiare dati da un database MySQL generico situato in locale o nel cloud, usare il [connettore MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database di Azure per MySQL è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Guida introduttiva

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di data factory specifiche in un connettore del database Azure per MySQL.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato del database di Azure per MySQL sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureMySql** | Sì |
| connectionString | Specifica le informazioni necessarie per connettersi all'istanza del database di Azure per MySQL. <br/> È anche possibile inserire la password in Azure Key Vault ed eseguire lo spostamento forzato dei dati della configurazione `password` all'esterno della stringa di connessione. Vedere gli esempi seguenti e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Una stringa di connessione tipica è `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Altre proprietà che è possibile impostare per il case:

| Proprietà | Descrizione | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |
| SSLMode | Questa opzione specifica se il driver usa la crittografia TLS e verifica la connessione a MySQL. ad esempio `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(impostazione predefinita)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Questa opzione specifica se usare o meno un certificato CA dall'archivio di attendibilità di sistema o da un file PEM specificato. ad esempio `UseSystemTrustStore=<0/1>;`| Abilitato (1) / Disabilitato (0) **(impostazione predefinita)** | No |

**Esempio:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la password in Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati del database di Azure per MySQL.

Per copiare dati dal database di Azure per MySQL, impostare la proprietà type del set di dati su **AzureMySqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureMySqlTable** | Sì |
| tableName | Nome della tabella nel database MySQL. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database di Azure per MySQL.

### <a name="azure-database-for-mysql-as-source"></a>Database di Azure per MySQL come origine

Per copiare dati da database di Azure per MySQL, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **AzureMySqlSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |
| queryCommandTimeout | Tempo di attesa prima del timeout della richiesta di query. Il valore predefinito è 120 minuti (02:00:00) | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Database di Azure per MySQL come sink

Per copiare dati in database di Azure per MySQL, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su: **AzureMySqlSink** | Sì |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database di Azure per MySQL in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |
| writeBatchSize | Inserisce i dati nella tabella di database di Azure per MySQL quando la dimensione del buffer raggiunge writeBatchSize.<br>Il valore consentito è Integer che rappresenta il numero di righe. | No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br>I valori consentiti sono un intervallo di tempo. Ad esempio "00:30:00" (30 minuti). | No (il valore predefinito è 00:00:30) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere in tabelle da database di Azure per MySQL. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping. È possibile scegliere di usare un set di dati di database di Azure per MySQL o un [set di dati inline](data-flow-source.md#inline-datasets) come tipo di origine e sink.

### <a name="source-transformation"></a>Trasformazione origine

La tabella seguente elenca le proprietà supportate dall'origine del database di Azure per MySQL. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabella | Se si seleziona tabella come input, il flusso di dati recupera tutti i dati dalla tabella specificata nel set di dati. | No | - |*(solo per set di dati inline)*<br>tableName |
| Query | Se si seleziona query come input, specificare una query SQL per recuperare i dati dall'origine, che esegue l'override di qualsiasi tabella specificata nel set di dati. L'uso delle query è un ottimo modo per ridurre le righe per il test o le ricerche.<br><br>La clausola **Order by** non è supportata, ma è possibile impostare un'istruzione SELECT from completa. È possibile usare anche funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella che è possibile utilizzare nel flusso di dati.<br>Esempio di query: `select * from mytable where customerId > 1000 and customerId < 2000` o `select * from "MyTable"` .| No | string | query |
| Dimensioni dei batch | Specificare le dimensioni del batch per suddividere i dati di grandi dimensioni in batch. | No | Integer | batchSize |
| Livello di isolamento | Scegliere uno dei seguenti livelli di isolamento:<br>-Read Committed<br>-Read uncommitted (impostazione predefinita)<br>-Lettura ripetibile<br>-Serializzabile<br>-None (ignora il livello di isolamento) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NESSUNO</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Esempio di script di origine del database di Azure per MySQL

Quando si usa database di Azure per MySQL come tipo di origine, lo script del flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Trasformazione sink

La tabella seguente elenca le proprietà supportate dal sink del database di Azure per MySQL. È possibile modificare queste proprietà nella scheda **Opzioni sink** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update (metodo) | Specificare le operazioni consentite per la destinazione del database. Per impostazione predefinita, sono consentiti solo gli inserimenti.<br>Per aggiornare, Upsert o eliminare righe, è necessaria una [trasformazione alter Row](data-flow-alter-row.md) per contrassegnare le righe per tali azioni. | Sì | `true` o `false` | cancellabile <br/>inseribile <br/>aggiornabile <br/>upsertable |
| Colonne chiave | Per gli aggiornamenti, Upsert ed eliminazioni, è necessario impostare le colonne chiave per determinare la riga da modificare.<br>Il nome della colonna scelto come chiave verrà usato come parte del successivo aggiornamento, Upsert, DELETE. Pertanto, è necessario selezionare una colonna esistente nel mapping del sink. | No | Array | chiavi |
| Ignora scrittura colonne chiave | Se non si desidera scrivere il valore nella colonna chiave, selezionare "Ignora scrittura colonne chiave". | No | `true` o `false` | skipKeyWrites |
| azione Tabella |Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.<br>- **None**: nessuna azione verrà eseguita nella tabella.<br>- **Ricrea**: la tabella viene eliminata e ricreata. Questa opzione è obbligatoria se si crea una nuova tabella in modo dinamico.<br>- **Truncate**: tutte le righe della tabella di destinazione vengono rimosse. | No | `true` o `false` | ricreare<br/>truncate |
| Dimensioni dei batch | Consente di specificare il numero di righe scritte in ogni batch. Dimensioni batch più grandi migliorano l'ottimizzazione della compressione e della memoria, ma rischiano di causare eccezioni di memoria insufficiente durante la memorizzazione nella cache dei dati. | No | Integer | batchSize |
| Script pre e post SQL | Specificare gli script SQL a più righe che verrà eseguito prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink. | No | string | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Esempio di script di sink per database di Azure per MySQL

Quando si usa database di Azure per MySQL come tipo di sink, lo script del flusso di dati associato è:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapping dei tipi di dati per il database di Azure per MySQL

Quando si copiano dati dal database di Azure per MySQL, vengono usati i mapping seguenti tra i tipi di dati MySQL e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati del database di Azure per MySQL | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
