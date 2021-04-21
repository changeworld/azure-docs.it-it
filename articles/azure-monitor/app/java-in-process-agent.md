---
title: Monitoraggio di Azure Application Insights Java
description: Monitoraggio delle prestazioni delle applicazioni Java in esecuzione in qualsiasi ambiente senza richiedere la modifica del codice. Traccia distribuita e mappa delle applicazioni.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3f22e165fe4a3f86ecce8b1e307b19fae0eeac81
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812049"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Monitoraggio delle applicazioni senza codice Java Monitoraggio di Azure Application Insights

Il monitoraggio di applicazioni codeless Java è tutta una questione di semplicità: senza cambiare il codice, l'agente Java può essere abilitato solo con un paio di modifiche di configurazione.

 L'agente Java funziona in qualsiasi ambiente e consente di monitorare tutte le applicazioni Java. In altre parole, indipendentemente dal fatto che le app Java siano in esecuzione in macchine virtuali, in locale, nel servizio AppKs, in Windows o Linux, l'agente Java 3.0 monitorerà l'app.

L'aggiunta Application Insights Java SDK all'applicazione non è più necessaria, perché l'agente 3.0 raccoglie automaticamente richieste, dipendenze e log di per sé.

È comunque possibile inviare dati di telemetria personalizzati dall'applicazione. L'agente 3.0 lo tiene traccia e lo correla insieme a tutti i dati di telemetria raccolti automaticamente.

L'agente 3.0 supporta Java 8 e versione precedente.

## <a name="quickstart"></a>Guida introduttiva

**1. Scaricare l'agente**

> [!WARNING]
> **Se si esegue l'aggiornamento dalla versione di anteprima 3.0**
>
> Esaminare attentamente tutte le opzioni [di](./java-standalone-config.md) configurazione, in quanto la struttura json è stata completamente modificata, oltre al nome file stesso che è diventato tutto minuscolo.

