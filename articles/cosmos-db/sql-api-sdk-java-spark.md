---
title: Azure Cosmos DB Apache Spark 2 oltp Connector per le risorse e le note sulla versione dell'API SQL
description: Informazioni sul connettore Azure Cosmos DB Apache Spark 2 OLTP per l'API SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra ogni versione di SQL Async Java SDK Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: bd948814b4b647bcc3fbfe58b090b1e794504232
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363632"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 2 CONNETTORE OLTP per l'API Core (SQL): note sulla versione e risorse
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Connettore OLTP Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Connettore OLTP Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

È possibile accelerare l'analisi dei Big Data usando Azure Cosmos DB Apache Spark 2 connettore OLTP per Core (SQL). Il connettore Spark consente di eseguire processi [Spark](https://spark.apache.org/) sui dati archiviati in Azure Cosmos DB. Sono supportate l'elaborazione batch e di flusso.

È possibile usare il connettore [con Azure Databricks](https://azure.microsoft.com/services/databricks) o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), che forniscono cluster Spark gestiti in Azure. La tabella seguente illustra le versioni supportate:

| Componente | Versione |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x*, 2.2. *x* e 2.1. *x* |
| Scala | 2,11 |
| Azure Databricks (versione di runtime) | Versione successiva alla 3.4 |

> [!WARNING]
> Questo connettore supporta l'API core (SQL) di Azure Cosmos DB.
> Per l'API Cosmos DB per MongoDB, usare [il connettore MongoDB per Spark.](https://docs.mongodb.com/spark-connector/master/)
> Per il Cosmos DB API Cassandra, usare il [connettore Cassandra Spark.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="resources"></a>Risorse

| Risorsa | Collegamento |
|---|---|
| **Download dell'SDK** | [Scaricare il file con estensione jar più](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) [recente, Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**Documentazione sull'API** | [Informazioni di riferimento sul connettore Spark]() |
|**Contribuire all'SDK** | [Azure Cosmos DB Connector per Apache Spark su GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Operazioni preliminari** | [Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB](./spark-connector.md#bk_working_with_connector) <br> [Usare Apache Spark Structured Streaming con Apache Kafka e Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Cronologia delle versioni

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nuove funzionalità
- Aggiunge una nuova opzione di configurazione, , che può essere usata per specificare l'ora di `changefeedstartfromdatetime` inizio per l'elaborazione delfeed delle modifiche. Per altre informazioni, vedere [Opzioni di configurazione.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
- Corregge una regressione che ha causato un utilizzo eccessivo della memoria negli executor per set di risultati di grandi dimensioni (ad esempio, con milioni di righe), causando infine l'errore `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Corregge un caso limite del checkpoint di streaming in cui contiene il carattere barra verticale `ID` (|) con la `ChangeFeedMaxPagesPerBatch` configurazione applicata.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge il supporto per gli aggiornamenti bulk quando vengono usate chiavi di partizione annidate.
* Aggiunge il supporto per i tipi di dati Decimal e Float durante le operazioni di scrittura Azure Cosmos DB.
* Aggiunge il supporto per i tipi Timestamp quando usano Long (periodo Unix) come valore.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Corregge l'eccezione typecast che si verifica quando `WriteThroughputBudget` viene usata la configurazione.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge informazioni sull'errore per gli errori in blocco all'eccezione e al log.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Corregge i problemi relativi ai checkpoint di streaming.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Per ridurre il rumore, corregge il livello di log di un messaggio lasciato involontariamente con il livello ERROR.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione di un bug nello streaming strutturato durante le divisioni delle partizioni. Il bug potrebbe causare alcuni record del feed di modifiche mancanti o eccezioni Null per le scritture di checkpoint.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione di un bug che causa l'ignorato di uno schema personalizzato fornito per readStream.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Corregge una regressione (unshaded JAR include tutte le dipendenze ombreggiate) che aumenta il tempo di compilazione del 50%.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione di un problema di dipendenza che causa l'esito negativo del trasporto diretto su TCP con RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nuove funzionalità
* Migliora la gestione delle connessioni e il pool di connessioni per ridurre il numero di chiamate ai metadati.

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Per altre informazioni, vedere [Apache Spark](https://spark.apache.org/).
