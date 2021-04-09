---
title: Raccomandazioni sulla sicurezza per l'hub Internet
description: Informazioni sul concetto di consigli sulla sicurezza e su come vengono usati nell'hub Defender for Internet.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779342"
---
# <a name="security-recommendations-for-iot-hub"></a>Raccomandazioni sulla sicurezza per l'hub Internet

Defender for Internet esegue l'analisi delle risorse di Azure e dei dispositivi Internet e fornisce consigli sulla sicurezza per ridurre la superficie di attacco.
Le raccomandazioni sulla sicurezza sono praticabili e mirano a aiutare i clienti a rispettare le procedure consigliate per la sicurezza.

In questo articolo è disponibile un elenco di raccomandazioni che possono essere attivate nell'hub Internet delle cose.

## <a name="built-in-recommendations-in-iot-hub"></a>Suggerimenti predefiniti nell'hub Internet

Gli avvisi di raccomandazione forniscono informazioni e suggerimenti sulle azioni per migliorare il comportamento di sicurezza dell'ambiente.

| Gravità | Nome | origine dati | Descrizione |
|--|--|--|--|
| Alto | Credenziali di autenticazione identiche usate da più dispositivi | Hub IoT | Le credenziali di autenticazione dell'hub Internet vengono usate da più dispositivi. Questo processo può indicare un dispositivo illegittimo che rappresenta un dispositivo legittimo. L'uso di credenziali duplicate aumenta il rischio di rappresentazione del dispositivo da parte di un attore malintenzionato. |
| Medio | Il criterio di filtro IP predefinito deve essere Deny | Hub IoT | Per la configurazione del filtro IP è necessario definire regole per il traffico consentito e, per impostazione predefinita, negare tutto il resto del traffico per impostazione predefinita. |
| Medio | La regola di filtro IP include un intervallo IP di grandi dimensioni | Hub IoT | Un intervallo di indirizzi IP di origine della regola di filtro IP è troppo grande. Le regole eccessivamente permissive possono esporre l'hub delle cose a attori malintenzionati. |
| Basso | Abilitare i log di diagnostica nell'hub Internet | Hub IoT | Abilitare i log e conservarli per un periodo massimo di un anno. Il mantenimento dei log consente di ricreare percorsi attività a scopo di analisi quando si verifica un evento imprevisto di sicurezza o la rete viene compromessa. |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
