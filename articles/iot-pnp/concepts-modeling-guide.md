---
title: Informazioni sui modelli di dispositivi Plug and Play | Microsoft Docs
description: Informazioni sul linguaggio di modellazione DTDL (Digital Twin Definition Language) per i dispositivi Plug and Play. In questo articolo vengono descritti i tipi di Datatypes e complessi, i modelli di riutilizzo che utilizzano componenti ed ereditarietà e i tipi semantici. Questo articolo fornisce indicazioni sulla scelta dell'identificatore del modello del dispositivo gemello e del supporto degli strumenti per la creazione di modelli.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a3389408b0942875aa7d760f1c514b995e381f9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609479"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Guida alla modellazione Plug and Play

Il nucleo del Plug and Play Internet è un _modello_ di dispositivo che descrive le funzionalità di un dispositivo per un'applicazione con plug and Play Internet delle cose. Questo modello è strutturato come un set di interfacce che definiscono:

- _Proprietà_ che rappresentano lo stato di sola lettura e di scrittura di un dispositivo o di un'altra entità. Ad esempio, il numero di serie del dispositivo può essere una proprietà di sola lettura, mentre la temperatura di destinazione di un termostato può essere una proprietà scrivibile.
- Campi di _telemetria_ che definiscono i dati emessi da un dispositivo, se i dati sono un flusso normale di letture di sensori, un errore occasionale o un messaggio informativo.
- _Comandi_ che descrivono una funzione o un'operazione che può essere eseguita su un dispositivo. Ad esempio, un comando può riavviare un gateway o scattare una foto usando una fotocamera remota.

Per altre informazioni sul modo in cui Plug and Play usa i modelli di dispositivo, vedere la guida per gli [sviluppatori del dispositivo Plug and Play](concepts-developer-guide-device.md) plug and Play e la guida per gli sviluppatori del [servizio](concepts-developer-guide-service.md)Internet.

