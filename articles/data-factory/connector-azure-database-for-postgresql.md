---
title: Copiare e trasformare i dati in database di Azure per PostgreSQL
description: Informazioni su come copiare e trasformare i dati in database di Azure per PostgreSQL usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738056"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiare e trasformare i dati nel database di Azure per PostgreSQL usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in database di Azure per PostgreSQL e usare il flusso di dati per trasformare i dati in database di Azure per PostgreSQL. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

Questo connettore è specializzato per il [servizio database di Azure per PostgreSQL](../postgresql/overview.md). Per copiare dati da un database PostgreSQL generico situato in locale o nel cloud, usare il [connettore PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database di Azure per PostgreSQL è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con una [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

Attualmente, il flusso di dati supporta il server singolo database di Azure per PostgreSQL ma non un server flessibile o iperscalabile (CITUS).

## <a name="getting-started"></a>Guida introduttiva

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche del connettore database di Azure per PostgreSQL.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato database di Azure per PostgreSQL sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su: **AzurePostgreSql**. | Sì |
| connectionString | Stringa di connessione ODBC per la connessione al Database di Azure per PostgreSQL.<br/>È anche possibile inserire una password in Azure Key Vault ed estrarre la `password` configurazione dalla stringa di connessione. Per ulteriori informazioni, vedere gli esempi seguenti e [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) . | Sì |
| connectVia | Questa proprietà rappresenta il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

Una stringa di connessione tipica è `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Di seguito sono riportate altre proprietà che è possibile impostare in base al caso:

| Proprietà | Descrizione | Opzioni | Obbligatoria |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Il metodo usato dal driver per crittografare i dati inviati tra il driver e il server di database. Ad esempio, `EncryptionMethod=<0/1/6>;`| 0 (Nessuna crittografia) **(impostazione predefinita)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Determina se il driver convalida il certificato inviato dal server di database quando è abilitata la crittografia SSL (metodo di crittografia = 1). Ad esempio, `ValidateServerCertificate=<0/1>;`| 0 (disabilitato) **(impostazione predefinita)** / 1 (abilitato) | No |

**Esempio**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Esempio**:

***Archivia la password in Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [DataSets in Azure Data Factory](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate da database di Azure per PostgreSQL nei set di dati.

Per copiare dati dal Database di Azure per PostgreSQL, impostare la proprietà type del set di dati su **AzurePostgreSqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **AzurePostgreSqlTable** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate da un'origine database di Azure per PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Database di Azure per PostgreSQL come origine

Per copiare i dati da un Database di Azure per PostgreSQL, impostare il tipo di origine nell'attività di copia su **AzurePostgreSqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AzurePostgreSqlSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio, `SELECT * FROM mytable` o `SELECT * FROM "MyTable"`. Nota in PostgreSQL, il nome dell'entità viene considerato senza distinzione tra maiuscole e minuscole se non è racchiuso tra virgolette. | No (se è specificata la proprietà TableName nel set di dati) |

**Esempio**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Database di Azure per PostgreSQL come sink

Per copiare dati in database di Azure per PostgreSQL, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **AzurePostgreSQLSink**. | Sì |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database di Azure per PostgreSQL a ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |
| writeMethod | Metodo usato per scrivere i dati nel database di Azure per PostgreSQL.<br>I valori consentiti sono: **CopyCommand** (anteprima, che è più efficiente), **BulkInsert** (impostazione predefinita). | No |
| writeBatchSize | Numero di righe caricate nel database di Azure per PostgreSQL per batch.<br>Il valore consentito è un numero intero che rappresenta il numero di righe. | No (il valore predefinito è 1 milione) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br>I valori consentiti sono stringhe TimeSpan. Ad esempio "00:30:00" (30 minuti). | No (il valore predefinito è 00:30:00) |

**Esempio**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere in tabelle da database di Azure per PostgreSQL. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping. È possibile scegliere di usare un set di dati di database di Azure per PostgreSQL o un [set di dati inline](data-flow-source.md#inline-datasets) come tipo di origine e sink.

### <a name="source-transformation"></a>Trasformazione origine

La tabella seguente elenca le proprietà supportate dall'origine del database di Azure per PostgreSQL. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabella | Se si seleziona tabella come input, il flusso di dati recupera tutti i dati dalla tabella specificata nel set di dati. | No | - |*(solo per set di dati inline)*<br>tableName |
| Query | Se si seleziona query come input, specificare una query SQL per recuperare i dati dall'origine, che esegue l'override di qualsiasi tabella specificata nel set di dati. L'uso delle query è un ottimo modo per ridurre le righe per il test o le ricerche.<br><br>La clausola **Order by** non è supportata, ma è possibile impostare un'istruzione SELECT from completa. È possibile usare anche funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella che è possibile utilizzare nel flusso di dati.<br>Esempio di query: `select * from mytable where customerId > 1000 and customerId < 2000` o `select * from "MyTable"` . Nota in PostgreSQL, il nome dell'entità viene considerato senza distinzione tra maiuscole e minuscole se non è racchiuso tra virgolette.| No | string | query |
| Dimensioni dei batch | Specificare le dimensioni del batch per suddividere i dati di grandi dimensioni in batch. | No | Integer | batchSize |
| Livello di isolamento | Scegliere uno dei seguenti livelli di isolamento:<br>-Read Committed<br>-Read uncommitted (impostazione predefinita)<br>-Lettura ripetibile<br>-Serializzabile<br>-None (ignora il livello di isolamento) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NESSUNO</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Esempio di script di origine per database di Azure per PostgreSQL

Quando si usa database di Azure per PostgreSQL come tipo di origine, lo script del flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Trasformazione sink

La tabella seguente elenca le proprietà supportate dal sink del database di Azure per PostgreSQL. È possibile modificare queste proprietà nella scheda **Opzioni sink** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update (metodo) | Specificare le operazioni consentite per la destinazione del database. Per impostazione predefinita, sono consentiti solo gli inserimenti.<br>Per aggiornare, Upsert o eliminare righe, è necessaria una [trasformazione alter Row](data-flow-alter-row.md) per contrassegnare le righe per tali azioni. | Sì | `true` o `false` | cancellabile <br/>inseribile <br/>aggiornabile <br/>upsertable |
| Colonne chiave | Per gli aggiornamenti, Upsert ed eliminazioni, è necessario impostare le colonne chiave per determinare la riga da modificare.<br>Il nome della colonna scelto come chiave verrà usato come parte del successivo aggiornamento, Upsert, DELETE. Pertanto, è necessario selezionare una colonna esistente nel mapping del sink. | No | Array | chiavi |
| Ignora scrittura colonne chiave | Se non si desidera scrivere il valore nella colonna chiave, selezionare "Ignora scrittura colonne chiave". | No | `true` o `false` | skipKeyWrites |
| azione Tabella |Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.<br>- **None**: nessuna azione verrà eseguita nella tabella.<br>- **Ricrea**: la tabella viene eliminata e ricreata. Questa opzione è obbligatoria se si crea una nuova tabella in modo dinamico.<br>- **Truncate**: tutte le righe della tabella di destinazione vengono rimosse. | No | `true` o `false` | ricreare<br/>truncate |
| Dimensioni dei batch | Consente di specificare il numero di righe scritte in ogni batch. Dimensioni batch più grandi migliorano l'ottimizzazione della compressione e della memoria, ma rischiano di causare eccezioni di memoria insufficiente durante la memorizzazione nella cache dei dati. | No | Integer | batchSize |
| Script pre e post SQL | Specificare gli script SQL a più righe che verrà eseguito prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink. | No | string | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Esempio di script di sink per database di Azure per PostgreSQL

Quando si usa database di Azure per PostgreSQL come tipo di sink, lo script del flusso di dati associato è:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per ulteriori informazioni sulle proprietà, vedere [attività Lookup in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
