---
title: Processori di telemetria (anteprima)-Application Insights di monitoraggio di Azure per Java
description: Informazioni su come configurare i processori di telemetria in monitoraggio di Azure Application Insights per Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 991e52c13a5730b83552abb6b922d4d7a57c5429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024116"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processori di telemetria (anteprima)-Application Insights di monitoraggio di Azure per Java

> [!NOTE]
> La funzionalità processori di telemetria è in anteprima.

L'agente Java 3,0 per Application Insights può elaborare i dati di telemetria prima che i dati vengano esportati.

Ecco alcuni casi d'uso per i processori di telemetria:
 * Mascherare i dati sensibili.
 * Aggiungere dimensioni personalizzate in modo condizionale.
 * Aggiornare il nome dell'intervallo, usato per aggregare dati di telemetria simili nel portale di Azure.
 * Rilascia attributi span specifici per controllare i costi di inserimento.

> [!NOTE]
> Se si sta cercando di eliminare intervalli specifici (interi) per il controllo del costo di inserimento, vedere [override del campionamento](./java-standalone-sampling-overrides.md).

## <a name="terminology"></a>Terminologia

Prima di acquisire familiarità con i processori di telemetria, è necessario comprendere il termine *intervallo*. Un intervallo è un termine generale per:

* Una richiesta in ingresso.
* Dipendenza in uscita (ad esempio, una chiamata remota a un altro servizio).
* Dipendenza in-process, ad esempio il lavoro eseguito dai sottocomponenti del servizio.

Per i processori di telemetria, questi componenti span sono importanti:

* Nome
* Attributi

Il nome dell'intervallo è lo schermo principale per le richieste e le dipendenze nella portale di Azure. Gli attributi span rappresentano proprietà standard e personalizzate di una determinata richiesta o dipendenza.

## <a name="telemetry-processor-types"></a>Tipi di processori di telemetria

Attualmente, i due tipi di processori di telemetria sono processori di attributi e processori span.

Un processore di attributi può inserire, aggiornare, eliminare o gli attributi hash.
Può anche usare un'espressione regolare per estrarre uno o più nuovi attributi da un attributo esistente.

Un processore span può aggiornare il nome della telemetria.
Può anche usare un'espressione regolare per estrarre uno o più nuovi attributi dal nome dell'estensione.

> [!NOTE]
> Attualmente, i processori di telemetria elaborano solo gli attributi di tipo stringa. Non elaborano attributi di tipo booleano o numerico.

## <a name="getting-started"></a>Introduzione

Per iniziare, creare un file di configurazione denominato *applicationinsights.json*. Salvarlo nella stessa directory di *applicationinsights-Agent- \* . jar*. Usare il modello seguente.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="include-criteria-and-exclude-criteria"></a>Includi criteri ed Escludi criteri

Entrambi i processori di attributi e di intervallo supportano facoltativo `include` e i `exclude` criteri.
Un processore viene applicato solo agli intervalli che corrispondono ai `include` criteri (se disponibili) _e_ non corrispondono ai `exclude` criteri (se specificati).

Per configurare questa opzione, in `include` o `exclude` (o entrambi) specificare almeno un oggetto `matchType` e uno `spanNames` o `attributes` .
La configurazione include-exclude consente più di una condizione specificata.
Tutte le condizioni specificate devono restituire true per determinare una corrispondenza. 

* **Campo obbligatorio**: `matchType` Controlla la modalità di interpretazione degli elementi nelle matrici `spanNames` e nelle `attributes` matrici. I valori possibili sono `regexp` e `strict`. 

* **Campi facoltativi**: 
    * `spanNames` deve corrispondere ad almeno uno degli elementi. 
    * `attributes` Specifica l'elenco di attributi di cui trovare una corrispondenza. Tutti questi attributi devono corrispondere esattamente a un risultato corrispondente.
    
> [!NOTE]
> Se `include` `exclude` vengono specificati sia che, le `include` proprietà vengono verificate prima che le `exclude` proprietà vengano controllate.

### <a name="sample-usage"></a>Esempio di utilizzo

