---
title: Risolvere gli errori comuni nell'Azure Cosmos DB API Cassandra
description: In questo articolo vengono illustrati i problemi comuni nell'API Cassandra Azure Cosmos DB e come risolverli.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967354"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Risolvere i problemi comuni nell'Azure Cosmos DB API Cassandra

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Il API Cassandra in [Azure Cosmos DB](./introduction.md) è un livello di compatibilità che fornisce il [supporto del protocollo wire](cassandra-support.md) per il database Apache Cassandra open source.

Questo articolo descrive gli errori comuni e le soluzioni per le applicazioni che usano il Azure Cosmos DB API Cassandra. Se l'errore non è elencato e si verifica un errore durante l'esecuzione di un' [operazione supportata in Cassandra](cassandra-support.md), ma l'errore non è presente quando si usa Apache Cassandra nativo, [creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>In qualità di servizio cloud nativo completamente gestito, Azure Cosmos DB [garantisce disponibilità, velocità effettiva e coerenza](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) per la API Cassandra. Il API Cassandra facilita anche le operazioni della piattaforma di manutenzione zero e l'applicazione di patch con zero tempi di inattività.
>
>Queste garanzie non sono possibili nelle implementazioni precedenti di Apache Cassandra, quindi molte delle operazioni di API Cassandra back-end differiscono da Apache Cassandra. Si consiglia di usare impostazioni e approcci specifici per evitare errori comuni.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Questo errore è un'eccezione wrapper di primo livello con un numero elevato di possibili cause ed eccezioni interne, molti dei quali possono essere correlati ai client.

Cause e soluzioni comuni:

- **Timeout di inattività di Azure LoadBalancers**: questo problema potrebbe anche essere manifesto come `ClosedConnectionException` . Per risolvere il problema, impostare l'impostazione Keep-Alive nel driver (vedere [abilitare Keep-Alive per il driver Java](#enable-keep-alive-for-the-java-driver)) e aumentare le impostazioni Keep-Alive nel sistema operativo o modificare il [timeout di inattività in Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Esaurimento delle risorse dell'applicazione client**: assicurarsi che i computer client dispongano di risorse sufficienti per completare la richiesta.

## <a name="cant-connect-to-a-host"></a>Impossibile connettersi a un host

È possibile che venga visualizzato questo errore: "Impossibile connettersi a un host, pianificare un nuovo tentativo in 600000 millisecondi".

Questo errore potrebbe essere causato dall'esaurimento del Network Address Translation di origine (SNAT) sul lato client. Eseguire la procedura descritta in [SNAT per le connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md) per escludere questo problema.

L'errore potrebbe anche essere un problema di timeout di inattività in cui il servizio di bilanciamento del carico di Azure ha quattro minuti di timeout di inattività per impostazione predefinita. Vedere [timeout di inattività](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)del servizio di bilanciamento del carico. [Abilitare Keep-Alive per il driver Java](#enable-keep-alive-for-the-java-driver) e impostare l' `keepAlive` intervallo del sistema operativo su un numero inferiore a quattro minuti.

## <a name="overloadedexception-java"></a>Overloadexception (Java)

Le richieste sono limitate perché il numero totale di unità richiesta utilizzate è superiore al numero di unità richiesta di cui è stato effettuato il provisioning nella tabella o nello spazio.

Valutare la possibilità di ridimensionare la velocità effettiva assegnata a una tabella o a uno spazio di portale di Azure (vedere [ridimensionare in modo elastico un account Azure Cosmos DB API Cassandra](manage-scale-cassandra.md)) o implementare criteri di ripetizione.

Per Java, vedere esempi di tentativi per il [driver v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e il [driver v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Vedere anche [estensioni di Azure Cosmos Cassandra per Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>Overloadexception, nonostante la velocità effettiva sufficiente

Il sistema sembra limitare le richieste anche se viene effettuato il provisioning di una velocità effettiva sufficiente per il volume della richiesta o il costo unitario della richiesta utilizzato. Le cause possono essere due:

- **Operazioni a livello di schema**: il API Cassandra implementa un budget della velocità effettiva del sistema per le operazioni a livello di schema (Create Table, ALTER TABLE, drop table). Questo budget dovrebbe essere sufficiente per le operazioni dello schema in un sistema di produzione. Tuttavia, se si dispone di un numero elevato di operazioni a livello di schema, è possibile che si superi questo limite.

  Poiché il budget non è controllato dall'utente, provare a ridurre il numero di operazioni dello schema eseguite. Se questa azione non risolve il problema o non è fattibile per il carico di lavoro, [creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Asimmetria dei dati**: quando viene effettuato il provisioning della velocità effettiva nel API Cassandra, è divisa equamente tra le partizioni fisiche e ogni partizione fisica ha un limite superiore. Se si dispone di una quantità elevata di dati inseriti o sottoposti a query da una determinata partizione, è possibile che la frequenza sia limitata anche se si effettua il provisioning di una grande quantità di velocità effettiva complessiva (unità richiesta) per la tabella.

  Esaminare il modello di dati e assicurarsi che non si disponga di un'eccessiva asimmetria che potrebbe causare l'uso di partizioni a caldo.

## <a name="intermittent-connectivity-errors-java"></a>Errori di connettività intermittenti (Java)

La connessione viene eliminata o scade in modo imprevisto.

I driver Apache Cassandra per Java forniscono due criteri di riconnessione nativi: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . Il valore predefinito è `ExponentialReconnectionPolicy`. Tuttavia, per Azure Cosmos DB API Cassandra, è consigliabile utilizzare `ConstantReconnectionPolicy` un ritardo di due secondi.

Vedere la [documentazione relativa al driver Java 4. x](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), la [documentazione per il driver Java 3. x](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)o la pagina relativa alla [configurazione di ReconnectionPolicy per gli esempi di driver Java](#configure-reconnectionpolicy-for-the-java-driver) .

## <a name="error-with-load-balancing-policy"></a>Errore con i criteri di bilanciamento del carico

È possibile che sia stato implementato un criterio di bilanciamento del carico in V3. x del driver DataStax Java con codice simile al seguente:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Se il valore di `withLocalDc()` non corrisponde al datacenter del punto di contatto, è possibile che si verifichi un errore intermittente: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

Implementare [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Per farlo funzionare, potrebbe essere necessario aggiornare DataStax usando il codice seguente:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Il conteggio ha esito negativo in una tabella di grandi dimensioni

Quando si esegue `select count(*) from table` o simile per un numero elevato di righe, si verifica il timeout del server.

Se si usa un client CQLSH locale, modificare le `--connect-timeout` impostazioni di o `--request-timeout` . Vedere [cqlsh: la shell di CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Se il conteggio scade ancora, è possibile ottenere un conteggio dei record dalla Azure Cosmos DB di telemetria back-end passando alla scheda metriche della portale di Azure, selezionando la metrica `document count` e quindi aggiungendo un filtro per il database o la raccolta (l'analogia della tabella in Azure Cosmos DB). È quindi possibile passare il mouse sul grafico risultante per il punto nel tempo in cui si desidera un conteggio del numero di record.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="Visualizzazione metriche":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Configurare ReconnectionPolicy per il driver Java

### <a name="version-3x"></a>Versione 3.x

Per la versione 3. x del driver Java, configurare i criteri di riconnessione quando si crea un oggetto cluster:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Versione 4. x

Per la versione 4. x del driver Java, configurare i criteri di riconnessione eseguendo l'override delle impostazioni nel `reference.conf` file:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Abilita keep-alive per il driver Java

### <a name="version-3x"></a>Versione 3.x

Per la versione 3. x del driver Java, impostare keep-alive quando si crea un oggetto cluster e quindi assicurarsi che il Keep-Alive sia [abilitato nel sistema operativo](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Versione 4. x

Per la versione 4. x del driver Java, impostare Keep-Alive eseguendo l'override delle impostazioni in `reference.conf` e quindi assicurarsi che il Keep-Alive sia [abilitato nel sistema operativo](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [funzionalità supportate](cassandra-support.md) nel API Cassandra di Azure Cosmos DB.
- Informazioni su come [eseguire la migrazione da Apache Cassandra nativo a Azure Cosmos DB API Cassandra](cassandra-migrate-cosmos-db-databricks.md).
