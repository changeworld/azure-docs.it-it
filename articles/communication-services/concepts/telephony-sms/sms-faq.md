---
title: DOMANDE FREQUENTI SU SMS
titleSuffix: An Azure Communication Services concept document
description: DOMANDE FREQUENTI SU SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646134"
---
# <a name="sms-faq"></a>DOMANDE FREQUENTI SU SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Un cliente può usare i servizi di comunicazione di Azure per scopi di emergenza?

I servizi di comunicazione di Azure non supportano la funzionalità da testo a 911 nella Stati Uniti, ma è possibile che si abbia l'obbligo di eseguire questa operazione in base alle regole della Federal Communications Commission (FCC).  È necessario valutare se le regole del testo da 911 FCC sono valide per il servizio o l'applicazione. Nella misura in cui si tratta di queste regole, sarà necessario instradare i messaggi di testo 911 ai Call Center di emergenza che li richiedono. È possibile determinare il modello di distribuzione da testo a 911, ma un approccio accettato dalla FCC prevede l'avvio automatico del dialer nativo sul dispositivo mobile dell'utente per fornire 911 testi tramite il gestore mobile sottostante.

## <a name="are-there-any-limits-on-sending-messages"></a>Sono previsti limiti per l'invio di messaggi?

Per garantire che Microsoft continui a offrire l'alta qualità del servizio coerente con i contratti di servizio, i servizi di comunicazione di Azure applicano limiti di velocità (diversi per ogni primitiva). Gli sviluppatori che chiamano le API oltre il limite riceveranno una risposta del codice di stato HTTP 429. Se la società ha requisiti che superano i limiti di frequenza, inviare un messaggio di posta elettronica all'indirizzo phone@microsoft.com .

Limiti di velocità per SMS:

|Operazione|Ambito|Intervalli di tempo| Limite (richiesta #) | Unità messaggi al minuto|
|---------|-----|-------------|-------------------|-------------------------|
|Invia messaggio|Per numero|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>In che modo i servizi di comunicazione di Azure gestiscono gli opt-out per i numeri senza pedaggio?

I rifiuti espliciti per i numeri verdi sono considerati obbligatori e vengono applicati dai gestori telefonici USA.
- STOP: se il destinatario di un SMS vuole rifiutarlo esplicitamente, può inviare il messaggio 'STOP' al numero verde. Il gestore invia la risposta predefinita seguente per STOP: "messaggio di rete: si è risposto con la parola" Stop "che blocca tutti i testi inviati da questo numero. Tornare al testo "unstop" per ricevere nuovamente i messaggi ".
- START/UNSTOP: se il destinatario vuole risottoscrivere gli SMS inviati da un numero verde, può inviare il messaggio 'START' o 'UNSTOP' al numero verde. Il gestore telefonico invia la risposta predefinita seguente al messaggio START/UNSTOP: "NETWORK MSG: You have replied "unstop" and will begin receiving messages again from this number."
- Servizi di comunicazione di Azure rileva il messaggio STOP e blocca l'invio di ulteriori messaggi al destinatario. Il rapporto di recapito indicherà un recapito non riuscito con un messaggio di stato analogo a "Mittente bloccato per il destinatario specificato".
- I messaggi STOP, UNSTOP e START verranno inoltrati di nuovo all'utente. Servizi di comunicazione di Azure invita a monitorare e implementare questi rifiuti espliciti per assicurarsi che non vengano eseguiti ulteriori tentativi di invio di messaggi ai destinatari che hanno rifiutato le comunicazioni.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Come è possibile ricevere messaggi usando i servizi di comunicazione di Azure?

I clienti di servizi di comunicazione Azure possono usare griglia di eventi di Azure per ricevere i messaggi in arrivo. Seguire questa [Guida introduttiva](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) per configurare la griglia di eventi per la ricezione di messaggi.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>È possibile inviare/ricevere messaggi lunghi (>2048 caratteri)?

I servizi di comunicazione di Azure supportano l'invio e la ricezione di messaggi lunghi via SMS. Tuttavia, alcuni vettori o dispositivi wireless potrebbero funzionare in modo diverso durante la ricezione di messaggi lunghi.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Come vengono inviati i messaggi ai numeri fissi?

Nel Stati Uniti, servizi di comunicazione Azure non controlla i numeri di rete fissa e tenterà di inviarli ai vettori per la consegna. Ai clienti verranno addebitati i messaggi inviati ai numeri fissi. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>È possibile inviare messaggi a più destinatari?


Sì, è possibile effettuare una richiesta con più destinatari. Seguire questa [Guida introduttiva](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) per inviare messaggi a più destinatari.
