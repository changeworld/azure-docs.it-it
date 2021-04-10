---
title: Defender per Internet delle cose Defender-Internet per IoT Edge
description: Informazioni sull'architettura e sulle funzionalità di Azure Defender per l'IT Defender-Internet per la IoT Edge.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 81eb8816e1bcf74a9e27e34d14465102599c7d5d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782657"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Azure Defender per IoT Edge Defender-cose-micro-Agent

[Azure IOT Edge](../iot-edge/index.yml) offre potenti funzionalità per la gestione e l'esecuzione di flussi di lavoro aziendali nei dispositivi perimetrali.
La parte principale che IoT Edge gioca negli ambienti Internet è particolarmente interessante per gli attori malintenzionati.

Defender per Internet delle cose Defender-molto-micro-Agent offre una soluzione di sicurezza completa per i dispositivi IoT Edge.
Il modulo Defender for Internet raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema di contenitori in consigli e avvisi di sicurezza di utilità pratica.

Analogamente a Defender per gli agenti di sicurezza per i dispositivi Internet delle cose, il Defender per IoT Edge modulo è altamente personalizzabile tramite il modulo gemello.
Per altre informazioni, vedere [configurare l'agente](how-to-agent-configuration.md) .

Defender per Internet delle cose Defender-Internet delle cose: micro-Agent per IoT Edge offre le funzionalità seguenti:

- Raccoglie gli eventi di sicurezza non elaborati dal sistema operativo (Linux) sottostante e dai sistemi contenitore IoT Edge.

  Per ulteriori informazioni sugli agenti di raccolta dati di sicurezza disponibili, vedere [Defender per la configurazione dell'agente](how-to-agent-configuration.md) Internet.

- Analisi dei manifesti di distribuzione di IoT Edge.

- Aggrega gli eventi di sicurezza non elaborati in messaggi inviati tramite [Hub IOT Edge](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Rimuovere la configurazione tramite l'uso di Defender-Internet-micro-Agent gemello.

  Per altre informazioni, vedere [configurare un Defender per l'agente](how-to-agent-configuration.md) .

Defender for Internet delle cose Defender-Internet per l'IoT Edge viene eseguito in modalità privilegiata in IoT Edge.
La modalità con privilegi è necessaria per consentire al modulo di monitorare il sistema operativo e altri moduli IoT Edge.

## <a name="module-supported-platforms"></a>Piattaforme supportate dal modulo

Defender for Internet delle cose Defender-Internet per la IoT Edge è attualmente disponibile solo per Linux.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate l'architettura e le funzionalità di Defender per Internet delle cose Defender-Internet per la IoT Edge.

Per continuare a usare Defender per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

- Distribuisci [Defender-Internet di un micro-Agent per IOT Edge](how-to-deploy-edge.md)
- Informazioni su come [configurare l'agente Defender-](how-to-agent-configuration.md) Internet
- Esaminare il Defender per il [Defender](resources-manage-proprietary-protocols.md) per Internet delle cose Horizon
- Informazioni su come [abilitare Defender per il servizio Internet delle cose nell'hub Internet delle](quickstart-onboard-iot-hub.md) cose
- Altre informazioni sul servizio sono disponibili nella pagina [relativa alle domande frequenti su Defender](resources-frequently-asked-questions.md)