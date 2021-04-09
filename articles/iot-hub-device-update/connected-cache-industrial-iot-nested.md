---
title: Microsoft Connected cache all'interno di un Azure IoT Edge per la configurazione delle cose industriali | Microsoft Docs
description: Esercitazione sulla configurazione di Microsoft Connected cache in un'Azure IoT Edge for Industrial.
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664778"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Esempio di scenario di distribuzione dell'anteprima di Microsoft Connected cache: Microsoft Connected cache all'interno di un Azure IoT Edge per la configurazione delle cose industriali

Le reti di produzione sono spesso organizzate in livelli gerarchici che seguono il [modello di rete Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (incluso negli standard [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) e [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) ). In queste reti solo il livello superiore presenta connettività al cloud e i livelli inferiori della gerarchia possono comunicare solo con i livelli adiacenti Nord e sud.

Questo esempio di GitHub, [Azure IOT Edge for Industrial](https://github.com/Azure-Samples/iot-edge-for-iiot)Internet, distribuisce quanto segue:

* Rete Purdue simulata in Azure
* Asset industriali 
* Gerarchia dei gateway Azure IoT Edge
  
Questi componenti verranno usati per acquisire dati industriali e caricarli in modo sicuro nel cloud senza compromettere la sicurezza della rete. La cache connessa Microsoft può essere distribuita per supportare il download del contenuto a tutti i livelli all'interno della rete conforme a ISA 95.

La chiave per configurare le distribuzioni di cache connesse Microsoft in una rete conforme a ISA 95 consiste nel configurare sia il proxy OT *che* l'host upstream nel gateway IOT Edge L3.

1. Configurare distribuzioni di cache connesse Microsoft a livelli L5 e L4 come descritto nell'esempio Two-Level annidato IoT Edge Gateway 
2. La distribuzione nel gateway IoT Edge L3 deve specificare:
   
   * UPSTREAM_HOST: IP/FQDN del gateway di IoT Edge L4, che la cache con connessione Microsoft L3 richiederà il contenuto.
   * UPSTREAM_PROXY-indirizzo IP/FQDN: porta del server proxy OT.

3. Il proxy OT deve aggiungere l'indirizzo IP/FQDN di L4 MCC all'elenco Consenti.

Per verificare il corretto funzionamento della cache connessa Microsoft, eseguire il comando seguente nel terminale del dispositivo IoT Edge, che ospita il modulo o qualsiasi dispositivo in rete.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```