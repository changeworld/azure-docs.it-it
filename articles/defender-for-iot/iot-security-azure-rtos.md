---
title: Panoramica di Defender-RTO-micro-Agent per Azure
description: Scopri di più sul supporto e sull'implementazione di Defender-RTO-micro-Agent per Azure come parte di Azure Defender per l'it.
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 8f162cc7e284abc9f1fdc08a10e62369855017c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785904"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>Panoramica: Defender for Internet degli elementi Defender-Internet di Azure RTO (anteprima)

Il modulo Azure Defender for Internet è una soluzione di sicurezza completa per i dispositivi che usano Azure RTO. Fornisce la copertura per le minacce comuni e le potenziali attività dannose sui dispositivi RTO (Real-Time Operating System). Azure RTO è ora disponibile con Azure Internet delle cose Defender-Internet.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualizzazione di Defender per la RTO di Azure.":::


Il modulo micro per Azure RTO offre le funzionalità seguenti:

- Rilevamento di attività di rete dannose
- Linea di base comportamento del dispositivo basato su avvisi personalizzati
- Miglioramento dell'igiene della sicurezza dei dispositivi

## <a name="detect-malicious-network-activities"></a>Rilevare le attività di rete dannosa

Viene monitorata l'attività di rete in ingresso e in uscita di ogni dispositivo. I protocolli supportati sono TCP, UDP e ICMP in IPv4 e IPv6. Defender per le cose esamina tutte le attività di rete nel feed di intelligence per le minacce di Microsoft. Il feed viene aggiornato in tempo reale con milioni di indicatori di minaccia univoci raccolti in tutto il mondo.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Baseline del comportamento del dispositivo in base agli avvisi personalizzati

La linea di base consente il clustering di dispositivi in gruppi di sicurezza e la definizione del comportamento previsto di ogni gruppo. Poiché i dispositivi Internet vengono in genere progettati per funzionare in scenari ben definiti e limitati, è facile creare una baseline che definisce il comportamento previsto usando un set di parametri. Qualsiasi deviazione dalla linea di base attiva un avviso.

## <a name="improve-your-device-security-hygiene"></a>Migliorare l'igiene della sicurezza del dispositivo

Con l'infrastruttura consigliata di Defender per le informazioni dettagliate, è possibile ottenere informazioni e approfondimenti sui problemi dell'ambiente che incidono e danneggiano il comportamento di sicurezza dei dispositivi. Un comportamento di sicurezza del dispositivo è debole, che può consentire potenziali attacchi se viene lasciato invariato. La sicurezza viene sempre misurata in base al collegamento più debole all'interno di qualsiasi organizzazione.

## <a name="get-started-protecting-azure-rtos-devices"></a>Inizia a proteggere i dispositivi RTO di Azure

Defender-molto-micro-Agent per Azure RTO è disponibile come download gratuito per i dispositivi. Il servizio cloud Defender for Internet è disponibile con una versione di valutazione di 30 giorni per ogni sottoscrizione di Azure. Per iniziare, scaricare [Defender-RTO-micro-Agent per Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Defender-RTO-micro-Agent per Azure. Per ulteriori informazioni su Defender-Internet e per iniziare, vedere gli articoli seguenti:

- [Azure RTO Internet delle cose Defender-cose-concetti di micro-Agent](concept-rtos-security-module.md)
- [Guida introduttiva: Azure RTO Internet delle cose Defender-Internet](quickstart-azure-rtos-security-module.md)