Per definire un modello, è possibile utilizzare il linguaggio DTDL (Digital Gemini Definition Language). DTDL usa una variante JSON denominata [JSON-LD](https://json-ld.org/). Il frammento di codice seguente mostra il modello per un dispositivo termostato che:

- Ha un ID modello univoco: `dtmi:com:example:Thermostat;1` .
- Invia la telemetria della temperatura.
- Dispone di una proprietà scrivibile per impostare la temperatura di destinazione.
- Dispone di una proprietà di sola lettura per indicare la temperatura massima dall'ultimo riavvio.
- Risponde a un comando che richiede temperature massime, minime e medie per un periodo di tempo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Il modello del termostato ha una sola interfaccia. Negli esempi successivi di questo articolo vengono illustrati modelli più complessi che utilizzano componenti ed ereditarietà.

In questo articolo viene descritto come progettare e creare modelli personalizzati e vengono trattati argomenti quali i tipi di dati, la struttura del modello e gli strumenti.

Per altre informazioni, vedere la specifica del [linguaggio di definizione dei gemelli digitali V2](https://github.com/Azure/opendigitaltwins-dtdl) .

## <a name="model-structure"></a>Struttura del modello

Le proprietà, la telemetria e i comandi sono raggruppati in interfacce. In questa sezione viene descritto come utilizzare le interfacce per descrivere modelli semplici e complessi utilizzando componenti ed ereditarietà.

### <a name="model-ids"></a>ID modello

Ogni interfaccia ha un identificatore univoco del modello DTMI (Digital gemelle). I modelli complessi usano DTMIs per identificare i componenti. Le applicazioni possono usare il DTMIs che i dispositivi inviano per individuare le definizioni di modello in un repository.

DTMIs deve seguire la convenzione di denominazione necessaria per il [repository del modello plug and Play](https://github.com/Azure/iot-plugandplay-models):

- Il prefisso DTMI è `dtmi:` .
- Il suffisso DTMI è il numero di versione per il modello, ad esempio `;2` .
- Il corpo di DTMI esegue il mapping alla cartella e al file nel repository del modello in cui è archiviato il modello. Il numero di versione fa parte del nome del file.

Ad esempio, il modello identificato da DTMI `dtmi:com:Example:Thermostat;2` viene archiviato in *DTMI/com/example/thermostat-2.jssu* file.

Il frammento di codice seguente illustra la struttura di una definizione di interfaccia con il relativo DTMI univoco:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Nessun componente

Un modello semplice, ad esempio il termostato mostrato in precedenza, non usa componenti incorporati o a cascata. I dati di telemetria, le proprietà e i comandi sono definiti nel `contents` nodo dell'interfaccia.

Nell'esempio seguente viene illustrata una parte di un modello semplice che non utilizza i componenti di:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Strumenti come Azure Internet Explorer e la finestra di progettazione del modello di dispositivo IoT Central etichettano un'interfaccia autonoma come il termostato come _componente predefinito_.

Lo screenshot seguente mostra il modo in cui il modello viene visualizzato nello strumento Esplora risorse di Azure:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Componente predefinito in Azure Internet Explorer":::

Lo screenshot seguente mostra il modo in cui il modello viene visualizzato come componente predefinito nella finestra di progettazione del modello di dispositivo IoT Central. Selezionare **Visualizza identità** per visualizzare il DTMI del modello:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Screenshot che mostra il modello del termostato in IoT Central designer":::

L'ID modello viene archiviato in una proprietà del dispositivo gemello, come illustrato nella schermata seguente:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="ID modello nella proprietà del dispositivo gemello digitale":::

Un modello DTDL senza componenti è una semplificazione utile per un dispositivo o un modulo di IoT Edge con un unico set di dati di telemetria, proprietà e comandi. Un modello che non usa i componenti semplifica la migrazione di un dispositivo o di un modulo esistente come un Plug and Play dispositivo o modulo. si crea un modello DTDL che descrive il dispositivo o il modulo effettivo senza la necessità di definire alcun componente.

> [!TIP]
> Un modulo può essere [un modulo di dispositivo o](../iot-hub/iot-hub-devguide-module-twins.md) un modulo [IOT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Riutilizzo

Esistono due modi per riutilizzare le definizioni di interfaccia. Usare più componenti in un modello per fare riferimento ad altre definizioni di interfaccia. Utilizzare l'ereditarietà per estendere le definizioni di interfaccia esistenti.

### <a name="multiple-components"></a>Più componenti

I componenti consentono di creare un'interfaccia del modello come assembly di altre interfacce.

Ad esempio, l'interfaccia del [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) è definita come modello. È possibile incorporare questa interfaccia come uno o più componenti quando si definisce il [modello di controller di temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). Nell'esempio seguente questi componenti sono denominati `thermostat1` e `thermostat2` .

Per un modello DTDL con più componenti, sono presenti due o più sezioni di componenti. Ogni sezione è `@type` impostata su `Component` e fa riferimento in modo esplicito a uno schema, come illustrato nel frammento di codice seguente:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Questo modello include tre componenti definiti nella sezione contenuto, due `Thermostat` componenti e un `DeviceInformation` componente. La sezione Content include anche le definizioni di proprietà, telemetria e comandi.

Gli screenshot seguenti illustrano il modo in cui questo modello viene visualizzato in IoT Central. Le definizioni di proprietà, telemetria e comando nel controller di temperatura vengono visualizzate nel **componente predefinito** di primo livello. Le definizioni di proprietà, telemetria e comando per ogni termostato vengono visualizzate nelle definizioni dei componenti:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Screenshot che illustra il modello di dispositivo del controller di temperatura in IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Screenshot che mostra i componenti del termostato nel modello di dispositivo del controller di temperatura in IoT Central.":::

Per informazioni su come scrivere codice dispositivo che interagisce con i componenti, vedere la guida per gli [sviluppatori di dispositivi Plug and Play](concepts-developer-guide-device.md).

Per informazioni su come scrivere codice del servizio che intergatto con i componenti in un dispositivo, vedere la guida per gli [sviluppatori del servizio Plug and Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Ereditarietà

L'ereditarietà consente di riutilizzare le funzionalità di un'interfaccia di base per estendere le funzionalità di un'interfaccia. Ad esempio, diversi modelli di dispositivo possono condividere funzionalità comuni, ad esempio un numero di serie:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Esempio di ereditarietà in un modello di dispositivo che mostra un termostato e un controller di flusso che condividono funzionalità da un'interfaccia di base." border="false":::

Il frammento di codice seguente mostra un modello DTML che usa la `extends` parola chiave per definire la relazione di ereditarietà illustrata nel diagramma precedente:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

Lo screenshot seguente mostra questo modello nell'ambiente del modello di dispositivo IoT Central:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Screenshot che mostra l'ereditarietà dell'interfaccia in IoT Central":::

Quando si scrive codice del dispositivo o del lato servizio, il codice non deve eseguire alcuna operazione speciale per gestire le interfacce ereditate. Nell'esempio illustrato in questa sezione, il codice del dispositivo segnala il numero di serie come se facesse parte dell'interfaccia del termostato.

### <a name="tips"></a>Suggerimenti

Quando si crea un modello, è possibile combinare componenti ed ereditarietà. Il diagramma seguente mostra un `thermostat` modello che eredita da un' `baseDevice` interfaccia. L' `baseDevice` interfaccia dispone di un componente, che a sua volta eredita da un'altra interfaccia:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagramma che illustra un modello che utilizza sia i componenti che l'ereditarietà." border="false":::

Il frammento di codice seguente mostra un modello DTML che usa le `extends` `component` parole chiave e per definire la relazione di ereditarietà e l'utilizzo del componente illustrato nel diagramma precedente:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Tipi di dati

Usare i tipi di dati per definire la telemetria, le proprietà e i parametri del comando. I tipi di dati possono essere primitivi o complessi. I tipi di DataType complessi usano primitivi o altri tipi complessi. La profondità massima per i tipi complessi è cinque livelli.

### <a name="primitive-types"></a>Tipi primitivi

La tabella seguente illustra il set di tipi primitivi che è possibile usare:

| Tipo primitivo | Descrizione |
| --- | --- |
| `boolean` | Valore booleano |
| `date` | Data completa, come definito nella [sezione 5,6 della specifica RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Data-ora come definito nella [specifica RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Virgola mobile a 8 byte IEEE |
| `duration` | Durata nel formato ISO 8601 |
| `float` | Virgola mobile a 4 byte IEEE |
| `integer` | Intero con segno a 4 byte |
| `long` | Intero con segno a 8 byte |
| `string` | Stringa UTF8 |
| `time` | A tempo pieno come definito nella [sezione 5,6 della specifica RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

Il frammento di codice seguente mostra una definizione di telemetria di esempio che usa il `double` tipo nel `schema` campo:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Tipi di DataType complessi

I tipi di oggetti DataType complessi sono uno dei tipi di *matrice*, *enumerazione*, *mappa*, *oggetto* o uno dei tipi geospaziali.

#### <a name="arrays"></a>Matrici

Una matrice è un tipo di dati indicizzabile in cui tutti gli elementi sono dello stesso tipo. Il tipo di elemento può essere un tipo primitivo o complesso.

Il frammento di codice seguente mostra una definizione di telemetria di esempio che usa il `Array` tipo nel `schema` campo. Gli elementi della matrice sono valori booleani:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Enumerazioni

Un'enumerazione descrive un tipo con un set di etichette denominate con mapping ai valori. I valori possono essere numeri interi o stringhe, ma le etichette sono sempre stringhe.

Il frammento di codice seguente mostra una definizione di telemetria di esempio che usa il `Enum` tipo nel `schema` campo. I valori nell'enumerazione sono numeri interi:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Mappe

Una mappa è un tipo con coppie chiave-valore in cui tutti i valori hanno lo stesso tipo. La chiave in una mappa deve essere una stringa. I valori in una mappa possono essere di qualsiasi tipo, incluso un altro tipo complesso.

Il frammento di codice seguente mostra una definizione di proprietà di esempio che usa il `Map` tipo nel `schema` campo. I valori nella mappa sono stringhe:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Oggetti

Un tipo di oggetto è costituito da campi denominati. I tipi dei campi in una mappa oggetto possono essere tipi primitivi o complessi.

Il frammento di codice seguente mostra una definizione di telemetria di esempio che usa il `Object` tipo nel `schema` campo. I campi nell'oggetto sono i `dateTime` `duration` tipi, e `string` :

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Tipi geospaziali

DTDL fornisce un set di tipi geospaziali, basati su [GeoJSON](https://geojson.org/), per la modellazione di strutture di dati geografici: `point` , `multiPoint` , `lineString` , `multiLineString` , `polygon` e `multiPolygon` . Questi tipi sono strutture annidate predefinite di matrici, oggetti ed enumerazioni.

Il frammento di codice seguente mostra una definizione di telemetria di esempio che usa il `point` tipo nel `schema` campo:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Poiché i tipi geospaziali sono basati su matrici, non possono attualmente essere usati nelle definizioni di proprietà.

## <a name="semantic-types"></a>Tipi semantici

Il tipo di dati della definizione di una proprietà o di una telemetria specifica il formato dei dati scambiati da un dispositivo con un servizio. Il tipo semantico fornisce informazioni sui dati di telemetria e sulle proprietà che un'applicazione può usare per determinare come elaborare o visualizzare un valore. Ogni tipo semantico ha una o più unità associate. Ad esempio, Celsius e Fahrenheit sono unità per il tipo di semantica di temperatura. IoT Central dashboard e analisi possono usare le informazioni sul tipo semantico per determinare come tracciare i valori di telemetria o di proprietà e le unità di visualizzazione. Per informazioni su come usare il parser del modello per leggere i tipi semantici, vedere [comprendere il parser del modello Digital gemelle](concepts-model-parser.md).

Il frammento di codice seguente mostra una definizione di telemetria di esempio che include informazioni sul tipo semantico. Il tipo semantico `Temperature` viene aggiunto alla `@type` matrice e il `unit` valore `degreeCelsius` è una delle unità valide per il tipo semantico:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Localizzazione

Le applicazioni, ad esempio IoT Central, usano le informazioni nel modello per creare dinamicamente un'interfaccia utente intorno ai dati scambiati con un dispositivo Plug and Play. I riquadri in un dashboard, ad esempio, possono visualizzare nomi e descrizioni per la telemetria, le proprietà e i comandi.

I campi facoltativi `description` e `displayName` nel modello contengono stringhe destinate all'uso in un'interfaccia utente. Questi campi possono ospitare stringhe localizzate che possono essere usate da un'applicazione per il rendering di un'interfaccia utente localizzata.

Il frammento di codice seguente mostra una definizione di telemetria della temperatura di esempio che include stringhe localizzate:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

L'aggiunta di stringhe localizzate è facoltativa. Nell'esempio seguente è presente una sola lingua predefinita:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Ciclo di vita e strumenti

Le quattro fasi del ciclo di vita per un modello di dispositivo sono creazione, pubblicazione, utilizzo e controllo delle versioni:

### <a name="author"></a>Autore

I modelli di dispositivo DTML sono documenti JSON che è possibile creare in un editor di testo. Tuttavia, in IoT Central è possibile usare l'ambiente GUI modello di dispositivo per creare un modello DTML. In IoT Central è possibile:

- Creare interfacce che definiscono proprietà, telemetria e comandi.
- Usare i componenti per assemblare più interfacce.
- Definire le relazioni di ereditarietà tra le interfacce.
- Importare ed esportare i file del modello DTML.

Per altre informazioni, vedere [definire un nuovo tipo di dispositivo Internet all'interno dell'applicazione IoT Central di Azure](../iot-central/core/howto-set-up-template.md).

L' [editor di DTDL per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) offre un ambiente di modifica basato su testo con convalida della sintassi e completamento automatico per un controllo più preciso sull'esperienza di creazione di modelli.

### <a name="publish"></a>Pubblica

Per rendere i modelli DTML condivisibili e individuabili, è possibile pubblicarli in un repository di modelli di dispositivo.

Prima di pubblicare un modello nel repository pubblico, è possibile usare gli `dmr-client` strumenti per convalidare il modello.

Per altre informazioni, vedere [repository di modelli di dispositivo](concepts-model-repository.md).

### <a name="use"></a>Uso

Le applicazioni, ad esempio IoT Central, usano i modelli di dispositivo. In IoT Central, un modello fa parte del modello di dispositivo che descrive le funzionalità del dispositivo. IoT Central USA il modello di dispositivo per creare dinamicamente un'interfaccia utente per il dispositivo, inclusi dashboard e analisi.

Una soluzione personalizzata può usare il [parser di modelli digitali gemelli](concepts-model-parser.md) per comprendere le funzionalità di un dispositivo che implementa il modello. Per altre informazioni, vedere [usare i modelli plug and Play in una soluzione Internet delle](concepts-model-discovery.md)cose.

### <a name="version"></a>Versione

Per garantire che i dispositivi e le soluzioni lato server che usano i modelli continuino a funzionare, i modelli pubblicati non sono modificabili.

Il DTMI include un numero di versione che è possibile usare per creare più versioni di un modello. I dispositivi e le soluzioni lato server possono utilizzare la versione specifica che sono stati progettati per l'utilizzo.

IoT Central implementa più regole di controllo delle versioni per i modelli di dispositivo. Se si esegue la versione di un modello di dispositivo e del relativo modello in IoT Central, è possibile migrare i dispositivi da versioni precedenti a versioni successive. Tuttavia, i dispositivi migrati non possono usare nuove funzionalità senza un aggiornamento del firmware. Per altre informazioni, vedere [creare una nuova versione del modello di dispositivo](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Limiti e i vincoli

Nell'elenco seguente vengono riepilogati alcuni vincoli e limiti chiave per i modelli:

- Attualmente, la profondità massima per matrici, mappe e oggetti è cinque livelli di profondità.
- Non è possibile usare matrici nelle definizioni di proprietà.
- È possibile estendere le interfacce a una profondità di 10 livelli.
- Un'interfaccia può estendere al massimo altre due interfacce.
- Un componente non può contenere un altro componente.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modellare i dispositivi, di seguito sono riportate alcune risorse aggiuntive:

- [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md)
- [Digital Gemini Definition Language V2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repository di modelli](./concepts-model-repository.md)
