---
title: Note sulla versione e risorse di Spring Data Azure Cosmos DB v2 per l'API SQL
description: Informazioni sull'API Spring Data Azure Cosmos DB v2 per SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra ogni versione di SQL Async Java SDK Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4cd92efb7b6596288e4b6ef8e81f82f775a24e01
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363445"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>API Spring Data Azure Cosmos DB v2 for Core (SQL): note sulla versione e risorse
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

 Spring Data Azure Cosmos DB versione 2 per Core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring Data Azure Cosmos DB l'interfaccia Spring Data per la modifica di database e raccolte, l'uso di documenti e l'emissione di query. Entrambe le API Sync e Async (Reattive) sono supportate nello stesso artefatto Maven. 

[Spring Framework è](https://spring.io/projects/spring-framework) un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Spring semplifica il "plumbing" delle applicazioni usando l'inserimento delle dipendenze. Molti sviluppatori come Spring rendono più semplice la compilazione e il test di applicazioni. [Spring Boot](https://spring.io/projects/spring-boot) estende questa gestione dell'impianto plumbing con attenzione allo sviluppo di applicazioni Web e microservizi. [Spring Data](https://spring.io/projects/spring-data) è un modello di programmazione per l'accesso agli archivi dati come Azure Cosmos DB dal contesto di un'applicazione Spring o Spring Boot dati. 

È possibile usare Spring Data Azure Cosmos DB nelle applicazioni [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) applicazioni.

> [!IMPORTANT]  
> Queste note sulla versione sono relative alla versione 2 di Spring Data Azure Cosmos DB. È possibile trovare le [note sulla versione 3 qui.](sql-api-sdk-java-spring-v3.md) 
>
> Spring Data Azure Cosmos DB supporta solo l'API SQL.
>
> Per informazioni su Spring Data in altre API Azure Cosmos DB, vedere gli articoli seguenti:
> * [Spring Data per Apache Cassandra con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Vuoi iniziare rapidamente?
> 1. Installare il [runtime Java minimo supportato, JDK 8,](/java/azure/jdk/)in modo da poter usare l'SDK.
> 2. Creare un'app Spring Data Azure Cosmos DB usando [l'applicazione iniziale](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). È semplice.
> 3. Vedere la guida [per gli sviluppatori Azure Cosmos DB Spring Data,](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)che illustra le richieste di Azure Cosmos DB base.
>
> È possibile creare rapidamente Spring Boot starter usando [Spring Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Risorse

| Risorsa | Collegamento |
|---|---|
| **Download dell'SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentazione sull'API** | [Documentazione di riferimento Azure Cosmos DB Spring Data]() |
|**Contribuire all'SDK** | [Repository Spring Data Azure Cosmos DB su GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Azure Cosmos DB Spring Boot libreria client starter per Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Esempio di app Spring TODO con Azure Cosmos DB**| [Esperienza Java end-to-end nel servizio app linux (parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guida per gli sviluppatori** | [Guida per sviluppatori di Spring Data Azure Cosmos DB](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Uso di Starter** | [Come usare Spring Boot Starter con l'API AZURE COSMOS DB SQL](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repository GitHub per Azure Cosmos DB Spring Boot Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Esempio con Servizio app di Azure** | [Come usare Spring e Cosmos DB con Servizio app in Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Esempio di app TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Cronologia delle versioni

### <a name="230-may-21-2020"></a>2.3.0 (21 maggio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Spring Boot alla versione 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 maggio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Azure Cosmos DB alla versione 3.7.3.
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Contiene correzioni delle perdite di memoria e aggiornamenti della versione di Netty Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 aprile 2020)
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione del `allowTelemetry` flag da prendere in considerazione da `CosmosDbConfig` .
* Corregge la `TTL` proprietà nel contenitore.

### <a name="223-february-25-2020"></a>2.2.3 (25 febbraio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge nuovo in `findAll` base all'API della chiave di partizione.
* Aggiorna Azure Cosmos DB alla versione 3.7.0.
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Corregge `collectionName`  ->  `containerName` .
* Correzioni `entityClass` e `domainClass`  ->  `domainType` .
* Correzione di "Restituire la raccolta di entità salvata dal repository anziché dall'entità di input".

### <a name="2110-february-25-2020"></a>2.1.10 (25 febbraio 2020)
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione backports per "Restituire la raccolta di entità salvata dal repository anziché dall'entità di input".

### <a name="222-january-15-2020"></a>2.2.2 (15 gennaio 2020)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Azure Cosmos DB alla versione 3.6.0.
#### <a name="key-bug-fixes"></a>Principali correzioni di bug

### <a name="221-december-31-2019"></a>2.2.1 (31 dicembre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna Azure Cosmos DB SDK alla versione 3.5.0.
* Aggiunge il campo di annotazione per abilitare o disabilitare la creazione automatica della raccolta.
* Migliora la gestione delle eccezioni. Espone tramite `CosmosClientException` `CosmosDBAccessException` .
* Espone e `requestCharge` `activityId` tramite `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* L'aggiornamento di SDK 3.5.0 corregge "Eccezione quando l'intestazione della risposta HTTP Cosmos DB è maggiore di 8192 byte", "ConsistencyPolicy.defaultConsistencyLevel() ha esito negativo in Delimitazione staleness e Prefisso coerente".
* Corregge il `findById` comportamento del metodo. In precedenza, questo metodo restituisce vuoto se l'entità non è stata trovata anziché generare un'eccezione.
* Correzione di un bug in cui l'ordinamento non è stato applicato nella pagina successiva quando `CosmosPageRequest` è stato usato.

### <a name="219-december-26-2019"></a>2.1.9 (26 dicembre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge il campo di annotazione per abilitare o disabilitare la creazione automatica della raccolta.
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
*  Corregge il `findById` comportamento del metodo. In precedenza, questo metodo restituisce vuoto se l'entità non è stata trovata anziché generare un'eccezione.

### <a name="220-october-21-2019"></a>2.2.0 (21 ottobre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Completare il supporto di Reactive Cosmos Repository.
* Azure Cosmos DB supporto della stringa di diagnostica delle richieste e delle metriche di query.
* Azure Cosmos DB aggiornamento della versione dell'SDK alla versione 3.3.1.
* Aggiornamento della versione di Spring Framework alla versione 5.2.0.RELEASE.
* Aggiornamento della versione di Spring Data Commons alla versione 2.2.0.RELEASE.
* Aggiunge `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` le API e .
* Rimuove la dipendenza da azure-documentdb.
* Rebrands DocumentDB in Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione di "L'ordinamento genera un'eccezione quando pageSize è minore del totale degli elementi nel repository".

### <a name="218-october-18-2019"></a>2.1.8 (18 ottobre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Deprecazione delle API di DocumentDB.
* Aggiunge `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` le API e .
* Aggiunge il blocco ottimistico in base a `_etag` .
* Abilita l'espressione SpEL per il nome della raccolta documenti.
* Aggiunge `ObjectMapper` miglioramenti.

### <a name="217-october-18-2019"></a>2.1.7 (18 ottobre 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge Azure Cosmos DB dipendenza di SDK versione 3.
* Aggiunge reactive Cosmos Repository.
* Aggiorna l'implementazione di `DocumentDbTemplate` per usare Azure Cosmos DB SDK versione 3.
* Aggiunge altre modifiche di configurazione per il supporto di Reactive Cosmos Repository.

### <a name="212-march-19-2019"></a>2.1.2 (19 marzo 2019)
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Rimuove `applicationInsights` la dipendenza per:
    * Rischio potenziale di inquinamento delle dipendenze.
    * Incompatibilità di Java 11.
    * Evitare un potenziale impatto sulle prestazioni della CPU e/o della memoria.

### <a name="207-march-20-2019"></a>2.0.7 (20 marzo 2019)
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Backport rimuove `applicationInsights` la dipendenza per:
    * Rischio potenziale di inquinamento delle dipendenze.
    * Incompatibilità di Java 11.
    * Evitare un potenziale impatto sulle prestazioni della CPU e/o della memoria.

### <a name="211-march-7-2019"></a>2.1.1 (7 marzo 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna la versione principale alla 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 marzo 2019)
#### <a name="new-features"></a>Nuove funzionalità
* Ignorare tutte le eccezioni dai dati di telemetria.

### <a name="210-december-17-2018"></a>2.1.0 (17 dicembre 2018)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiorna la versione alla versione 2.1.0 per risolvere il problema.

### <a name="205-september-13-2018"></a>2.0.5 (13 settembre 2018)
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge le parole `exists` chiave e `startsWith` .
* Aggiorna il file Leggimi.
#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Corregge "Can't call self href directly for Entity".
* Corregge "findAll avrà esito negativo se la raccolta non viene creata".

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (versione preliminare) (23 agosto 2018)
#### <a name="new-features"></a>Nuove funzionalità
* Rinomina il pacchetto da documentdb a cosmosdb.
* Aggiunge una nuova funzionalità della parola chiave del metodo di query. Sono ora supportate 16 parole chiave dell'API SQL.
* Aggiunge una nuova funzionalità di query con paging e ordinamento.
* Semplifica la configurazione di spring-data-cosmosdb.
* Aggiunge `deleteCollection` `deleteAll` le API e .

#### <a name="key-bug-fixes"></a>Principali correzioni di bug
* Correzione di bug e mitigazione dei difetti.

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Altre informazioni su [Spring Framework.](https://spring.io/projects/spring-framework)

Altre informazioni su [Spring Boot](https://spring.io/projects/spring-boot).

Altre informazioni su [Spring Data.](https://spring.io/projects/spring-data)