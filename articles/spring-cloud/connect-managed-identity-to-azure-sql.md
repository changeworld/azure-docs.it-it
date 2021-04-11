---
title: Usare l'identità gestita per connettere Azure SQL ad Azure Spring cloud app
description: Configurare l'identità gestita per connettere Azure SQL a un'app cloud di Azure Spring.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3f1cde0bf233c67d02b9b7266ce3b3c8a3696db8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123381"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Usare un'identità gestita per connettere il database SQL di Azure a un'app cloud di Azure Spring

**Questo articolo si applica a:** ✔️ Java

Questo articolo illustra come creare un'identità gestita per un'app cloud di Azure Spring e usarla per accedere al database SQL di Azure.

Il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è il servizio di database relazionale intelligente, scalabile e basato sul cloud. È sempre aggiornato, con le funzionalità basate su intelligenza artificiale e automatizzate che ottimizzano le prestazioni e la durabilità. Le opzioni di archiviazione con scalabilità e calcolo senza server ridimensionano automaticamente le risorse su richiesta, consentendo di concentrarsi sulla creazione di nuove applicazioni senza doversi preoccupare delle dimensioni di archiviazione o della gestione delle risorse.

## <a name="prerequisites"></a>Prerequisiti
In questo esempio vengono usate le risorse seguenti.
* Seguire l' [esercitazione sull'app Spring data](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) per eseguire il provisioning di un database SQL di Azure e usarlo con un'app Java in locale
* Seguire l' [esercitazione sull'identità gestita assegnata dal sistema di Azure Spring cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) per eseguire il provisioning di un'app Azure Spring cloud con mi abilitato

## <a name="grant-permission-to-the-managed-identity"></a>Concedere l'autorizzazione all'identità gestita
Connettersi a SQL Server ed eseguire la query SQL seguente:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

<MIName>Segue la regola: `<service instance name>/apps/<app name>` , ad esempio myspringcloud/Apps/SQLDEMO. È anche possibile eseguire query su MIName con l'interfaccia della riga di comando di Azure:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Configurare l'app java per l'uso dell'identità gestita
Aprire il `src/main/resources/application.properties` file e aggiungere `Authentication=ActiveDirectoryMSI;` alla fine della riga seguente. Assicurarsi di usare il valore corretto per $AZ _DATABASE_NAME variabile.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Compilare e distribuire l'app nel cloud Spring di Azure
Ricompilare l'app e distribuirla nell'app Azure Spring cloud sottoposta a provisioning nel secondo punto elenco in prerequisiti. A questo punto si dispone di un'applicazione Spring boot, autenticata da un'identità gestita, che usa l'app per l'archiviazione e il recupero di dati da un database SQL di Azure nel cloud Spring di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Come accedere a un BLOB di archiviazione con un'identità gestita in Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Come abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Altre informazioni sulle identità gestite per le risorse di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autenticazione di Azure Spring Cloud con Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md)