```json
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
```
Per altre informazioni, vedere [esempi di processori di telemetria](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Processore di attributi

Il processore di attributi modifica gli attributi di un intervallo. Può supportare la possibilità di includere o escludere gli intervalli. Accetta un elenco di azioni che vengono eseguite nell'ordine specificato dal file di configurazione. Il processore supporta le azioni seguenti:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

L' `insert` azione inserisce un nuovo attributo nell'intervallo in cui la chiave non esiste già.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
Per l' `insert` azione sono necessarie le impostazioni seguenti:
* `key`
* `value`O`fromAttribute`
* `action`: `insert`

### `update`

L' `update` azione aggiorna un attributo in intervalli in cui la chiave esiste già.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
Per l' `update` azione sono necessarie le impostazioni seguenti:
* `key`
* `value`O`fromAttribute`
* `action`: `update`


### `delete` 

L' `delete` azione Elimina un attributo da un intervallo.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
Per l' `delete` azione sono necessarie le impostazioni seguenti:
* `key`
* `action`: `delete`

### `hash`

`hash`Hash di azione (SHA1) un valore di attributo esistente.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
Per l' `hash` azione sono necessarie le impostazioni seguenti:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> La `extract` funzionalità è disponibile solo nella versione 3.0.2 e successive.

L' `extract` azione estrae i valori usando una regola di espressione regolare dalla chiave di input alle chiavi di destinazione specificate dalla regola. Se una chiave di destinazione esiste già, viene sottoposta a override. Questa azione si comporta come l'impostazione del [processore span](#extract-attributes-from-the-span-name) `toAttributes` , in cui l'attributo esistente è l'origine.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
Per l' `extract` azione sono necessarie le impostazioni seguenti:
* `key`
* `pattern`
* `action`: `extract`

Per altre informazioni, vedere [esempi di processori di telemetria](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Processore span

Il processore span modifica il nome dell'intervallo o gli attributi di un intervallo in base al nome dell'estensione. Può supportare la possibilità di includere o escludere gli intervalli.

### <a name="name-a-span"></a>Assegnare un nome a un intervallo

La `name` sezione richiede l' `fromAttributes` impostazione. I valori di questi attributi vengono usati per creare un nuovo nome, concatenato nell'ordine specificato dalla configurazione. Il processore cambierà il nome dell'intervallo solo se tutti questi attributi sono presenti nell'intervallo.

L' `separator` impostazione è facoltativa. Questa impostazione è una stringa. Viene specificato per suddividere i valori.
> [!NOTE]
> Se la ridenominazione si basa sul processore degli attributi per modificare gli attributi, verificare che il processore span sia specificato dopo il processore degli attributi nella specifica della pipeline.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>Estrai attributi dal nome dell'intervallo

Nella `toAttributes` sezione sono elencate le espressioni regolari per le quali trovare la corrispondenza con il nome dell'estensione. Estrae gli attributi in base alle sottoespressioni.

L' `rules` impostazione è obbligatoria. Questa impostazione elenca le regole utilizzate per estrarre i valori degli attributi dal nome dell'estensione. 

I valori nel nome dell'intervallo sono sostituiti dai nomi degli attributi estratti. Ogni regola nell'elenco è una stringa di modello di espressione regolare (Regex). 

Ecco il modo in cui i valori vengono sostituiti dai nomi degli attributi estratti:

1. Il nome dell'intervallo viene verificato rispetto all'espressione regolare. 
1. Se l'espressione regolare corrisponde, tutte le sottoespressioni denominate dell'espressione regolare vengono estratte come attributi. 
1. Gli attributi estratti vengono aggiunti all'intervallo. 
1. Il nome di ogni sottoespressione diventa un nome di attributo. 
1. La parte corrispondente della sottoespressione diventa il valore dell'attributo. 
1. La parte corrispondente nel nome dell'intervallo viene sostituita dal nome dell'attributo Estratto. Se gli attributi sono già presenti nell'intervallo, verranno sovrascritti. 
 
Questo processo viene ripetuto per tutte le regole nell'ordine in cui sono specificate. Ogni regola successiva funziona sul nome dell'intervallo che è l'output della regola precedente.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>Attributi span comuni

Questa sezione elenca alcuni attributi di intervallo comuni che i processori di telemetria possono usare.

### <a name="http-spans"></a>Intervalli HTTP

| Attributo  | Type | Descrizione | 
|---|---|---|
| `http.method` | string | Metodo di richiesta HTTP.|
| `http.url` | string | URL completo della richiesta HTTP nel formato `scheme://host[:port]/path?query[#fragment]` . Il frammento non viene in genere trasmesso tramite HTTP. Tuttavia, se il frammento è noto, deve essere incluso.|
| `http.status_code` | d'acquisto | [Codice di stato della risposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo di protocollo HTTP. |
| `http.user_agent` | string | Valore dell'intestazione [http dell'agente utente](https://tools.ietf.org/html/rfc7231#section-5.5.3) inviata dal client. |

### <a name="jdbc-spans"></a>Intervalli JDBC

| Attributo  | Type | Descrizione  |
|---|---|---|
| `db.system` | string | Identificatore per il prodotto del sistema di gestione di database (DBMS) utilizzato. |
| `db.connection_string` | string | Stringa di connessione utilizzata per connettersi al database. Si consiglia di rimuovere le credenziali incorporate.|
| `db.user` | string | Nome utente per l'accesso al database. |
| `db.name` | string | Stringa utilizzata per segnalare il nome del database a cui si accede. Per i comandi che comunicano il database, è necessario impostare questa stringa sul database di destinazione, anche se il comando ha esito negativo.|
| `db.statement` | string | Istruzione di database in esecuzione.|
