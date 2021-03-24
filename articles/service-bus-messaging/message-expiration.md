---
title: Bus di servizio di Azure-scadenza messaggio
description: Questo articolo illustra la scadenza e l'ora di vita dei messaggi del bus di servizio di Azure. Dopo tale scadenza, il messaggio non viene più recapitato.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 5d60d84bdc0d437d97c369296a414d55beda4167
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952321"
---
# <a name="message-expiration-time-to-live"></a>Scadenza dei messaggi (durata)
Il payload all'interno di un messaggio, oppure di un comando o di una richiesta che un messaggio trasmette a un ricevitore, è quasi sempre soggetto a un qualche tipo di scadenza a livello di applicazione. Dopo tale scadenza, il contenuto non viene più recapitato oppure l'operazione richiesta non viene più eseguita.

Per gli ambienti di sviluppo e test in cui code e argomenti vengono spesso usati nel contesto di esecuzioni parziali di applicazioni o parti di applicazioni, è anche consigliabile che i messaggi di test abbandonati vengano automaticamente sottoposti a Garbage Collection, in modo che l'esecuzione dei test successiva possa iniziare in modo pulito.

La scadenza di ogni singolo messaggio può essere controllata impostando la proprietà di sistema **time-to-Live** , che specifica una durata relativa. La scadenza diventa un istante assoluto quando il messaggio viene accodato nell'entità. In quel momento, la proprietà **Expires-at-UTC** accetta il valore della   +  **durata (time-to-Live**) dell'ora UTC. L'impostazione TTL (time-to-Live) in un messaggio negoziato non viene applicata quando non ci sono client in ascolto attivo.

Oltre la **scadenza** immediata, i messaggi diventano non idonei per il recupero. La scadenza non influisce sui messaggi attualmente bloccati per il recapito. Questi messaggi vengono comunque gestiti normalmente. Se il blocco scade o il messaggio viene abbandonato, la scadenza ha effetto immediato.

Quando il messaggio è bloccato, l'applicazione potrebbe essere in possesso di un messaggio che è scaduto. Il fatto che l'applicazione proceda con l'elaborazione o scelga di abbandonare il messaggio dipende dall'implementatore.

Il valore TTL estremamente basso nell'ordine di millisecondi o secondi può causare la scadenza dei messaggi prima che le applicazioni riceventi lo ricevano. Prendere in considerazione la massima durata (TTL) che funziona per l'applicazione.

## <a name="entity-level-expiration"></a>Scadenza a livello di entità
Tutti i messaggi inviati a una coda o a un argomento sono soggetti a una scadenza predefinita impostata a livello di entità. Può anche essere impostato nel portale durante la creazione e modificato in un secondo momento. La scadenza predefinita viene usata per tutti i messaggi inviati all'entità in cui la durata (TTL) non è impostata in modo esplicito. La scadenza predefinita funziona anche come limite per il valore di durata (TTL). I messaggi con una scadenza più lunga della durata (TTL) rispetto al valore predefinito vengono regolati automaticamente sul valore di durata (TTL) del messaggio predefinito prima dell'accodamento.

> [!NOTE]
> Il valore di durata (TTL) predefinito per un messaggio negoziato è il valore massimo possibile per un intero a 64 bit cantato se non diversamente specificato.
>
> Per le entità di messaggistica (code e argomenti), l'ora di scadenza predefinita è anche il valore massimo possibile per un intero a 64 bit con segno per i livelli standard e Premium del bus di servizio. Per il livello **Basic** , l'ora di scadenza predefinita (anche massima) è di **14 giorni**.

Facoltativamente, i messaggi scaduti possono essere spostati in una coda di messaggi non [recapitabili](service-bus-dead-letter-queues.md). È possibile configurare questa impostazione a livello di codice o usando il portale di Azure. Se l'opzione viene lasciata disabilitata, i messaggi scaduti vengono eliminati. I messaggi scaduti spostati nella coda dei messaggi non recapitabili possono essere distinti da altri messaggi non recapitabili valutando la proprietà relativa al motivo dei messaggi non [recapitabili](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) archiviata dal broker nella sezione Proprietà utente. 

Nel caso menzionato in precedenza in cui il messaggio è protetto da scadenza fino a quando è bloccato e se il flag è impostato sull'entità, il messaggio viene spostato nella coda di messaggi non recapitabili non appena il blocco viene abbandonato o scade. Tuttavia, non viene spostato se il messaggio viene risolto correttamente, il che presuppone che l'applicazione sia stata gestita correttamente, a prescindere dalla scadenza nominale.

La combinazione di durata (TTL) e automatica (e transazionale) di messaggi non recapitabili alla scadenza è uno strumento utile per stabilire se un processo assegnato a un gestore o a un gruppo di gestori in una scadenza viene recuperato per l'elaborazione quando viene raggiunta la scadenza.

Si consideri, ad esempio, un sito Web che deve eseguire in modo affidabile i processi in un back-end con vincoli di scalabilità e che in alcuni casi è soggetto a picchi di traffico o richiede un isolamento rispetto a episodi di disponibilità di tale back-end. In una situazione normale, il gestore sul lato server per i dati utente inviati esegue il push delle informazioni in una coda e successivamente riceve una risposta che conferma la corretta gestione della transazione in una coda di risposta. Se si verifica un picco di traffico e il gestore back-end non è in grado di elaborare gli elementi del backlog nel tempo, i processi scaduti vengono restituiti nella coda dei messaggi non recapitabili. L'utente interattivo può ricevere una notifica che indica che l'operazione richiesta impiegherà un po' più tempo del solito e la richiesta può quindi essere inserita in un'altra coda per un percorso di elaborazione in cui il risultato dell'elaborazione finale viene inviato all'utente tramite posta elettronica. 


## <a name="temporary-entities"></a>Entità temporanee

È possibile creare code, argomenti e sottoscrizioni del bus di servizio come entità temporanee, che vengono automaticamente rimosse quando non sono state usate per un periodo di tempo specificato.
 
La pulizia automatica è utile negli scenari di sviluppo e test in cui le entità vengono create in modo dinamico e non vengono eliminate dopo l'uso, a causa di un'interruzione dell'esecuzione del test o del debug. È utile anche quando un'applicazione crea entità dinamiche, ad esempio una coda di risposta, per la ricezione di risposte in un processo del server Web o in un altro oggetto di durata relativamente breve, in cui è difficile eseguire la pulizia delle entità in modo affidabile quando l'istanza dell'oggetto non è più presente.

La funzionalità viene abilitata utilizzando la proprietà **auto delete on Idle** nello spazio dei nomi. Questa proprietà deve essere impostata sulla durata per cui l'entità deve rimanere inattiva (non usata) prima di essere eliminata automaticamente. Il valore minimo per questa proprietà è 5 minuti.
 
## <a name="idleness"></a>Inattività

Ecco cosa identifica le entità (code, argomenti e sottoscrizioni) come inattive:

- Code
    - Nessun invio  
    - Nessuna ricezione  
    - Nessun aggiornamento alla coda  
    - Nessun messaggio pianificato  
    - Nessuna esplorazione/visualizzazione 
- Argomenti  
    - Nessun invio  
    - Nessun aggiornamento all'argomento  
    - Nessun messaggio pianificato 
- Sottoscrizioni
    - Nessuna ricezione  
    - Nessun aggiornamento alla sottoscrizione  
    - Nessuna nuova regola aggiunta alla sottoscrizione  
    - Nessuna esplorazione/visualizzazione  
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
