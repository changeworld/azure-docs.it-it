---
title: 'Esempi di processori di telemetria: monitoraggio di Azure Application Insights per Java'
description: Esempi che illustrano i processori di telemetria in monitoraggio di Azure Application Insights per Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696313"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Esempi di processori di telemetria: monitoraggio di Azure Application Insights per Java

## <a name="includeexclude-samples"></a>Esempi di inclusione/esclusione

### <a name="1-include-spans"></a>1. Includi intervalli

Nell'esempio seguente vengono illustrati gli intervalli per questo processore di attributi. Tutti gli altri intervalli che non corrispondono alle proprietà non vengono elaborati dal processore.

Di seguito sono riportate le condizioni da soddisfare per una corrispondenza:
* Il nome dell'estensione deve essere uguale a "SPANA" o "spanB" 

Di seguito sono riportati gli intervalli che corrispondono alle proprietà di inclusione e vengono applicate le azioni del processore.
* Nome span1:' SPANA ' attributi: {ENV: dev, test_request: 123, credit_card: 1234}
* Nome Span2: attributi ' spanB ': {ENV: dev, test_request: false}
* Nome Span3:' SPANA ' attributi: {ENV: 1, test_request: dev, credit_card: 1234}

L'intervallo seguente non corrisponde alle proprietà di inclusione e le azioni del processore non vengono applicate.
* Nome Span4: attributi ' spanC ': {ENV: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. escludere gli intervalli

Di seguito viene illustrato come escludere gli intervalli per questo processore di attributi. Tutti gli intervalli che corrispondono alle proprietà non vengono elaborati dal processore.

Di seguito sono riportate le condizioni da soddisfare per una corrispondenza:
* Il nome dell'estensione deve essere uguale a "SPANA" o "spanB" 

Di seguito sono riportati gli intervalli che corrispondono alle proprietà di esclusione e le azioni del processore non vengono applicate.
* Nome span1:' SPANA ' attributi: {ENV: dev, test_request: 123, credit_card: 1234}
* Nome Span2: attributi ' spanB ': {ENV: dev, test_request: false}
* Nome Span3:' SPANA ' attributi: {ENV: 1, test_request: dev, credit_card: 1234}

L'intervallo seguente non corrisponde alle proprietà Exclude e vengono applicate le azioni del processore.
* Nome Span4: attributi ' spanC ': {ENV: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti intervalli

Di seguito viene illustrato come escludere gli intervalli per questo processore di attributi. Tutti gli intervalli che corrispondono alle proprietà non vengono elaborati dal processore.

Di seguito sono riportate le condizioni da soddisfare per una corrispondenza:
* Un attributo (' env ',' dev ') deve esistere nell'intervallo per una corrispondenza.
* Se è presente un attributo con la chiave "test_request" nell'intervallo, esiste una corrispondenza.

Di seguito sono riportati gli intervalli che corrispondono alle proprietà di esclusione e le azioni del processore non vengono applicate.
* Nome span1: attributi ' spanB ': {ENV: dev, test_request: 123, credit_card: 1234}
* Nome Span2:' SPANA ' attributi: {ENV: dev, test_request: false}

L'intervallo seguente non corrisponde alle proprietà Exclude e vengono applicate le azioni del processore.
* Nome Span3: attributi ' spanB ': {ENV: 1, test_request: dev, credit_card: 1234}
* Nome Span4: attributi ' spanC ': {ENV: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. elaborazione selettiva

Nell'esempio seguente viene illustrato come specificare il set di proprietà span per indicare gli intervalli a cui deve essere applicato questo processore. Le `include` proprietà di indicano quali devono essere incluse e le `exclude` Proprietà filtrano ulteriormente gli intervalli che non devono essere elaborati.

Con la configurazione seguente, vengono applicati gli intervalli seguenti per le proprietà e le azioni del processore:

* Nome span1: attributi ' spanB ': {ENV: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Nome Span2:' SPANA ' attributi: {ENV: staging, test_request: false, redact_trace: true}

Gli intervalli seguenti non corrispondono alle proprietà di inclusione e le azioni del processore non vengono applicate:
* Nome Span3: attributi ' spanB ': {ENV: Production, test_request: true, credit_card: 1234, redact_trace: false}
* Nome Span4: attributi ' spanC ': {ENV: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>Esempi di processore di attributi

### <a name="insert"></a>Insert

Il codice seguente inserisce un nuovo attributo {"attribute1": "attributeValue1"} in cui si estende dove la chiave "attribute1" non esiste.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Inserisci da un'altra chiave

Nell'esempio seguente viene utilizzato il valore dell'attributo "anotherkey" per inserire un nuovo attributo {"newKey": "valore dall'attributo" anotherkey "} in cui si estende dove la chiave" newKey "non esiste. Se l'attributo ' anotherkey ' non esiste, non viene inserito alcun nuovo attributo in spans.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Aggiornamento

Il codice seguente aggiorna l'attributo a {"DB. Secret": "redatto"} e aggiorna l'attributo ' Boo ' usando il valore dell'attributo ' foo '. Gli intervalli senza l'attributo "Boo" non vengono modificati.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Elimina

Nell'esempio seguente viene illustrata l'eliminazione dell'attributo con la chiave ' credit_card '.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hash

Di seguito vengono illustrati i valori degli attributi esistenti hash.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extract

Nell'esempio seguente viene illustrato l'utilizzo di Regex per creare nuovi attributi in base al valore di un altro attributo.
Ad esempio, se si specifica http. URL =' http://example.com/path?queryParam1=value1 , queryParam2 = value2' verranno inseriti gli attributi seguenti:
* httpProtocol: http
* httpDomain: example.com
* httpPath: percorso
* httpQueryParams: queryParam1 = value1, queryParam2 = value2
* il valore http. URL non cambia.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

Nell'esempio seguente viene illustrato come elaborare gli intervalli con un nome di intervallo che corrispondono ai modelli RegExp.
Questo processore rimuoverà l'attributo "token" e l'attributo "password" verrà offuscato negli intervalli in cui span name corrisponde a "auth \* ". e dove span name non corrisponde a " \* login".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Esempi di processore span

### <a name="name-a-span"></a>Assegnare un nome a un intervallo

Nell'esempio seguente vengono specificati i valori dell'attributo "DB. svc", "Operation" e "ID" formerà il nuovo nome dell'intervallo, in questo ordine, separato dal valore "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Estrai attributi dal nome dell'estensione

Supponiamo che il nome dell'intervallo di input sia/API/v1/Document/12345678/Update. L'applicazione dei seguenti risultati nel nome dell'intervallo di output/api/v1/document/{documentId}/update aggiungerà un nuovo attributo "documentId" = "12345678" all'intervallo.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Estrai attributi dal nome span con Includi ed Escludi

Nell'esempio seguente viene illustrato come rinominare il nome dell'estensione in "{operation_website}" e aggiungere l'attributo {Key: operation_website, value: oldSpanName} quando l'intervallo presenta le proprietà seguenti:
- Il nome dell'intervallo contiene '/' in un punto qualsiasi della stringa.
- Il nome dell'intervallo non è' donot/Change '.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```