---
title: Processori di telemetria (anteprima)-Application Insights di monitoraggio di Azure per Java
description: Come configurare i processori di telemetria in monitoraggio di Azure Application Insights per Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 39897e490e4653fbaad7a64ecc0b33f161d1264b
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165791"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processori di telemetria (anteprima)-Application Insights di monitoraggio di Azure per Java

> [!NOTE]
> Questa funzionalità è ancora in anteprima.

Java 3,0 Agent per Application Insights ora offre le funzionalità per elaborare i dati di telemetria prima che i dati vengano esportati.

Di seguito sono riportati alcuni casi d'uso dei processori di telemetria:
 * Maschera dati sensibili
 * Aggiungere dimensioni personalizzate in modo condizionale
 * Aggiornare il nome usato per l'aggregazione e la visualizzazione nella portale di Azure
 * Attributi drop span per controllare il costo di inserimento

## <a name="terminology"></a>Terminologia

Prima di passare ai processori di telemetria, è importante comprendere a cosa si riferisce l'intervallo di termini.

Un intervallo è un termine generale per uno di questi tre elementi:

* Una richiesta in ingresso
* Dipendenza in uscita (ad esempio, una chiamata remota a un altro servizio)
* Dipendenza in-process, ad esempio il lavoro eseguito dai sottocomponenti del servizio.

Ai fini dei processori di telemetria, i componenti importanti di un intervallo sono i seguenti:

* Nome
* Attributi

Il nome dell'intervallo è lo schermo principale utilizzato per le richieste e le dipendenze nella portale di Azure.

Gli attributi span rappresentano proprietà standard e personalizzate di una determinata richiesta o dipendenza.

## <a name="telemetry-processor-types"></a>Tipi di processori di telemetria

Attualmente esistono due tipi di processori di telemetria.

#### <a name="attribute-processor"></a>Processore di attributi

