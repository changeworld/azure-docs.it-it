---
title: Code dei messaggi non recapitabili del bus di servizio | Documentazione Microsoft
description: Descrive le code dei messaggi non recapitabili nel bus di servizio di Azure. Le code del bus di servizio e le sottoscrizioni dell'argomento includono una coda secondaria chiamata coda di messaggi non recapitabili.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812373"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Panoramica delle code dei messaggi non recapitabili del bus di servizio

Le code del bus di servizio di Azure e le sottoscrizioni dell'argomento includono una coda secondaria chiamata *coda di messaggi non recapitabili* (DLQ, Dead-Letter Queue). Non è necessario creare in modo esplicito la coda dei messaggi non recapitare e non può essere eliminata o gestita indipendentemente dall'entità principale.

Questo articolo descrive le code dei messaggi non recapitabili nel bus di servizio. Questo argomento viene in gran parte illustrato nell'[esempio relativo alle code di messaggi non recapitabili](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) su GitHub.
 
## <a name="the-dead-letter-queue"></a>Coda di messaggi non recapitabili

Lo scopo della coda dei messaggi non recapitabili è conservare i messaggi che non possono essere recapitati ai ricevitori o che non possono essere elaborati. I messaggi possono essere rimossi dalla coda e verificati. Con l'aiuto di un operatore, un'applicazione potrebbe correggere i problemi e inviare nuovamente il messaggio, registrare la notizia che si è verificato un errore e intraprendere azioni correttive. 

Dal punto di vista di API e protocolli, la coda DLQ è molto simile a qualsiasi altra coda, ad eccezione del fatto che i messaggi possono essere inviati ad essa solo tramite l'operazione messaggi non recapitabili dell'entità padre. Inoltre, il parametro time-to-live non viene rispettato e non è possibile impostare come non recapitabile un messaggio di una coda DLQ. La coda dei messaggi non recapitabili supporta completamente il recapito con blocco di visualizzazione e le operazioni transazionali.

Non è prevista alcuna pulizia automatica della coda. I messaggi rimangono nella DLQ fino a quando non vengono recuperati in modo esplicito dalla DLQ e non viene completato il messaggio non recapitato.


## <a name="dlq-message-count"></a>Numero di messaggi in coda DQL
Non è possibile ottenere il numero di messaggi nella coda dei messaggi non recapitabili a livello di argomento. Ciò è dovuto al fatto che i messaggi non si trovano a livello di argomento a meno che il bus di servizio non generi un errore interno. Al contrario, quando un mittente invia un messaggio a un argomento, il messaggio viene inoltrato alle sottoscrizioni per l'argomento nell'arco di millisecondi e quindi non risiede più a livello di argomento. È quindi possibile visualizzare i messaggi in coda DQL associati alla sottoscrizione per l'argomento. Nell'esempio seguente **Service Bus Explorer** indica che sono presenti 62 messaggi in coda DQL per la sottoscrizione "test1". 

