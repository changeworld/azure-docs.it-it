---
title: Esempi di scenari di distribuzione dell'anteprima di Microsoft Connected cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Esercitazione sugli esempi di scenari di distribuzione di Microsoft Connected cache Preview
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101664775"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Esempi di scenari di distribuzione dell'anteprima di Microsoft Connected cache

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Singolo livello Azure IoT Edge gateway senza proxy

Il diagramma seguente illustra lo scenario in cui un gateway di Azure IoT Edge che ha accesso diretto alle risorse della rete CDN ed è presente un dispositivo foglia di Azure, ad esempio un dispositivo di Raspberry PI, ovvero dispositivi figlio isolati da Internet del gateway di Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aggiornamento del dispositivo disconnesso dalla cache connessa Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Aggiungere il modulo Microsoft Connected cache alla distribuzione del dispositivo del gateway Azure IoT Edge nell'hub Azure Internet ( `MCC concepts` per informazioni dettagliate su come ottenere il modulo), vedere.
2. Aggiungere le variabili di ambiente per la distribuzione. Di seguito è riportato un esempio delle variabili di ambiente.

    **Variabili di ambiente**
    
    | Nome                 | Valore                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Vedere la descrizione della variabile di ambiente. |
    | CUSTOMER_ID                   | Vedere la descrizione della variabile di ambiente. |
    | CUSTOMER_KEY                  | Vedere la descrizione della variabile di ambiente. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Aggiungere le opzioni di creazione del contenitore per la distribuzione. Di seguito è riportato un esempio delle opzioni di creazione del contenitore.

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

Per la convalida di Microsoft Connected cache correttamente funzionante, eseguire il comando seguente nel terminale del dispositivo IoT Edge che ospita il modulo o qualsiasi dispositivo in rete.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Singolo livello Azure IoT Edge Gateway con proxy non autenticato in uscita

In questo scenario è presente un gateway Azure IoT Edge che ha accesso alle risorse della rete CDN tramite un proxy non autenticato in uscita. La cache connessa Microsoft viene configurata in modo da memorizzare nella cache il contenuto da un repository personalizzato e il report di riepilogo è reso visibile a tutti gli utenti della rete. Di seguito è riportato un esempio delle variabili di ambiente MCC che verrebbero impostate.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Proxy a livello singolo Microsoft Connected cache" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

Per la convalida di Microsoft Connected cache correttamente funzionante, eseguire il comando seguente nel terminale del dispositivo Azure IoT Edge che ospita il modulo o qualsiasi dispositivo in rete.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
