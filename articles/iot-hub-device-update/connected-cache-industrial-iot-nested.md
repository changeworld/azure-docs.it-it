---
title: Microsoft cache connessa all'interno di un Azure IoT Edge per la configurazione ioT industriale | Microsoft Docs
description: Microsoft cache connessa all'interno di un'esercitazione Azure IoT Edge per la configurazione di IoT industriale
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811814"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Esempio cache connessa scenario di distribuzione in anteprima di Microsoft: Microsoft cache connessa all'interno di Azure IoT Edge per una configurazione IoT industriale

Le reti di produzione sono spesso organizzate in livelli gerarchici in base al modello di rete [Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (incluso negli standard [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) e [ISA 99).](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) In queste reti solo il livello superiore ha connettività al cloud e i livelli inferiori della gerarchia possono comunicare solo con i livelli nord e sud adiacenti.

Questo esempio di GitHub, [Azure IoT Edge ioT industriale,](https://github.com/Azure-Samples/iot-edge-for-iiot)distribuisce quanto segue:

* Rete Purdue simulata in Azure
* Asset industriali 
* Gerarchia Azure IoT Edge gateway
  
Questi componenti verranno usati per acquisire dati industriali e caricarlo in modo sicuro nel cloud senza compromettere la sicurezza della rete. Microsoft cache connessa essere distribuita per supportare il download di contenuto a tutti i livelli all'interno della rete conforme a ISA 95.

La chiave per configurare le distribuzioni di Microsoft cache connessa all'interno di una rete conforme a ISA 95 è la configurazione del *proxy* OT e dell'host upstream nel gateway IoT Edge L3.

1. Configurare le distribuzioni cache connessa Microsoft a livello L5 e L4 come descritto nell'esempio Two-Level gateway IoT Edge annidato 
2. La distribuzione nel gateway IoT Edge L3 deve specificare:
   
   * UPSTREAM_HOST: indirizzo IP/FQDN del gateway di IoT Edge L4, che l'cache connessa Microsoft L3 richiederà il contenuto.
   * UPSTREAM_PROXY: INDIRIZZO IP/FQDN:PORTA del server proxy OT.

3. Il proxy OT deve aggiungere l'indirizzo IP/FQDN MCC L4 all'elenco elementi consentiti.

Per verificare che Microsoft cache connessa funzioni correttamente, eseguire il comando seguente nel terminale del dispositivo IoT Edge, ospitando il modulo o qualsiasi dispositivo in rete. Sostituire \<Azure IoT Edge Gateway IP\> con l'indirizzo IP o il nome host del gateway IoT Edge rete. Per informazioni sulla visibilità di questo report, vedere i dettagli delle variabili di ambiente.

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```