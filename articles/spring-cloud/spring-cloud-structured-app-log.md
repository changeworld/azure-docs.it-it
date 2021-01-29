---
title: Registro applicazioni strutturate per il cloud Spring di Azure | Microsoft Docs
description: Questo articolo illustra come generare e raccogliere dati di log applicazioni strutturati nel cloud di Azure Spring.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: c5c35fe8a352a1bc3467e9512a7fcbc068375bfb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055926"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Registro applicazioni strutturate per il cloud Spring di Azure

Questo articolo illustra come generare e raccogliere dati di log applicazioni strutturati nel cloud di Azure Spring. Con la configurazione corretta, Azure Spring cloud fornisce un'analisi e una query del registro applicazioni utili tramite Log Analytics.

## <a name="log-schema-requirements"></a>Requisiti dello schema di log
Per migliorare l'esperienza di query di log, è necessario che il registro applicazioni sia in formato JSON e conforme a uno schema. Azure Spring cloud usa questo schema per analizzare l'applicazione e il flusso per Log Analytics. 

**Requisiti dello schema JSON:**

| Chiave JSON      | Tipo di valore JSON|  Necessario | Colonne in Log Analytics| Descrizione |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     Sì   | AppTimestamp    | timestamp in formato UTC  |
| logger        | stringa      |     No    | Logger          | logger                   |
| livello         | stringa      |     No    | CustomLevel     | livello di registrazione                |
| thread        | stringa      |     No    | Thread          | thread                   |
| message       | string      |     No    | Message         | messaggio di log              |
| stackTrace    | stringa      |     No    | StackTrace      | analisi dello stack dell'eccezione    |
| P:System.EnterpriseServices.ExceptionClassAttribute.Value| stringa      |     No    | P:System.EnterpriseServices.ExceptionClassAttribute.Value  | nome della classe di eccezione     |
| MDC           | JSON annidato |     No    |                 | contesto di diagnostica mappato|
| MDC. traceId   | stringa      |     No    | TraceId         |ID di traccia per la traccia distribuita|
| MDC. spanId    | stringa      |     No    | SpanId          |ID span per la traccia distribuita |
|               |             |           |                 |                          |

* Il campo "timestamp" è obbligatorio e deve essere in formato UTC, tutti gli altri campi sono facoltativi.
* per lo scopo della traccia vengono utilizzati "traceId" e "spanId" nel campo "MDC".
* Registrare ogni record JSON in una sola riga. 

**Esempio di record di log** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Genera log JSON conforme allo schema  
Per le applicazioni Spring, è possibile generare il formato di log JSON previsto usando i [Framework di registrazione](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)comuni, ad esempio [logback](http://logback.qos.ch/) e [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Registra con logback 
Quando si usano gli avvii Spring boot, per impostazione predefinita viene usato logback. Per le app logback, usare [logstash-encoder](https://github.com/logstash/logstash-logback-encoder) per generare il log in formato JSON. Questo metodo è supportato in Spring Boot versione 2.1 +. 

Procedura:

1. Aggiungere la dipendenza logstash nel file di pom.xml. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Aggiornare il file di configurazione logback.xml per impostare il formato JSON.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Registra con log4j2 

Per le app log4j2, usare [JSON-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) per generare il log in formato JSON. Questo metodo è supportato in Spring Boot versione 2.1 +.

Procedura:

1. Escludere `spring-boot-starter-logging` da `spring-boot-starter` , aggiungere dipendenze `spring-boot-starter-log4j2` `log4j-layout-template-json` nel file di pom.xml.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Preparare un file del modello di layout JSON jsonTemplate.json nel percorso della classe.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Usare questo modello di layout JSON nel file di configurazione log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analizzare i log in Log Analytics

Dopo che l'applicazione è stata configurata correttamente, il log della console dell'applicazione verrà trasmesso a Log Analytics. La struttura consente una query efficiente in Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Controllare la struttura del log in Log Analytics
Utilizzare la procedura seguente:
1. Passare alla pagina Panoramica servizio dell'istanza del servizio.
2. Fare clic sulla `Logs` voce sotto la `Monitoring` sezione.
3. Eseguire la query.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. I log applicazioni restituiscono come illustrato nell'immagine seguente:

![Mostra log JSON](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Mostra voci di log contenenti errori

Per esaminare le voci di log in cui si è verificato un errore, eseguire la query seguente:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Usare questa query per trovare gli errori o modificare i termini della query per trovare una classe di eccezione o un codice di errore specifico. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Mostra voci di log per un traceId specifico
Per esaminare le voci di log per un ID di traccia specifico "trace_id", eseguire la query seguente:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla query di log, vedere [Introduzione alle query di log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
