---
title: Panoramica sul portfolio degli agenti e supporto del sistema operativo (anteprima)
description: Azure Defender per l'it fornisce un ampio portfolio di agenti basati sul tipo di dispositivo.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: d2e463051d0897afe52981ea2d50ddd1f06bb54d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383423"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Panoramica sul portfolio degli agenti e supporto del sistema operativo (anteprima)

Azure Defender per l'it fornisce un ampio portfolio di agenti basati sul tipo di dispositivo. 

## <a name="standalone-agent"></a>Agente autonomo

L'agente autonomo copre la maggior parte dei sistemi operativi Linux, che possono essere distribuiti come pacchetto binario o come codice sorgente che può essere incorporato come parte del firmware e consentire la modifica e la personalizzazione in base alle esigenze dei clienti. Esempio di supporto del sistema operativo: 

| Sistema operativo | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Per informazioni dettagliate, supporto del sistema operativo o per richiedere l'accesso al codice sorgente in modo che sia possibile incorporarlo come parte del firmware del dispositivo, contattare l'account Manager o inviare un messaggio di posta elettronica a <defender_micro_agent@microsoft.com> . 

## <a name="azure-rtos-micro-agent"></a>Azure RTO micro Agent

Azure Defender per gli elementi micro Agent offre una soluzione di sicurezza completa e leggera per i dispositivi che usano Azure RTO. Azure Defender per gli elementi micro Agent fornisce la copertura per le minacce più comuni e le potenziali attività dannose sui dispositivi RTO (Real-Time Operating System). L'agente micro viene incorporato come parte del componente Azure RTO NetX Duo e monitora l'attività di rete del dispositivo. 

Il componente Azure Defender per gli elementi micro è integrato nell'ambito del componente Azure RTO NetX Duo e monitora l'attività di rete del dispositivo. Il micro Agent è costituito da una soluzione di sicurezza completa e leggera che fornisce la copertura per le minacce comuni e le potenziali attività dannose in un sistema operativo in tempo reale (RTO).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [Panoramica di micro Agent autonomo (anteprima)](concept-standalone-micro-agent-overview.md).