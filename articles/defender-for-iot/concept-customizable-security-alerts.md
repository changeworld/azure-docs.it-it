---
title: Avvisi di sicurezza personalizzati per l'hub Internet
description: Informazioni sugli avvisi di sicurezza personalizzabili e sul monitoraggio e l'aggiornamento consigliati usando Defender per le funzionalità e il servizio dell'hub Internet.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: d7a58bcdb759c3f31290cc7930eba6ca52fcc17b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784731"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Protezione per gli avvisi di sicurezza personalizzati dell'hub

Defender for Internet esegue continuamente l'analisi della soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

Si consiglia di creare avvisi personalizzati in base alla propria conoscenza del comportamento previsto del dispositivo per assicurarsi che gli avvisi fungano da indicatori più efficienti di potenziali compromessi per la distribuzione e il paesaggio aziendali univoci.

Gli elenchi seguenti di Defender per gli avvisi per le cose sono definibili dall'utente in base al comportamento dell'hub degli elementi del tutto previsto. Per ulteriori informazioni su come personalizzare ogni avviso, vedere [creare avvisi personalizzati](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Avvisi personalizzati incorporati nell'hub Internet delle cose

| Gravità | Nome avviso | Origine dati | Descrizione | Correzione consigliata |
|--|--|--|--|--|
| Basso | Avviso personalizzato: il numero di messaggi da cloud a dispositivo nel protocollo AMQP non è compreso nell'intervallo consentito | Hub IoT | Il numero di messaggi da cloud a dispositivo (protocollo AMQP) in un intervallo di tempo specifico non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di messaggi da cloud a dispositivo rifiutati nel protocollo AMQP non è compreso nell'intervallo consentito | Hub IoT | Il numero di messaggi da cloud a dispositivo (protocollo AMQP) rifiutati dal dispositivo, all'interno di un intervallo di tempo specifico, non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di messaggi da dispositivo a cloud nel protocollo AMQP non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi da dispositivo a cloud (protocollo AMQP) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di Invoke diretti al metodo non è compreso nell'intervallo consentito | Hub IoT | La quantità di richiami al metodo diretto in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di caricamenti di file non è compreso nell'intervallo consentito | Hub IoT | La quantità di caricamenti di file in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di messaggi da cloud a dispositivo nel protocollo HTTP non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi dal cloud al dispositivo (protocollo HTTP) in un intervallo di tempo non è compreso nell'intervallo consentito configurato |
| Basso | Avviso personalizzato: il numero di messaggi da cloud a dispositivo rifiutati nel protocollo HTTP non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi da cloud a dispositivo (protocollo HTTP) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |
| Basso | Avviso personalizzato: il numero di messaggi da dispositivo a cloud nel protocollo HTTP non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi da dispositivo a cloud (protocollo HTTP) in un intervallo di tempo specifico non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di messaggi da cloud a dispositivo nel protocollo MQTT non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi da cloud a dispositivo (protocollo MQTT) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di messaggi da cloud a dispositivo rifiutati nel protocollo MQTT non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi da cloud a dispositivo (protocollo MQTT) rifiutati dal dispositivo in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |
| Basso | Avviso personalizzato: il numero di messaggi da dispositivo a cloud nel protocollo MQTT non è compreso nell'intervallo consentito | Hub IoT | La quantità di messaggi da dispositivo a cloud (protocollo MQTT) in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |
| Basso | Avviso personalizzato: il numero di eliminazioni della coda dei comandi che non rientrano nell'intervallo consentito | Hub IoT | La quantità di pulizie della coda di comando in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: il numero di aggiornamenti del modulo gemello non è compreso nell'intervallo consentito | Hub IoT | La quantità di aggiornamenti di moduli gemelli in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |
| Basso | Avviso personalizzato: il numero di operazioni non autorizzate non è compreso nell'intervallo consentito | Hub IoT | La quantità di operazioni non autorizzate in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare un avviso](quickstart-create-custom-alerts.md)
- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
