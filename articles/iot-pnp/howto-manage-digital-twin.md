---
title: Come gestire i IoT Plug and Play gemelli digitali
description: Come gestire un IoT Plug and Play usando le API di gemelli digitali
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739873"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Gestire IoT Plug and Play gemelli digitali

IoT Plug and Play supporta Get **digital twin (Ottieni gemelli digitali)** **e Update digital twin** operations to manage digital twins (Aggiorna le operazioni dei gemelli digitali per gestire i gemelli digitali). È possibile usare le [API REST o](/rest/api/iothub/service/digitaltwin) uno degli SDK del [servizio](libraries-sdks.md).

Al momento della stesura di questo video, la versione dell'API del gemello digitale è `2020-09-30` .

## <a name="update-a-digital-twin"></a>Aggiornare un gemello digitale

Un IoT Plug and Play implementa un modello descritto da [DTDL (Digital Twins Definition Language v2).](https://github.com/Azure/opendigitaltwins-dtdl) Gli sviluppatori di soluzioni possono **usare l'API Di** aggiornamento di Gemelli digitali per aggiornare lo stato del componente e le proprietà del gemello digitale.

Il IoT Plug and Play usato come esempio in questo articolo implementa il modello [Temperature Controller](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) con componenti [Thermostat.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

Il frammento di codice seguente mostra la risposta a una **richiesta Get digital twin** formattata come oggetto JSON. Per altre informazioni sul formato dei gemelli digitali, vedere [Informazioni IoT Plug and Play gemelli digitali:](./concepts-digital-twin.md#digital-twin-example)

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

I gemelli digitali consentono di aggiornare un intero componente o proprietà usando una [patch JSON.](http://jsonpatch.com/)

Ad esempio, è possibile aggiornare la `targetTemperature` proprietà come segue:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

L'aggiornamento precedente imposta il valore desiderato di una proprietà nel corrispondente livello di `$metadata` componente, come illustrato nel frammento di codice seguente. L'hub IoT aggiorna la versione desiderata della proprietà:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Aggiungere, sostituire o rimuovere un componente

Le operazioni a livello di componente richiedono un marcatore `$metadata` di oggetto vuoto all'interno del valore.

Un'operazione di aggiunta o sostituzione di componenti imposta i valori desiderati di tutte le proprietà fornite. Cancella anche i valori desiderati per tutte le proprietà scrivibili non fornite con l'aggiornamento.

La rimozione di un componente cancella i valori desiderati di tutte le proprietà scrivibili presenti. Un dispositivo sincronizza infine la rimozione e interrompe la segnalazione delle singole proprietà. Il componente viene quindi rimosso dal gemello digitale.

L'esempio di patch JSON seguente illustra come aggiungere, sostituire o rimuovere un componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Aggiungere, sostituire o rimuovere una proprietà

Un'operazione di aggiunta o sostituzione imposta il valore desiderato di una proprietà. Il dispositivo può sincronizzare lo stato e segnalare un aggiornamento del valore insieme a `ack` un codice, una versione e una descrizione.

La rimozione di una proprietà cancella il valore desiderato della proprietà, se impostata. Il dispositivo può quindi arrestare la segnalazione di questa proprietà e viene rimossa dal componente. Se questa proprietà è l'ultima nel componente, viene rimosso anche il componente .

L'esempio di patch JSON seguente illustra come aggiungere, sostituire o rimuovere una proprietà all'interno di un componente:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regole per l'impostazione del valore desiderato di una proprietà del gemello digitale

**Nome**

Il nome di un componente o di una proprietà deve essere un nome DTDL v2 valido.

I caratteri consentiti sono a-z, A-Z, 0-9 (non come primo carattere) e caratteri di sottolineatura (non come primo o ultimo carattere).

Un nome può contenere da 1 a 64 caratteri.

**Valore della proprietà**

Il valore deve essere una proprietà [DTDL v2 valida.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)

Sono supportati tutti i tipi primitivi. All'interno di tipi complessi sono supportate enumerazioni, mappe e oggetti. Per altre informazioni, vedere [Schemi DTDL v2.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)

Le proprietà non supportano una matrice o uno schema complesso con una matrice.

Per un oggetto complesso è supportata una profondità massima di cinque livelli.

Tutti i nomi di campo all'interno di un oggetto complesso devono essere nomi DTDL v2 validi.

Tutte le chiavi di mapping devono essere nomi DTDL v2 validi.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Risolvere gli errori di aggiornamento dell'API gemelli digitali

L'API del gemello digitale genera il messaggio di errore generico seguente:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Se viene visualizzato questo errore, assicurarsi che la patch di aggiornamento segua le regole per l'impostazione [del valore desiderato di una proprietà del gemello digitale](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Quando si aggiorna un componente, assicurarsi che l'oggetto vuoto $metadata [marcatore](#add-replace-or-remove-a-component) sia impostato.

Gli aggiornamenti possono non riuscire se i valori segnalati di un dispositivo non sono conformi alle [convenzioni plug and play IoT](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le informazioni sui gemelli digitali, ecco alcune risorse aggiuntive:

- [Interagire con un dispositivo dalla soluzione](quickstart-service.md)
- [API REST di Gemelli digitali IoT](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
