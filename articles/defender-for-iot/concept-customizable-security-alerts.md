---
title: Avvisi di sicurezza personalizzati
description: Informazioni sugli avvisi di sicurezza personalizzabili e sulla correzione consigliata usando Defender per le funzionalità e il servizio Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809287"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Avvisi di sicurezza personalizzati di Defender

Defender for Internet esegue continuamente l'analisi della soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

Si consiglia di creare avvisi personalizzati in base alla propria conoscenza del comportamento previsto del dispositivo per assicurarsi che gli avvisi fungano da indicatori più efficienti di potenziali compromessi per la distribuzione e il paesaggio aziendali univoci.

Gli elenchi seguenti di Defender per gli avvisi per le cose sono definibili dall'utente in base all'hub e/o al comportamento del dispositivo. Per ulteriori informazioni su come personalizzare ogni avviso, vedere [creare avvisi personalizzati](quickstart-create-custom-alerts.md).

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


## <a name="agent-based-security-custom-alerts"></a>Avvisi personalizzati sulla sicurezza basata su agenti

| Gravità | Nome avviso | Origine dati | Descrizione | Correzione consigliata |
|--|--|--|--|--|
| Basso | Avviso personalizzato: il numero di connessioni attive non è compreso nell'intervallo consentito | Modulo di sicurezza classica, Azure RTO | Il numero connessioni attive in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. | Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di connessioni consentite. |
| Basso | Avviso personalizzato-la connessione in uscita creata a un indirizzo IP non è consentita | Modulo di sicurezza classica, Azure RTO | È stata creata una connessione in uscita a un IP che non rientra nell'elenco di indirizzi IP consentiti. | Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di indirizzi IP consentiti. |
| Basso | Avviso personalizzato: il numero di accessi locali non riusciti non è compreso nell'intervallo consentito | Modulo di sicurezza classica, Azure RTO | Il numero di accessi locali non riusciti in un intervallo di tempo specifico non è compreso nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: l'accesso di un utente che non è presente nell'elenco utenti consentiti | Modulo di sicurezza classica, Azure RTO | Un utente locale non incluso nell'elenco di utenti consentiti ha effettuato l'accesso al dispositivo. | Se si salvano dati non elaborati, passare all'account di log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco Consenti/blocca per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. |
| Basso | Avviso personalizzato: è stato eseguito un processo non consentito | Modulo di sicurezza classica, Azure RTO | Nel dispositivo è stato eseguito un processo non consentito. | Se si salvano dati non elaborati, passare all'account di log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco Consenti/blocca per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. |
|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare un avviso](quickstart-create-custom-alerts.md)
- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
