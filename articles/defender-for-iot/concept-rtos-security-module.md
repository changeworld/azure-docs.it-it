---
title: Spiegazione concettuale delle nozioni di base di Defender-IoT-micro-agent per Azure RTOS
description: Informazioni di base su Defender-IoT-micro-agent per Azure RTOS e sul flusso di lavoro.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750483"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro-agent per Azure RTOS (anteprima)

Usare questo articolo per comprendere meglio Defender-IoT-micro-agent per Azure RTOS, inclusi funzionalità e vantaggi, nonché collegamenti a risorse di configurazione e riferimento pertinenti. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

Defender-IoT-micro-agent per Azure RTOS offre una soluzione di sicurezza completa per Azure RTOS dispositivi come parte dell'offerta NetX Duo. All'interno dell'offerta NetX Duo, Azure RTOS viene fornito con l'agente integrato Azure IoT Defender-IoT-micro-agent e fornisce copertura per le minacce comuni sui dispositivi del sistema operativo in tempo reale dopo l'attivazione.

Defender-IoT-micro-agent per Azure RTOS viene eseguito in background e offre un'esperienza utente semplice, inviando messaggi di sicurezza usando le connessioni univoche di ogni cliente all'hub IoT. Defender-IoT-micro-agent per Azure RTOS è abilitato per impostazione predefinita.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo è uno stack di rete TCP/IP avanzato di livello industriale progettato specificamente per applicazioni IoT e in tempo reale con un'incorporazione approfondita. Azure RTOS NetX Duo è un doppio stack di rete IPv4 e IPv6 che offre un set di protocolli ricco, tra cui sicurezza e cloud. Altre informazioni sulle [Azure RTOS NetX Duo.](/azure/rtos/netx-duo/)

Il modulo offre le funzionalità seguenti:

- **Rilevare attività di rete dannose**
- **Linee di base del comportamento del dispositivo basate su avvisi personalizzati**
- **Migliorare l'efficienza della sicurezza dei dispositivi**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-micro-agent per l'architettura Azure RTOS

Defender-IoT-micro-agent per Azure RTOS viene inizializzato dalla piattaforma middleware Azure IoT e usa i client dell'hub IoT per inviare i dati di telemetria di sicurezza all'hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT diagramma di stato e flusso delle informazioni di Defender-IoT-micro-agent":::

Defender-IoT-micro-agent per Azure RTOS monitora le attività e le informazioni del dispositivo seguenti usando tre agenti di raccolta:
- Attività di rete **del dispositivo TCP,** **UDP** e **ICM**
- Informazioni di sistema come **versioni di Threadx** **e NetX Duo**
- Eventi heartbeat

Ogni agente di raccolta è collegato a un gruppo di priorità e ogni gruppo di priorità ha un proprio intervallo con i possibili valori **Low,** **Medium** e **High.** Gli intervalli influiscono sull'intervallo di tempo in cui i dati vengono raccolti e inviati.

Ogni intervallo di tempo è configurabile e i connettori IoT possono essere abilitati e disabilitati per personalizzare [ulteriormente la soluzione.](how-to-azure-rtos-security-module.md) 

## <a name="supported-security-alerts-and-recommendations"></a>Avvisi di sicurezza e raccomandazioni supportati

Il micro-agente Defender-IoT per Azure RTOS supporta avvisi di sicurezza e raccomandazioni specifici. Assicurarsi di esaminare [e personalizzare i valori di avviso e raccomandazione](concept-rtos-security-alerts-recommendations.md) pertinenti per il servizio dopo aver completato la configurazione iniziale.

## <a name="ready-to-begin"></a>Si è pronti per iniziare?

Defender-IoT-micro-agent per Azure RTOS viene fornito come download gratuito per i dispositivi IoT. Il servizio cloud Defender per IoT è disponibile con una versione di valutazione di 30 giorni per ogni sottoscrizione di Azure. [Scaricare ora Defender-IoT-micro-agent](https://github.com/azure-rtos/azure-iot-preview/releases) per iniziare. 

## <a name="next-steps"></a>Passaggi successivi

- Introduzione a Defender-IoT-micro-agent per Azure RTOS [e configurare](quickstart-azure-rtos-security-module.md).
- Altre informazioni su Defender-IoT-micro-agent per l'Azure RTOS avvisi di sicurezza e il supporto [delle raccomandazioni.](concept-rtos-security-alerts-recommendations.md) 
- Usare Defender-IoT-micro-agent per l Azure RTOS aPI [di riferimento.](azure-rtos-security-module-api.md)