Scaricare [applicationinsights-agent-3.0.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. Puntare la JVM all'agente**

Aggiungere `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` agli argomenti JVM dell'applicazione

Gli argomenti JVM tipici includono `-Xmx512m` e `-XX:+UseG1GC` . Quindi, se si sa dove aggiungerli, si sa già dove aggiungerli.

Per altre informazioni sulla configurazione degli argomenti JVM dell'applicazione, vedere Suggerimenti per l'aggiornamento [degli argomenti JVM.](./java-standalone-arguments.md)

**3. Puntare l'agente alla risorsa Application Insights**

Se non si dispone già di una risorsa Application Insights, è possibile crearne una nuova seguendo la procedura descritta nella guida alla [creazione della risorsa](./create-new-resource.md).

Puntare l'agente alla risorsa Application Insights, impostando una variabile di ambiente:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Oppure creando un file di configurazione denominato `applicationinsights.json` e inserendolo nella stessa directory di `applicationinsights-agent-3.0.3.jar` , con il contenuto seguente:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

È possibile trovare la stringa di connessione nella Application Insights seguente:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights di connessione":::

**4. È tutto.**

Avviare ora l'applicazione e passare alla risorsa Application Insights nella portale di Azure per visualizzare i dati di monitoraggio.

> [!NOTE]
> La visualizzazione dei dati di monitoraggio nel portale potrebbe richiedere alcuni minuti.


## <a name="configuration-options"></a>Opzioni di configurazione

Nel `applicationinsights.json` file è anche possibile configurare:

* Nome del ruolo cloud
* Istanza del ruolo del cloud
* campionamento
* Metriche JMX
* Dimensioni personalizzate
* Processori di telemetria (anteprima)
* Registrazione raccolta automaticamente
* Metriche di Micrometer raccolte automaticamente (incluse le Spring Boot actuator)
* Heartbeat
* HTTP Proxy
* Auto-diagnostica

Per [informazioni dettagliate, vedere](./java-standalone-config.md) opzioni di configurazione.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Richieste, dipendenze, log e metriche raccolte automaticamente

### <a name="requests"></a>Requests

* Consumer JMS
* Consumer Kafka
* Netty/WebFlux
* Servlet
* Pianificazione di Spring

### <a name="dependencies-with-distributed-trace-propagation"></a>Dipendenze con propagazione della traccia distribuita

* Apache HttpClient e HttpAsyncClient
* gRPC
* Java.net.HttpURLConnection
* JMS
* Kafka
* Client Netty
* OkHttp

### <a name="other-dependencies"></a>Altre dipendenze

* Cassandra
* JDBC
* MongoDB (asincrono e sincronizzato)
* Redis (Lattuga e Jedi)

### <a name="logs"></a>Log

* java.util.logging
* Log4j (incluse le proprietà MDC)
* SLF4J/Logback (incluse le proprietà MDC)

### <a name="metrics"></a>Metriche

* Micrometro (incluse le Spring Boot dell'attuatore)
* Metriche JMX

### <a name="azure-sdks-preview"></a>Azure SDK (anteprima)

Vedere le [opzioni di configurazione](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) per abilitare questa funzionalità di anteprima e acquisire i dati di telemetria generati da questi SDK di Azure:

* [Configurazione app](https://docs.microsoft.com/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Ricerca cognitiva](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Chat di](https://docs.microsoft.com/java/api/overview/azure/communication-chat-readme) comunicazione 1.0.0+
* [Comuni di](https://docs.microsoft.com/java/api/overview/azure/communication-common-readme) comunicazione 1.0.0+
* [Identità di](https://docs.microsoft.com/java/api/overview/azure/communication-identity-readme) comunicazione 1.0.0+
* [Sms di comunicazione](https://docs.microsoft.com/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](https://docs.microsoft.com/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Griglia di](https://docs.microsoft.com/java/api/overview/azure/messaging-eventgrid-readme) eventi 4.0.0+
* [Hub eventi](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Hub eventi - Archivio Archiviazione BLOB di Azure checkpoint](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [riconoscimento modulo](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identità](https://docs.microsoft.com/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault - Certificati](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault - Chiavi](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault - Segreti](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Bus di servizio](https://docs.microsoft.com/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Analisi del testo](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "i nomi e i collegamenti precedenti di cui è stato rimosso il frammento https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "e la sincronizzazione delle versioni si basano manualmente sulla versione meno recente in Maven Central basata su azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    Continuare"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Inviare dati di telemetria personalizzati dall'applicazione

L'obiettivo della versione 3.0+ è quello di consentire l'invio dei dati di telemetria personalizzati usando le API standard.

Finora sono supportati Micrometer, framework di registrazione più diffusi e Application Insights SDK Java 2.x.
Application Insights Java 3.0 acquisisce automaticamente i dati di telemetria inviati tramite queste API e lo correla con i dati di telemetria raccolti automaticamente.

### <a name="supported-custom-telemetry"></a>Telemetria personalizzata supportata

La tabella seguente rappresenta i tipi di telemetria personalizzati attualmente supportati che è possibile abilitare per integrare l'agente Java 3.0. Per riepilogare, le metriche personalizzate sono supportate tramite micrometro, le eccezioni e le tracce personalizzate possono essere abilitate tramite i framework di registrazione e qualsiasi tipo di telemetria personalizzata è supportato tramite [Application Insights Java 2.x SDK.](#send-custom-telemetry-using-the-2x-sdk)

|                     | Micrometer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Eventi personalizzati**   |            |                     |  Sì    |
| **Metriche personalizzate**  |  Sì       |                     |  Sì    |
| **Dipendenze**    |            |                     |  Sì    |
| **Eccezioni**      |            |  Sì                |  Sì    |
| **Visualizzazioni pagina**      |            |                     |  Sì    |
| **Richieste**        |            |                     |  Sì    |
| **Tracce**          |            |  Sì                |  Sì    |

Non si prevede di rilasciare un SDK con Application Insights 3.0 al momento.

Application Insights Java 3.0 è già in ascolto dei dati di telemetria inviati a Application Insights Java 2.x SDK. Questa funzionalità è una parte importante della storia dell'aggiornamento per gli utenti 2.x esistenti e colmerà un'importante lacune nel supporto dei dati di telemetria personalizzati fino a quando l'API OpenTelemetry non sarà disponibile a livello ga.

### <a name="send-custom-metrics-using-micrometer"></a>Inviare metriche personalizzate con Micrometer

Aggiungere Micrometer all'applicazione:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Usare il registro [globale](https://micrometer.io/docs/concepts#_global_registry) di Micrometer per creare un contatore:

```java
static final Counter counter = Metrics.counter("test_counter");
```

e usarlo per registrare le metriche:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Inviare tracce ed eccezioni personalizzate usando il framework di registrazione preferito

Log4j, Logback e java.util.logging vengono instrumentati automaticamente e la registrazione eseguita tramite questi framework di registrazione viene raccolta automaticamente come telemetria di traccia ed eccezione.

Per impostazione predefinita, la registrazione viene raccolta solo quando la registrazione viene eseguita a livello info o superiore.
Vedere le [opzioni di configurazione](./java-standalone-config.md#auto-collected-logging) per informazioni su come modificare questo livello.

Se si desidera collegare dimensioni personalizzate ai log, è possibile usare [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)o [Logback MDC](http://logback.qos.ch/manual/mdc.html)e Application Insights Java 3.0 acquisisce automaticamente tali proprietà MDC come dimensioni personalizzate nella traccia e nei dati di telemetria delle eccezioni.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Inviare dati di telemetria personalizzati con l'SDK 2.x

Aggiungere all'applicazione (tutte le versioni 2.x sono supportate da Application Insights Java 3.0, ma è opportuno usare la versione più recente se si `applicationinsights-core-2.6.2.jar` ha la possibilità di scegliere):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Creare un oggetto TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usarlo per inviare dati di telemetria personalizzati:

##### <a name="events"></a>Eventi

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metriche

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Dependencies

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Log

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Eccezioni

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Aggiungere dimensioni personalizzate della richiesta usando l'SDK 2.x

> [!NOTE]
> Questa funzionalità è disponibile solo nella versione 3.0.2 e successive

Aggiungere all'applicazione (tutte le versioni 2.x sono supportate da Application Insights Java 3.0, ma è opportuno usare la versione più recente se si `applicationinsights-web-2.6.2.jar` ha la possibilità di scegliere):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e aggiungere dimensioni personalizzate nel codice:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Impostare i dati di telemetria user_Id richiesta usando l'SDK 2.x

> [!NOTE]
> Questa funzionalità è disponibile solo nella versione 3.0.2 e successive

Aggiungere all'applicazione (tutte le versioni 2.x sono supportate da Application Insights Java 3.0, ma è opportuno usare la versione più recente se si `applicationinsights-web-2.6.2.jar` ha la possibilità di scegliere):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e impostare `user_Id` nel codice:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Eseguire l'override del nome di telemetria della richiesta usando l'SDK 2.x

> [!NOTE]
> Questa funzionalità è disponibile solo nella versione 3.0.2 e successive

Aggiungere all'applicazione (tutte le versioni 2.x sono supportate da Application Insights Java 3.0, ma è opportuno usare la versione più recente se si `applicationinsights-web-2.6.2.jar` ha la possibilità di scegliere):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e impostare il nome nel codice:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Ottenere l'ID di telemetria della richiesta e l'ID operazione usando l'SDK 2.x

> [!NOTE]
> Questa funzionalità è disponibile solo nella versione 3.0.3 e successive

Aggiungere all'applicazione (tutte le versioni 2.x sono supportate da Application Insights Java 3.0, ma è opportuno usare la versione più recente se si `applicationinsights-web-2.6.2.jar` ha la possibilità di scegliere):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

e ottenere l'ID di telemetria della richiesta e l'ID operazione nel codice:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
