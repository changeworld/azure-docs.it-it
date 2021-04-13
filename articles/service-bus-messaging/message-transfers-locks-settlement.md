---
title: Trasferimenti di messaggi, blocchi e regolamento del bus di servizio di Azure
description: Questo articolo fornisce una panoramica dei trasferimenti di messaggi del bus di servizio di Azure, dei blocchi e delle operazioni di regolamento.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306773"
---
# <a name="message-transfers-locks-and-settlement"></a>Trasferimenti, blocchi e finalizzazione dei messaggi

La funzionalità principale di un broker di messaggi come il bus di servizio è quella di accettare i messaggi in una coda o in un argomento e conservali rendendoli disponibili per un successivo recupero. *Invio* è il termine comunemente usato per il trasferimento di un messaggio in un broker di messaggi. *Ricezione* è il termine comunemente usato per il trasferimento di un messaggio in un client che esegue il recupero.

Quando un client invia un messaggio, vuole in genere sapere se il messaggio è stato correttamente trasferito e accettato dal broker o se si è verificato qualche tipo di errore. Questo riconoscimento positivo o negativo stabilisce il client e la comprensione del broker sullo stato di trasferimento del messaggio. Quindi, viene definito come *insediamento*.

Analogamente, quando un broker trasferisce un messaggio a un client, il broker e il client vogliono sapere se il messaggio è stato elaborato correttamente e può quindi essere rimosso oppure se l'elaborazione o il recapito del messaggio non è riuscito e di conseguenza il messaggio deve essere recapitato di nuovo.

## <a name="settling-send-operations"></a>Finalizzazione delle operazioni di invio

Usando qualsiasi client API del bus di servizio supportato, le operazioni di invio nel bus di servizio vengono sempre finalizzate in modo esplicito, ovvero l'operazione API attende l'arrivo del risultato dell'accettazione dal bus di servizio e quindi completa l'operazione di invio.

Se il messaggio viene rifiutato dal bus di servizio, il rifiuto contiene un indicatore di errore e un testo con un **ID di rilevamento** . Il rifiuto include anche informazioni relative al fatto che sia possibile o meno provare a eseguire di nuovo l'operazione con la possibilità di avere un esito positivo. Nel client queste informazioni vengono trasformate in un'eccezione, generata per il chiamante dell'operazione di invio. Se il messaggio è stato accettato, l'operazione viene completata automaticamente.

