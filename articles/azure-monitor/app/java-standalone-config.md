---
title: Opzioni di configurazione - Monitoraggio di Azure Application Insights per Java
description: Come configurare Monitoraggio di Azure Application Insights per Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b78aaa659598e6eb58841c5cef0c209daaced5e0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811977"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Opzioni di configurazione - Monitoraggio di Azure Application Insights per Java

> [!WARNING]
> **Se si esegue l'aggiornamento dalla versione 3.0 Preview**
>
> Esaminare attentamente tutte le opzioni di configurazione riportate di seguito, perché la struttura JSON è stata completamente modificata, oltre al nome del file stesso che è stato interamente in lettere minuscole.

## <a name="connection-string-and-role-name"></a>Stringa di connessione e nome del ruolo

La stringa di connessione e il nome del ruolo sono le impostazioni più comuni necessarie per iniziare:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

La stringa di connessione è obbligatoria e il nome del ruolo è importante ogni volta che si inviano dati da applicazioni diverse alla stessa Application Insights risorsa.

Altri dettagli e opzioni di configurazione aggiuntive sono disponibili di seguito.

## <a name="configuration-file-path"></a>Percorso del file di configurazione

Per impostazione predefinita, Application Insights Java 3.0 prevede che il file di configurazione sia denominato e che si trovi `applicationinsights.json` nella stessa directory di `applicationinsights-agent-3.0.3.jar` .

È possibile specificare il proprio percorso del file di configurazione usando

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variabile di ambiente o
* `applicationinsights.configuration.file` Proprietà di sistema Java

Se si specifica un percorso relativo, questo verrà risolto in relazione alla directory in `applicationinsights-agent-3.0.3.jar` cui si trova .

## <a name="connection-string"></a>Stringa di connessione

La stringa di connessione è obbligatoria. È possibile trovare la stringa di connessione nella Application Insights seguente:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights di connessione":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

È anche possibile impostare la stringa di connessione usando la variabile di ambiente , che avrà quindi la precedenza sulla stringa di connessione `APPLICATIONINSIGHTS_CONNECTION_STRING` specificata nella configurazione JSON.

Se non si imposta la stringa di connessione, l'agente Java verrà disabilitato.

## <a name="cloud-role-name"></a>Nome del ruolo cloud

Il nome del ruolo cloud viene usato per etichettare il componente nella mappa dell'applicazione.

Per impostare il nome del ruolo cloud:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Se il nome del ruolo cloud non è impostato, Application Insights nome della risorsa verrà usato per etichettare il componente nella mappa dell'applicazione.

È anche possibile impostare il nome del ruolo cloud usando la variabile di ambiente ( che avrà quindi la precedenza sul nome del ruolo `APPLICATIONINSIGHTS_ROLE_NAME` cloud specificato nella configurazione json).

## <a name="cloud-role-instance"></a>Istanza del ruolo del cloud

Per impostazione predefinita, l'istanza del ruolo cloud è il nome del computer.

Se si vuole impostare l'istanza del ruolo cloud su un valore diverso rispetto al nome del computer:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

È anche possibile impostare l'istanza del ruolo cloud usando la variabile di ambiente ( che avrà quindi la precedenza sull'istanza del ruolo `APPLICATIONINSIGHTS_ROLE_INSTANCE` cloud specificata nella configurazione json).

## <a name="sampling"></a>campionamento

Il campionamento è utile se è necessario ridurre i costi.
Il campionamento viene eseguito come funzione sull'ID operazione (noto anche come ID traccia), in modo che lo stesso ID operazione si trasmerà sempre nella stessa decisione di campionamento. Ciò garantisce che non si otterrà parti di una transazione distribuita campionate in mentre altre parti di essa vengono campionate.

Ad esempio, se si imposta il campionamento su 10%, si oserà solo il 10% delle transazioni, ma ognuna di queste 10% avrà tutti i dettagli delle transazioni end-to-end.

Ecco un esempio di come impostare il campionamento per acquisire circa **1/3** di tutte le transazioni: assicurarsi di impostare la frequenza di campionamento corretta per il caso d'uso:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

È anche possibile impostare la percentuale di campionamento usando la variabile di ambiente , che avrà quindi la precedenza sulla percentuale di campionamento `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` specificata nella configurazione json.

> [!NOTE]
> Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.

## <a name="sampling-overrides-preview"></a>Sostituzioni di campionamento (anteprima)

Questa funzionalità è disponibile in anteprima, a partire dalla 3.0.3.

Le sostituzioni di campionamento consentono di eseguire l'override [della percentuale di campionamento predefinita,](#sampling)ad esempio:
* Impostare la percentuale di campionamento su 0 (o un valore ridotto) per i controlli di integrità rumorosi.
* Impostare la percentuale di campionamento su 0 (o un valore ridotto) per le chiamate di dipendenza rumorose.
* Impostare la percentuale di campionamento su 100 per un tipo di richiesta importante ,ad esempio , anche se il campionamento predefinito è `/login` configurato su un valore inferiore.