![Numero di messaggi in coda DQL](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

È anche possibile ottenere il numero di messaggi in coda DQL usando il comando dell'interfaccia della riga di comando di Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show). 

## <a name="moving-messages-to-the-dlq"></a>Spostare messaggi nella coda DLQ
Nel bus di servizio sono presenti diverse attività che comportano l'inserimento di messaggi nella coda DLQ dall'interno del motore di messaggistica stesso. Un'applicazione può anche spostare in modo esplicito i messaggi nella coda dei messaggi non recapitabili. Le due proprietà seguenti (motivo dei messaggi non recapitati e descrizione dei messaggi non recapitati) vengono aggiunte ai messaggi non recapitati. Le applicazioni possono definire i propri codici per la proprietà del motivo del messaggio non recapitato, ma il sistema imposta i valori seguenti.

| Motivo della lettera non recapitata | Descrizione dell'errore non recapitato |
| --- | --- |
|HeaderSizeExceeded |È stata superata la dimensione del flusso. |
|TTLExpiredException |Il messaggio è scaduto ed è stato configurato come non recapitabile. Per informazioni [dettagliate, vedere la](#time-to-live) sezione Time to live (Tempo di attività). |
|L'ID sessione ha valore null. |L'entità attivata dalla sessione non consente il recapito di un messaggio il cui identificatore di sessione è null. |
|MaxTransferHopCountExceeded | Numero massimo di hop consentiti durante l'inoltro tra le code. Il valore è impostato su 4. |
| MaxDeliveryCountExceededExceptionMessage | Impossibile utilizzare il messaggio dopo il numero massimo di tentativi di recapito. Per informazioni [dettagliate, vedere](#maximum-delivery-count) la sezione Numero massimo di recapito. |

## <a name="maximum-delivery-count"></a>Conteggio distribuzione massimo
È previsto un limite al numero di tentativi di recapitare messaggi per le code e le sottoscrizioni del bus di servizio. Il valore predefinito è 10. Ogni volta che un messaggio è stato recapitato in un blocco di visualizzazione, ma è stato abbandonato in modo esplicito o il blocco è scaduto, il numero di recapito per il messaggio viene incrementato. Quando il numero di recapito supera il limite, il messaggio viene spostato nella DLQ. Il motivo del messaggio non recapitabile in DLQ è impostato su: MaxDeliveryCountExceeded. Questo comportamento non può essere disabilitato, ma è possibile impostare il numero massimo di recapito su un numero elevato.

## <a name="time-to-live"></a>Durata (TTL)
Quando si abilita l'invio di messaggi non recapitbili in code o sottoscrizioni, tutti i messaggi in scadenza vengono spostati nella DLQ. Il codice motivo del messaggio non recapitabile è impostato su TTLExpiredException.

I messaggi scaduti vengono eliminati e spostati nella DLQ solo quando è presente almeno un ricevitore attivo che esegue il pull dalla coda o dalla sottoscrizione principale. Anche i messaggi posticipati non verranno eliminati e spostati nella coda dei messaggi non recapitati dopo la scadenza. Questo comportamento dipende dalla progettazione.

## <a name="errors-while-processing-subscription-rules"></a>Errori durante l'elaborazione di regole di sottoscrizione
Se si abilita l'invio di messaggi non recapitati nelle eccezioni di valutazione del filtro, tutti gli errori che si verificano durante l'esecuzione della regola di filtro SQL di una sottoscrizione vengono acquisiti nella DLQ insieme al messaggio che causa l'errore. Non usare questa opzione in un ambiente di produzione in cui non tutti i tipi di messaggio hanno sottoscrittori.

## <a name="application-level-dead-lettering"></a>Definizione di messaggi non recapitabili a livello di applicazione
Oltre alle funzionalità di definizione dei messaggi non recapitabili del sistema, le applicazioni possono usare la coda DLQ per rifiutare esplicitamente i messaggi inaccettabili. Questi possono riguardare i messaggi che non possono essere elaborati correttamente a causa diversi problemi del sistema, i messaggi contenenti payload in formato non valido o che non superino il processo di autenticazione quando viene utilizzato un schema di sicurezza a livello di messaggio.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Messaggi non recapitabili negli scenari ForwardTo o SendVia
I messaggi verranno inviati nella coda dei messaggi non recapitabili di trasferimento nelle condizioni seguenti:

- Un messaggio passa attraverso più di quattro code o argomenti che sono [concatenati](service-bus-auto-forwarding.md).
- L'argomento o la coda di destinazione è disattivato o eliminato.
- L'argomento o la coda di destinazione supera le dimensioni massime dell'entità.

## <a name="path-to-the-dead-letter-queue"></a>Percorso della coda di messaggi non recapitabili
È possibile accedere alla coda dei messaggi non recapitabili utilizzando la sintassi seguente:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Passaggi successivi
Vedere [Abilitare i messaggi non recapitbili per una coda](enable-dead-letter.md) o una sottoscrizione per informazioni sulle diverse modalità di configurazione dei messaggi non recapitbili alla scadenza **del** messaggio.
