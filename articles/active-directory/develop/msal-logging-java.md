---
title: Registrazione di errori ed eccezioni in MSAL per Java
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare gli errori e le eccezioni in MSAL per Java
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954919"
---
# <a name="logging-in-msal-for-java"></a>Registrazione in MSAL per Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL per la registrazione Java

MSAL per Java consente di usare la libreria di registrazione già in uso con l'app, purché sia compatibile con SLF4J. MSAL per Java usa la [semplice registrazione Facade per Java](http://www.slf4j.org/) (SLF4J) come facciata o astrazione semplice per vari Framework di registrazione, ad esempio [java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) e [log4j](https://logging.apache.org/log4j/2.x/). SLF4J consente all'utente di inserire il Framework di registrazione desiderato in fase di distribuzione.

Ad esempio, per usare Logback come Framework di registrazione nell'applicazione, aggiungere la dipendenza Logback al file POM di Maven per l'applicazione:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Aggiungere quindi il file di configurazione Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J viene associato automaticamente a Logback al momento della distribuzione. I log MSAL verranno scritti nella console.

Per istruzioni su come eseguire l'associazione ad altri Framework di registrazione, vedere il [Manuale di SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informazioni personali e aziendali

Per impostazione predefinita, la registrazione di MSAL non acquisisce né registra dati personali o aziendali. Nell'esempio seguente la registrazione di dati personali o aziendali è disattivata per impostazione predefinita:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Attivare la registrazione dei dati personali e aziendali impostando il `logPii()` Generatore di applicazioni client. Se si attiva la registrazione dei dati personali o aziendali, l'app deve avere la responsabilità di gestire in modo sicuro i dati altamente sensibili e rispettare eventuali requisiti normativi.

Nell'esempio seguente viene abilitata la registrazione dei dati personali o aziendali:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).