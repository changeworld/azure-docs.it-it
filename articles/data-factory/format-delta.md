---
title: Formato Delta in Azure Data Factory
description: Trasformare e spostare i dati da un Delta Lake usando il formato Delta
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: weetok
ms.openlocfilehash: 6d9d2b0d185750cf8ed8192661f28a2b82d88b78
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222539"
---
# <a name="delta-format-in-azure-data-factory"></a>Formato Delta in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati da e verso un Delta Lake archiviato in [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) o nell' [archiviazione BLOB di Azure](connector-azure-blob-storage.md) usando il formato Delta. Questo connettore è disponibile come set di dati [inline](data-flow-source.md#inline-datasets) nel mapping di flussi di dati come origine e sink.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Questo connettore è disponibile come set di dati [inline](data-flow-source.md#inline-datasets) nel mapping di flussi di dati come origine e sink.

### <a name="source-properties"></a>Proprietà origine

Nella tabella seguente sono elencate le proprietà supportate da un'origine Delta. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Il formato deve essere `delta` | sì | `delta` | format |
| File system | Contenitore/file system di Delta Lake | sì | string | fileSystem |
| Percorso della cartella | Il diretto di Delta Lake | sì | string | folderPath |
| Tipo di compressione | Tipo di compressione della tabella Delta | no | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Livello di compressione | Scegliere se la compressione viene completata il più rapidamente possibile o se il file risultante deve essere compresso in modo ottimale. | obbligatorio se `compressedType` si specifica. | `Optimal` o `Fastest` | compressionLevel |
| Tempo di viaggio | Scegliere se eseguire una query su uno snapshot precedente di una tabella Delta | no | Query per timestamp: timestamp <br> Query per versione: integer | timestampAsOf <br> versionAsOf |
| Consenti nessun file trovato | Se true, non viene generato alcun errore se non viene trovato alcun file | no | `true` o `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importa schema

Delta è disponibile solo come set di dati inline e, per impostazione predefinita, non dispone di uno schema associato. Per ottenere i metadati della colonna, fare clic sul pulsante **Importa schema** nella scheda **proiezione** . In questo modo è possibile fare riferimento ai nomi di colonna e ai tipi di dati specificati dal corpus. Per importare lo schema, è necessario che sia attiva una [sessione di debug del flusso di dati](concepts-data-flow-debug-mode.md) ed è necessario disporre di un file di definizione dell'entità CDM esistente a cui puntare.
 

### <a name="delta-source-script-example"></a>Esempio di script di origine Delta

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Proprietà sink

Nella tabella seguente sono elencate le proprietà supportate da un sink Delta. È possibile modificare queste proprietà nella scheda **Impostazioni** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formato | Il formato deve essere `delta` | sì | `delta` | format |
| File system | Contenitore/file system di Delta Lake | sì | string | fileSystem |
| Percorso della cartella | Il diretto di Delta Lake | sì | string | folderPath |
| Tipo di compressione | Tipo di compressione della tabella Delta | no | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Livello di compressione | Scegliere se la compressione viene completata il più rapidamente possibile o se il file risultante deve essere compresso in modo ottimale. | obbligatorio se `compressedType` si specifica. | `Optimal` o `Fastest` | compressionLevel |
| Vacuum | Specificare la soglia di conservazione in ore per le versioni precedenti della tabella. Il valore predefinito è 0 o inferiore a 30 giorni | sì | Integer | vuoto |
| Update (metodo) | Consente di specificare quali operazioni di aggiornamento sono consentite sul Delta Lake. Per i metodi che non vengono inseriti, è necessaria una trasformazione alter Row precedente per contrassegnare le righe. | sì | `true` o `false` | cancellabile <br> inseribile <br> aggiornabile <br> merge |
| Scrittura ottimizzata | Ottenere una velocità effettiva più elevata per l'operazione di scrittura tramite l'ottimizzazione della riproduzione casuale interna negli esecutori Spark. Di conseguenza, è possibile notare un minor numero di partizioni e file di dimensioni maggiori | no | `true` o `false` | optimizedWrite: true |
| Compattazione automatica | Al termine di un'operazione di scrittura, Spark eseguirà automaticamente il ```OPTIMIZE``` comando per riorganizzare i dati, ottenendo così più partizioni, se necessario, per migliorare le prestazioni di lettura in futuro | no | `true` o `false` |    AutoCompact: true |

### <a name="delta-sink-script-example"></a>Esempio di script di sink Delta

Lo script del flusso di dati associato è:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Limitazioni note

Quando si scrive in un sink Delta, esiste una limitazione nota in cui il numero di righe scritte non verrà restituito nell'output di monitoraggio.

## <a name="next-steps"></a>Passaggi successivi

* Creazione di una [trasformazione di origine](data-flow-source.md) nel flusso di dati del mapping.
* Creazione di una [trasformazione sink](data-flow-sink.md) nel flusso di dati di mapping.
* Creare una [trasformazione alter Row](data-flow-alter-row.md) per contrassegnare le righe come INSERT, Update, Upsert o DELETE.
