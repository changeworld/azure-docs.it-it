---
title: Usare l'identità gestita per connettersi Azure SQL'app Azure Spring Cloud app
description: Configurare l'identità gestita per connettersi Azure SQL a un'app Azure Spring Cloud app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378789"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Usare un'identità gestita per connettersi database SQL di Azure a un'app Azure Spring Cloud app

**Questo articolo si applica a:** ✔️ Java

Questo articolo illustra come creare un'identità gestita per un'app Azure Spring Cloud e usarla per accedere database SQL di Azure.

[database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è il servizio di database relazionale, scalabile e intelligente creato per il cloud. È sempre aggiornato, con funzionalità automatizzate e basate sull'intelligenza artificiale che ottimizzano le prestazioni e la durabilità. Le opzioni di calcolo serverless e di archiviazione Hyperscale ridimensionano automaticamente le risorse su richiesta, quindi è possibile concentrarsi sulla creazione di nuove applicazioni senza doversi preoccupare delle dimensioni di archiviazione o della gestione delle risorse.

## <a name="prerequisites"></a>Prerequisiti
In questo esempio vengono utilizzate le risorse seguenti.
* Seguire [l'esercitazione di Spring Data JPA](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) per effettuare il provisioning di un database SQL di Azure e fare in modo che funzioni con un'app Java in locale
* Seguire [l'Azure Spring Cloud di identità gestita assegnata dal](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) sistema per effettuare il provisioning di un'app Azure Spring Cloud con istanza gestita abilitata

## <a name="grant-permission-to-the-managed-identity"></a>Concedere l'autorizzazione all'identità gestita
Connettersi al server SQL ed eseguire la query SQL seguente:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Segue <MIName> la regola: `<service instance name>/apps/<app name>` , ad esempio myspringcloud/apps/sqldemo. È anche possibile eseguire una query su MIName con l'interfaccia della riga di comando di Azure:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Configurare l'app Java per l'uso dell'identità gestita
Aprire il `src/main/resources/application.properties` file e aggiungere alla fine della riga `Authentication=ActiveDirectoryMSI;` seguente. Assicurarsi di usare il valore corretto per $AZ_DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Compilare e distribuire l'app in Azure Spring Cloud
Ricompilare l'app e distribuirla nell Azure Spring Cloud app di cui è stato effettuato il provisioning nel secondo punto elenco in Prerequisiti. È ora disponibile un'applicazione Spring Boot, autenticata da un'identità gestita, che usa JPA per archiviare e recuperare i dati da un database SQL di Azure in Azure Spring Cloud.

## <a name="next-steps"></a>Passaggi successivi

* [Come accedere a un BLOB di archiviazione con un'identità gestita in Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Come abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Altre informazioni sulle identità gestite per le risorse di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autenticazione di Azure Spring Cloud con Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md)