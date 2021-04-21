---
title: Esempi di cache connessa di distribuzione di Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Esercitazioni cache connessa esempi di scenari di distribuzione in anteprima di Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811725"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Esempi di cache connessa di distribuzione in anteprima di Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Gateway a livello Azure IoT Edge gateway senza proxy

Il diagramma seguente descrive lo scenario in cui un gateway Azure IoT Edge con accesso diretto alle risorse della rete CDN e un dispositivo foglia Azure IoT, ad esempio un dispositivo raspberry PI, è un dispositivo figlio isolato da Internet del gateway Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft cache connessa dispositivo disconnesso" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Aggiungere il modulo Microsoft cache connessa alla distribuzione del dispositivo gateway Azure IoT Edge in hub IoT di Azure (vedere [Supporto](connected-cache-disconnected-device-update.md) per dispositivi disconnessi per informazioni dettagliate su come ottenere il modulo).
2. Aggiungere le variabili di ambiente per la distribuzione. Di seguito è riportato un esempio delle variabili di ambiente.

    **Variabili di ambiente**
    
    | Nome                          | Valore                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Vedere [le descrizioni delle variabili](connected-cache-configure.md) di ambiente |
    | CUSTOMER_ID                   | Vedere [le descrizioni delle variabili](connected-cache-configure.md) di ambiente |
    | CUSTOMER_KEY                  | Vedere [le descrizioni delle variabili](connected-cache-configure.md) di ambiente |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Aggiungere le opzioni di creazione del contenitore per la distribuzione. Di seguito è riportato un esempio delle opzioni di creazione del contenitore.

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

Per una convalida del corretto funzionamento di Microsoft cache connessa, eseguire il comando seguente nel terminale del dispositivo IoT Edge che ospita il modulo o qualsiasi dispositivo in rete. Sostituire \<Azure IoT Edge Gateway IP\> con l'indirizzo IP o il nome host del gateway IoT Edge rete. Per informazioni sulla visibilità di questo report, vedere i dettagli delle variabili di ambiente.

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Gateway a Azure IoT Edge singolo con proxy non autenticato in uscita

In questo scenario è presente un gateway Azure IoT Edge che ha accesso alle risorse della rete CDN tramite un proxy non autenticato in uscita. Microsoft cache connessa configurato per memorizzare nella cache il contenuto di un repository personalizzato e il report di riepilogo è stato reso visibile a tutti gli utenti della rete. Di seguito è riportato un esempio delle variabili di ambiente MCC che verrebbero impostate.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft cache connessa proxy a livello singolo" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Aggiungere il modulo Microsoft cache connessa alla distribuzione Azure IoT Edge dispositivo gateway in hub IoT di Azure.
2. Aggiungere le variabili di ambiente per la distribuzione. Di seguito è riportato un esempio delle variabili di ambiente.

    **Variabili di ambiente**

    | Nome                          | Valore                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Vedere le [descrizioni delle](connected-cache-configure.md) variabili di ambiente |
    | CUSTOMER_ID                   | Vedere le [descrizioni delle](connected-cache-configure.md) variabili di ambiente |
    | CUSTOMER_KEY                  | Vedere le [descrizioni delle](connected-cache-configure.md) variabili di ambiente |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |
    | UPSTREAM_PROXY                | INDIRIZZO IP o FQDN del server proxy                                          |

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

Per una convalida del corretto funzionamento di Microsoft cache connessa, eseguire il comando seguente nel terminale del dispositivo Azure IoT Edge che ospita il modulo o qualsiasi dispositivo in rete. Sostituire \<Azure IoT Edge Gateway IP\> con l'indirizzo IP o il nome host del gateway IoT Edge locale. Per informazioni sulla visibilità di questo report, vedere i dettagli delle variabili di ambiente.

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
