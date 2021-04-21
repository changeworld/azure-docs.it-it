---
title: Sessioni di messaggi del bus di servizio di Azure | Microsoft Docs
description: In questo articolo viene illustrato come usare le sessioni per abilitare la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: e22dfb2aa7372a227f70fd2bfa8f72d2161cda17
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750753"
---
# <a name="message-sessions"></a>Sessioni di messaggistica
Le sessioni del bus di servizio di Microsoft Azure consentono la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati. Le sessioni possono essere usate in criteri **First in, First out (FIFO)** e di **richiesta-risposta**. Questo articolo illustra come usare le sessioni per implementare questi criteri quando si usa il bus di servizio. 

> [!NOTE]
> Il livello Basic del bus di servizio non supporta le sessioni. I livelli Standard e Premium supportano le sessioni. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Criterio FIFO (First in, First out)
Per realizzare una garanzia FIFO nel bus di servizio, usare le sessioni. Il bus di servizio non prescrive la natura della relazione tra i messaggi e non definisce nemmeno un modello specifico per determinare dove inizia o finisce una sequenza di messaggi.

Qualsiasi mittente può creare una sessione quando invia messaggi a un argomento o a una coda impostando la proprietà **ID** sessione su un identificatore definito dall'applicazione univoco per la sessione. A livello di protocollo AMQP 1.0, questo valore è mappato alla proprietà *group-id*.

Nelle code o nelle sottoscrizioni in grado di riconoscere la sessione, le sessioni vengono aperte quando è presente almeno un messaggio con l'ID sessione. Una volta creata una sessione, non c'è un'API o un tempo definito per la scadenza o la rimozione della sessione. In teoria, è possibile ricevere un messaggio per una sessione oggi stesso, il messaggio successivo tra un anno e, se l'ID sessione corrisponde, la sessione è la stessa dal punto di vista del bus di servizio.

In genere, tuttavia, un'applicazione riconosce chiaramente dove inizia e dove finisce un set di messaggi correlati, il bus di servizio non imposta alcuna regola specifica. Ad esempio, l'applicazione potrebbe impostare la proprietà **Label** per il primo messaggio per avviare **,** per i messaggi intermedi sul contenuto **e** per l'ultimo messaggio per **terminare**. La posizione relativa dei messaggi di contenuto può essere calcolata come differenza tra il valore *SequenceNumber* del messaggio corrente e il valore **SequenceNumber** del messaggio contrassegnato come *start*.