Quando si usa il protocollo AMQP, che è il protocollo esclusivo per i client di .NET Standard, Java, JavaScript, Python e go e [un'opzione per il client di .NET Framework](service-bus-amqp-dotnet.md), i trasferimenti di messaggi e gli stabilimenti sono pipeline e asincrona. Si consiglia di usare le varianti API del modello di programmazione asincrona.

Un mittente può inviare diversi messaggi in transito in rapida successione senza dover attendere il riconoscimento di ogni messaggio, come avverrebbe invece con il protocollo SBMP o HTTP 1.1. Le operazioni di invio asincrone vengono completate man mano che i relativi messaggi vengono accettati e archiviati, nelle entità partizionate o quando le operazioni di invio a entità diverse si sovrappongono. Il completamento può avvenire anche fuori dall'ordine di invio originale.

La strategia per gestire il risultato delle operazioni di invio può avere un impatto immediato e significativo sulle prestazioni dell'applicazione. Gli esempi in questa sezione sono scritti in C# e si applicano ai futuri Java, ai mono Java, alle promesse JavaScript e ai concetti equivalenti in altri linguaggi.

Se l'applicazione genera picchi di messaggi, illustrati qui con un ciclo semplice, e si attende il completamento di ogni operazione di invio prima di inviare il messaggio successivo, le API sincrone o asincrone hanno un funzionamento simile e l'invio di 10 messaggi viene completato solo dopo 10 round trip completi sequenziali per la finalizzazione.

Con un presupposto di latenza del round trip TCP di 70 millisecondi da un sito locale a un bus di servizio e con soli 10 ms affinché il bus di servizio accetti e memorizzi ogni messaggio, il ciclo seguente richiede almeno 8 secondi, senza contare i tempi di trasferimento del payload o potenziali effetti della congestione della route:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se l'applicazione avvia le 10 operazioni di invio asincrone in successione immediata e attende il rispettivo completamento separatamente, il tempo di round trip per le 10 operazioni di invio si sovrappone. I 10 messaggi vengono trasferiti in successione immediata, potenzialmente anche condividendo frame TCP, e la durata complessiva del trasferimento dipende in gran parte dal tempo di rete necessario per trasferire i messaggi al broker.

Basandosi sugli stessi presupposti illustrati per il ciclo precedente, il tempo di esecuzione totale sovrapposto per il ciclo seguente potrebbe rimanere ben sotto un secondo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

È importante notare che tutti i modelli di programmazione asincroni usano una forma di coda di lavoro nascosta basata sulla memoria che contiene le operazioni in sospeso. Quando l'API di invio restituisce, l'attività di invio viene accodata in tale coda di lavoro, ma l'azione del protocollo inizia solo dopo l'esecuzione dell'attività. Per il codice che tende a eseguire il push di picchi di messaggi e per i casi in cui l'affidabilità è importante, è necessario fare attenzione che non ci siano troppi messaggi "in corso" contemporaneamente, in quanto tutti i messaggi inviati occupano memoria fino a quando non sono effettivamente in transito.

I semafori, come illustrato nel frammento di codice seguente in C#, sono oggetti di sincronizzazione che consentono, quando necessario, tale limitazione a livello di applicazione. Questo uso di un semaforo consente la presenza al massimo di 10 messaggi in corso contemporaneamente. Uno dei 10 blocchi tramite semaforo disponibili viene acquisito prima dell'invio e rilasciato al completamento dell'invio. L'undicesimo passaggio nel ciclo resta in attesa fino a quando uno degli invii precedenti viene completato e quindi rende disponibile il blocco:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Le applicazioni non devono **mai** avviare un'operazione di invio asincrona adottando l'approccio "fire-and-forget", ovvero senza recuperare il risultato dell'operazione. Ciò potrebbe caricare la coda di attività interna e invisibile fino a esaurire la memoria, impedendo all'applicazione di rilevare gli errori di invio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Con un client AMQP di basso livello, il bus di servizio accetta anche i trasferimenti "pre-finalizzati". Un trasferimento pre-finalizzato è un'operazione di tipo fire-and-forget per la quale il risultato, qualunque esso sia, non viene segnalato al client e il messaggio viene considerato finalizzato quando inviato. La mancanza di feedback per il client significa anche che non ci sono dati disponibili per la diagnostica, quindi questa modalità non è idonea per richiedere assistenza al supporto tecnico di Azure.

## <a name="settling-receive-operations"></a>Finalizzazione delle operazioni di ricezione

Per le operazioni di ricezione, i client API del bus di servizio consentono due diverse modalità esplicite: *ricezione ed eliminazione* e *blocco di visualizzazione*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

La modalità di **ricezione e eliminazione** indica al broker di prendere in considerazione tutti i messaggi inviati al client ricevente come risolti quando vengono inviati. Ciò significa che il messaggio è considerato utilizzato non appena il broker lo ha inviato in transito. Se il trasferimento del messaggio non riesce, il messaggio viene perso.

Il vantaggio di questa modalità è il fatto che il ricevitore non deve eseguire altre azioni sul messaggio e non viene rallentato dall'attesa del risultato della finalizzazione. Se i dati contenuti nei singoli messaggi hanno un valore basso e/o sono significativi solo per un tempo molto breve, questa modalità è una scelta ragionevole.

### <a name="peeklock"></a>PeekLock

La modalità **blocco di visualizzazione** indica al broker che il client ricevente desidera risolvere i messaggi ricevuti in modo esplicito. Il messaggio viene reso disponibile per l'elaborazione da parte del ricevitore, mentre viene applicato un blocco esclusivo nel servizio in modo che altri ricevitori concorrenti non possano visualizzarlo. La durata del blocco viene inizialmente definita a livello di coda o di sottoscrizione e può essere estesa dal client proprietario del blocco. Per informazioni dettagliate sul rinnovo dei blocchi, vedere la sezione [renew Locks](#renew-locks) in questo articolo. 

Quando un messaggio è bloccato, gli altri client che ricevono dalla stessa coda o sottoscrizione possono accettare i blocchi e recuperare i successivi messaggi disponibili non attivamente bloccati. Quando il blocco su un messaggio viene rilasciato in modo esplicito o scade, il messaggio ritorna all'inizio o in prossimità dell'inizio dell'ordine di recupero per essere recapitato nuovamente.

Quando il messaggio viene rilasciato ripetutamente dai ricevitori o lascia trascorrere il blocco per un numero definito di volte ([conteggio recapito massimo](service-bus-dead-letter-queues.md#maximum-delivery-count)), il messaggio viene rimosso automaticamente dalla coda o dalla sottoscrizione e inserito nella coda dei messaggi non recapitabili associata.

Il client ricevente avvia lo stabilimento di un messaggio ricevuto con un riconoscimento positivo quando chiama l' `Complete` API per il messaggio. Indica al broker che il messaggio è stato elaborato correttamente e che il messaggio viene rimosso dalla coda o dalla sottoscrizione. Il broker risponde all'intenzione di finalizzazione del ricevitore indicando se la finalizzazione è stata possibile.

Quando il client ricevente non riesce a elaborare un messaggio, ma vuole che il messaggio venga recapitato nuovamente, può chiedere esplicitamente di rilasciare e sbloccare il messaggio immediatamente chiamando l' `Abandon` API per il messaggio o non può eseguire alcuna operazione e lasciare che il blocco sia trascorso.

Se un client ricevente non riesce a elaborare un messaggio e sa che la redistribuzione del messaggio e il nuovo tentativo di esecuzione dell'operazione non sono utili, può rifiutare il messaggio, che lo sposta nella coda dei messaggi non recapitabili chiamando l' `DeadLetter` API del messaggio, che consente anche di impostare una proprietà personalizzata, incluso un codice motivo che può essere recuperato con il messaggio dalla coda dei messaggi non recapita

Un caso speciale di insediamento è il rinvio, descritto in un [articolo separato](message-deferral.md).

Le `Complete` `Deadletter` operazioni, o `RenewLock` possono avere esito negativo a causa di problemi di rete, se il blocco mantenuto è scaduto o se sono presenti altre condizioni sul lato del servizio che impediscono il regolamento. In uno degli ultimi casi, il servizio invia un riconoscimento negativo (NAK) che genera un'eccezione nei client API. Se il motivo è un'interruzione della connessione di rete, il blocco viene eliminato perché il bus di servizio non supporta il ripristino dei collegamenti AMQP esistenti in una connessione diversa.

Se ha `Complete` esito negativo, che in genere si trova alla fine della gestione dei messaggi e, in alcuni casi, dopo i minuti di elaborazione, l'applicazione ricevente può decidere se mantenere lo stato del lavoro e ignorare lo stesso messaggio quando viene recapitato una seconda volta o se estrae il risultato del lavoro e i tentativi di recapito del messaggio.

Il meccanismo tipico per l'identificazione di recapiti di messaggi duplicati consiste nel verificare il valore di message-id, che può e deve essere impostato dal mittente su un valore univoco, possibilmente allineato con un identificatore del processo di origine. Probabilmente, un pianificatore di processi imposta il valore di message-id sull'identificatore del processo che sta cercando di assegnare a un ruolo di lavoro con il ruolo di lavoro specificato e il ruolo di lavoro ignora la seconda occorrenza dell'assegnazione del processo, se tale processo è già stato eseguito.

> [!IMPORTANT]
> È importante notare che il blocco che PeekLock acquisisce sul messaggio è volatile e può andare perduto nelle condizioni seguenti
>   * Aggiornamento del servizio
>   * Aggiornamento del sistema operativo
>   * Modifica delle proprietà nell'entità (coda, argomento, sottoscrizione) durante la conservazione del blocco.
>
> Quando il blocco viene perso, il bus di servizio di Azure genera una LockLostException che verrà rilevata nel codice dell'applicazione client. In questo caso, la logica di ripetizione dei tentativi predefinita del client dovrebbe avviarsi automaticamente, quindi ripetere l'operazione.

## <a name="renew-locks"></a>Rinnova blocchi
Il valore predefinito per la durata del blocco è **30 secondi**. È possibile specificare un valore diverso per la durata del blocco a livello di coda o di sottoscrizione. Il client proprietario del blocco può rinnovare il blocco del messaggio usando i metodi sull'oggetto Receiver. È invece possibile usare la funzionalità di rinnovo automatico del blocco, in cui è possibile specificare la durata dell'intervallo di tempo per cui si vuole che il blocco venga rinnovato. 

## <a name="next-steps"></a>Passaggi successivi
- Un caso speciale di insediamento è il rinvio. Per informazioni dettagliate, vedere il [rinvio del messaggio](message-deferral.md) . 
- Per informazioni sui messaggi non recapitabili, vedere Code dei messaggi non [recapitabili](service-bus-dead-letter-queues.md).
- Per altre informazioni sulla messaggistica del bus di servizio in generale, vedere [code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)