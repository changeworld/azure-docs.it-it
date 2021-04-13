---
title: Connettersi Apache Spark 2 a Azure Cosmos DB
description: Informazioni sul connettore OLTP Azure Cosmos DB Spark 2 che consente di connettersi Apache Spark a Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 9d4a1a7d359bac83159d0e5ca4bddfc5935394a5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364261"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-v2-to-azure-cosmos-db-oltp-connector"></a>Accelerare l'analisi dei Big Data usando Apache Spark da v2 a Azure Cosmos DB connettore OLTP
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

È possibile eseguire [processi Spark v2](https://spark.apache.org/) con i dati archiviati in Azure Cosmos DB usando il connettore OLTP Cosmos DB Spark 2. Cosmos può essere usato per l'elaborazione batch e di flusso e come livello di servizio per l'accesso a bassa latenza.

È possibile usare il connettore [con Azure Databricks](https://azure.microsoft.com/services/databricks) o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), che forniscono cluster Spark gestiti in Azure. La tabella seguente illustra le versioni di Spark supportate.

| Componente | Versione |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x e 2.1.x |
| Scala | 2,11 |
| Versione del runtime di Azure Databricks | > 3.4 |

> [!WARNING]
> Questo connettore supporta l'API core (SQL) di Azure Cosmos DB.
> Per Cosmos DB'API MongoDB, usare il [connettore Spark MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Per Cosmos DB API Cassandra, usare il [connettore Cassandra Spark.](https://github.com/datastax/spark-cassandra-connector)

> [!IMPORTANT]
> Il Azure Cosmos DB spark OLTP non è attualmente supportato per gli [account serverless.](serverless.md) Questa operazione verrà affrontata quando l'offerta serverless diventa disponibile a livello generale.

## <a name="quickstart"></a>Avvio rapido

* Seguire la procedura descritta in [Introduzione a Java SDK](./create-sql-api-java.md) per configurare un account Cosmos DB e popolare alcuni dati.
* Seguire la procedura descritta in Azure Databricks [per iniziare](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) a configurare un'area Azure Databricks un cluster e un'area di lavoro.
* È ora possibile creare nuovi notebook e importare la Cosmos DB connettore. Passare a [Uso del connettore Cosmos DB per](#bk_working_with_connector) informazioni dettagliate su come configurare l'area di lavoro.
* La sezione seguente include frammenti di codice su come leggere e scrivere usando il connettore.

### <a name="batch-reads-from-cosmos-db"></a>Batch legge da Cosmos DB

Il frammento di codice seguente illustra come creare un dataframe Spark da leggere Cosmos DB in PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E lo stesso frammento di codice in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Scritture batch in Cosmos DB

Il frammento di codice seguente illustra come scrivere un frame di dati in Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

E lo stesso frammento di codice in Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streaming delle operazioni di lettura Cosmos DB

Il frammento di codice seguente illustra come connettersi e leggere da Azure Cosmos DB feed di modifiche.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
E lo stesso frammento di codice in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Operazioni di scrittura in Cosmos DB

Il frammento di codice seguente illustra come scrivere un frame di dati in Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

E lo stesso frammento di codice in Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Per altri frammenti ed esempi end-to-end, vedere [Jupyter.](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Uso del connettore

È possibile compilare il connettore dall'origine in GitHub o scaricare i file uber jar da Maven nei collegamenti seguenti.

| Spark | Scala | Versione più recente |
|---|---|---|
| 2.4.0 | 2,11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Uso dei notebook di Databricks

Creare una libreria usando l'area di lavoro di Databricks seguendo le indicazioni riportate nella guida Azure Databricks > usare il [connettore Spark Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> La pagina Use the Azure Cosmos DB Spark Connector (Usa **il connettore Spark)** non è attualmente aggiornata. Invece di scaricare i sei jar separati in sei librerie diverse, è possibile scaricare il file jar uber da maven in [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) e installare questo file jar/libreria.
> 

### <a name="using-spark-cli"></a>Uso di spark-cli

Per usare il connettore usando spark-cli (ovvero , , ), è possibile usare il parametro con le coordinate `spark-shell` `pyspark` `spark-submit` `--packages` [maven del connettore.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Uso dei notebook di Jupyter

Se si usa Jupyter Notebook in HDInsight, è possibile usare la cella spark-magic per specificare le coordinate `%%configure` maven del connettore.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Si noti che l'inclusione di è specifica per rimuovere potenziali conflitti tra il `spark.jars.excludes` connettore, Apache Spark e Livy.

### <a name="build-the-connector"></a>Creare il connettore

Attualmente, questo progetto connettore `maven` usa per compilare senza dipendenze, è possibile eseguire:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Uso degli esempi

Il [repository Cosmos DB GitHub spark](https://github.com/Azure/azure-cosmosdb-spark) include i notebook e gli script di esempio seguenti che è possibile provare.

* Prestazioni dei voli in tempo reale con Spark e **Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)html: connettere Spark a Cosmos DB usando il servizio Jupyter Notebook HDInsight per presentare Spark  |  [](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)SQL, GraphFrames e prevedere i ritardi dei voli tramite pipeline di Machine Learning.
* **Origine Twitter con Apache Spark e Azure Cosmos DB feed di modifiche:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Uso Apache Spark per eseguire query Cosmos DB Graphs**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Connessione Azure Databricks a Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** tramite `azure-cosmosdb-spark` .  Qui è collegata anche una Azure Databricks del [notebook On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architettura lambda con Azure Cosmos DB e HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** è possibile ridurre il sovraccarico operativo della gestione delle pipeline di Big Data usando Cosmos DB e Spark.

## <a name="more-information"></a>Altre informazioni

Nel wiki sono disponibili altre `azure-cosmosdb-spark` [](https://github.com/Azure/azure-cosmosdb-spark/wiki) informazioni, tra cui:

* [Azure Cosmos DB utente del connettore Spark](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Esempi di aggregazioni](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Installazione e configurazione

* [Configurazione del connettore Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Configurazione di Spark Cosmos DB Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (in corso)
* [Configurazione di Power BI Direct Query per Azure Cosmos DB tramite Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Risoluzione dei problemi

* [Uso Cosmos DB funzioni di aggregazione](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemi noti](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestazioni

* [Suggerimenti sulle prestazioni](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Esecuzioni di test di query](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Scrittura di esecuzioni dei test](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed di modifiche

* [Modifiche all'elaborazione del flusso Azure Cosmos DB feed di modifiche e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demo dei feed di modifiche](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demo di Structured Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitoraggio

* [Monitoraggio dei processi Spark con Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passaggi successivi

Se ancora non è stato fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Esplorare le risorse aggiuntive seguenti nel repository:

* [Aggregations examples (Esempi di aggregazioni)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Script e notebook di esempio](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È anche possibile vedere [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Guida ad Apache Spark SQL, frame di dati e set di dati) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).