> [!IMPORTANT]
> Quando le sessioni sono abilitate in una coda o in una sottoscrizione, le applicazioni client ***non possono più*** inviare/ricevere messaggi regolari. Tutti i messaggi devono essere inviati come parte di una sessione (impostando l'ID sessione) e ricevuti accettando la sessione.

Le API per le sessioni sono presenti nei client di accodamento e di sottoscrizione. Esiste un modello imperativo che controlla quando vengono ricevuti sessioni e messaggi e un modello basato su gestori che nasconde la complessità della gestione del ciclo di ricezione. 

Per gli esempi, usare i collegamenti nella [sezione Passaggi](#next-steps) successivi. 

### <a name="session-features"></a>Funzionalità delle sessioni

Le sessioni forniscono il demultiplexing simultaneo dei flussi di messaggi con interfoliazione, conservando e garantendo il recapito ordinato.

![Diagramma che mostra come la funzionalità Sessioni mantiene il recapito ordinato.][1]

Un ricevitore di sessione viene creato da un client che accetta una sessione. Quando la sessione viene accettata e mantenuta da un client, il client mantiene un blocco esclusivo su tutti i messaggi con **l'ID** sessione della sessione nella coda o nella sottoscrizione. Contenerà anche blocchi esclusivi su tutti i messaggi con **l'ID sessione** che arriveranno in un secondo momento.

Il blocco viene rilasciato quando si chiamano i metodi correlati alla chiusura sul ricevitore o alla scadenza del blocco. Esistono anche metodi per rinnovare i blocchi nel ricevitore. È invece possibile usare la funzionalità di rinnovo automatico del blocco in cui è possibile specificare la durata del periodo di tempo per cui si vuole continuare a rinnovare il blocco. Il blocco della sessione deve essere considerato come un blocco esclusivo su un file, vale a dire che l'applicazione deve chiudere la sessione non appena non è più necessaria e/o quando non sono previsti altri messaggi.

Quando più ricevitori simultanei eseguono il pull dalla coda, i messaggi che appartengono a una sessione specifica vengono inviati al ricevitore specifico che attualmente ha bloccato la sessione. Con questa operazione, viene eseguito il demultiplexing di un flusso di messaggi con interfoliazione in una coda o una sottoscrizione in ricevitori diversi, che possono trovarsi anche in computer client diversi, perché la gestione del blocco avviene sul lato del servizio, all'interno del bus di servizio.

L'illustrazione precedente mostra tre ricevitori di sessioni simultanee. Una sessione con `SessionId` = 4 non ha nessun client proprietario attivo, il che significa che i messaggi non vengono recapitati da questa sessione specifica. Una sessione opera in molti modi, ad esempio come una coda secondaria.

Il blocco di sessione mantenuto dal ricevitore di sessione comprende i blocchi dei messaggi usati dalla modalità di finalizzazione *blocco di visualizzazione*. Un blocco su una sessione è possibile su un solo ricevitore. Anche se un ricevitore può avere molti messaggi in transito, i messaggi verranno ricevuti in ordine. Se un messaggio viene abbandonato, verrà presentato di nuovo con la successiva operazione di ricezione.

### <a name="message-session-state"></a>Stato della sessione di messaggi

Quando i flussi di lavoro vengono elaborati in sistemi cloud a scalabilità elevata e a disponibilità elevata, il gestore del flusso di lavoro associato a una sessione specifica deve essere in grado di eseguire il ripristino in caso di errori imprevisti e riprendere un lavoro completato parzialmente su un processo o un computer diverso da quello dove il lavoro è stato iniziato.

La funzionalità di stato della sessione consente un'annotazione definita dall'applicazione di una sessione di messaggi all'interno del broker, in modo che lo stato di elaborazione registrato rispetto alla sessione diventi immediatamente disponibile quando la sessione viene acquisita da un nuovo elaboratore.

Dal punto di vista del bus di servizio, lo stato della sessione di messaggi è un oggetto binario opaco che può contenere i dati delle dimensioni di un messaggio, ovvero 256 KB per il livello Standard del bus di servizio e 1 MB per il livello Premium. Lo stato di elaborazione relativo a una sessione può essere conservato all'interno dello stato della sessione oppure lo stato della sessione può puntare a una posizione di archiviazione o a un record di database che contiene tali informazioni.

I metodi per la gestione dello stato della sessione, SetState e GetState, sono disponibili nell'oggetto ricevitore di sessione. Una sessione che in precedenza non aveva stato sessione restituisce un riferimento Null per GetState. Lo stato della sessione impostato in precedenza può essere cancellato passando null al metodo SetState sul ricevitore.

Lo stato della sessione rimane invariato fino a quando non viene cancellata (restituendo **null**), anche se sono stati usati tutti i messaggi presenti nella sessione.

Lo stato della sessione conservato in una coda o in una sottoscrizione viene conteggiato per il raggiungimento della quota di archiviazione dell'entità. Quando un'applicazione finisce di usare una sessione, è quindi consigliabile che esegua la pulizia dello stato conservato, per evitare costi di gestione esterni.

### <a name="impact-of-delivery-count"></a>Effetti del numero di recapiti

La definizione di numero di recapiti per messaggio nel contesto delle sessioni varia leggermente rispetto alla definizione in assenza di sessioni. Di seguito è riportata una tabella che riepiloga quando viene incrementato il numero di recapiti.

| Scenario | Numero di recapiti del messaggio incrementato |
|----------|---------------------------------------------|
| La sessione viene accettata, ma il blocco della sessione scade (a causa del timeout) | Sì |
| La sessione viene accettata, i messaggi all'interno della sessione non vengono completati (anche se sono bloccati) e la sessione viene chiusa | No |
| La sessione viene accettata, i messaggi vengono completati e la sessione viene chiusa in modo esplicito | N/A (flusso standard. Qui i messaggi vengono rimossi dalla sessione) |

## <a name="request-response-pattern"></a>Criterio richiesta-risposta
Il [criterio richiesta-risposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) è un criterio di integrazione ben definito che consente all'applicazione mittente di inviare una richiesta e offre al ricevitore un modo per inviare correttamente una risposta all'applicazione mittente. Questo criterio richiede in genere un argomento o una coda di breve durata per l'invio di risposte da parte dell'applicazione. In questo scenario, le sessioni offrono una semplice soluzione alternativa con semantica paragonabile. 

Più applicazioni possono inviare richieste a una singola coda, con un parametro di intestazione specifico impostato per identificare in modo univoco l'applicazione mittente. L'applicazione ricevente è in grado di elaborare le richieste in arrivo nella coda e di inviare risposte nella coda abilitata per la sessione, impostando l'ID sessione sull'identificatore univoco inviato dal mittente sul messaggio della richiesta. L'applicazione che ha inviato la richiesta può quindi ricevere messaggi sull'ID di sessione specifico ed elaborare correttamente le risposte.

> [!NOTE]
> L'applicazione che invia le richieste iniziali deve conoscere l'ID sessione e usarlo per accettare la sessione in modo che la sessione in cui si prevede che la risposta sia bloccata. È buona idea usare un GUID che identifichi in modo univoco l'istanza dell'applicazione come ID sessione. Non deve essere specificato alcun gestore di sessione o un timeout nel ricevitore di sessione per la coda per garantire che le risposte siano disponibili per essere bloccate ed elaborate da ricevitori specifici.

## <a name="next-steps"></a>Passaggi successivi
È possibile abilitare le sessioni di messaggi durante la creazione di una coda usando portale di Azure, PowerShell, l'interfaccia della riga di comando, il modello Resource Manager, .NET, Java, Python e JavaScript. Per altre informazioni, vedere [Abilitare le sessioni di messaggistica.](enable-message-sessions.md) 

Provare gli esempi nel linguaggio preferito per esplorare le bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure di libreria client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [bus di servizio di Azure di libreria client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Esempi di Azure.Messaging.ServiceBus per .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