Per altre informazioni, vedere la documentazione sulle [sostituzioni di campionamento.](./java-standalone-sampling-overrides.md)

## <a name="jmx-metrics"></a>Metriche JMX

Se si desidera raccogliere alcune metriche JMX aggiuntive:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` è il nome della metrica che verrà assegnato a questa metrica JMX (può essere qualsiasi elemento).

`objectName` è il [nome dell'oggetto](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) JMX MBean che si desidera raccogliere.

`attribute` è il nome dell'attributo all'interno di JMX MBean che si desidera raccogliere.

Sono supportati valori numerici e booleani delle metriche JMX. Viene eseguito il mapping delle metriche JMX booleane `0` a per false e per `1` true.

## <a name="custom-dimensions"></a>Dimensioni personalizzate

Per aggiungere dimensioni personalizzate a tutti i dati di telemetria:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` può essere usato per leggere il valore dalla variabile di ambiente specificata all'avvio.

> [!NOTE]
> A partire dalla versione 3.0.2, se si aggiunge una dimensione personalizzata denominata , il valore verrà archiviato nella colonna della tabella logs di Application Insights anziché come dimensione `service.version` `application_Version` personalizzata.

## <a name="telemetry-processors-preview"></a>Processori di telemetria (anteprima)

Questa funzionalità è in anteprima.

Consente di configurare le regole che verranno applicate alla telemetria di richiesta, dipendenza e traccia, ad esempio:
 * Mascherare i dati sensibili
 * Aggiungere in modo condizionale dimensioni personalizzate
 * Aggiornare il nome dell'intervallo, che viene usato per aggregare dati di telemetria simili nel portale di Azure.
 * Eliminare attributi di intervallo specifici per controllare i costi di inserimento.

Per altre informazioni, vedere la documentazione del processore [di telemetria.](./java-standalone-telemetry-processors.md)

> [!NOTE]
> Se si desidera eliminare intervalli specifici (interi) per controllare i costi di inserimento, vedere le sostituzioni [di campionamento.](./java-standalone-sampling-overrides.md)

## <a name="auto-collected-logging"></a>Registrazione raccolta automaticamente

Log4j, Logback e java.util.logging sono instrumentati automaticamente e la registrazione eseguita tramite questi framework di registrazione viene raccolta automaticamente.

La registrazione viene acquisita solo se soddisfa prima il livello configurato per il framework di registrazione e in secondo luogo soddisfa anche il livello configurato per Application Insights.

Ad esempio, se il framework di registrazione è configurato per registrare (e versioni successive) dal pacchetto e Application Insights è configurato per l'acquisizione (e versioni successive), Application Insights acquisisce solo (e versioni `WARN` `com.example` `INFO` `WARN` successive) dal pacchetto `com.example` .

Il livello predefinito configurato per Application Insights è `INFO` . Per modificare questo livello:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

È anche possibile impostare il livello usando la variabile di ambiente ( che avrà quindi la precedenza sul livello `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` specificato nella configurazione json).

Questi sono i valori validi che è possibile specificare nel file e il modo in cui corrispondono ai livelli di registrazione `level` in framework di registrazione `applicationinsights.json` diversi:

