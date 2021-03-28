---
title: Concetti sugli SMS di Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui concetti relativi agli SMS di Servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bdba05dd75b6b6f4e32bf3f536e794e568fa13d7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642675"
---
# <a name="sms-concepts"></a>Concetti sugli SMS
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

I servizi di comunicazione di Azure consentono di inviare e ricevere SMS tramite gli SDK SMS di servizi di comunicazione. Questi SDK possono essere usati per supportare gli scenari del servizio clienti, i promemoria degli appuntamenti, l'autenticazione a due fattori e altre esigenze di comunicazione in tempo reale. SMS di comunicazione consente di inviare messaggi in modo affidabile esponendo metriche di recapito e di risposta.

Le funzionalità principali degli SDK SMS per i servizi di comunicazione di Azure includono:

-  Esperienza di configurazione **semplice** per l'aggiunta di funzionalità SMS alle applicazioni.
- Supporto per i messaggi a **velocità elevata** su numeri verdi per i casi d'uso di A2P (da applicazione a persona) negli Stati Uniti.
- **Messaggistica Bulk** supportata per consentire l'invio di messaggi a più destinatari alla volta.
- Conversazioni **bidirezionali** per supportare scenari come supporto clienti, avvisi e promemoria degli appuntamenti.
- **Recapito affidabile** con report di recapito in tempo reale per i messaggi inviati dall'applicazione.
- **Analisi** per tenere traccia dei modelli di utilizzo di SMS.
- **Rifiuto esplicito della gestione del supporto** per rilevare automaticamente e rispettare i rifiuti espliciti per i numeri verdi. I rifiuti espliciti per i numeri verdi sono considerati obbligatori e vengono applicati dai gestori telefonici USA.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'invio di SMS](../../quickstarts/telephony-sms/send.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con [SMS SDK](../telephony-sms/sdk-features.md)
- Ottenere un [numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md) con supporto per gli SMS
- [Tipi di numeri di telefono in Servizi di comunicazione di Azure](../telephony-sms/plan-solution.md)