Un processore di attributi è in grado di inserire, aggiornare, eliminare o gli attributi hash.
Può inoltre estrarre (tramite un'espressione regolare) uno o più nuovi attributi da un attributo esistente.

#### <a name="span-processor"></a>Processore span

Un processore span è in grado di aggiornare il nome della telemetria.
Può inoltre estrarre (tramite un'espressione regolare) uno o più nuovi attributi dal nome dell'estensione.

> [!NOTE]
> Si noti che attualmente i processori di telemetria elaborano solo gli attributi di tipo stringa e non elaborano gli attributi di tipo booleano o numero.

## <a name="getting-started"></a>Introduzione

Creare un file di configurazione denominato `applicationinsights.json` e posizionarlo nella stessa directory di `applicationinsights-agent-*.jar` , con il modello seguente.

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

## <a name="includeexclude-criteria"></a>Includi/Escludi criteri

Entrambi i processori di attributi e di intervallo supportano facoltativo `include` e i `exclude` criteri.
Un processore verrà applicato solo a questi intervalli che corrispondono ai `include` criteri (se specificati) _e_ non corrispondono ai `exclude` criteri (se specificati).

Per configurare questa opzione, in `include` e/o almeno `exclude` uno `matchType` e uno di `spanNames` o `attributes` è obbligatorio.
La configurazione di inclusione/esclusione è supportata per avere più di una condizione specificata.
Tutte le condizioni specificate devono restituire true perché si verifichi una corrispondenza. 

**Campo obbligatorio**: 
* `matchType` Controlla la modalità di interpretazione degli elementi nelle `spanNames` `attributes` matrici e. I possibili valori sono `regexp` o `strict`. 

**Campi facoltativi**: 
* `spanNames` deve corrispondere ad almeno uno degli elementi. 
* `attributes` Specifica l'elenco di attributi da confrontare. Tutti questi attributi devono corrispondere esattamente affinché si verifichi una corrispondenza.

> [!NOTE]
> Se `include` `exclude` vengono specificati sia che, le `include` proprietà vengono verificate prima delle `exclude` Proprietà.

#### <a name="sample-usage"></a>Esempio di utilizzo

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
Per ulteriori informazioni, vedere la documentazione relativa agli [esempi di processori di telemetria](./java-standalone-telemetry-processors-examples.md) .

## <a name="attribute-processor"></a>Processore di attributi

Il processore degli attributi modifica gli attributi di un intervallo. Supporta facoltativamente la possibilità di includere/escludere gli intervalli. Accetta un elenco di azioni che vengono eseguite nell'ordine specificato nel file di configurazione. Le azioni supportate sono:

### `insert`

Inserisce un nuovo attributo in spans in cui la chiave non esiste già.   

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
Per l' `insert` azione, sono necessari i seguenti elementi
  * `key`
  * uno di `value` o `fromAttribute`
  * `action`:`insert`

### `update`

Aggiorna un attributo in intervalli in cui esiste la chiave

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
Per l' `update` azione, sono necessari i seguenti elementi
  * `key`
  * uno di `value` o `fromAttribute`
  * `action`:`update`


### `delete` 

Elimina un attributo da un intervallo

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
Per l' `delete` azione, sono necessari i seguenti elementi
  * `key`
  * `action`: `delete`

### `hash`

Hash (SHA1) valore di attributo esistente

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
Per l' `hash` azione, sono necessari i seguenti elementi
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Questa funzionalità è solo in 3.0.1 e versioni successive

Estrae i valori usando una regola di espressione regolare dalla chiave di input alle chiavi di destinazione specificate nella regola. Se una chiave di destinazione esiste già, verrà sottoposta a override. Si comporta in modo simile all'impostazione [span Processor](#extract-attributes-from-span-name) `toAttributes` con l'attributo esistente come origine.

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
Per l' `extract` azione, sono necessari i seguenti elementi
* `key`
* `pattern`
* `action` : `extract`

Per ulteriori informazioni, vedere la documentazione relativa agli [esempi di processori di telemetria](./java-standalone-telemetry-processors-examples.md) .

## <a name="span-processor"></a>Processore span

Il processore span modifica il nome dell'intervallo o gli attributi di un intervallo in base al nome dell'estensione. Supporta facoltativamente la possibilità di includere/escludere gli intervalli.

### <a name="name-a-span"></a>Assegnare un nome a un intervallo

L'impostazione seguente è obbligatoria come parte della sezione Nome:

* `fromAttributes`: Il valore dell'attributo per le chiavi viene usato per creare un nuovo nome nell'ordine specificato nella configurazione. È necessario specificare tutte le chiavi dell'attributo nell'intervallo per la ridenominazione da parte del processore.

È possibile configurare facoltativamente la seguente impostazione:

* `separator`: Stringa, specificata, che verrà usata per suddividere i valori
> [!NOTE]
> Se la ridenominazione dipende dagli attributi modificati dal processore degli attributi, verificare che il processore span venga specificato dopo il processore degli attributi nella specifica della pipeline.

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

### <a name="extract-attributes-from-span-name"></a>Estrai attributi dal nome dell'estensione

Accetta un elenco di espressioni regolari per la corrispondenza tra il nome dell'estensione e l'estrazione degli attributi in base alle sottoespressioni. Deve essere specificato nella `toAttributes` sezione.

Sono necessarie le impostazioni seguenti:

`rules` : Elenco di regole per estrarre i valori degli attributi dal nome dell'estensione. I valori nel nome dell'intervallo sono sostituiti dai nomi degli attributi estratti. Ogni regola nell'elenco è una stringa di criteri Regex. Il nome dell'estensione viene verificato rispetto all'espressione regolare. Se l'espressione regolare corrisponde, tutte le sottoespressioni denominate dell'espressione regolare vengono estratte come attributi e vengono aggiunte all'intervallo. Ogni nome di sottoespressione diventa un nome di attributo e la parte corrispondente della sottoespressione diventa il valore dell'attributo. La parte corrispondente nel nome dell'intervallo viene sostituita dal nome dell'attributo Estratto. Se gli attributi sono già presenti nell'intervallo, verranno sovrascritti. Il processo viene ripetuto per tutte le regole nell'ordine in cui sono specificate. Ogni regola successiva funziona sul nome dell'intervallo che rappresenta l'output dopo l'elaborazione della regola precedente.

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

## <a name="list-of-attributes"></a>Elenco di attributi

Di seguito è riportato un elenco di alcuni attributi di intervallo comuni che possono essere usati nei processori di telemetria.

### <a name="http-spans"></a>Intervalli HTTP

| Attributo  | Type | Descrizione | 
|---|---|---|
| `http.method` | string | Metodo di richiesta HTTP.|
| `http.url` | string | URL completo della richiesta HTTP nel formato `scheme://host[:port]/path?query[#fragment]` . In genere, il frammento non viene trasmesso tramite HTTP, ma se è noto, deve essere incluso comunque.|
| `http.status_code` | d'acquisto | [Codice di stato della risposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo di protocollo HTTP utilizzato |
| `http.user_agent` | string | Valore dell'intestazione [http dell'agente utente](https://tools.ietf.org/html/rfc7231#section-5.5.3) inviata dal client. |

### <a name="jdbc-spans"></a>Intervalli JDBC

| Attributo  | Type | Descrizione  |
|---|---|---|
| `db.system` | string | Identificatore per il prodotto del sistema di gestione di database (DBMS) utilizzato. |
| `db.connection_string` | string | Stringa di connessione utilizzata per la connessione al database. Si consiglia di rimuovere le credenziali incorporate.|
| `db.user` | string | Nome utente per l'accesso al database. |
| `db.name` | string | Questo attributo viene utilizzato per segnalare il nome del database a cui si accede. Per i comandi che comunicano il database, questa opzione deve essere impostata sul database di destinazione, anche se il comando non riesce.|
| `db.statement` | string | Istruzione di database in esecuzione.|
