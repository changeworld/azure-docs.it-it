---
title: Note sulla versione e risorse di Spring Data Azure Cosmos DB v3 per l'API SQL
description: Informazioni sull'API Spring Data Azure Cosmos DB v3 per SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra ogni versione di SQL Async Java SDK Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363513"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>API Spring Data Azure Cosmos DB v3 for Core (SQL): note sulla versione e risorse
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

Spring Data Azure Cosmos DB versione 3 per Core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring Data Azure Cosmos DB l'interfaccia Spring Data per la modifica di database e raccolte, l'uso di documenti e l'emissione di query. Entrambe le API Sync e Async (Reattive) sono supportate nello stesso artefatto Maven. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB ha una dipendenza dal framework Spring Data.
> 
> Le versioni di azure-spring-data-cosmos da 3.0.0 a 3.4.0 supportano spring data versioni 2.2 e 2.3.
> 
> azure-spring-data-cosmos versioni 3.5.0 e successive supportano spring data versioni 2.4.3 e successive.
>

[Spring Framework è](https://spring.io/projects/spring-framework) un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Spring semplifica il "plumbing" delle applicazioni usando l'inserimento delle dipendenze. Molti sviluppatori come Spring rendono più semplice la compilazione e il test di applicazioni. [Spring Boot](https://spring.io/projects/spring-boot) estende questa gestione dell'impianto plumbing con attenzione allo sviluppo di applicazioni Web e microservizi. [Spring Data](https://spring.io/projects/spring-data) è un modello di programmazione e un framework per l'accesso agli archivi dati come Azure Cosmos DB dal contesto di un'applicazione Spring o Spring Boot dati. 

È possibile usare Spring Data Azure Cosmos DB nelle applicazioni [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) applicazioni.

> [!IMPORTANT]  
> Queste note sulla versione sono relative alla versione 3 di Spring Data Azure Cosmos DB. È possibile trovare [qui le note sulla versione per la versione 2](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB supporta solo l'API SQL.
>
> Per informazioni su Spring Data in altre API di Azure Cosmos DB, vedere questi articoli:
> * [Spring Data per Apache Cassandra con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin con Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Introduzione rapida

  Iniziare a utilizzare Spring Data Azure Cosmos DB seguendo la guida [Spring Boot Starter.](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) L Spring Boot starter è il modo consigliato per iniziare a usare il connettore Spring Data Azure Cosmos DB.

  In alternativa, è possibile aggiungere la dipendenza Spring Data Azure Cosmos DB al file come `pom.xml` illustrato di seguito:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Contenuto utile

| Content | Collegamento |
|---|---|
|**Download dell'SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**Documentazione sull'API** | [Documentazione di riferimento API Java](/java/api/com.azure.spring.data.cosmos) |
|**Contribuire all'SDK** | [Repository centrale di Azure SDK per Java in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Operazioni preliminari** | [Guida introduttiva: Creare un'app Spring Data Azure Cosmos DB per gestire Azure Cosmos DB dati dell'API SQL](./create-sql-api-spring-data.md) <br> [Repository GitHub con codice di avvio rapido](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Esempi di codice di base** | [Azure Cosmos DB: Esempi di Spring Data Azure Cosmos DB per l'API SQL](sql-api-spring-data-sdk-samples.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Suggerimenti per incrementare le prestazioni**| [Suggerimenti sulle prestazioni per Java SDK v4 (applicabile a Spring Data)](performance-tips-java-sdk-v4-sql.md)| 
| **Risoluzione dei problemi** | [Risolvere i problemi relativi a Java SDK v4 (applicabile a Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Workshop e laboratori su Azure Cosmos DB** |[Home page dei workshop per Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Note aggiuntive

* Spring Data Azure Cosmos DB supporta Java JDK 8 e Java JDK 11.
* Spring Data 2.3 è attualmente supportato, Spring Data 2.4 non è attualmente supportato.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Altre informazioni su [Spring Framework.](https://spring.io/projects/spring-framework)

Altre informazioni su [Spring Boot](https://spring.io/projects/spring-boot).

Altre informazioni su [Spring Data.](https://spring.io/projects/spring-data)