| livello             | Log4j  | Logback | LUG     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| Fatale             | Fatale  | ERRORE   | SEVERE  |
| ERROR (o SEVERE) | ERRORE  | ERRORE   | SEVERE  |
| WARN (o WARNING) | Avvertire   | Avvertire    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (o FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (o FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Se un oggetto eccezione viene passato al logger, il messaggio di log (e i dettagli dell'oggetto eccezione) verranno visualizzati nel portale di Azure sotto la tabella anziché `exceptions` nella `traces` tabella.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Metriche del micrometro raccolte automaticamente (incluse Spring Boot dell'attuatore)

Se l'applicazione [usa Micrometer,](https://micrometer.io)le metriche inviate al registro globale di Micrometer vengono raccolte automaticamente.

Inoltre, se l'applicazione [usa Spring Boot actuator,](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)vengono raccolte automaticamente anche le metriche configurate da Spring Boot Actuator.

Per disabilitare la raccolta automatica delle metriche di Micrometer (incluse le Spring Boot actuator):

> [!NOTE]
> Le metriche personalizzate vengono fatturate separatamente e possono generare costi aggiuntivi. Assicurarsi di controllare le informazioni dettagliate [sui prezzi.](https://azure.microsoft.com/pricing/details/monitor/) Per disabilitare le metriche di Micrometer e Spring Actuator, aggiungere la configurazione seguente al file di configurazione.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="auto-collected-azure-sdk-telemetry-preview"></a>Telemetria di Azure SDK raccolta automaticamente (anteprima)

Molte delle librerie di Azure SDK più recenti generano dati di telemetria (vedere [l'elenco completo).](./java-in-process-agent.md#azure-sdks-preview)

A partire da Application Insights Java 3.0.3, è possibile abilitare l'acquisizione di questi dati di telemetria.

Se si vuole abilitare questa funzionalità:

```json
{
  "preview": {
    "instrumentation": {
      "azureSdk": {
        "enabled": true
      }
    }
  }
}
```

È anche possibile abilitare questa funzionalità usando la variabile di ambiente `APPLICATIONINSIGHTS_PREVIEW_INSTRUMENTATION_AZURE_SDK_ENABLED`
(che avrà quindi la precedenza su enabled specificato nella configurazione JSON).

## <a name="suppressing-specific-auto-collected-telemetry"></a>Eliminazione di dati di telemetria specifici raccolti automaticamente

A partire dalla versione 3.0.3, è possibile eliminare specifici dati di telemetria raccolti automaticamente usando queste opzioni di configurazione:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

È anche possibile eliminare queste strumentazioni usando queste variabili di ambiente:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(che avrà quindi la precedenza su enabled specificato nella configurazione JSON).

> NOTA Se si sta cercando un controllo più granulare, ad esempio per eliminare alcune chiamate Redis ma non tutte le chiamate a Redis, vedere le sostituzioni [del campionamento.](./java-standalone-sampling-overrides.md)

## <a name="heartbeat"></a>Heartbeat

Per impostazione predefinita, Application Insights Java 3.0 invia una metrica di heartbeat una volta ogni 15 minuti. Se si usa la metrica heartbeat per attivare gli avvisi, è possibile aumentare la frequenza di questo heartbeat:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Non è possibile aumentare l'intervallo a più di 15 minuti, perché i dati di heartbeat vengono usati anche per tenere traccia Application Insights utilizzo.

## <a name="http-proxy"></a>HTTP Proxy

Se [l'applicazione](./ip-addresses.md)è dietro un firewall e non può connettersi direttamente a Application Insights (vedere Indirizzi IP usati da Application Insights ), è possibile configurare Application Insights Java 3.0 per l'uso di un proxy HTTP:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.0 rispetta anche le impostazioni globali `-Dhttps.proxyHost` e `-Dhttps.proxyPort` se sono impostate.

## <a name="metric-interval"></a>Intervallo metrica

Questa funzionalità è in anteprima.

Per impostazione predefinita, le metriche vengono acquisite ogni 60 secondi.

A partire dalla versione 3.0.3, è possibile modificare questo intervallo:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

L'impostazione si applica a tutte queste metriche:

* Contatori delle prestazioni predefiniti, ad esempio CPU e memoria
* Metriche personalizzate predefinite, ad esempio l'intervallo di Garbage Collection
* Metriche JMX configurate ([vedere sopra](#jmx-metrics))
* Metriche del micrometro ([vedere sopra](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "NOTA Il supporto di OpenTelemetry è in anteprima privata fino a quando l'API OpenTelemetry non raggiunge la versione 1.0"

[//]: # "Supporto ## per le versioni precedenti alla 1.0 dell'API OpenTelemetry"

[//]: # "Il supporto per le versioni precedenti alla 1.0 dell'API OpenTelemetry è il consenso esplicito, perché l'API OpenTelemetry non è ancora stabile"
[//]: # "e quindi ogni versione dell'agente supporta solo una specifica versione precedente alla 1.0 dell'API OpenTelemetry"
[//]: # "Questa limitazione non verrà applicata dopo il rilascio dell'API OpenTelemetry 1.0."

[//]: # "'''json"
[//]: # "{"
[//]: # "  \"anteprima: \" {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Auto-diagnostica

Per "auto diagnostics" si intende la registrazione interna Application Insights Java 3.0.

Questa funzionalità può essere utile per individuare e diagnosticare i problemi Application Insights se stessa.

Per impostazione predefinita, Application Insights log di Java 3.0 a livello sia per il file che per la `INFO` `applicationinsights.log` console, corrispondenti a questa configurazione:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` può essere uno dei tipi `file` `console` , o `file+console` .

`level` può essere uno dei `OFF` tipi , , , , o `ERROR` `WARN` `INFO` `DEBUG` `TRACE` .

`path` può essere un percorso assoluto o relativo. I percorsi relativi vengono risolti rispetto alla directory in `applicationinsights-agent-3.0.3.jar` cui si trova .

`maxSizeMb` è la dimensione massima del file di log prima del roll over.

`maxHistory` è il numero di file di log di cui è stato eseguito il roll over che vengono mantenuti (oltre al file di log corrente).

A partire dalla versione 3.0.2, è anche possibile impostare la diagnostica automatica usando la variabile di ambiente , che avrà quindi la precedenza sul livello di auto-diagnostica specificato nella configurazione `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` JSON.

## <a name="an-example"></a>un esempio

Questo è solo un esempio per illustrare l'aspetto di un file di configurazione con più componenti.
Configurare opzioni specifiche in base alle esigenze.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
