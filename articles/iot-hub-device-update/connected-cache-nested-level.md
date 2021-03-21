---
title: Gateway di Azure IoT Edge annidato a due livelli di cache connessa Microsoft con proxy non autenticato in uscita | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Esercitazione di Microsoft Connected cache a due livelli annidato Azure IoT Edge Gateway con proxy non autenticato in uscita
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615381"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Esempio di scenario di distribuzione dell'anteprima di Microsoft Connected cache: gateway Azure IoT Edge annidato a due livelli con proxy non autenticato in uscita

Dato il diagramma seguente, in questo scenario sono presenti un gateway Azure IoT Edge e un dispositivo Azure IoT Edge downstream, uno Azure IoT Edge Gateway che è stato associato a un altro gateway Azure IoT Edge e un server proxy nella rete perimetrale IT. Di seguito è riportato un esempio delle variabili di ambiente della cache connessa Microsoft che verrebbero impostate nell'portale di Azure UX per entrambi i moduli di MCC distribuiti nei gateway di Azure IoT Edge. Nell'esempio riportato di seguito viene illustrata la configurazione per due livelli di gateway Azure IoT Edge, ma non esiste alcun limite alla profondità degli host upstream supportati da Microsoft Connected cache. Non esiste alcuna differenza nelle opzioni di creazione del contenitore MCC degli esempi precedenti.

Per informazioni dettagliate sulla configurazione di distribuzioni a più livelli di Azure IoT Edge Gateway, vedere la documentazione [connessione a downstream IOT Edge Devices-Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true) . Si noti inoltre che quando si distribuiscono Azure IoT Edge, la cache connessa Microsoft e i moduli personalizzati, tutti i moduli devono trovarsi nello stesso registro contenitori.

Il diagramma seguente illustra lo scenario in cui un Azure IoT Edge Gateway come accesso diretto alle risorse della rete CDN funge da padre per un altro gateway Azure IoT Edge che funge da padre per un dispositivo foglia di Azure, ad esempio un dispositivo Raspberry Pi. Solo l'elemento padre del gateway Azure IoT Edge dispone di connettività Internet alle risorse della rete CDN ed entrambi i dispositivi Azure IoT Edge figlio e Azure Internet sono isolati. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Cache connessa Microsoft annidata" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configurazione del gateway padre

1. Aggiungere il modulo Microsoft Connected cache alla distribuzione del dispositivo Azure IoT Edge Gateway nell'hub Azure.
2. Aggiungere le variabili di ambiente per la distribuzione. Di seguito è riportato un esempio delle variabili di ambiente.

    **Variabili di ambiente**

    | Nome                 | Valore                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Vedere la descrizione della variabile di ambiente. |
    | CUSTOMER_ID                   | Vedere la descrizione della variabile di ambiente. |
    | CUSTOMER_KEY                  | Vedere la descrizione della variabile di ambiente. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | IP o FQDN del server proxy                     |

3. Aggiungere le opzioni di creazione del contenitore per la distribuzione. Non esiste alcuna differenza nelle opzioni di creazione del contenitore di MCC dall'esempio precedente. Di seguito è riportato un esempio delle opzioni di creazione del contenitore.

### <a name="container-create-options"></a>Opzioni di creazione del contenitore

```markdown
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
```

## <a name="child-gateway-configuration"></a>Configurazione del gateway figlio

>[!Note]
>Se sono stati replicati i contenitori usati nella configurazione nel proprio registro privato, sarà necessario apportare una modifica alle impostazioni di configurazione. toml e alle impostazioni di runtime nella distribuzione del modulo. Per altre informazioni, vedere [esercitazione-creare una gerarchia di dispositivi IOT Edge-Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device) per altri dettagli.

1. Modificare il percorso dell'immagine per l'agente perimetrale, come illustrato nell'esempio seguente:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Modificare le impostazioni di runtime dell'hub perimetro e dell'agente perimetrale nella distribuzione Azure IoT Edge come illustrato nell'esempio seguente:
    
    * In Hub Edge, nel campo immagine, immettere ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * In agente Edge, nel campo immagine, immettere ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Aggiungere il modulo Microsoft Connected cache alla distribuzione del dispositivo Azure IoT Edge Gateway nell'hub Azure.

   * Scegliere un nome per il modulo: ```ConnectedCache```
   * Modificare l'URI dell'immagine: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Aggiungere le stesse variabili di ambiente e le stesse opzioni di creazione del contenitore utilizzate nella distribuzione padre.

Per la convalida di Microsoft Connected cache correttamente funzionante, eseguire il comando seguente nel terminale del dispositivo IoT Edge che ospita il modulo o qualsiasi dispositivo in rete.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```