---
title: Microsoft cache connessa due livelli annidati Azure IoT Edge gateway con proxy non autenticato in uscita | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Esercitazione cache connessa gateway di Azure IoT Edge a due livelli con proxy non autenticato in uscita
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811887"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Esempio di scenario cache connessa di distribuzione di Microsoft Azure IoT Edge: gateway di distribuzione annidato a due livelli con proxy non autenticato in uscita

Il diagramma seguente descrive lo scenario in cui un gateway Azure IoT Edge ha accesso diretto alle risorse della rete CDN e funge da padre di un altro gateway Azure IoT Edge rete. Il gateway IoT Edge figlio funge da elemento padre di un Azure IoT foglia, ad esempio Raspberry Pi. Sia il Azure IoT Edge figlio che Azure IoT dispositivo sono isolati da Internet. L'esempio seguente illustra la configurazione per due livelli di gateway Azure IoT Edge, ma non esiste alcun limite alla profondità degli host upstream che verranno supportati da Microsoft cache connessa. Non esiste alcuna differenza nelle opzioni di creazione cache connessa contenitori di Microsoft degli esempi precedenti.

Per altre informazioni sulla configurazione di distribuzioni a più livelli di IoT Edge gateway Azure IoT Edge Azure IoT Edge downstream, vedere la documentazione Connettere dispositivi Azure IoT Edge [downstream.](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) Si noti anche che quando si distribuiscono Azure IoT Edge, Microsoft cache connessa e moduli personalizzati, tutti i moduli devono risiedere nello stesso registro contenitori.

>[!Note]
>Quando si distribuiscono Azure IoT Edge, microsoft cache connessa e moduli personalizzati, tutti i moduli devono risiedere nello stesso registro contenitori.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft cache connessa annidato" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configurazione del gateway padre
1. Aggiungere il modulo Microsoft cache connessa alla distribuzione del dispositivo gateway Azure IoT Edge in hub IoT di Azure (vedere [Supporto](connected-cache-disconnected-device-update.md) per dispositivi disconnessi per informazioni dettagliate su come ottenere il modulo).
2. Aggiungere le variabili di ambiente per la distribuzione. Di seguito è riportato un esempio delle variabili di ambiente.

    **Variabili di ambiente**

    | Nome                          | Valore                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Vedere le [descrizioni delle](connected-cache-configure.md) variabili di ambiente |
    | CUSTOMER_ID                   | Vedere [le descrizioni delle variabili](connected-cache-configure.md) di ambiente |
    | CUSTOMER_KEY                  | Vedere [le descrizioni delle variabili](connected-cache-configure.md) di ambiente |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Aggiungere le opzioni di creazione del contenitore per la distribuzione. Non esistono differenze nelle opzioni di creazione del contenitore MCC dell'esempio precedente. Di seguito è riportato un esempio delle opzioni di creazione del contenitore.

### <a name="container-create-options"></a>Opzioni di creazione del contenitore

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Configurazione del gateway figlio

>[!Note]
>Se sono stati replicati contenitori usati nella configurazione nel registro privato, sarà necessario apportare una modifica alle impostazioni config.toml e alle impostazioni di runtime nella distribuzione del modulo. Per altre informazioni, vedere [Connettere dispositivi downstream IoT Edge - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) per altri dettagli.


1. Modificare il percorso dell'immagine per l'agente Edge, come illustrato nell'esempio seguente:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Modificare le impostazioni di runtime dell'hub Edge e dell'agente Edge nella distribuzione Azure IoT Edge come illustrato in questo esempio:
    
    * In Hub edge immettere nel campo immagine ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * In Edge Agent (Agente Edge) immettere nel campo image (Immagine) ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Aggiungere il modulo Microsoft cache connessa alla distribuzione Azure IoT Edge dispositivo gateway in hub IoT di Azure.

   * Scegliere un nome per il modulo: ```ConnectedCache```
   * Modificare l'URI dell'immagine: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Aggiungere lo stesso set di variabili di ambiente e le opzioni di creazione del contenitore usate nella distribuzione padre.
>[!Note]
>Il CACHE_NODE_ID deve essere univoco.  I CUSTOMER_ID e CUSTOMER_KEY specificati saranno identici all'elemento padre. (vedere [Configurare Microsoft cache connessa](connected-cache-configure.md)

Per una convalida del corretto funzionamento di Microsoft cache connessa, eseguire il comando seguente nel terminale del dispositivo IoT Edge che ospita il modulo o qualsiasi dispositivo in rete. Sostituire \<Azure IoT Edge Gateway IP\> con l'indirizzo IP o il nome host del gateway IoT Edge rete. Per informazioni sulla visibilità di questo report, vedere i dettagli delle variabili di ambiente.

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```