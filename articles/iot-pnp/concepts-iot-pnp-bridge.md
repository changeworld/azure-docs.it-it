---
title: Plug and Play Bridge | Microsoft Docs
description: Informazioni sul Bridge Plug and Play e su come usarlo per connettere i dispositivi esistenti collegati a un gateway Windows o Linux come dispositivi Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e20e8ff6492db39d281fb70e35dd575ac3f8f991
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066858"
---
# <a name="iot-plug-and-play-bridge"></a>Bridge Plug and Play IoT

Il Plug and Play Bridge è un'applicazione open source per connettere i dispositivi esistenti collegati al gateway Windows o Linux come dispositivi Plug and Play. Dopo l'installazione e la configurazione dell'applicazione nel computer Windows o Linux, è possibile usarla per connettere i dispositivi collegati a un hub Internet. È possibile usare il Bridge per eseguire il mapping delle interfacce Plug and Play ai dati di telemetria inviati dai dispositivi collegati, usare le proprietà del dispositivo e richiamare i comandi.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Sul lato sinistro sono presenti un paio di sensori esistenti collegati (sia cablati che wireless) a un PC Windows o Linux che contiene il Plug and Play Bridge. Il Plug and Play Bridge si connette quindi a un hub Internet delle cose sul lato destro":::

Internet delle cose Plug and Play Bridge può essere distribuito come eseguibile autonomo su qualsiasi dispositivo, PC industriale, server o gateway che esegue Windows 10 o Linux. Può anche essere compilato nel codice dell'applicazione. Un semplice file JSON di configurazione indica ai Plug and Play Bridge i dispositivi/periferiche collegati che devono essere esposti ad Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolli e sensori supportati

Per impostazione predefinita, i tipi di periferiche Plug and Play Bridge supportano i seguenti tipi di periferiche, con collegamenti alla documentazione dell'adapter:

|Periferica|Windows|Linux|
|---------|---------|---------|
|La connessione della [scheda sensore Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) ha rilevato sensori abilitati per l'alimentazione a basso consumo (BLE).       |Sì|No|
|La [scheda fotocamera](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) connette le fotocamere in un dispositivo Windows 10.               |Sì|No|
|L' [Adapter Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) connette i sensori in un dispositivo Modbus.              |Sì|Sì|
|L' [Adapter MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) connette i dispositivi che usano un broker MQTT.                  |Sì|Sì|
|L' [Adapter SerialPnP](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) connette i dispositivi che comunicano tramite una connessione seriale.               |Sì|Sì|
|Le [periferiche USB di Windows](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) usano un elenco di classi di interfaccia del dispositivo supportate dall'adapter per connettere i dispositivi con un ID hardware specifico.  |Sì|Non applicabile|

Per informazioni su come estendere il Plug and Play Bridge per supportare altri protocolli del dispositivo, vedere [estendere il bridge plug and Play](howto-author-pnp-bridge-adapter.md). Per informazioni su come creare e distribuire il Bridge Plug and Play, vedere [creare e distribuire il bridge plug and Play](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Architettura Plug and Play Bridge

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Sul lato sinistro sono disponibili diverse caselle che indicano le varie periferiche collegate a un PC Windows o Linux che contiene il Plug and Play Bridge. Dalla parte superiore, una casella con etichetta punti di configurazione verso il Bridge. Il Bridge si connette quindi a un hub Internet delle cose sul lato destro del diagramma.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Adattatori Plug and Play Bridge

Internet delle cose Plug and Play Bridge supporta una serie di schede Plug and Play Bridge per diversi tipi di dispositivo. Un *manifesto dell'adapter* definisce in modo statico gli adapter a un Bridge.

Bridge Adapter Manager usa il manifesto per identificare e chiamare le funzioni dell'adapter. Gestione adattatori chiama solo la funzione create sugli adapter Bridge richiesti dai componenti dell'interfaccia elencati nel file di configurazione. Viene creata un'istanza dell'adapter per ogni componente Plug and Play.

Un adattatore Bridge crea e acquisisce un handle di interfaccia gemello digitale. L'adapter usa questo handle per associare le funzionalità del dispositivo al dispositivo gemello digitale.

Usando le informazioni nel file di configurazione, l'adattatore Bridge usa le tecniche seguenti per abilitare la comunicazione tra dispositivi completi e dispositivi gemelli tramite il Bridge:

- Stabilisce direttamente un canale di comunicazione.
- Crea un Watcher dispositivo per attendere che un canale di comunicazione diventi disponibile.

### <a name="configuration-file"></a>File di configurazione

Il Bridge Plug and Play usa un file di configurazione basato su JSON che specifica:

- Come connettersi a un hub o a un'applicazione IoT Central: le opzioni includono stringhe di connessione, parametri di autenticazione o il servizio Device provisioning (DPS).
- La posizione dei Plug and Play i modelli di capacità utilizzati dal Bridge. Il modello definisce le funzionalità di un dispositivo Plug and Play e è statico e non modificabile.
- Un elenco dei componenti dell'interfaccia Plug and Play e le informazioni seguenti per ogni componente:
- ID di interfaccia e nome del componente.
- Adattatore Bridge necessario per interagire con il componente.
- Informazioni sul dispositivo necessarie alla scheda Bridge per stabilire la comunicazione con il dispositivo. Ad esempio un ID hardware o informazioni specifiche per un adapter, un'interfaccia o un protocollo.
- Una configurazione di interfaccia o sottotipo di adattatore Bridge facoltativo se l'adapter supporta più tipi di comunicazione con dispositivi simili. Nell'esempio viene illustrato come configurare un componente del sensore Bluetooth:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Elenco facoltativo di parametri dell'adattatore Bridge globale. Ad esempio, l'adattatore del Bridge del sensore Bluetooth dispone di un dizionario di configurazioni supportate. Un componente dell'interfaccia che richiede la scheda sensore Bluetooth può scegliere una delle configurazioni seguenti `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Scarica Plug and Play Bridge

È possibile scaricare una versione predefinita del Bridge con gli adapter supportati in Internet delle cose [plug and Play le versioni Bridge](https://github.com/Azure/iot-plug-and-play-bridge/releases) ed espandere l'elenco degli asset per la versione più recente. Scaricare la versione più recente dell'applicazione per il sistema operativo in uso.

È anche possibile scaricare e visualizzare il codice sorgente del [plug and Play Bridge su GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di una panoramica dell'architettura di Internet delle cose Plug and Play Bridge, i passaggi successivi sono ulteriori informazioni su:

- [Come connettere un esempio Plug and Play Bridge in esecuzione in Linux o Windows all'hub Internet](./howto-use-iot-pnp-bridge.md)
- [Compila e Distribuisci Plug and Play Bridge](howto-build-deploy-extend-pnp-bridge.md)
- [Estendi Plug and Play Bridge](howto-author-pnp-bridge-adapter.md)
- [Plug and Play Bridge su GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
