---
title: Migrare i dati da PostgreSQL a Azure Cosmos DB account API Cassandra usando Apache Kafka
description: Informazioni su come usare Kafka Connect per sincronizzare i dati da PostgreSQL a Azure Cosmos DB API Cassandra in tempo reale.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203066"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrare i dati da PostgreSQL a Azure Cosmos DB account API Cassandra usando Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra in Azure Cosmos DB è diventata la scelta ideale per i carichi di lavoro aziendali in esecuzione su Apache Cassandra per diversi motivi, ad esempio:

* **Risparmi significativi sui costi:** È possibile risparmiare sui costi con Azure Cosmos DB, che include il costo di macchine virtuali, larghezza di banda ed eventuali licenze Oracle applicabili. Inoltre, non è necessario gestire i Data Center, i server, l'archiviazione SSD, la rete e i costi elettrici.

* **Maggiore scalabilità e disponibilità:** Elimina i singoli punti di errore, una migliore scalabilità e la disponibilità per le applicazioni.

* **Nessun sovraccarico di gestione e monitoraggio:** Come servizio cloud completamente gestito, Azure Cosmos DB Elimina il sovraccarico di gestione e monitoraggio di una miriade di impostazioni.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) è una piattaforma che consente di trasmettere i dati tra [Apache Kafka](https://kafka.apache.org/) e altri sistemi in modo scalabile e affidabile. Supporta diversi connettori fuori piano, il che significa che non è necessario codice personalizzato per integrare sistemi esterni con Apache Kafka.

Questo articolo illustra come usare una combinazione di connettori Kafka per configurare una pipeline di dati per sincronizzare continuamente i record da un database relazionale, ad esempio [PostgreSQL](https://www.postgresql.org/) , per [Azure Cosmos DB API Cassandra](cassandra-introduction.md).

## <a name="overview"></a>Panoramica

Ecco una panoramica di alto livello del flusso end-to-end presentato in questo articolo.

Verrà effettuato il push dei dati nella tabella PostgreSQL a Apache Kafka usando il [connettore PostgreSQL di Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), che è un connettore di **origine** Kafka Connect. Gli inserimenti, gli aggiornamenti o le eliminazioni nei record della tabella PostgreSQL verranno acquisiti come `change data` eventi e inviati agli argomenti di Kafka. Il [connettore Apache Kafka DataStax](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (connettore Kafka Connect **sink** ) costituisce la seconda parte della pipeline. La sincronizzazione dell'argomento Change Data Events from Kafka viene sincronizzata con Azure Cosmos DB API Cassandra tabelle.

> [!NOTE]
> L'uso di funzionalità specifiche del connettore Apache Kafka DataStax consente di eseguire il push dei dati in più tabelle. In questo esempio, il connettore ci aiuterà a salvare in modo permanente i record dei dati delle modifiche in due tabelle Cassandra in grado di supportare requisiti di query diversi.

## <a name="prerequisites"></a>Prerequisiti

* [Effettuare il provisioning di un account API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)
* [Usare cqlsh o la shell ospitata per la convalida](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 o versione successiva
* [Docker](https://www.docker.com/) (facoltativo)

## <a name="base-setup"></a>Configurazione di base

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Configurare il database PostgreSQL se non è già stato fatto. 

Potrebbe trattarsi di un database locale esistente oppure è possibile [scaricarlo e installarne uno](https://www.postgresql.org/download/) nel computer locale. È anche possibile usare un [contenitore Docker](https://hub.docker.com/_/postgres).

Per avviare un contenitore:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Connettersi all'istanza di PostgreSQL usando il [`psql`](https://www.postgresql.org/docs/current/app-psql.html) client:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Creare una tabella per archiviare le informazioni sugli ordini di esempio:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Usando il portale di Azure, creare lo spazio delle applicazioni Cassandra e le tabelle necessarie per l'applicazione demo.

> [!NOTE]
> Usare lo stesso spazio dei nomi e lo stesso nome di tabella indicati di seguito

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Apache Kafka di installazione 

Questo articolo usa un cluster locale, ma è possibile scegliere qualsiasi altra opzione. [Scaricare Kafka](https://kafka.apache.org/downloads), decomprimerlo, avviare il cluster Zookeeper e Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Connettori di installazione

Installare Debezium PostgreSQL e DataStax Apache Kafka Connector. Scaricare l'archivio plug-in del connettore Debezium PostgreSQL. Ad esempio, per scaricare la versione 1.3.0 del connettore (più recente al momento della stesura), usare [questo collegamento](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Scaricare DataStax Apache Kafka Connector da [questo collegamento](https://downloads.datastax.com/#akc).

Decomprimere entrambi gli archivi del connettore e copiare i file JAR nel plug-in [Kafka Connect. Path](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Per informazioni dettagliate, fare riferimento alla documentazione di [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) e [DataStax](https://docs.datastax.com/en/kafka/doc/) .

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Configurare Kafka Connect e avviare la pipeline di dati

### <a name="start-kafka-connect-cluster"></a>Avviare il cluster Kafka Connect

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Avviare l'istanza del connettore PostgreSQL

Salvare la configurazione del connettore (JSON) in un file di esempio `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Per avviare l'istanza del connettore PostgreSQL:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Per eliminare, è possibile usare: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Inserire i dati

La `orders_info` tabella contiene i dettagli dell'ordine, ad esempio ID ordine, ID cliente, città e così via. Popolare la tabella con dati casuali usando il seguente SQL.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Deve inserire 10 record nella tabella. Assicurarsi di aggiornare il numero di record riportato di seguito in base ai `generate_series(1, 10)` requisiti di esempio, per inserire i `100` record, usare `generate_series(1, 100)`

Per confermare:

```bash
select * from retail.orders_info;
```

Controllare gli eventi Change Data Capture nell'argomento Kafka

> [!NOTE]
> Si noti che il nome dell'argomento è il secondo `myserver.retail.orders_info` della [convenzione del connettore](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Verranno visualizzati gli eventi di modifica dei dati in formato JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Avviare l'istanza di DataStax Apache Kafka Connector

Salvare la configurazione del connettore (JSON) in un file di esempio `cassandra-sink-config.json` e aggiornare le proprietà in base all'ambiente in uso.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Per avviare l'istanza del connettore:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Il connettore dovrebbe rientrare in azione e la pipeline end-to-end da PostgreSQL a Azure Cosmos DB sarà operativa.

### <a name="query-azure-cosmos-db"></a>Eseguire query in Azure Cosmos DB

Controllare le tabelle Cassandra in Azure Cosmos DB. Di seguito sono riportate alcune delle query che è possibile provare:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

È possibile continuare a inserire più dati in PostgreSQL e verificare che i record vengano sincronizzati con Azure Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

* [Integrare Apache Kafka e Azure Cosmos DB API Cassandra usando Kafka Connect](cassandra-kafka-connect.md)
* [Integrare Apache Kafka connettersi all'hub eventi di Azure (anteprima) con Debezium for Change Data Capture](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Eseguire la migrazione dei dati da Oracle a Azure Cosmos DB API Cassandra mediante blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md) 
* [Procedure consigliate per le chiavi di partizione](partitioning-overview.md#choose-partitionkey)
* [Stimare ur/sec usando gli articoli Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)

