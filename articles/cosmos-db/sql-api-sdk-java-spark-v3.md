---
title: Azure Cosmos DB Apache Spark 3 note sulla versione e risorse del connettore OLTP per l'API SQL (anteprima)
description: Informazioni sul connettore Azure Cosmos DB Apache Spark 3 OLTP per l'API SQL (anteprima), incluse le date di rilascio, le date di ritiro e le modifiche apportate tra ogni versione dell'SDK java Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368639"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 API del connettore OLTP per Core (SQL) (anteprima): note sulla versione e risorse
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

**Azure Cosmos DB connettore OLTP spark 3 (anteprima)** offre Apache Spark v3 per Azure Cosmos DB'API SQL.
[Azure Cosmos DB](introduction.md) è un servizio di database distribuito a livello globale che consente agli sviluppatori di usare i dati usando un'ampia gamma di API standard, ad esempio SQL, MongoDB, Cassandra, Graph e Table.

> [!Note]
> Questa versione del connettore OLTP Azure Cosmos DB Spark 3 è una build di anteprima.
> Questa compilazione non è stata testata per il carico o le prestazioni.
> Questa build non è consigliata per l'uso negli scenari di produzione.
>

## <a name="documentation"></a>Documentazione

- [Attività iniziali](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [API Catalogo](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Informazioni di riferimento sul parametro di configurazione](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Compatibilità tra versioni

| Connettore     | Spark         | Versione minima di Java | Versioni di Scala supportate |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta.1  | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>Scarica

È possibile usare la coordinata maven del file jar per installare automaticamente il connettore Spark nel Databricks Runtime 8 da Maven: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

È anche possibile eseguire l'integrazione Cosmos DB connettore Spark nel progetto SBT:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Cosmos DB Connettore Spark è disponibile in [Maven Central Repo.](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar)

### <a name="general"></a>Generale

Se si verifica un bug, determinare un problema [qui.](https://github.com/Azure/azure-sdk-for-java/issues/new)

Per suggerire una nuova funzionalità o le modifiche che possono essere apportate, mettere un problema nello stesso modo in cui si farebbe per un bug.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere la [guida introduttiva per l'uso del connettore OLTP Azure Cosmos DB Spark 3.](create-sql-api-spark.md)
