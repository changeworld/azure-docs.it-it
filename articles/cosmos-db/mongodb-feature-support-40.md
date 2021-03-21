---
title: funzionalità e sintassi supportate per la versione del server 4,0 nell'API Azure Cosmos DB per MongoDB
description: Informazioni sulle funzionalità e sulla sintassi supportate per la versione del server per MongoDB 4,0 e sulle API Azure Cosmos DB. Informazioni sui comandi del database, il supporto del linguaggio di query, i tipi di dati, i comandi della pipeline di aggregazione e gli operatori supportati.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 9eebc77c5b3d9402c766320fddfdaf05d50b574f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485402"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>API di Azure Cosmos DB per MongoDB (versione Server 4,0): funzionalità e sintassi supportate
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API di Azure Cosmos DB per MongoDB tramite uno dei [driver](https://docs.mongodb.org/ecosystem/drivers) open source del client MongoDB. L'API di Azure Cosmos DB per MongoDB consente di usare driver client esistenti aderendo al [protocollo di collegamento](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Usando l'API di Azure Cosmos DB per MongoDB è possibile sfruttare i noti vantaggi di MongoDB con tutte le funzionalità aziendali offerte da Cosmos DB: [distribuzione globale](distribute-data-globally.md), [partizionamento orizzontale automatico](partitioning-overview.md), garanzie di disponibilità e latenza, crittografia di dati inattivi, backup e molto altro.

## <a name="protocol-support"></a>Protocolli supportati

Gli operatori supportati con i relativi limiti ed eccezioni sono elencati di seguito. I driver client che identificano questi protocolli dovrebbero essere in grado di collegarsi all'API di Cosmos DB per MongoDB. Quando si usa l'API di Azure Cosmos DB per gli account MongoDB, le versioni 3.6 + degli account hanno l'endpoint nel formato `*.mongo.cosmos.azure.com` mentre la versione 3,2 degli account ha l'endpoint nel formato `*.documents.azure.com` .

> [!NOTE]
> Questo articolo elenca solo i comandi server supportati e non le funzioni wrapper lato client. Le funzioni wrapper lato client come `deleteMany()` e `updateMany()` utilizzano internamente i comandi server `delete()` e `update()`. Le funzioni che utilizzano i comandi server supportati sono compatibili con l'API di Azure Cosmos DB per MongoDB.

## <a name="query-language-support"></a>Linguaggi di query supportati

L'API di Azure Cosmos DB per MongoDB offre il supporto completo dei costrutti del linguaggio di query MongoDB. Di seguito è possibile trovare l'elenco dettagliato di operazioni, operatori, fasi, comandi e opzioni attualmente supportati.

## <a name="database-commands"></a>Comandi del database

L'API di Azure Cosmos DB per MongoDB supporta i comandi di database seguenti:

### <a name="query-and-write-operation-commands"></a>Comandi per le operazioni di query e scrittura

| Comando | Supportato |
|---------|---------|
| [modificare i flussi](mongodb-change-streams.md) | Sì |
| eliminare | Sì |
| eval | No |
| trovare | Sì |
| findAndModify | Sì |
| getLastError | Sì |
| getMore | Sì |
| getPrevError | No |
| insert | Sì |
| parallelCollectionScan | No |
| resetError | No |
| update | Sì |

### <a name="transaction-commands"></a>Comandi delle transazioni

| Comando | Supportato |
|---------|---------|
| abortTransaction | Sì |
| commitTransaction | Sì |

### <a name="authentication-commands"></a>Comandi di autenticazione

| Comando | Supportato |
|---------|---------|
| authenticate | Sì |
| getnonce | Sì |
| logout | Sì |

### <a name="administration-commands"></a>Comandi di amministrazione

| Comando | Supportato |
|---------|---------|
| cloneCollectionAsCapped | No |
| collMod | No |
| connectionStatus | No |
| convertToCapped | No |
| copydb | No |
| create | Sì |
| createIndexes | Sì |
| currentOp | Sì |
| drop | Sì |
| dropDatabase | Sì |
| dropIndexes | Sì |
| filemd5 | Sì |
| killCursors | Sì |
| killOp | No |
| listCollections | Sì |
| listDatabases | Sì |
| listIndexes | Sì |
| reIndex | Sì |
| renameCollection | No |

### <a name="diagnostics-commands"></a>Comandi di diagnostica

| Comando | Supportato |
|---------|---------|
| buildInfo | Sì |
| collStats | Sì |
| connPoolStats | No |
| connectionStatus | No |
| dataSize | No |
| dbHash | No |
| dbStats | Sì |
| explain | Sì |
| funzionalità | No |
| hostInfo | Sì |
| listDatabases | Sì |
| listCommands | No |
| profiler | No |
| serverStatus | No |
| top | No |
| whatsmyuri | Sì |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Pipeline di aggregazione

### <a name="aggregation-commands"></a>Comandi di aggregazione

| Comando | Supportato |
|---------|---------|
| aggregate | Sì |
| count | Sì |
| distinct | Sì |
| mapReduce | No |

### <a name="aggregation-stages"></a>Fasi di aggregazione

| Comando | Supportato |
|---------|---------|
| $addFields | Sì |
| $bucket | No |
| $bucketAuto | No |
| $changeStream | Sì |
| $collStats | No |
| $count | Sì |
| $currentOp | No |
| $facet | Sì |
| $geoNear | Sì |
| $graphLookup | Sì |
| $group | Sì |
| $indexStats | No |
| $limit | Sì |
| $listLocalSessions | No |
| $listSessions | No |
| $lookup | Sì |
| $match | Sì |
| $out | Sì |
| $project | Sì |
| $redact | Sì |
| $replaceRoot | Sì |
| $replaceWith | No |
| $sample | Sì |
| $skip | Sì |
| $sort | Sì |
| $sortByCount | Sì |
| $unwind | Sì |

### <a name="boolean-expressions"></a>Espressioni booleane

| Comando | Supportato |
|---------|---------|
| $and | Sì |
| $not | Sì |
| $or | Sì |

### <a name="conversion-expressions"></a>Espressioni di conversione

| Comando | Supportato |
|---------|---------|
| $convert | Sì |
| $toBool | Sì |
| $toDate | Sì |
| $toDecimal | Sì |
| $toDouble | Sì |
| $toInt | Sì |
| $toLong | Sì |
| $toObjectId | Sì |
| $toString | Sì |

### <a name="set-expressions"></a>Espressioni Set

| Comando | Supportato |
|---------|---------|
| $setEquals | Sì |
| $setIntersection | Sì |
| $setUnion | Sì |
| $setDifference | Sì |
| $setIsSubset | Sì |
| $anyElementTrue | Sì |
| $allElementsTrue | Sì |

### <a name="comparison-expressions"></a>Espressioni di confronto

| Comando | Supportato |
|---------|---------|
| $cmp | Sì |
| $eq | Sì | 
| $gt | Sì | 
| $gte | Sì | 
| $lt | Sì |
| $lte | Sì | 
| $ne | Sì | 
| $in | Sì | 
| $nin | Sì | 

### <a name="arithmetic-expressions"></a>Espressioni aritmetiche

| Comando | Supportato |
|---------|---------|
| $abs | Sì |
| $add | Sì |
| $ceil | Sì |
| $divide | Sì |
| $exp | Sì |
| $floor | Sì |
| $ln | Sì |
| $log | Sì |
| $log10 | Sì |
| $mod | Sì |
| $multiply | Sì |
| $pow | Sì |
| $sqrt | Sì |
| $subtract | Sì |
| $trunc | Sì |

### <a name="string-expressions"></a>Espressioni stringa

| Comando | Supportato |
|---------|---------|
| $concat | Sì |
| $indexOfBytes | Sì |
| $indexOfCP | Sì |
| $ltrim | Sì |
| $rtrim | Sì |
| $trim | Sì |
| $split | Sì |
| $strLenBytes | Sì |
| $strLenCP | Sì |
| $strcasecmp | Sì |
| $substr | Sì |
| $substrBytes | Sì |
| $substrCP | Sì |
| $toLower | Sì |
| $toUpper | Sì |

### <a name="text-search-operator"></a>Operatore di ricerca testo

| Comando | Supportato |
|---------|---------|
| $meta | No |

### <a name="array-expressions"></a>Espressioni di matrice

| Comando | Supportato |
|---------|---------|
| $arrayElemAt | Sì |
| $arrayToObject | Sì |
| $concatArrays | Sì |
| $filter | Sì |
| $indexOfArray | Sì |
| $isArray | Sì |
| $objectToArray | Sì |
| $range | Sì |
| $reverseArray | Sì |
| $reduce | Sì |
| $size | Sì |
| $slice | Sì |
| $zip | Sì |
| $in | Sì |

### <a name="variable-operators"></a>Operatori variabili

| Comando | Supportato |
|---------|---------|
| $map | Sì |
| $let | Sì |

### <a name="system-variables"></a>Variabili di sistema

| Comando | Supportato |
|---------|---------|
| $$CURRENT | Sì |
| $$DESCEND | Sì |
| $$KEEP | Sì |
| $$PRUNE | Sì |
| $$REMOVE | Sì |
| $$ROOT | Sì |

### <a name="literal-operator"></a>Operatore letterale

| Comando | Supportato |
|---------|---------|
| $literal | Sì |

### <a name="date-expressions"></a>Espressioni di data

| Comando | Supportato |
|---------|---------|
| $dayOfYear | Sì |
| $dayOfMonth | Sì |
| $dayOfWeek | Sì |
| $year | Sì |
| $month | Sì | 
| $week | Sì |
| $hour | Sì |
| $minute | Sì | 
| $second | Sì |
| $millisecond | Sì | 
| $dateToString | Sì |
| $isoDayOfWeek | Sì |
| $isoWeek | Sì |
| $dateFromParts | No | 
| $dateToParts | No |
| $dateFromString | No |
| $isoWeekYear | Sì |

### <a name="conditional-expressions"></a>Espressioni condizionali

| Comando | Supportato |
|---------|---------|
| $cond | Sì |
| $ifNull | Sì |
| $switch | Sì |

### <a name="data-type-operator"></a>Operatore tipo di dati

| Comando | Supportato |
|---------|---------|
| $type | Sì |

### <a name="accumulator-expressions"></a>Espressioni accumulatore

| Comando | Supportato |
|---------|---------|
| $sum | Sì |
| $avg | Sì |
| $first | Sì |
| $last | Sì |
| $max | Sì |
| $min | Sì |
| $push | Sì |
| $addToSet | Sì |
| $stdDevPop | Sì |
| $stdDevSamp | Sì |

### <a name="merge-operator"></a>Operatore merge

| Comando | Supportato |
|---------|---------|
| $mergeObjects | Sì |

## <a name="data-types"></a>Tipi di dati

L'API di Azure Cosmos DB per MongoDB supporta i documenti codificati nel formato BSON MongoDB. La versione dell'API 4,0 migliora l'utilizzo interno di questo formato per migliorare le prestazioni e ridurre i costi. I documenti scritti o aggiornati tramite un endpoint che esegue 4,0 traggono vantaggio da questa operazione.
 
In uno [scenario di aggiornamento](mongodb-version-upgrade.md), i documenti scritti prima dell'aggiornamento alla versione 4,0 non trarranno vantaggio dalle prestazioni migliorate fino a quando non vengono aggiornati tramite un'operazione di scrittura tramite l'endpoint 4,0.

| Comando | Supportato |
|---------|---------|
| Double | Sì |
| string | Sì |
| Oggetto | Sì |
| Array | Sì |
| Binary Data | Sì | 
| ObjectId | Sì |
| Boolean | Sì |
| Data | Sì |
| Null | Sì |
| 32-bit Integer (int) | Sì |
| Timestamp | Sì |
| 64-bit Integer (long) | Sì |
| MinKey | Sì |
| MaxKey | Sì |
| Decimal128 | Sì | 
| Espressione regolare | Sì |
| JavaScript | Sì |
| JavaScript (with scope)| Sì |
| Non definito | Sì |

## <a name="indexes-and-index-properties"></a>Indici e proprietà degli indici

### <a name="indexes"></a>Indici

| Comando | Supportato |
|---------|---------|
| Single Field Index | Sì |
| Compound Index | Sì |
| Multikey Index | Sì |
| Text Index | No |
| 2dsphere | Sì |
| 2d Index | No |
| Hashed Index | Sì |

### <a name="index-properties"></a>Proprietà degli indici

| Comando | Supportato |
|---------|---------|
| TTL | Sì |
| Univoco | Sì |
| Parziale | No |
| Case Insensitive | No |
| Sparse | No |
| Background | Sì |

## <a name="operators"></a>Operatori

### <a name="logical-operators"></a>Operatori logici

| Comando | Supportato |
|---------|---------|
| $or | Sì |
| $and | Sì |
| $not | Sì |
| $nor | Sì | 

### <a name="element-operators"></a>Operatori elemento

| Comando | Supportato |
|---------|---------|
| $exists | Sì |
| $type | Sì |

### <a name="evaluation-query-operators"></a>Operatori di query di valutazione

| Comando | Supportato |
|---------|---------|
| $expr | No |
| $jsonSchema | No |
| $mod | Sì |
| $regex | Sì |
| $text | No (non è supportato. In alternativa, usare $regex).| 
| $where | No | 

Nelle query $regex, le espressioni ancorate a sinistra consentono la ricerca nell'indice. L'uso del modificatore 'i' (senza distinzione tra maiuscole e minuscole) e 'm' (su più righe) provoca l'analisi della raccolta in tutte le espressioni.

Quando è necessario includere '$' o '|' è consigliabile creare due o più query regex. La query originale `find({x:{$regex: /^abc$/})` deve essere ad esempio modificata come segue:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

La prima parte userà l'indice per limitare la ricerca ai documenti che iniziano con ^abc, mentre la seconda parte individuerà le voci esatte. L'operatore barra '|' funge da funzione "or". La query `find({x:{$regex: /^abc |^def/})` individua i documenti in cui il campo 'x' ha un valore che inizia con "abc" o "def". Per usare l'indice è consigliabile per suddividere la query in due query diverse unite dall'operatore $or: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Operatori matrice

| Comando | Supportato | 
|---------|---------|
| $all | Sì | 
| $elemMatch | Sì | 
| $size | Sì | 

### <a name="comment-operator"></a>Operatore commento

| Comando | Supportato | 
|---------|---------|
| $comment | Sì | 

### <a name="projection-operators"></a>Operatori proiezione

| Comando | Supportato |
|---------|---------|
| $elemMatch | Sì |
| $meta | No |
| $slice | Sì |

### <a name="update-operators"></a>Operatori di aggiornamento

#### <a name="field-update-operators"></a>Operatori di aggiornamento di campo

| Comando | Supportato |
|---------|---------|
| $inc | Sì |
| $mul | Sì |
| $rename | Sì |
| $setOnInsert | Sì |
| $set | Sì |
| $unset | Sì |
| $min | Sì |
| $max | Sì |
| $currentDate | Sì |

#### <a name="array-update-operators"></a>Operatori di aggiornamento di matrice

| Comando | Supportato |
|---------|---------|
| $ | Sì |
| $[]| Sì |
| $[<identifier>]| Sì |
| $addToSet | Sì |
| $pop | Sì |
| $pullAll | Sì |
| $pull | Sì |
| $push | Sì |
| $pushAll | Sì |

#### <a name="update-modifiers"></a>Modificatori aggiornamento

| Comando | Supportato |
|---------|---------|
| $each | Sì |
| $slice | Sì |
| $sort | Sì |
| $position | Sì |

#### <a name="bitwise-update-operator"></a>Operatore di aggiornamento bit per bit

| Comando | Supportato |
|---------|---------|
| $bit | Sì | 
| $bitsAllSet | No |
| $bitsAnySet | No |
| $bitsAllClear | No |
| $bitsAnyClear | No |

### <a name="geospatial-operators"></a>Operatori geospaziali

Operatore | Supportato | 
--- | --- |
$geoWithin | Sì |
$geoIntersects | Sì | 
$near | Sì |
$nearSphere | Sì |
$geometry | Sì |
$minDistance | Sì |
$maxDistance | Sì |
$center | No |
$centerSphere | No |
$box | No |
$polygon | No |

## <a name="sort-operations"></a>Operazioni di ordinamento

Quando si usa l'operazione `findOneAndUpdate`, sono supportate le operazioni di ordinamento in un singolo campo, ma non quelle su più campi.

## <a name="unique-indexes"></a>Indici univoci

Gli [indici univoci](mongodb-indexing.md#unique-indexes) assicurano che un campo specifico non abbia valori duplicati in tutti i documenti di una raccolta, in modo simile alla preservazione dell'univocità per la chiave "_id" predefinita. È possibile creare indici univoci in Azure Cosmos DB usando il `createIndex` comando con il `unique` parametro constraint:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Indici composti

Gli [indici composti](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) consentono di creare un indice per gruppi di campi per un massimo di otto campi. Questo tipo di indice è diverso dagli indici composti nativi di MongoDB. In Azure Cosmos DB gli indici composti vengono usati per le operazioni di ordinamento applicate a più campi. Per creare un indice composto, è necessario specificare più di una proprietà come parametro:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
    "createdCollectionAutomatically" : false, 
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

Azure Cosmos DB supporta GridFS tramite qualsiasi driver Mongo compatibile con GridFS.

## <a name="replication"></a>Replica

Azure Cosmos DB supporta la replica automatica e nativa ai livelli più bassi. Questa logica viene estesa per ottenere anche una replica globale a bassa latenza. Cosmos DB non supporta comandi di replica manuali.

## <a name="retryable-writes"></a>Scritture ripetibili

Cosmos DB non supporta ancora le Scritture riprovabili. I driver client devono aggiungere il parametro URL ' retryWrites = false ' alla relativa stringa di connessione. È possibile aggiungere più parametri URL anteponendo il prefisso "&". 

## <a name="sharding"></a>Partizionamento orizzontale

Azure Cosmos DB supporta il partizionamento orizzontale automatico lato server. Gestisce automaticamente la creazione, la selezione e il bilanciamento delle partizioni. Azure Cosmos DB non supporta i comandi di partizionamento orizzontale manuali, ovvero non è necessario richiamare comandi come addShard, balancerStart, moveChunk e così via. È sufficiente specificare la chiave di partizione durante la creazione dei contenitori o l'esecuzione di query sui dati.

## <a name="sessions"></a>Sessioni

Azure Cosmos DB non supporta ancora i comandi delle sessioni sul lato server.

## <a name="time-to-live-ttl"></a>Durata (TTL)

Azure Cosmos DB supporta un valore TTL (time-to-Live) in base al timestamp del documento. La durata può essere abilitata per le raccolte passando al [portale di Azure](https://portal.azure.com).

## <a name="transactions"></a>Transazioni

Le transazioni a più documenti sono supportate all'interno di una raccolta non partizionata. Le transazioni a più documenti non sono supportate nelle raccolte o nelle raccolte partizionate. Il timeout per le transazioni è di 5 secondi fissi.

## <a name="user-and-role-management"></a>Gestione di utenti e ruoli

Azure Cosmos DB non supporta ancora utenti e ruoli. Cosmos DB, tuttavia, supporta il Controllo degli accessi in base al ruolo di Azure e chiavi/password di lettura/scrittura e sola lettura ottenibili tramite il [portale di Azure](https://portal.azure.com) nella pagina Stringa di connessione.

## <a name="write-concern"></a>Write concern

Alcune applicazioni si basano su un [problema di scrittura](https://docs.mongodb.com/manual/reference/write-concern/), che specifica il numero di risposte necessarie durante un'operazione di scrittura. A causa della modalità in cui Cosmos DB gestisce la replica in background, per impostazione predefinita tutte le operazioni di scrittura sono automaticamente Quorum. Qualsiasi write concern specificato dal codice client viene ignorato. Per altre informazioni, vedere [Uso dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni](consistency-levels.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
