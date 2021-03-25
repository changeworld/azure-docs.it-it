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
ms.openlocfilehash: 0ddc9bfeb0df32614d835e0eaef9da52e917ee91
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108435"
---
# <a name="sms-concepts"></a>Concetti sugli SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

I servizi di comunicazione di Azure consentono di inviare e ricevere SMS tramite gli SDK SMS di servizi di comunicazione. Questi SDK possono essere usati per supportare gli scenari del servizio clienti, i promemoria degli appuntamenti, l'autenticazione a due fattori e altre esigenze di comunicazione in tempo reale. Gli SMS di Servizi di comunicazione consentono di inviare messaggi in modo affidabile, esponendo al contempo informazioni dettagliate su recapitabilità e percentuale di risposta.

Le funzionalità principali degli SDK SMS per i servizi di comunicazione di Azure includono:

-  Esperienza di configurazione **semplice** per l'aggiunta di funzionalità SMS alle applicazioni.
- Supporto per i messaggi a **velocità elevata** su numeri verdi per i casi d'uso di A2P (da applicazione a persona) negli Stati Uniti.
- Conversazioni **bidirezionali** per supportare scenari come supporto clienti, avvisi e promemoria degli appuntamenti.
- **Recapito affidabile** con report di recapito in tempo reale per i messaggi inviati dall'applicazione.
- **Analisi** per tenere traccia dei modelli di utilizzo e del coinvolgimento dei clienti.
- **Rifiuto esplicito della gestione del supporto** per rilevare automaticamente e rispettare i rifiuti espliciti per i numeri verdi. I rifiuti espliciti per i numeri verdi sono considerati obbligatori e vengono applicati dai gestori telefonici USA.
  - STOP: se il destinatario di un SMS vuole rifiutarlo esplicitamente, può inviare il messaggio 'STOP' al numero verde. Il gestore telefonico invia la risposta predefinita seguente al messaggio STOP: *"NETWORK MSG: You replied with the word "stop" which blocks all texts sent from this number. Text back "unstop" to receive messages again."*
  - START/UNSTOP: se il destinatario vuole risottoscrivere gli SMS inviati da un numero verde, può inviare il messaggio 'START' o 'UNSTOP' al numero verde. Il gestore telefonico invia la risposta predefinita seguente al messaggio START/UNSTOP: *"NETWORK MSG: You have replied "unstop" and will begin receiving messages again from this number."*
  - Servizi di comunicazione di Azure rileva il messaggio STOP e blocca l'invio di ulteriori messaggi al destinatario. Il rapporto di recapito indicherà un recapito non riuscito con un messaggio di stato analogo a "Mittente bloccato per il destinatario specificato".
  - I messaggi STOP, UNSTOP e START verranno inoltrati di nuovo all'utente. Servizi di comunicazione di Azure invita a monitorare e implementare questi rifiuti espliciti per assicurarsi che non vengano eseguiti ulteriori tentativi di invio di messaggi ai destinatari che hanno rifiutato le comunicazioni.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'invio di SMS](../../quickstarts/telephony-sms/send.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con [SMS SDK](../telephony-sms/sdk-features.md)
- Ottenere un [numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md) con supporto per gli SMS
- [Tipi di numeri di telefono in Servizi di comunicazione di Azure](../telephony-sms/plan-solution.md)
