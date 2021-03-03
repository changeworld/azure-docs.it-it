---
title: Esempi di processori di telemetria-Application Insights di monitoraggio di Azure per Java
description: Esplorare esempi che illustrano i processori di telemetria in monitoraggio di Azure Application Insights per Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734588"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Esempi di processori di telemetria-Application Insights di monitoraggio di Azure per Java

Questo articolo fornisce esempi di processori di telemetria in Application Insights per Java. Sono disponibili esempi per le configurazioni di inclusione ed esclusione. Sono inoltre disponibili esempi per processori di attributi e processori di estensione.
## <a name="include-and-exclude-samples"></a>Includere ed escludere esempi

In questa sezione si vedrà come includere ed escludere gli intervalli. Si vedrà anche come escludere più intervalli e applicare l'elaborazione selettiva.
### <a name="include-spans"></a>Includi span

In questa sezione viene illustrato come includere gli intervalli per un processore di attributi. Gli intervalli che non corrispondono alle proprietà non vengono elaborati dal processore.

Una corrispondenza richiede che il nome dell'intervallo sia uguale a `spanA` o `spanB` . 

Questi intervalli corrispondono alle proprietà di inclusione e vengono applicate le azioni del processore:
* Nome span1:' SPANA ' attributi: {ENV: dev, test_request: 123, credit_card: 1234}
* Nome Span2: attributi ' spanB ': {ENV: dev, test_request: false}
* Nome Span3:' SPANA ' attributi: {ENV: 1, test_request: dev, credit_card: 1234}

Questo intervallo non corrisponde alle proprietà di inclusione e le azioni del processore non vengono applicate:
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

### <a name="exclude-spans"></a>Escludi span

Questa sezione illustra come escludere gli intervalli per un processore di attributi. Gli intervalli che corrispondono alle proprietà non vengono elaborati dal processore.

Una corrispondenza richiede che il nome dell'intervallo sia uguale a `spanA` o `spanB` .

Gli intervalli seguenti corrispondono alle proprietà di esclusione e le azioni del processore non vengono applicate:
* Nome span1:' SPANA ' attributi: {ENV: dev, test_request: 123, credit_card: 1234}
* Nome Span2: attributi ' spanB ': {ENV: dev, test_request: false}
* Nome Span3:' SPANA ' attributi: {ENV: 1, test_request: dev, credit_card: 1234}

Questo intervallo non corrisponde alle proprietà di esclusione e vengono applicate le azioni del processore:
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

### <a name="exclude-spans-by-using-multiple-criteria"></a>Escludi intervalli usando più criteri

Questa sezione illustra come escludere gli intervalli per un processore di attributi. Gli intervalli che corrispondono alle proprietà non vengono elaborati dal processore.

Una corrispondenza richiede che siano soddisfatte le condizioni seguenti:
* Un attributo (ad esempio `env` o `dev` ) deve esistere nell'intervallo.
* L'intervallo deve avere un attributo con chiave `test_request` .

Gli intervalli seguenti corrispondono alle proprietà di esclusione e le azioni del processore non vengono applicate.
* Nome span1: attributi ' spanB ': {ENV: dev, test_request: 123, credit_card: 1234}
* Nome Span2:' SPANA ' attributi: {ENV: dev, test_request: false}

L'intervallo seguente non corrisponde alle proprietà di esclusione e vengono applicate le azioni del processore:
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

### <a name="selective-processing"></a>Elaborazione selettiva

In questa sezione viene illustrato come specificare il set di proprietà Span che indicano gli intervalli a cui deve essere applicato questo processore. Le proprietà di inclusione indicano quali intervalli devono essere elaborati. Le proprietà di esclusione filtrano gli intervalli che non devono essere elaborati.

Nella configurazione seguente, questi intervalli corrispondono alle proprietà e vengono applicate le azioni del processore:

* Nome span1: attributi ' spanB ': {ENV: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Nome Span2:' SPANA ' attributi: {ENV: staging, test_request: false, redact_trace: true}

Questi intervalli non corrispondono alle proprietà di inclusione e non vengono applicate le azioni del processore:
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

Nell'esempio seguente il nuovo attributo viene inserito `{"attribute1": "attributeValue1"}` in spans in cui la chiave `attribute1` non esiste.

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

Nell'esempio seguente viene usato il valore da Attribute `anotherkey` per inserire il nuovo attributo `{"newKey": "<value from attribute anotherkey>"}` in spans in cui la chiave `newKey` non esiste. Se l'attributo `anotherkey` non esiste, non viene inserito alcun nuovo attributo in spans.

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

### <a name="update"></a>Aggiorna

Nell'esempio seguente l'attributo viene aggiornato a `{"db.secret": "redacted"}` . Aggiorna l'attributo `boo` usando il valore dell'attributo `foo` . Gli intervalli che non hanno l'attributo `boo` non cambiano.

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

Nell'esempio seguente viene illustrato come eliminare un attributo con la chiave `credit_card` .

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

Nell'esempio seguente viene illustrato come eseguire l'hashing dei valori di attributo esistenti.

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

Nell'esempio seguente viene illustrato come utilizzare un'espressione regolare (Regex) per creare nuovi attributi in base al valore di un altro attributo.
Dato, ad esempio `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` , vengono inseriti gli attributi seguenti:
* HttpProtocol `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *Nessuna* modifica

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

Nell'esempio seguente viene illustrato come elaborare gli intervalli con un nome di intervallo che corrisponde ai modelli Regex.
Questo processore rimuove l' `token` attributo. Offusca l' `password` attributo negli intervalli in cui il nome dell'intervallo corrisponde `auth.*` a e dove il nome dell'intervallo non corrisponde `login.*` .

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

Nell'esempio seguente vengono specificati i valori degli attributi `db.svc` , `operation` e `id` . Forma il nuovo nome dell'intervallo usando gli attributi, in questo ordine, separati dal valore `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>Estrai attributi da un nome span

Supponiamo che il nome dell'intervallo di input sia `/api/v1/document/12345678/update` . Nell'esempio seguente viene restituito il nome dell'intervallo di output `/api/v1/document/{documentId}/update` . Aggiunge il nuovo attributo `documentId=12345678` all'intervallo.
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Estrarre gli attributi da un nome span usando Includi ed Escludi

Nell'esempio seguente viene illustrato come modificare il nome dell'estensione in `{operation_website}` . Aggiunge un attributo con la chiave `operation_website` e `{oldSpanName}` il valore quando l'intervallo presenta le proprietà seguenti:
- Il nome dell'intervallo contiene `/` qualsiasi punto della stringa.
- Il nome dell'intervallo non è `donot/change` .
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
