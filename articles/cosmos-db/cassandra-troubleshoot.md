---
title: Risolvere gli errori comuni in Azure Cosmos DB API Cassandra
description: In questo documento vengono illustrate le modalità di risoluzione dei problemi comuni riscontrati in Azure Cosmos DB API Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: c969e4fac3ae30088cfe47a7b0edff22c578cb8b
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802372"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Risolvere i problemi comuni in Azure Cosmos DB API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra in Azure Cosmos DB è un livello di compatibilità, che fornisce il [supporto del protocollo wire](cassandra-support.md) per il popolare database Apache Cassandra open source ed è alimentato da [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). In qualità di servizio cloud nativo completamente gestito, Azure Cosmos DB [garantisce disponibilità, velocità effettiva e coerenza](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) per API Cassandra. Queste garanzie non sono possibili nelle implementazioni legacy di Apache Cassandra. API Cassandra facilita anche le operazioni della piattaforma di manutenzione zero e l'applicazione di patch senza tempi di inattività. Di conseguenza, molte delle operazioni di back-end sono diverse da Apache Cassandra, quindi è consigliabile usare impostazioni e approcci specifici per evitare errori comuni. 

Questo articolo descrive gli errori comuni e le soluzioni per le applicazioni che utilizzano Azure Cosmos DB API Cassandra.

## <a name="common-errors-and-solutions"></a>Errori comuni e soluzioni

| Errore               |  Descrizione             | Soluzione  |
|---------------------|--------------------------|-----------|
| Overloadexception (Java) | Il numero totale di unità richiesta utilizzate è superiore a quello delle unità richiesta di cui è stato effettuato il provisioning nello spazio di o nella tabella. Quindi, le richieste sono limitate. | Provare a ridimensionare la velocità effettiva assegnata a una tabella o a uno spazio di portale di Azure (vedere [qui](manage-scale-cassandra.md) per le operazioni di ridimensionamento in API Cassandra) oppure è possibile implementare un criterio di ripetizione dei tentativi. Per Java, vedere esempi di tentativi per [driver v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [driver v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Vedere anche [estensioni di Azure Cosmos Cassandra per Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| Overloadexception (Java) anche con velocità effettiva sufficiente | Il sistema sembra essere limitato alle richieste nonostante la velocità effettiva di cui è stato effettuato il provisioning per il volume di richiesta e/o il costo unitario della richiesta utilizzato  | API Cassandra implementa un budget di velocità effettiva del sistema per le operazioni a livello di schema (CREATE TABLE, ALTER TABLE, DROP TABLE). Questo budget dovrebbe essere sufficiente per le operazioni dello schema in un sistema di produzione. Tuttavia, se si dispone di un numero elevato di operazioni a livello di schema, è possibile che si stia superando questo limite. Poiché questo budget non è controllato dall'utente, è necessario prendere in considerazione la riduzione del numero di operazioni dello schema in esecuzione. Se l'esecuzione di questa azione non risolve il problema o non è fattibile per il carico di lavoro, [creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | Dopo un periodo di inattività dopo il completamento delle connessioni, l'applicazione non è in grado di connettersi| Questo errore potrebbe essere dovuto al timeout di inattività di Azure LoadBalancers, che è 4 minuti. Impostare Mantieni attivo nell'impostazione del driver (vedere di seguito) e aumentare le impostazioni Keep-Alive nel sistema operativo o [modificare il timeout di inattività in Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Altri errori intermittenti di connettività (Java) | La connessione si interrompe o si verifica un timeout imprevisto | I driver Apache Cassandra per Java forniscono due criteri di riconnessione nativi: `ExponentialReconnectionPolicy` e `ConstantReconnectionPolicy` . Il valore predefinito è `ExponentialReconnectionPolicy`. Tuttavia, per Azure Cosmos DB API Cassandra, è consigliabile utilizzare `ConstantReconnectionPolicy` un ritardo di 2 secondi. Vedere la [documentazione driver](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  per il driver Java V4. x e [qui](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) per informazioni aggiuntive su Java 3. x (vedere anche gli esempi seguenti).|

Se l'errore non è elencato in precedenza e si verifica un errore durante l'esecuzione di un' [operazione supportata in API Cassandra, in](cassandra-support.md)cui l'errore *non è presente quando si usa Apache Cassandra nativo*, [creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)

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

