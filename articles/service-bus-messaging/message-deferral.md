---
title: 'bus di servizio di Azure: rinvio dei messaggi'
description: Questo articolo illustra come rinviare il recapito di bus di servizio di Azure messaggi. Il messaggio rimane nella coda o nella sottoscrizione, ma viene messo da parte.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869492"
---
# <a name="message-deferral"></a>Differimento di messaggi
Quando un client di coda o sottoscrizione riceve un messaggio che è disposto a elaborare, ma l'elaborazione non è attualmente possibile a causa di circostanze speciali, ha la possibilità di "posticipare" il recupero del messaggio a un punto successivo. Il messaggio rimane nella coda o nella sottoscrizione, ma viene messo da parte.

> [!NOTE]
> I messaggi posticied non verranno spostati automaticamente nella coda dei messaggi non [recapitati dopo la scadenza di](./service-bus-dead-letter-queues.md#time-to-live). Questo comportamento è da progettazione.

## <a name="sample-scenarios"></a>Scenari di esempio
Il rinvio è una funzionalità creata in modo specifico per gli scenari di elaborazione del flusso di lavoro. I framework del flusso di lavoro possono richiedere l'elaborazione di determinate operazioni in un ordine specifico. Potrebbe essere necessario posticipare l'elaborazione di alcuni messaggi ricevuti fino a quando non vengono completate le attività preposte informate da altri messaggi.

Un semplice esempio illustrativo è dato da una sequenza di elaborazione degli ordini in cui la notifica di pagamento del fornitore esterno dei servizi di pagamento viene visualizzata in un sistema prima che l'ordine di acquisto corrispondente sia stato propagato dal negozio al sistema di evasione degli ordini. In tal caso, il sistema di adempimenti potrebbe rinviare l'elaborazione della notifica di pagamento fino a quando non è presente un ordine a cui associarla. Negli scenari rendezvous in cui i messaggi di origini diverse fanno andare avanti un flusso di lavoro, l'ordine di esecuzione in tempo reale potrebbe essere corretto, ma i messaggi che mostrano i risultati potrebbero arrivare non in ordine.

Il differimento consente di riordinare i messaggi, sostituendo l'ordine di arrivo con un ordine in cui possono essere elaborati, lasciando nell'archivio i messaggi per cui l'elaborazione deve essere posticipata.

Se non è possibile elaborare un messaggio perché una determinata risorsa per la gestione del messaggio è temporaneamente non disponibile, ma l'elaborazione del messaggio non [](message-sequencing.md) deve essere sospesa in modo riepilogato, un modo per inserire tale messaggio sul lato per alcuni minuti è ricordare il numero di sequenza in un messaggio pianificato da pubblicare in pochi minuti e recuperare nuovamente il messaggio posticipato all'arrivo del messaggio pianificato. Se un gestore di messaggi dipende da un database per tutte le operazioni e tale database è temporaneamente non disponibile, non deve usare il rinvio, ma sospendere completamente la ricezione dei messaggi fino a quando il database non sarà nuovamente disponibile. 

## <a name="retrieving-deferred-messages"></a>Recupero di messaggi posticimanti
I messaggi posticimanti rimangono nella coda principale insieme a tutti gli altri messaggi attivi (a differenza dei messaggi non recapitati presenti in una coda secondaria), ma non possono più essere ricevuti usando le normali operazioni di ricezione. I messaggi rinviati possono essere individuati tramite l'[esplorazione dei messaggi](message-browsing.md) se un'applicazione ne perde traccia.

Per recuperare un messaggio posticipato, il proprietario è responsabile della memoria del numero di sequenza mentre lo rinvia. Qualsiasi ricevitore che conosce il numero di sequenza di un messaggio posticipato può ricevere il messaggio in un secondo momento usando metodi di ricezione che accettano il numero di sequenza come parametro. Per altre informazioni sui numeri di sequenza, vedere [Sequenziazione dei messaggi e timestamp](message-sequencing.md).

## <a name="next-steps"></a>Passaggi successivi
Provare gli esempi nel linguaggio scelto per esplorare bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure esempi di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure esempi di librerie client per Python:](/samples/azure/azure-sdk-for-python/servicebus-samples/) vedere l'esempio **receive_deferred_message_queue.py.** 
- [bus di servizio di Azure esempi di librerie client per JavaScript:](/samples/azure/azure-sdk-for-js/service-bus-javascript/) vedere l'esempio **advanced/deferral.js.** 
- [bus di servizio di Azure esempi di librerie client per TypeScript:](/samples/azure/azure-sdk-for-js/service-bus-typescript/) vedere l'esempio **advanced/deferral.ts.** 
- [Esempi di Azure.Messaging.ServiceBus per .NET:](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) vedere **l'esempio di impostazione dei** messaggi. 

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) vedere **l'esempio deferral.** 
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
