---
title: Defender-RTO-micro-Agent per Azure incorporato & avvisi e consigli personalizzabili
description: Informazioni sugli avvisi di sicurezza e sulla correzione consigliata con Azure Internet delle cose Defender-Internet
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: d6dfeb01e174d71c926e9fcf7f5f1ee078524cf9
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494275"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender-be-micro-Agent per gli avvisi di sicurezza e le raccomandazioni di Azure RTO (anteprima)

Defender-molto-micro-Agent per Azure RTO analizza continuamente la soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per avvertire l'utente di potenziali attività dannose e modifiche sospette del sistema. È anche possibile creare avvisi personalizzati in base alle proprie conoscenze sul comportamento previsto e sulle baseline del dispositivo.

Un avviso Defender-RTO-micro-Agent per Azure funge da indicatore del potenziale compromesso e deve essere analizzato e aggiornato. Una raccomandazione Defender-RTO-micro-Agent for Azure identifica un comportamento di sicurezza debole da correggere e aggiornare. 

In questo articolo è presente un elenco di avvisi e consigli predefiniti che vengono attivati in base agli intervalli predefiniti e personalizzabili con i propri valori, in base al comportamento previsto o di base. 

Per ulteriori informazioni sul funzionamento della personalizzazione degli avvisi nel servizio Defender for Internet, vedere la pagina relativa agli [avvisi personalizzabili](concept-customizable-security-alerts.md). Le tabelle seguenti illustrano in dettaglio gli avvisi e le raccomandazioni specifici che è possibile personalizzare quando si usa Defender-Internet. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-RTO-micro-Agent per gli avvisi di sicurezza supportati da Azure

### <a name="device-related-security-alerts"></a>Avvisi di sicurezza relativi al dispositivo

|Attività di avviso di sicurezza correlata al dispositivo  |Nome avviso  |
|---------|---------|
|Indirizzo IP| Comunicazione con un indirizzo IP sospetto rilevato|
|Identificazione personale del certificato del dispositivo X. 509|Identificazione personale del certificato del dispositivo X. 509 non corrispondente|
|Certificato X.509| Certificato X. 509 scaduto|
|Token di firma di accesso condiviso| Token SAS scaduto|
|Token di firma di accesso condiviso| Firma del token SAS non valida|

### <a name="iot-hub-related-security-alerts"></a>Avvisi di sicurezza correlati all'hub Internet

|Attività avviso di sicurezza dell'hub Internet  |Nome avviso  |
|---------|---------|
|Aggiungere un certificato    |  È stato rilevato un tentativo non riuscito di aggiungere un certificato a un hub Internet.       |
|Aggiunta o modifica di un'impostazione di diagnostica    | È stato rilevato un tentativo di aggiungere o modificare un'impostazione di diagnostica di un hub Internet.      |
|Eliminare un certificato    |  È stato rilevato un tentativo non riuscito di eliminare un certificato da un hub Internet       |
|Eliminare un'impostazione di diagnostica    |  Rilevato tentativo di eliminazione di un'impostazione di diagnostica da un hub Internet      |
|Certificato eliminato    | L'eliminazione di un certificato da un hub Internet è stata rilevata        |
|Nuovo certificato     |  È stata rilevata l'aggiunta di un nuovo certificato a un hub Internet.       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender-RTO-micro-Agent per Azure supporta gli avvisi personalizzabili

### <a name="device-related-customizable-alerts"></a>Avvisi personalizzabili correlati al dispositivo

|Attività correlata al dispositivo |Nome avviso  |
|---------|---------|
|Connessioni attive|Il numero di connessioni attive non è compreso nell'intervallo consentito|
|Messaggi da cloud a dispositivo nel protocollo **MQTT**|Il numero di messaggi da cloud a dispositivo nel protocollo **MQTT** non è compreso nell'intervallo consentito|
|Connessione in uscita| Connessione in uscita a un indirizzo IP non consentito|

### <a name="hub-related-customizable-alerts"></a>Avvisi personalizzabili correlati all'hub 

|Attività correlate all'hub  |Nome avviso  |
|---------|---------|
|Ripulitura della coda di comandi     |  Numero di eliminazioni della coda dei comandi non comprese nell'intervallo consentito       |
|Messaggi da cloud a dispositivo nel protocollo **MQTT**    |  Numero di messaggi da cloud a dispositivo nel protocollo **MQTT** al di fuori dell'intervallo consentito       |
|Messaggi da dispositivo a cloud nel protocollo **MQTT**    | Numero di messaggi da dispositivo a cloud nel protocollo **MQTT** al di fuori dell'intervallo consentito        |
|Invoke di metodi diretti     |  Numero di richiami al metodo diretto non compresi nell'intervallo consentito       |
|Messaggi da cloud a dispositivo rifiutati nel protocollo **MQTT**     |   Numero di messaggi da cloud a dispositivo rifiutati nel protocollo **MQTT** al di fuori dell'intervallo consentito      |
|Aggiornamenti ai moduli gemelli     |  Numero di aggiornamenti ai moduli gemelli al di fuori dell'intervallo consentito       |
|Operazioni non autorizzate    |  Numero di operazioni non autorizzate al di fuori dell'intervallo consentito       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-RTO-micro-Agent per Azure-consigli supportati

### <a name="device-related-recommendations"></a>Suggerimenti relativi ai dispositivi

|Attività correlata al dispositivo  |Nome raccomandazione |
|---------|---------|
|Credenziali di autenticazione    |  Credenziali di autenticazione identiche usate da più dispositivi       |

### <a name="hub-related-recommendations"></a>Raccomandazioni relative all'hub

|Attività correlate all'hub Internet  |Nome raccomandazione |
|---------|---------|
|Criterio filtro IP   |  I criteri di filtro IP predefiniti devono essere impostati su **Nega**  |
|Regola di filtro IP| La regola di filtro IP include un intervallo IP di grandi dimensioni|
|Log di diagnostica|Suggerimento per abilitare i log di diagnostica nell'hub Internet|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Tutti gli avvisi e le raccomandazioni di Defender

Per un elenco completo di tutti gli avvisi e le raccomandazioni relative al servizio Defender per il servizio Internet, vedere gli [avvisi di sicurezza](concept-security-alerts.md)per le cose, [consigli](concept-recommendations.md)sulla sicurezza di Internet.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Defender-cose-micro-Agent per Azure RTO](quickstart-azure-rtos-security-module.md)
- [Configurare e personalizzare Defender-RTO-micro-Agent per Azure](how-to-azure-rtos-security-module.md)
- Fare riferimento all' [API Defender-RTO-micro-Agent per Azure](azure-rtos-security-module-api.md)