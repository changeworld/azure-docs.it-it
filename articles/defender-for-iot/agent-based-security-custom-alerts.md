---
title: Avvisi personalizzati sulla sicurezza basata su agenti
titleSuffix: Azure Defender for IoT
description: Informazioni sugli avvisi di sicurezza personalizzabili e sulla correzione consigliata usando Defender per le funzionalità e il servizio del dispositivo.
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
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 5d0eeb046d7a4ba474a1ed4a2cfb07a07f1c3888
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493306"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Avvisi di sicurezza personalizzati per i dispositivi Defender

Defender for Internet esegue continuamente l'analisi della soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

Si consiglia di creare avvisi personalizzati in base alla propria conoscenza del comportamento previsto del dispositivo per assicurarsi che gli avvisi fungano da indicatori più efficienti di potenziali compromessi per la distribuzione e il paesaggio aziendali univoci.

Gli elenchi seguenti di Defender per gli avvisi relativi ai dispositivi sono definibili dall'utente in base al comportamento previsto per i dispositivi. Per ulteriori informazioni su come personalizzare ogni avviso, vedere [creare avvisi personalizzati](quickstart-create-custom-alerts.md).

## <a name="agent-based-security-custom-alerts"></a>Avvisi personalizzati sulla sicurezza basata su agenti

| Gravità | Nome avviso | Origine dati | Descrizione | Correzione consigliata |
|--|--|--|--|--|
| Basso | Avviso personalizzato: il numero di connessioni attive non è compreso nell'intervallo consentito | Defender (Classic): micro-Agent, Azure RTO | Il numero connessioni attive in una finestra temporale specifica non rientra nell'intervallo attualmente configurato e consentito. | Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di connessioni consentite. |
| Basso | Avviso personalizzato-la connessione in uscita creata a un indirizzo IP non è consentita | Defender (Classic): micro-Agent, Azure RTO | È stata creata una connessione in uscita a un IP che non rientra nell'elenco di indirizzi IP consentiti. | Esaminare i log del dispositivo. Scoprire l'origine della connessione e determinare se è dannosa o meno. Se è dannosa, rimuovere il possibile malware e identificare l'origine. Se non è dannosa, aggiungere l'origine all'elenco di indirizzi IP consentiti. |
| Basso | Avviso personalizzato: il numero di accessi locali non riusciti non è compreso nell'intervallo consentito | Defender (Classic): micro-Agent, Azure RTO | Il numero di accessi locali non riusciti in un intervallo di tempo specifico non è compreso nell'intervallo attualmente configurato e consentito. |  |
| Basso | Avviso personalizzato: l'accesso di un utente che non è presente nell'elenco utenti consentiti | Defender (Classic): micro-Agent, Azure RTO | Un utente locale non incluso nell'elenco di utenti consentiti ha effettuato l'accesso al dispositivo. | Se si salvano dati non elaborati, passare all'account di log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco Consenti/blocca per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. |
| Basso | Avviso personalizzato: è stato eseguito un processo non consentito | Defender (Classic): micro-Agent, Azure RTO | Nel dispositivo è stato eseguito un processo non consentito. | Se si salvano dati non elaborati, passare all'account di log Analytics e usare i dati per esaminare il dispositivo, identificare l'origine e quindi correggere l'elenco Consenti/blocca per tali impostazioni. Se attualmente non si salvano dati non elaborati, passare al dispositivo e correggere l'elenco di elementi consentiti/bloccati per tali impostazioni. |
|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare un avviso](quickstart-create-custom-alerts.md)
- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
