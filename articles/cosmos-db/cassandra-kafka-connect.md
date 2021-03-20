---
title: Integrare Apache Kafka e Azure Cosmos DB API Cassandra usando Kafka Connect
description: Informazioni su come inserire dati da Kafka a Azure Cosmos DB API Cassandra con DataStax Apache Kafka Connector
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803630"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Inserire i dati da Apache Kafka in Azure Cosmos DB API Cassandra usando Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Le applicazioni Cassandra esistenti possono interagire facilmente con la [Azure Cosmos DB API Cassandra](cassandra-introduction.md) grazie alla [compatibilità del driver CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Questa funzionalità viene usata per l'integrazione con piattaforme di streaming, ad esempio [Apache Kafka](https://kafka.apache.org/) e per portare i dati in Azure Cosmos DB.

I dati in Apache Kafka (argomenti) sono utili solo se utilizzati da altre applicazioni o inseriti in altri sistemi. È possibile creare una soluzione usando le API [Kafka producer/consumer](https://kafka.apache.org/documentation/#api) [usando un linguaggio e un SDK client di propria scelta](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect fornisce una soluzione alternativa. Si tratta di una piattaforma per trasmettere i dati tra Apache Kafka e altri sistemi in modo scalabile e affidabile. Poiché Kafka Connect supporta i connettori di scaffale che includono Cassandra, non è necessario scrivere codice personalizzato per integrare Kafka con Azure Cosmos DB API Cassandra. 

In questo articolo verrà usato il connettore open source di [DataStax Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html), che funziona su Kafka Connect Framework per inserire i record da un argomento Kafka in righe di una o più tabelle Cassandra. Nell'esempio viene fornita una configurazione riutilizzabile utilizzando Docker Compose. Questa operazione è molto utile perché consente di eseguire il bootstrap di tutti i componenti richiesti localmente con un unico comando. Questi componenti includono Kafka, Zookeeper, Kafka Connect Worker e l'applicazione di esempio per il generatore di dati.

Ecco una suddivisione dei componenti e delle relative definizioni di servizio: è possibile fare riferimento al `docker-compose` file completo [nel repository GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka e Zookeeper usano immagini [debezium](https://hub.docker.com/r/debezium/kafka/) .
- Per l'esecuzione come contenitore Docker, il connettore DataStax Apache Kafka viene cotto sopra un'immagine Docker esistente, [debezium/Connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Questa immagine include un'installazione di Kafka e delle librerie di connessione Kafka, in modo da semplificare l'aggiunta di connettori personalizzati. È possibile fare riferimento a [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- Il `data-generator` servizio semina i dati generati in modo casuale (JSON) nell' `weather-data` argomento Kafka. È possibile fare riferimento al codice e `Dockerfile` nel [repository GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Prerequisiti

* [Effettuare il provisioning di un account API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Usare cqlsh o la shell ospitata per la convalida](cassandra-support.md#hosted-cql-shell-preview)

* Installare [Docker](https://docs.docker.com/get-docker/) e [Docker compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Creare spazio per le tabelle e avviare la pipeline di integrazione

Usando il portale di Azure, creare lo spazio delle applicazioni Cassandra e le tabelle necessarie per l'applicazione demo.

> [!NOTE]
> Usare lo stesso spazio dei nomi e lo stesso nome di tabella indicati di seguito

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Clonare il repository GitHub:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Avviare tutti i servizi:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Il download e l'avvio dei contenitori potrebbero richiedere alcuni minuti: si tratta solo di un processo una volta.

Per verificare se sono stati avviati tutti i contenitori:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

L'applicazione del generatore di dati avvierà la distribuzione dei dati nell' `weather-data` argomento di Kafka. È anche possibile eseguire un controllo di integrità rapido per confermare. Esaminare il contenitore Docker che esegue il ruolo di lavoro Kafka Connect:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Una volta rilasciata la shell di contenitori, è sufficiente avviare il consueto processo del consumer della console Kafka e dovrebbe essere visualizzato il flusso di dati meteorologici (in formato JSON).

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Installazione del connettore di sink Cassandra

Copiare il contenuto JSON riportato di seguito in un file. è possibile denominarlo `cassandra-sink-config.json` . Sarà necessario aggiornarlo in base alla configurazione e la parte restante di questa sezione fornirà informazioni aggiuntive su questo argomento.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Di seguito è riportato un riepilogo degli attributi:

**Connettività di base**

- `contactPoints`: immettere il punto di contatto per Cosmos DB Cassandra
- `loadBalancing.localDc`: immettere l'area per Cosmos DB account, ad esempio Asia sudorientale
- `auth.username`: immettere il nome utente
- `auth.password`: immettere la password
- `port`: immettere il valore della porta, ovvero `10350` Not `9042` . lasciarlo invariato

**Configurazione SSL**

Azure Cosmos DB impone la [connettività sicura tramite SSL](database-security.md) e il connettore di connessione Kafka supporta anche SSL.

- `ssl.keystore.path`: percorso dell'archivio chiavi JDK nel contenitore- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: Password dell'archivio chiavi JDK (impostazione predefinita)
- `ssl.hostnameValidation`: La convalida del nome host del nodo è attiva
- `ssl.provider`: `JDK` viene usato come provider SSL

**Parametri generici**

- `key.converter`: Viene usato il convertitore di stringhe `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: poiché i dati negli argomenti Kafka sono JSON, si usa `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Poiché al payload JSON non è associato alcuno schema (ai fini dell'app demo), è necessario indicare a Kafka Connect di non cercare uno schema impostando questo attributo su `false` . Questa operazione comporterà errori.

### <a name="install-the-connector"></a>Installare il connettore

Installare il connettore usando l'endpoint REST Connect di Kafka:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Per controllare lo stato:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Se tutto va bene, il connettore dovrebbe iniziare a tessere la sua magia. Deve eseguire l'autenticazione a Azure Cosmos DB e iniziare a inserire i dati dall'argomento Kafka ( `weather-data` ) nelle tabelle Cassandra, `weather.data_by_state``weather.data_by_station`

È ora possibile eseguire query sui dati nelle tabelle. Passare alla portale di Azure, visualizzare la shell CQL ospitata per l'account di Azure Cosmos DB.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Aprire CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Eseguire query sui dati da Azure Cosmos DB

Controllare le `data_by_state` `data_by_station` tabelle e. Ecco alcune query di esempio per iniziare:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md) 
* [Procedure consigliate per le chiavi di partizione](partitioning-overview.md#choose-partitionkey)
* [Stimare ur/sec usando gli articoli Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
