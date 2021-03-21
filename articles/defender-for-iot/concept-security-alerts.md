---
title: Elenco di avvisi personalizzati & predefiniti
description: Informazioni sugli avvisi di sicurezza e sulla correzione consigliata usando Defender per le funzionalità e il servizio dell'hub Internet.
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
ms.openlocfilehash: ef33851600c576494e4e0903c6ab8ffefc9a1a59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636493"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Defender per gli avvisi di sicurezza dell'hub

Defender for Internet esegue continuamente l'analisi della soluzione Internet delle cose usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.
Inoltre, è possibile creare avvisi personalizzati in base alle proprie conoscenze sul comportamento previsto del dispositivo.
Un avviso funge da indicatore del potenziale compromesso e deve essere analizzato e aggiornato.

In questo articolo è disponibile un elenco di avvisi predefiniti, che possono essere attivati nell'hub Internet delle cose.
Oltre agli avvisi predefiniti, Defender per la gestione delle cose consente di definire avvisi personalizzati in base all'hub e/o al comportamento del dispositivo.
Per ulteriori informazioni, vedere la pagina relativa agli [avvisi personalizzabili](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Avvisi predefiniti per l'hub Internet

| Gravità | Nome | Descrizione | Correzione consigliata |
|--|--|--|--|
| Gravità **media** |  |  |  |
| Nuovo certificato aggiunto a un hub Internet | Medio | Un certificato denominato \' % {DescCertificateName} \' è stato aggiunto all'hub di Internet delle cose \' % {DescIoTHubName} \' . Se questa azione è stata eseguita da un'entità non autorizzata, potrebbe indicare attività dannose. | 1. Assicurarsi che il certificato sia stato aggiunto da un'entità autorizzata. <br> 2. se non è stato aggiunto da un'entità autorizzata, rimuovere il certificato e inoltrare l'avviso al team di sicurezza dell'organizzazione. |
| Certificato eliminato da un hub Internet delle cose | Medio | Un certificato denominato \' % {DescCertificateName} \' è stato eliminato dall'hub Internet delle cose \' % {DescIoTHubName} \' . Se questa azione è stata eseguita da un'entità non autorizzata, potrebbe indicare un'attività dannosa. | 1. Assicurarsi che il certificato sia stato rimosso da un'entità autorizzata. <br> 2. se il certificato non è stato rimosso da un'entità autorizzata, aggiungere nuovamente il certificato e inoltrare l'avviso al team di sicurezza dell'organizzazione. |
| Tentativo non riuscito di aggiungere un certificato a un hub Internet | Medio | Si è verificato un tentativo non riuscito di aggiungere il certificato \' % {DescCertificateName} all'hub Internet delle cose \' \' % {DescIoTHubName} \' . Se questa azione è stata eseguita da un'entità non autorizzata, potrebbe indicare attività dannose. | Assicurarsi che le autorizzazioni per modificare i certificati vengano concesse solo a entità autorizzate. |
| Tentativo non riuscito di eliminazione di un certificato da un hub Internet | Medio | Si è verificato un tentativo non riuscito di eliminare il certificato \' % {DescCertificateName} \' dall'hub di Internet delle cose \' % {DescIoTHubName} \' . Se questa azione è stata eseguita da un'entità non autorizzata, potrebbe indicare attività dannose. | Assicurarsi che le autorizzazioni per modificare i certificati vengano concesse solo a un'entità autorizzata. |
| identificazione personale del certificato del dispositivo x. 509 non corrispondente | Medio | l'identificazione personale del certificato del dispositivo x. 509 non corrisponde alla configurazione. | Esaminare gli avvisi sui dispositivi. Non è necessaria alcuna azione. |
| certificato x. 509 scaduto | Medio | Il certificato del dispositivo X. 509 è scaduto. | Potrebbe trattarsi di un dispositivo legittimo con un certificato scaduto o un tentativo di rappresentare un dispositivo legittimo. Se il dispositivo legittimo sta comunicando correttamente, probabilmente si tratta di un tentativo di rappresentazione. |
| Gravità **bassa** |  |  |  |
| Tentativo di aggiunta o modifica di un'impostazione di diagnostica di un hub di Internet delle cose rilevata | Basso | Il tentativo di aggiungere o modificare le impostazioni di diagnostica di un hub Internet è stato rilevato. Le impostazioni di diagnostica consentono di ricreare percorsi attività a scopo di analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa. Se questa azione non è stata eseguita da un'entità autorizzata, potrebbe indicare attività dannose. | 1. Assicurarsi che il certificato sia stato rimosso da un'entità autorizzata.<br> 2. se il certificato non è stato rimosso da un'entità autorizzata, aggiungere nuovamente il certificato ed eseguire l'escalation dell'avviso al team di sicurezza delle informazioni. |
| Tentativo di eliminazione di un'impostazione di diagnostica da un hub di Internet delle cose rilevato | Basso | % {DescAttemptStatusMessage} \' tentativo di aggiungere o modificare l'impostazione di diagnostica \' % {DescDiagnosticSettingName} \' dell'hub Internet delle cose \' % {DescIoTHubName} \' . L'impostazione di diagnostica consente di ricreare percorsi attività a scopo di analisi quando si verifica un evento imprevisto di sicurezza o la rete è compromessa. Se questa azione non è stata eseguita da un'entità autorizzata, potrebbe indicare un'attività dannosa. | Assicurarsi che le autorizzazioni per modificare le impostazioni di diagnostica siano concesse solo a un'entità autorizzata. |
| Token SAS scaduto | Basso | Token SAS scaduto usato da un dispositivo | Può essere un dispositivo legittimo con un token scaduto o un tentativo di rappresentare un dispositivo legittimo. Se il dispositivo legittimo sta attualmente comunicando correttamente, probabilmente si tratta di un tentativo di rappresentazione. |
| Firma del token SAS non valida | Basso | Un token SAS usato da un dispositivo ha una firma non valida. La firma non corrisponde alla chiave primaria o secondaria. | Esaminare gli avvisi sui dispositivi. Non è necessaria alcuna azione. |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
