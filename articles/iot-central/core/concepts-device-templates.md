---
title: Che cosa sono i modelli di dispositivo in Azure IoT Central | Microsoft Docs
description: I modelli di dispositivo IoT Central di Azure consentono di specificare il comportamento dei dispositivi connessi all'applicazione. Un modello di dispositivo specifica i dati di telemetria, le proprietà e i comandi che devono essere implementati dal dispositivo. Un modello di dispositivo definisce anche l'interfaccia utente per il dispositivo in IoT Central, ad esempio i moduli e i dashboard utilizzati da un operatore.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 2396768d87b93c4df16b6de78d03faf1d8d1cc2b
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492002"
---
# <a name="what-are-device-templates"></a>Che cosa sono i modelli di dispositivo?

_Questo articolo si applica agli sviluppatori di dispositivi e ai generatori di soluzioni._

Un modello di dispositivo in IoT Central di Azure è un progetto che definisce le caratteristiche e i comportamenti di un tipo di dispositivo che si connette all'applicazione. Ad esempio, il modello di dispositivo definisce i dati di telemetria inviati da un dispositivo in modo che IoT Central possibile creare visualizzazioni che usano le unità e i tipi di dati corretti.

Un generatore di soluzioni aggiunge modelli di dispositivo a un'applicazione IoT Central. Uno sviluppatore di dispositivi scrive il codice del dispositivo che implementa i comportamenti definiti nel modello di dispositivo.

Un modello di dispositivo include le sezioni seguenti:

- _Un modello di dispositivo_. Questa parte del modello di dispositivo definisce il modo in cui il dispositivo interagisce con l'applicazione. Uno sviluppatore di dispositivi implementa i comportamenti definiti nel modello.
    - _Componente predefinito_. Ogni modello di dispositivo ha un componente predefinito. L'interfaccia del componente predefinito descrive le funzionalità specifiche del modello di dispositivo.
    - _Componenti_ di. Un modello di dispositivo può includere componenti oltre al componente predefinito per descrivere le funzionalità del dispositivo. Ogni componente dispone di un'interfaccia che descrive le funzionalità del componente. Le interfacce dei componenti possono essere riutilizzate in altri modelli di dispositivo. Ad esempio, diversi modelli di dispositivi telefonici possono usare la stessa interfaccia della fotocamera.
    - _Interfacce ereditate_. Un modello di dispositivo contiene una o più interfacce che estendono le funzionalità del componente predefinito.
- _Proprietà del cloud_. Questa parte del modello di dispositivo consente allo sviluppatore di soluzioni di specificare i metadati del dispositivo da archiviare. Le proprietà del cloud non vengono mai sincronizzate con i dispositivi e esistono solo nell'applicazione. Le proprietà del cloud non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il modello di dispositivo.
- _Personalizzazioni_. Questa parte del modello di dispositivo consente allo sviluppatore di soluzioni di eseguire l'override di alcune delle definizioni nel modello di dispositivo. Le personalizzazioni sono utili se lo sviluppatore della soluzione vuole perfezionare il modo in cui l'applicazione gestisce un valore, ad esempio modificando il nome visualizzato per una proprietà o il colore usato per visualizzare un valore di telemetria. Le personalizzazioni non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il modello di dispositivo.
- _Viste_. Questa parte del modello di dispositivo consente allo sviluppatore di soluzioni di definire le visualizzazioni per visualizzare i dati dal dispositivo e i moduli per gestire e controllare un dispositivo. Le visualizzazioni usano il modello di dispositivo, le proprietà del cloud e le personalizzazioni. Le visualizzazioni non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il modello di dispositivo.

## <a name="device-models"></a>Modelli di dispositivo

Un modello di dispositivo definisce il modo in cui un dispositivo interagisce con l'applicazione IoT Central. Lo sviluppatore del dispositivo deve verificare che il dispositivo implementi i comportamenti definiti nel modello di dispositivo in modo che IoT Central possibile monitorare e gestire il dispositivo. Un modello di dispositivo è costituito da una o più _interfacce_ e ogni interfaccia può definire una raccolta di tipi di dati di _telemetria_ , _proprietà del dispositivo_ e _comandi_. Uno sviluppatore di soluzioni può importare un file JSON che definisce il modello di dispositivo in un modello di dispositivo o usare l'interfaccia utente Web in IoT Central per creare o modificare un modello di dispositivo. Le modifiche apportate a un modello di dispositivo tramite l'interfaccia utente Web richiedono il [controllo delle versioni del modello di dispositivo](./howto-version-device-template.md).

