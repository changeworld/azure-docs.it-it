---
title: Usare i dati di posizione in una soluzione IoT Central di Azure
description: Informazioni su come usare i dati di posizione inviati da un dispositivo connesso all'applicazione IoT Central. Tracciare i dati della posizione su una mappa o creare regole di geoschermatura.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98128022"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Usare i dati di posizione in una soluzione IoT Central di Azure

Questo articolo illustra come usare i dati del percorso in un'applicazione IoT Central. Un dispositivo connesso a IoT Central può inviare i dati di posizione come flusso di telemetria o usare una proprietà del dispositivo per segnalare i dati della località.

Un generatore di soluzioni può usare i dati del percorso per:

* Tracciare il percorso segnalato su una mappa.
* Tracciare la cronologia della località di telemetria una mappa.
* Creare regole di geoschermatura per inviare una notifica a un operatore quando un dispositivo entra o esce da un'area specifica.

## <a name="add-location-capabilities-to-a-device-template"></a>Aggiungere funzionalità di posizione a un modello di dispositivo

La schermata seguente mostra un modello di dispositivo con esempi di una proprietà del dispositivo e un tipo di telemetria che usano i dati di posizione. Le definizioni utilizzano il tipo semantico **location** e il tipo di schema **Geolocation** :

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Screenshot che mostra la definizione della proprietà location nel modello di dispositivo":::

Per riferimento, le definizioni [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) per queste funzionalità hanno un aspetto simile al frammento di codice seguente:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> Il tipo di schema **Geopoint** non fa parte della [specifica DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central supporta attualmente il tipo di schema **Geopoint** e il tipo semantico di **posizione** per la compatibilità con le versioni precedenti.

## <a name="send-location-data-from-a-device"></a>Inviare i dati dell'indirizzo da un dispositivo

Quando un dispositivo invia i dati per la proprietà **DeviceLocation** illustrata nella sezione precedente, il payload è simile al frammento di codice JSON seguente:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Quando un dispositivo invia i dati per i dati di telemetria di **rilevamento** illustrati nella sezione precedente, il payload è simile al frammento di codice JSON seguente:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Visualizza percorso dispositivo

È possibile visualizzare i dati relativi alla posizione in più punti dell'applicazione IoT Central. Ad esempio, nelle visualizzazioni associate a un singolo dispositivo o ai dashboard.

Quando si crea una visualizzazione per un dispositivo, è possibile scegliere di tracciare il percorso su una mappa oppure visualizzare i singoli valori:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Screenshot che mostra la visualizzazione di esempio con i dati sulla posizione":::

È possibile aggiungere tessere mappa a un dashboard per tracciare la posizione di uno o più dispositivi. Quando si aggiunge una tessera mappa per mostrare la telemetria del percorso, è possibile tracciare il percorso in un periodo di tempo. Lo screenshot seguente mostra la posizione indicata da un dispositivo simulato negli ultimi 30 minuti:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Screenshot che mostra un dashboard di esempio con dati sulla posizione":::

## <a name="create-a-geofencing-rule"></a>Creare una regola di geoschermatura

È possibile usare la telemetria del percorso per creare una regola di georecinzione che genera un avviso quando un dispositivo si sposta all'interno o all'esterno di un'area rettangolare. Lo screenshot seguente mostra una regola che usa quattro condizioni per definire un'area rettangolare usando i valori di latitudine e longitudine. La regola genera un messaggio di posta elettronica quando il dispositivo si sposta nell'area rettangolare:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Screenshot che mostra una definizione della regola di geoschermatura":::

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare le proprietà nell'applicazione IoT Central di Azure, vedere:

* [Payloads](concepts-telemetry-properties-commands.md)
* [Creare e connettere un'applicazione client all'applicazione Azure IoT Central](tutorial-connect-device.md)