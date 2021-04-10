---
title: Agenti di sicurezza
description: Introduzione alla comprensione, alla configurazione, alla distribuzione e all'uso di Azure Defender per gli agenti del servizio di sicurezza di Internet delle cose sui dispositivi.
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783490"
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