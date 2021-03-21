---
title: Spring data Azure Cosmos DB V3 per le risorse e le note sulla versione dell'API SQL
description: Informazioni sull'API Spring data Azure Cosmos DB V3 per SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 3c740aa00b158c7ddbca3e4f61d79e37978223c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493612"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring data Azure Cosmos DB V3 per l'API Core (SQL): Note sulla versione e risorse
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
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Esecuzione bulk - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Spring data Azure Cosmos DB versione 3 per core (SQL) consente agli sviluppatori di usare Azure Cosmos DB nelle applicazioni Spring. Spring data Azure Cosmos DB espone l'interfaccia Spring data per la modifica di database e raccolte, l'utilizzo di documenti e l'emissione di query. Entrambe le API Sync e async (Reactive) sono supportate nello stesso artefatto Maven. 

> [!IMPORTANT]
> Spring data Azure Cosmos DB presenta una dipendenza dal framework Spring data.
> 
> Azure-Spring-data-Cosmos versioni da 3.0.0 a 3.4.0 supportano Spring data Versions 2,2 e 2,3.
> 
> Azure-Spring-data-Cosmos versioni 3.5.0 e versioni successive supportano Spring data Versions 2.4.3 e versioni successive.
>

[Spring Framework](https://spring.io/projects/spring-framework) è un modello di programmazione e configurazione che semplifica lo sviluppo di applicazioni Java. Spring semplifica il "plumbing" delle applicazioni usando l'inserimento di dipendenze. Molti sviluppatori come Spring perché rendono più semplice la creazione e il test di applicazioni. [Spring boot](https://spring.io/projects/spring-boot) estende questa gestione del plumbing con un occhio verso lo sviluppo di applicazioni Web e microservizi. [Spring data](https://spring.io/projects/spring-data) è un modello di programmazione e un Framework per l'accesso a archivi dati come Azure Cosmos DB dal contesto di un'applicazione Spring o Spring boot. 

È possibile usare Spring data Azure Cosmos DB nelle applicazioni [cloud della primavera di Azure](https://azure.microsoft.com/services/spring-cloud/) .

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

  Per iniziare subito a usare Spring data Azure Cosmos DB, seguire la [Guida introduttiva a Spring boot](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). L'approccio di Spring boot Starter è il modo consigliato per iniziare a usare Spring data Azure Cosmos DB Connector.

  In alternativa, è possibile aggiungere la dipendenza Spring data Azure Cosmos DB al `pom.xml` file come illustrato di seguito:

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
|**Operazioni preliminari** | [Guida introduttiva: creare un'app Spring data Azure Cosmos DB per gestire Azure Cosmos DB dati dell'API SQL](./create-sql-api-spring-data.md) <br> [Repository GitHub con codice di avvio rapido](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Esempi di codice di base** | [Azure Cosmos DB: Spring data Azure Cosmos DB esempi per l'API SQL](sql-api-spring-data-sdk-samples.md) <br> [Repository GitHub con codice di esempio](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Suggerimenti per incrementare le prestazioni**| [Suggerimenti sulle prestazioni per Java SDK v4 (applicabile ai dati di Spring)](performance-tips-java-sdk-v4-sql.md)| 
| **Risoluzione dei problemi** | [Risoluzione dei problemi di Java SDK v4 (applicabile ai dati di Spring)](troubleshoot-java-sdk-v4-sql.md) | 
| **Workshop e laboratori su Azure Cosmos DB** |[Home page dei workshop per Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Note aggiuntive

* Spring data Azure Cosmos DB supporta Java JDK 8 e Java JDK 11.
* Spring data 2,3 è attualmente supportata, Spring data 2,4 non è attualmente supportata.

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Scopri di più sul [framework Spring](https://spring.io/projects/spring-framework).

Scopri di più su [Spring boot](https://spring.io/projects/spring-boot).

Scopri di più sui [dati primaverili](https://spring.io/projects/spring-data).