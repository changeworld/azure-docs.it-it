---
title: Migrare i dati da Apache Cassandra al API Cassandra di Azure Cosmos DB usando databricks (Spark)
description: Informazioni su come eseguire la migrazione dei dati da un database Apache Cassandra al Azure Cosmos DB API Cassandra tramite Azure Databricks e Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801010"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Migrare i dati da Cassandra a un account di API Cassandra Azure Cosmos DB usando Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra in Azure Cosmos DB è diventata un'ottima scelta per i carichi di lavoro aziendali in esecuzione su Apache Cassandra per diversi motivi:

* **Nessun sovraccarico di gestione e monitoraggio:** Elimina il sovraccarico dovuto alla gestione e al monitoraggio delle impostazioni tra i file di sistema operativo, JVM e YAML e le relative interazioni.

* **Risparmi significativi sui costi:** È possibile risparmiare sui costi con la Azure Cosmos DB, che include il costo delle macchine virtuali, la larghezza di banda e tutte le licenze applicabili. Non è necessario gestire Data Center, server, archiviazione SSD, rete e costi elettrici.

* **Possibilità di usare il codice e gli strumenti esistenti:** Il Azure Cosmos DB fornisce la compatibilità a livello di protocollo wire con gli SDK e gli strumenti Cassandra esistenti. Questa compatibilità garantisce che sia possibile usare la codebase esistente con la Azure Cosmos DB API Cassandra con modifiche semplici.

Esistono diversi modi per eseguire la migrazione dei carichi di lavoro del database da una piattaforma a un'altra. [Azure Databricks](https://azure.microsoft.com/services/databricks/) è un'offerta di piattaforma distribuita come servizio (PaaS) per [Apache Spark](https://spark.apache.org/) che offre un modo per eseguire migrazioni offline su larga scala. Questo articolo descrive i passaggi necessari per eseguire la migrazione dei dati dalle tabelle e dagli spazi dei dati di Apache Cassandra nativi al API Cassandra di Azure Cosmos DB usando Azure Databricks.

## <a name="prerequisites"></a>Prerequisiti

* Effettuare il [provisioning di un account di API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account).

* [Esaminare le nozioni di base per la connessione a un Azure Cosmos DB API Cassandra](cassandra-spark-generic.md).

* Per garantire la compatibilità, esaminare le [funzionalità supportate nella Azure Cosmos DB API Cassandra](cassandra-support.md) .

* Assicurarsi di aver già creato le tabelle e gli spazi di Azure Cosmos DB vuoti nella destinazione API Cassandra account.

* [Usare cqlsh o la shell ospitata per la convalida](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Effettuare il provisioning di un cluster di Azure Databricks

È possibile seguire le istruzioni per eseguire il [provisioning di un cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Si consiglia di selezionare databricks Runtime versione 7,5, che supporta Spark 3,0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Screenshot che mostra la ricerca della versione del runtime di databricks.":::

## <a name="add-dependencies"></a>Aggiungere le dipendenze

È necessario aggiungere al cluster la libreria del connettore di Apache Spark Cassandra per connettersi agli endpoint nativi e Azure Cosmos DB Cassandra. Nel cluster selezionare **librerie**  >  **Installa nuovo**  >  **Maven** e quindi Aggiungi `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` in coordinate Maven.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Screenshot che mostra la ricerca di pacchetti Maven in databricks.":::

Selezionare **Installa**, quindi riavviare il cluster al termine dell'installazione.

> [!NOTE]
> Assicurarsi di riavviare il cluster databricks dopo l'installazione della libreria del connettore Cassandra.

## <a name="create-scala-notebook-for-migration"></a>Creare un notebook scala per la migrazione

Creare un notebook scala in databricks. Sostituire le configurazioni Cassandra di origine e di destinazione con le credenziali corrispondenti e le tabelle e gli spazi dei dati di origine e di destinazione. Eseguire quindi il codice seguente:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> I `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` valori e e il numero di ruoli di lavoro nel cluster Spark sono importanti configurazioni da ottimizzare per evitare la [limitazione della frequenza](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/). La limitazione della frequenza si verifica quando le richieste di Azure Cosmos DB superano la velocità effettiva con provisioning o le [unità richiesta](./request-units.md) (UR). Potrebbe essere necessario modificare queste impostazioni, a seconda del numero di esecutori nel cluster Spark e potenzialmente delle dimensioni (e quindi del costo UR) di ogni record scritto nelle tabelle di destinazione.

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="rate-limiting-429-error"></a>Limitazione della frequenza (errore 429)

Potrebbe essere visualizzato un codice di errore 429 o il testo dell'errore "frequenza richieste è grande" anche se sono state ridotte le impostazioni ai valori minimi. Gli scenari seguenti possono causare la limitazione della frequenza:

* **La velocità effettiva allocata alla tabella è inferiore a 6.000 [unità richiesta](./request-units.md)**. Anche con le impostazioni minime, Spark può scrivere a una frequenza di circa 6.000 unità richiesta. Se è stato effettuato il provisioning di una tabella in uno spazio di spazio dei prezzi con una velocità effettiva condivisa, è possibile che la tabella disponga di meno di 6.000 UR disponibili in fase di esecuzione.

    Assicurarsi che la tabella di cui si sta eseguendo la migrazione disponga di almeno 6.000 UR disponibili quando si esegue la migrazione. Se necessario, allocare unità richiesta dedicate a tale tabella.

* **Asimmetria dati eccessiva con volume di dati di grandi dimensioni**. Se si dispone di una grande quantità di dati di cui eseguire la migrazione in una tabella specifica, ma è presente una differenza significativa nei dati (ovvero, viene scritto un numero elevato di record per lo stesso valore della chiave di partizione), è possibile che si verifichi una limitazione della frequenza anche in presenza di diverse [unità richiesta](./request-units.md) di cui è stato effettuato il provisioning nella tabella. Le unità di richiesta sono divise in modo uniforme tra le partizioni fisiche e la notevole asimmetria dei dati può causare un collo di bottiglia di richieste a una singola partizione.

    In questo scenario, ridurre le impostazioni della velocità effettiva minima in Spark e forzare l'esecuzione lenta della migrazione. Questo scenario può essere più comune quando si esegue la migrazione di tabelle di riferimento o di controllo, in cui l'accesso è meno frequente e l'inclinazione può essere elevata. Tuttavia, se in un altro tipo di tabella è presente un'asimmetria significativa, potrebbe essere necessario esaminare il modello di dati per evitare problemi di partizione a caldo per il carico di lavoro durante le operazioni di stato stazionario.

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Procedure consigliate per le chiavi di partizione](partitioning-overview.md#choose-partitionkey)
* [Stimare ur/sec usando Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
* [Scalabilità elastica in Azure Cosmos DB API Cassandra](manage-scale-cassandra.md)
