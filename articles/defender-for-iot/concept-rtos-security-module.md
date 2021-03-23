---
title: Spiegazione concettuale delle nozioni di base di Defender-RTO-micro-Agent per Azure
description: Scopri le nozioni di base sui concetti e sul flusso di lavoro di Defender-RTO-micro-Agent per Azure.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 83557930aeeccbb557382583e4d6666a000ce52c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779274"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-molto-micro-Agent per Azure RTO (anteprima)

Usare questo articolo per comprendere meglio il Defender-RTO-micro-Agent per Azure, incluse funzionalità e vantaggi, oltre a collegamenti a risorse di configurazione e di riferimento pertinenti. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTO Internet delle cose Defender-Internet

Defender-RTO-micro-Agent per Azure offre una soluzione di sicurezza completa per i dispositivi RTO di Azure come parte dell'offerta NetX Duo. All'interno dell'offerta NetX Duo, Azure RTO è dotato di Azure Internet delle cose Defender-Internet e del micro-agente predefinito e fornisce la copertura per le minacce più comuni sui dispositivi del sistema operativo in tempo reale, una volta attivati. 

Defender-RTO-micro-Agent per Azure viene eseguito in background e offre un'esperienza utente uniforme, mentre invia messaggi di sicurezza usando le connessioni univoche di ogni cliente all'hub Internet. Il Defender-RTO-micro-Agent per Azure è abilitato per impostazione predefinita.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo è uno stack di rete TCP/IP avanzato e di livello industriale progettato in modo specifico per le applicazioni in tempo reale e in tempo reale incorporate. Azure RTO NetX Duo è uno stack di rete IPv4 e IPv6 duale che fornisce un set completo di protocolli, tra cui sicurezza e cloud. Scopri di più sulle soluzioni [Azure RTO NETX Duo](/azure/rtos/netx-duo/) .

Il modulo offre le funzionalità seguenti:

- **Rilevare le attività di rete dannosa**
- **Linee di base del comportamento del dispositivo basate su avvisi personalizzati**
- **Migliorare l'igiene della sicurezza dei dispositivi**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-RTO-micro-Agent per l'architettura di Azure

Defender-RTO-micro-Agent per Azure viene inizializzato dalla piattaforma middleware Azure Internet e usa i client dell'hub Internet per inviare i dati di telemetria di sicurezza all'hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure Internet Information Defender-Internet-diagramma di stato e flusso di informazioni del micro-agente":::

Defender-RTO-micro-Agent per Azure monitora le attività e le informazioni del dispositivo seguenti usando tre agenti di raccolta:
- Attività di rete del dispositivo **TCP**, **UDP** e **ICM**
- Informazioni di sistema come versioni di **threadX** e **NETX Duo**
- Eventi heartbeat

Ogni agente di raccolta è collegato a un gruppo di priorità e a ogni gruppo di priorità è associato un intervallo con i valori possibili di **basso**, **medio** e **alto**. Gli intervalli influiscono sull'intervallo di tempo in cui i dati vengono raccolti e inviati.

Ogni intervallo di tempo è configurabile e i connettori di Internet delle cose possono essere abilitati e disabilitati per [personalizzare ulteriormente la soluzione](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Avvisi e raccomandazioni di sicurezza supportati

Defender-RTO-micro-Agent per Azure supporta avvisi e raccomandazioni di sicurezza specifici. Dopo aver completato la configurazione iniziale, assicurarsi di [rivedere e personalizzare i valori di avviso e di raccomandazione pertinenti](concept-rtos-security-alerts-recommendations.md) per il servizio.

## <a name="ready-to-begin"></a>Pronti per iniziare?

Defender-molto-micro-Agent per Azure RTO è disponibile come download gratuito per i dispositivi Internet delle cose. Il servizio cloud Defender for Internet è disponibile con una versione di valutazione di 30 giorni per ogni sottoscrizione di Azure. [Scarica ora il Defender-Internet degli elementi-micro-Agent](https://github.com/azure-rtos/azure-iot-preview/releases) e inizia subito. 

## <a name="next-steps"></a>Passaggi successivi

- Introduzione ai [prerequisiti e alla configurazione](quickstart-azure-rtos-security-module.md)di Defender-RTO-micro-Agent per Azure.
- Scopri di più sugli [avvisi di sicurezza](concept-rtos-security-alerts-recommendations.md)di Defender-Internet e su micro-Agent per Azure RTO. 
- Usare l' [API di riferimento](azure-rtos-security-module-api.md)Defender-are-micro-Agent per Azure RTO.