---
title: Agenti di sicurezza
description: Introduzione alla comprensione, alla configurazione, alla distribuzione e all'uso di Azure Defender per gli agenti del servizio di sicurezza di Internet delle cose sui dispositivi.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: 3b98013eab1ae8d21b9da7c1a4460551dc363c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487769"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Introduzione ad Azure Defender per gli agenti micro dei dispositivi

Defender for Internet Security Agents offre funzionalità di sicurezza avanzate, ad esempio il monitoraggio delle procedure consigliate per la configurazione del sistema operativo. Assumere il controllo della protezione dalle minacce per il campo del dispositivo e del comportamento di sicurezza con un unico servizio.

I difensori degli agenti di sicurezza Internet gestiscono la raccolta di eventi non elaborati dal sistema operativo del dispositivo, l'aggregazione di eventi per ridurre i costi e la configurazione tramite un modulo dispositivo gemello. I messaggi di sicurezza vengono inviati tramite l'hub Internet delle cose, in Defender per i servizi di analisi delle cose.

Usare il flusso di lavoro seguente per distribuire e testare il Defender per gli agenti di sicurezza:

1. [Abilitare Defender per il servizio Internet delle cose nell'hub Internet delle](quickstart-onboard-iot-hub.md)cose.

1. Se l'hub Internet delle cose non ha dispositivi registrati, [registrare un nuovo dispositivo](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Creare un modulo DefenderIotMicroAgent gemello](quickstart-create-micro-agent-module-twin.md) per i dispositivi.

1. Per installare l'agente in un dispositivo simulato di Azure anziché installare in un dispositivo effettivo, [creare una nuova macchina virtuale (VM) di Azure](../virtual-machines/linux/quick-create-portal.md) in una zona disponibile.

1. [Distribuire un Defender per l'agente sicurezza di Internet delle](how-to-deploy-linux-cs.md) cose nel dispositivo o nella nuova VM.

1. Seguire le istruzioni per [trigger_events](https://aka.ms/iot-security-github-trigger-events) per eseguire un evento baseline del sistema operativo.

1. Verificare le raccomandazioni di Defender per le cose in risposta all'errore di verifica della linea di base del sistema operativo simulato nel passaggio precedente. Inizia la verifica 30 minuti dopo l'esecuzione dello script.

## <a name="next-steps"></a>Passaggi successivi

- Configurare la [soluzione](quickstart-configure-your-solution.md)
- [Creazione di Defender-un sacco-Micro-Agents](quickstart-create-security-twin.md)
- Configurare gli [avvisi personalizzati](quickstart-create-custom-alerts.md)
- [Distribuire un agente di sicurezza](how-to-deploy-agent.md)