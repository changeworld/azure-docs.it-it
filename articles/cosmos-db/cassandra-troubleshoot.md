---
title: Risolvere gli errori comuni in Azure Cosmos DB API Cassandra
description: In questo documento vengono illustrate le modalità di risoluzione dei problemi comuni riscontrati in Azure Cosmos DB API Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658572"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Risolvere i problemi comuni in Azure Cosmos DB API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra in Azure Cosmos DB è un livello di compatibilità, che fornisce il [supporto del protocollo wire](cassandra-support.md) per il popolare database Apache Cassandra open source ed è alimentato da [Azure Cosmos DB](./introduction.md). In qualità di servizio cloud nativo completamente gestito, Azure Cosmos DB [garantisce disponibilità, velocità effettiva e coerenza](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) per API Cassandra. Queste garanzie non sono possibili nelle implementazioni legacy di Apache Cassandra. API Cassandra facilita anche le operazioni della piattaforma di manutenzione zero e l'applicazione di patch senza tempi di inattività. Di conseguenza, molte delle operazioni di back-end sono diverse da Apache Cassandra, quindi è consigliabile usare impostazioni e approcci specifici per evitare errori comuni. 

Questo articolo descrive gli errori comuni e le soluzioni per le applicazioni che utilizzano Azure Cosmos DB API Cassandra. Se l'errore non è elencato di seguito e si verifica un errore durante l'esecuzione di un' [operazione supportata in API Cassandra, in](cassandra-support.md)cui l'errore *non è presente quando si usa Apache Cassandra nativo*, [creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Si tratta di un'eccezione wrapper di primo livello con un numero elevato di possibili cause ed eccezioni interne, molte delle quali possono essere correlate ai client. 
### <a name="solution"></a>Soluzione
Di seguito sono riportate alcune delle cause e delle soluzioni più diffuse: 
- Timeout di inattività di Azure LoadBalancers: può anche essere manifesto come `ClosedConnectionException` . Per risolvere il problema, impostare Mantieni attivo nell'impostazione del driver (vedere di [seguito](#enable-keep-alive-for-java-driver)) e aumentare le impostazioni Keep-Alive nel sistema operativo o [modificare il timeout di inattività in Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Esaurimento delle risorse dell'applicazione client:** assicurarsi che i computer client dispongano di risorse sufficienti per completare la richiesta. 

## <a name="cannot-connect-to-host"></a>Impossibile connettersi all'host
È possibile che venga visualizzato questo errore: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Soluzione
Potrebbe trattarsi di un esaurimento SNAT sul lato client. Attenersi alla procedura descritta in [SNAT per le connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md) per escludere questo problema. Potrebbe trattarsi di un problema di timeout di inattività in cui il servizio di bilanciamento del carico di Azure ha 4 minuti di timeout di inattività per impostazione predefinita. Vedere la documentazione al [timeout di inattività](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)del servizio di bilanciamento del carico. Abilitare TCP: Mantieni attivo dalle impostazioni del driver (vedere di [seguito](#enable-keep-alive-for-java-driver)) e impostare l' `keepAlive` intervallo del sistema operativo su un numero inferiore a 4 minuti.

 

## <a name="overloadedexception-java"></a>Overloadexception (Java)
Il numero totale di unità richiesta utilizzate è superiore a quello delle unità richiesta di cui è stato effettuato il provisioning nello spazio di o nella tabella. Quindi, le richieste sono limitate.
### <a name="solution"></a>Soluzione
Provare a ridimensionare la velocità effettiva assegnata a una tabella o a uno spazio di portale di Azure (vedere [qui](manage-scale-cassandra.md) per le operazioni di ridimensionamento in API Cassandra) oppure è possibile implementare un criterio di ripetizione dei tentativi. Per Java, vedere esempi di tentativi per [driver v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [driver v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Vedere anche [estensioni di Azure Cosmos Cassandra per Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>Overloadexception anche con velocità effettiva sufficiente 
Il sistema sembra essere in grado di limitare le richieste nonostante la velocità effettiva di cui viene effettuato il provisioning per il volume di richiesta e/o il costo unitario della richiesta. Esistono due possibili cause di limitazione imprevista della frequenza:
- **Operazioni a livello di schema:** API Cassandra implementa un budget di velocità effettiva del sistema per le operazioni a livello di schema (CREATE TABLE, ALTER TABLE, DROP TABLE). Questo budget dovrebbe essere sufficiente per le operazioni dello schema in un sistema di produzione. Tuttavia, se si dispone di un numero elevato di operazioni a livello di schema, è possibile che si stia superando questo limite. Poiché questo budget non è controllato dall'utente, è necessario prendere in considerazione la riduzione del numero di operazioni dello schema in esecuzione. Se l'esecuzione di questa azione non risolve il problema o non è fattibile per il carico di lavoro, [creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Asimmetria dei dati:** quando viene effettuato il provisioning della velocità effettiva in API Cassandra, è divisa equamente tra le partizioni fisiche e ogni partizione fisica ha un limite superiore. Se si dispone di una quantità elevata di dati inseriti o sottoposti a query da una determinata partizione, è possibile che la frequenza sia limitata nonostante il provisioning di una grande quantità di velocità effettiva complessiva (unità richiesta) per la tabella. Esaminare il modello di dati e assicurarsi che non si disponga di un'eccessiva asimmetria che potrebbe causare la generazione di partizioni critiche. 

## <a name="intermittent-connectivity-errors-java"></a>Errori di connettività intermittenti (Java) 
La connessione viene eliminata o scade in modo imprevisto.

### <a name="solution"></a>Soluzione 
I driver Apache Cassandra per Java forniscono due criteri di riconnessione nativi: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . Il valore predefinito è `ExponentialReconnectionPolicy`. Tuttavia, per Azure Cosmos DB API Cassandra, è consigliabile utilizzare `ConstantReconnectionPolicy` un ritardo di 2 secondi. Vedere la [documentazione del driver](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  per il driver Java V4. x e [qui](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) per informazioni su Java 3. x vedere anche [configurazione di ReconnectionPolicy per esempi di driver Java](#configuring-reconnectionpolicy-for-java-driver) .

## <a name="error-with-load-balancing-policy"></a>Errore con i criteri di bilanciamento del carico

Se è stato implementato un criterio di bilanciamento del carico in V3. x del driver Datastax Java, con codice simile al seguente:

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

Se il valore di `withLocalDc()` non corrisponde al punto di contatto datacenter, è possibile che si verifichi un errore molto intermittente: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Soluzione 
Implementare [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (potrebbe essere necessario aggiornare la versione secondaria di datastax per renderla funzionante):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Conteggio non riuscito su una tabella di grandi dimensioni
Quando è in esecuzione `select count(*) from table` o simile per un numero elevato di righe, si verifica il timeout del server.

### <a name="solution"></a>Soluzione 
Se si usa un client CQLSH locale, è possibile provare a modificare le `--connect-timeout` `--request-timeout` impostazioni o. per ulteriori informazioni, vedere [qui](https://cassandra.apache.org/doc/latest/tools/cqlsh.html). Se questa operazione non è sufficiente e il conteggio scade ancora, è possibile ottenere un conteggio dei record dalla telemetria del back-end Azure Cosmos DB passando alla scheda metriche in portale di Azure, selezionando la metrica `document count` , quindi aggiungendo un filtro per il database o la raccolta (l'analogia della tabella in Azure Cosmos DB). È quindi possibile passare il mouse sul grafico risultante per il punto nel tempo in cui si desidera un conteggio del numero di record.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="Visualizzazione metriche":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Configurazione di ReconnectionPolicy per il driver Java

### <a name="version-3x"></a>Versione 3.x

Per la versione 3. x del driver Java, configurare i criteri di riconnessione durante la creazione di un oggetto cluster:

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

## <a name="enable-keep-alive-for-java-driver"></a>Abilita keep-alive per il driver Java

### <a name="version-3x"></a>Versione 3.x

Per la versione 3. x del driver Java, impostare Keep-Alive durante la creazione di un oggetto cluster e assicurarsi che Keep-Alive sia [abilitato nel sistema operativo](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

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

Per la versione 4. x del driver Java, impostare Keep-Alive eseguendo l'override delle impostazioni in `reference.conf` e verificare che [nel sistema operativo sia abilitato](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)Keep-Alive:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [funzionalità supportate](cassandra-support.md) in Azure Cosmos DB API Cassandra.
- Informazioni su come [eseguire la migrazione da Apache Cassandra nativo a Azure Cosmos DB API Cassandra](cassandra-migrate-cosmos-db-databricks.md)
