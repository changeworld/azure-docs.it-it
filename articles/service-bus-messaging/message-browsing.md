---
title: bus di servizio di Azure - Esplorazione dei messaggi
description: Esplorare e visualizzare i messaggi del bus di servizio consente a bus di servizio di Azure client di enumerare tutti i messaggi in una coda o in una sottoscrizione.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520123"
---
# <a name="message-browsing"></a>Esplorazione dei messaggi
L'esplorazione dei messaggi, o visualizzazione, consente a un client del bus di servizio di enumerare tutti i messaggi in una coda o in una sottoscrizione, a scopo di diagnostica e debug.

L'operazione Peek su una coda o una sottoscrizione restituisce al massimo il numero di messaggi richiesto. La tabella seguente illustra i tipi di messaggi restituiti dall'operazione Peek. 

| Tipo di messaggi | Incluso | 
| ---------------- | ----- | 
| Messaggi attivi | Sì |
| Messaggi non recapitabili | No | 
| Messaggi bloccati | Sì |
| Messaggi scaduti |  Può essere (prima che siano in formato non recapitato) |
| Messaggi pianificati | Sì per le code. No per le sottoscrizioni |

## <a name="dead-lettered-messages"></a>Messaggi non recapitabili
Per visualizzare i **messaggi non** recapitati di una coda o di una sottoscrizione, l'operazione di visualizzazione deve essere eseguita nella coda dei messaggi non recapitati associata alla coda o alla sottoscrizione. Per altre informazioni, vedere [Accesso alle code di messaggi non recapitare](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue).

## <a name="expired-messages"></a>Messaggi scaduti
I messaggi scaduti possono essere inclusi nei risultati restituiti dall'operazione Peek. I messaggi utilizzati e scaduti vengono puliti da un'esecuzione asincrona di "Garbage Collection". Questo passaggio potrebbe non verificarsi necessariamente immediatamente dopo la scadenza dei messaggi. Per questo motivo, un'operazione di visualizzazione può restituire messaggi già scaduti. Questi messaggi verranno rimossi o non recapitati quando un'operazione di ricezione viene richiamata nella coda o nella sottoscrizione la volta successiva. Tenere presente questo comportamento quando si tenta di recuperare i messaggi posticilati dalla coda. 

Un messaggio scaduto non è più idoneo per il recupero regolare con altri mezzi, anche quando viene restituito da Peek. La restituzione di questi messaggi è di progettazione perché Peek è uno strumento di diagnostica che riflette lo stato corrente del log.

## <a name="locked-messages"></a>Messaggi bloccati
Peek restituisce anche i messaggi **bloccati** e attualmente elaborati da altri ricevitori. Tuttavia, poiché Peek restituisce uno snapshot disconnesso, lo stato di blocco di un messaggio non può essere osservato nei messaggi visualizzati.

## <a name="peek-apis"></a>API Peek
Peek funziona su code, sottoscrizioni e relative code di messaggi non recapitbili. 

Quando viene chiamata ripetutamente, l'operazione di visualizzazione enumera tutti i messaggi nella coda o nella sottoscrizione, in ordine, dal numero di sequenza più basso disponibile al più alto. È l'ordine in cui i messaggi sono stati accodati, non l'ordine in cui i messaggi potrebbero essere recuperati.

È anche possibile passare un oggetto SequenceNumber a un'operazione di visualizzazione. Verrà usato per determinare da dove iniziare la visualizzazione. È possibile effettuare chiamate successive all'operazione di visualizzazione senza specificare il parametro per enumerare ulteriormente.

## <a name="next-steps"></a>Passaggi successivi
Provare gli esempi nel linguaggio preferito per esplorare la funzionalità di visualizzazione dei messaggi o di visualizzazione dei messaggi:

- [bus di servizio di Azure di libreria client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Esempio di visualizzazione di un** messaggio
- [bus di servizio di Azure di libreria client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek.py**
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.js** esempio
- [bus di servizio di Azure di libreria client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **Esempio browseMessages.ts**
- [Esempi di Azure.Messaging.ServiceBus per .NET:](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) vedere i metodi peek sulle classi ricevitore nella documentazione [di riferimento.](/dotnet/api/azure.messaging.servicebus)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  **Esempio di esplorazione dei messaggi (anteprima)** 
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **Esempio di esplorazione dei** messaggi. 