Uno sviluppatore di soluzioni può anche esportare un file JSON che contiene il modello di dispositivo. Uno sviluppatore di dispositivi può usare questo documento JSON per comprendere il modo in cui il dispositivo deve comunicare con l'applicazione IoT Central.

Il file JSON che definisce il modello di dispositivo usa [Digital Twin Definition Language (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central prevede che il file JSON contenga il modello di dispositivo con le interfacce definite inline, anziché in file distinti. Per altre informazioni, vedere la [Guida alla modellazione degli plug and Play](../../iot-pnp/concepts-modeling-guide.md).

Un dispositivo molto comune è costituito da:

- Parti personalizzate, ovvero le operazioni che rendono univoco il dispositivo.
- Parti standard, ovvero cose comuni a tutti i dispositivi.

Queste parti sono denominate _interfacce_ in un modello di dispositivo. Le interfacce definiscono i dettagli di ogni parte implementata dal dispositivo. Le interfacce sono riutilizzabili tra i modelli di dispositivo. In DTDL un componente si riferisce a un'altra interfaccia, che può essere definita in un file DTDL separato o in una sezione separata del file.

Nell'esempio seguente viene illustrata la struttura del modello di dispositivo per un [dispositivo controller di temperatura](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json). Il componente predefinito include le definizioni per `workingSet` , `serialNumber` e `reboot` . Il modello di dispositivo include anche due `thermostat` componenti e un `deviceInformation` componente. Il contenuto dei tre componenti è stato rimosso per motivi di brevità:

```json
[
  {
    "@context": [
      "dtmi:iotcentral:context;2",
      "dtmi:dtdl:context;2"
    ],
    "@id": "dtmi:com:example:TemperatureController;2",
    "@type": "Interface",
    "contents": [
      {
        "@type": [
          "Telemetry",
          "DataSize"
        ],
        "description": {
          "en": "Current working set of the device memory in KiB."
        },
        "displayName": {
          "en": "Working Set"
        },
        "name": "workingSet",
        "schema": "double",
        "unit": "kibibit"
      },
      {
        "@type": "Property",
        "displayName": {
          "en": "Serial Number"
        },
        "name": "serialNumber",
        "schema": "string",
        "writable": false
      },
      {
        "@type": "Command",
        "commandType": "synchronous",
        "description": {
          "en": "Reboots the device after waiting the number of seconds specified."
        },
        "displayName": {
          "en": "Reboot"
        },
        "name": "reboot",
        "request": {
          "@type": "CommandPayload",
          "description": {
            "en": "Number of seconds to wait before rebooting the device."
          },
          "displayName": {
            "en": "Delay"
          },
          "name": "delay",
          "schema": "integer"
        }
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat1"
        },
        "name": "thermostat1",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat2"
        },
        "name": "thermostat2",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "DeviceInfo"
        },
        "name": "deviceInformation",
        "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1"
      }
    ],
    "displayName": {
      "en": "Temperature Controller"
    }
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;2",
    "@type": "Interface",
    "displayName": "Thermostat",
    "description": "Reports current temperature and provides desired temperature control.",
    "contents": [
      ...
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:azure:DeviceManagement:DeviceInformation;1",
    "@type": "Interface",
    "displayName": "Device Information",
    "contents": [
      ...
    ]
  }
]
```

In un'interfaccia sono presenti alcuni campi obbligatori:

- `@id`: ID univoco sotto forma di un semplice nome di risorsa uniforme.
- `@type`: dichiara che questo oggetto è un'interfaccia.
- `@context`: specifica la versione di DTDL usata per l'interfaccia.
- `contents`: elenca le proprietà, la telemetria e i comandi che compongono il dispositivo. Le funzionalità possono essere definite in più interfacce.

Sono disponibili alcuni campi facoltativi che è possibile usare per aggiungere altri dettagli al modello di funzionalità, ad esempio il nome visualizzato e la descrizione.

Ogni voce dell'elenco di interfacce nella sezione Implements include:

- `name`: nome di programmazione dell'interfaccia.
- `schema`: interfaccia implementata dal modello di funzionalità.

## <a name="interfaces"></a>Interfacce

DTDL consente di descrivere le funzionalità del dispositivo. Le funzionalità correlate sono raggruppate in interfacce. Le interfacce descrivono le proprietà, la telemetria e i comandi che una parte del dispositivo implementa:

- `Properties`. Le proprietà sono campi dati che rappresentano lo stato del dispositivo. Usare le proprietà per rappresentare lo stato durevole del dispositivo, ad esempio lo stato di spegnimento di un pompa di raffreddamento. Le proprietà possono anche rappresentare le proprietà di base dei dispositivi, ad esempio la versione del firmware del dispositivo. È possibile dichiarare le proprietà in sola lettura o in scrittura. Solo i dispositivi possono aggiornare il valore di una proprietà di sola lettura. Un operatore può impostare il valore di una proprietà scrivibile da inviare a un dispositivo.
- `Telemetry`. I campi di telemetria rappresentano le misurazioni dai sensori. Ogni volta che il dispositivo acquisisce una misura del sensore, deve inviare un evento di telemetria contenente i dati del sensore.
- `Commands`. I comandi rappresentano i metodi che gli utenti del dispositivo possono eseguire sul dispositivo. Ad esempio, un comando reset o un comando per attivare o disattivare un ventilatore.

Nell'esempio seguente viene illustrata la definizione dell'interfaccia del termostato:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
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

Questo esempio mostra due proprietà (una sola lettura e una scrivibile), un tipo di telemetria e un comando. Una descrizione del campo minima presenta:

- `@type` per specificare il tipo di funzionalità: `Telemetry` , `Property` o `Command` .  In alcuni casi, il tipo include un tipo semantico per consentire IoT Central di fare alcune ipotesi sulla gestione del valore.
- `name` per il valore di telemetria.
- `schema` per specificare il tipo di dati per la telemetria o la proprietà. Questo valore può essere un tipo primitivo, ad esempio Double, Integer, Boolean o String. Sono supportati anche tipi di oggetto complessi e mappe.

I campi facoltativi, ad esempio nome visualizzato e descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

## <a name="properties"></a>Proprietà

Per impostazione predefinita, le proprietà sono di sola lettura. Le proprietà di sola lettura indicano che il dispositivo segnala gli aggiornamenti del valore della proprietà all'applicazione IoT Central. L'applicazione IoT Central non può impostare il valore di una proprietà di sola lettura.

È anche possibile contrassegnare una proprietà come scrivibile in un'interfaccia. Un dispositivo può ricevere un aggiornamento a una proprietà scrivibile dall'applicazione IoT Central, nonché segnalare gli aggiornamenti del valore della proprietà per l'applicazione.

Non è necessario che i dispositivi siano connessi per impostare i valori delle proprietà. I valori aggiornati vengono trasferiti alla successiva connessione del dispositivo all'applicazione. Questo comportamento si applica sia alle proprietà di sola lettura che a quelle scrivibili.

Non usare le proprietà per inviare dati di telemetria dal dispositivo. Una proprietà di sola lettura, ad esempio, `temperatureSetting=80` deve indicare che la temperatura del dispositivo è stata impostata su 80 e che il dispositivo sta provando a raggiungere o rimanere in corrispondenza della temperatura.

Per le proprietà scrivibili, l'applicazione del dispositivo restituisce un codice di stato desiderato, una versione e una descrizione per indicare se ha ricevuto e applicato il valore della proprietà.

## <a name="telemetry"></a>Telemetria

IoT Central consente di visualizzare i dati di telemetria nei dashboard e nei grafici e di usare le regole per attivare le azioni quando vengono raggiunte le soglie. IoT Central USA le informazioni nel modello di dispositivo, ad esempio tipi di dati, unità e nomi visualizzati, per determinare la modalità di visualizzazione dei valori di telemetria.

È possibile usare la funzionalità di esportazione dei dati IoT Central per trasmettere i dati di telemetria ad altre destinazioni, ad esempio archiviazione o hub eventi.

## <a name="commands"></a>Comandi

Per impostazione predefinita, un comando deve essere eseguito entro 30 secondi e il dispositivo deve essere connesso all'arrivo del comando. Se il dispositivo risponde nel tempo o il dispositivo non è connesso, il comando ha esito negativo.

I comandi possono avere parametri di richiesta e restituire una risposta.

### <a name="offline-commands"></a>Comandi offline

È possibile scegliere i comandi della coda se un dispositivo è attualmente offline abilitando l'opzione **queue if offline** per un comando nel modello di dispositivo.

I comandi offline sono notifiche unidirezionali al dispositivo dalla soluzione. I comandi offline possono avere parametri della richiesta ma non restituiscono una risposta.

> [!NOTE]
> Questa opzione è disponibile solo nell'interfaccia utente Web IoT Central. Questa impostazione non è inclusa se si esporta un modello o un'interfaccia dal modello di dispositivo.

## <a name="cloud-properties"></a>Proprietà cloud

Le proprietà del cloud fanno parte del modello di dispositivo, ma non fanno parte del modello di dispositivo. Le proprietà del cloud consentono allo sviluppatore di soluzioni di specificare i metadati del dispositivo da archiviare nell'applicazione IoT Central. Le proprietà del cloud non influiscono sul codice scritto da uno sviluppatore di dispositivi per implementare il modello di dispositivo.

Uno sviluppatore di soluzioni può aggiungere proprietà del cloud a dashboard e visualizzazioni insieme alle proprietà del dispositivo per consentire a un operatore di gestire i dispositivi connessi all'applicazione. Uno sviluppatore di soluzioni può anche usare le proprietà del cloud come parte di una definizione della regola per rendere un valore di soglia modificabile da un operatore.

## <a name="customizations"></a>Personalizzazioni

Le personalizzazioni fanno parte del modello di dispositivo, ma non fanno parte del modello di dispositivo. Le personalizzazioni consentono allo sviluppatore della soluzione di migliorare o eseguire l'override di alcune delle definizioni nel modello di dispositivo. Uno sviluppatore di soluzioni può, ad esempio, modificare il nome visualizzato per una proprietà o un tipo di telemetria. Uno sviluppatore di soluzioni può inoltre utilizzare le personalizzazioni per aggiungere la convalida, ad esempio la lunghezza minima o massima per una proprietà del dispositivo stringa.

Le personalizzazioni possono influenzare il codice scritto da uno sviluppatore di dispositivi per implementare il modello di dispositivo. Ad esempio, una personalizzazione potrebbe impostare lunghezze di stringa minime e massime o valori numerici minimi e massimi per la telemetria.

## <a name="views"></a>Viste

Uno sviluppatore di soluzioni crea visualizzazioni che consentono agli operatori di monitorare e gestire i dispositivi connessi. Le visualizzazioni fanno parte del modello di dispositivo, quindi una vista è associata a un tipo di dispositivo specifico. Una vista può includere:

- Grafici per tracciare i dati di telemetria.
- Riquadri per visualizzare le proprietà del dispositivo di sola lettura.
- Riquadri per consentire all'operatore di modificare le proprietà dei dispositivi scrivibili.
- Riquadri per consentire all'operatore di modificare le proprietà del cloud.
- Riquadri per consentire all'operatore di chiamare comandi, inclusi i comandi che prevedono un payload.
- Riquadri per visualizzare etichette, immagini o testo Markdown.

I dati di telemetria, le proprietà e i comandi che è possibile aggiungere a una vista sono determinati dal modello di dispositivo, dalle proprietà del cloud e dalle personalizzazioni nel modello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

In qualità di sviluppatore di dispositivi, ora che sono stati appresi i modelli dei dispositivi, i passaggi successivi suggeriti sono la lettura di dati di [telemetria, proprietà e payload di comandi](./concepts-telemetry-properties-commands.md) per ottenere altre informazioni sui dati scambiati con IOT Central.

In qualità di sviluppatore di soluzioni, un passaggio successivo suggerito consiste [nel leggere definire un nuovo tipo di dispositivo Internet all'interno dell'applicazione IoT Central di Azure](./howto-set-up-template.md) per altre informazioni su come creare un modello di dispositivo.
