---
title: Rilevamento di messaggi duplicati nel bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come rilevare i duplicati nei bus di servizio di Azure messaggi. Il messaggio duplicato può essere ignorato ed eliminato.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: a9ca9de988f5a3db15da773a870e2d929ab938c8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499479"
---
# <a name="duplicate-detection"></a>Rilevamento duplicati

Se un'applicazione si arresta a causa di un errore irreversibile subito dopo l'invio di un messaggio e l'istanza riavviata dell'applicazione ritiene erroneamente che il recapito del messaggio precedente non sia stato eseguito, il successivo invio provoca la doppia visualizzazione del messaggio nel sistema.

È anche possibile che un errore a livello di client o di rete si verifichi un momento prima e che il commit di un messaggio inviato nella coda non sia stato restituito correttamente al client. Questo scenario lascia il client in dubbio sull'esito dell'operazione di invio.

Il rilevamento dei duplicati consente di evitare queste situazioni, consentendo al mittente di inviare nuovamente lo stesso messaggio che, se duplicato, verrà rimosso automaticamente dalla coda o dall'argomento.

> [!NOTE]
> Il livello di base del bus di servizio non supporta il rilevamento duplicati. I livelli Standard e Premium supportano il rilevamento dei duplicati. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Funzionamento 
Abilitare il rilevamento dei duplicati consente di tenere traccia del *MessageId* controllato dall'applicazione di tutti i messaggi inviati a una coda o un argomento durante un intervallo di tempo specificato. Se viene inviato un nuovo messaggio con *MessageId* già registrato durante l'intervallo di tempo specificato, il messaggio viene segnalato come accettato (l'operazione di invio ha esito positivo), ma viene immediatamente ignorato ed eliminato. Viene presa in considerazione esclusivamente la parte *MessageId* del messaggio.

Il controllo dell'identificatore da parte dell'applicazione è essenziale, perché è solo questo che consente all'applicazione di associare il *MessageId* al contesto di un processo di business da cui possa essere ricostruito in modo prevedibile in caso di errore.

Per un processo di business in cui vengono inviati più messaggi durante la gestione di un contesto applicazione, il *MessageId* può essere composto dall'identificatore di contesto a livello di applicazione, ad esempio un numero d'ordine di acquisto, e dall'oggetto del messaggio, ad esempio **12345.2017/pagamento**.

*MessageId* può sempre essere un GUID, ma l'ancoraggio dell'identificatore al processo aziendale produce una ripetibilità prevedibile, che si desidera usare in modo efficace la funzionalità di rilevamento duplicati.

> [!IMPORTANT]
>- Quando **il partizionamento** è **abilitato,** `MessageId+PartitionKey` viene usato per determinare l'univocità. Quando le sessioni sono abilitate, la chiave di partizione e l'ID sessione devono essere uguali. 
>- Quando **il partizionamento** è **disabilitato** (impostazione predefinita), viene usato solo `MessageId` per determinare l'univocità.
>- Per informazioni su SessionId, PartitionKey e MessageId, vedere [Uso delle chiavi di partizione](service-bus-partitioning.md#use-of-partition-keys).
>- Il [livello Premier](service-bus-premium-messaging.md) non supporta il partizionamento, quindi è consigliabile usare ID messaggio univoci nelle applicazioni e non basarsi sulle chiavi di partizione per il rilevamento duplicati. 


## <a name="enable-duplicate-detection"></a>Abilitare il rilevamento dei duplicati

Oltre a abilitare solo il rilevamento duplicati, è anche possibile configurare le dimensioni dell'intervallo di tempo della cronologia di rilevamento duplicati durante il quale vengono conservati gli ID messaggio.
Il valore predefinito è 10 minuti per code e argomenti, con un valore minimo di 20 secondi al valore massimo di 7 giorni.

L'abilitazione del rilevamento dei duplicati e le dimensioni della finestra temporale hanno un impatto diretto sulla velocità effettiva di code e argomenti, dal momento che tutti gli ID messaggio registrati devono essere confrontati con l'identificatore dei nuovi messaggi inviati.

Mantenendo una finestra temporale di dimensioni ridotte, occorre conservare e confrontare un numero inferiore di ID messaggio, con un minore impatto sulla velocità effettiva. Per le entità con velocità effettiva elevata che richiedono il rilevamento dei duplicati, è consigliabile ridurre la finestra al minimo.

### <a name="using-the-portal"></a>Tramite il portale

Nel portale la funzionalità di rilevamento duplicati viene  attivata durante la creazione dell'entità con la casella di controllo Abilita rilevamento duplicati, che è disattivata per impostazione predefinita. L'impostazione per la creazione di nuovi argomenti è equivalente.

![Screenshot della finestra di dialogo Crea coda con l'opzione Abilita rilevamento duplicati selezionata e delineata in rosso.][1]

> [!IMPORTANT]
> È possibile abilitare o disabilitare il rilevamento dei duplicati dopo la creazione della coda. È possibile farlo solo al momento della creazione della coda. 

L'intervallo di tempo della cronologia di rilevamento duplicati può essere modificato nella finestra delle proprietà della coda e dell'argomento nel portale di Azure.

![Screenshot della funzionalità del bus di servizio con l'impostazione Proprietà evidenziata e l'opzione Cronologia rilevamento duplicati evidenziata in rosso.][2]

### <a name="using-sdks"></a>Uso di SDK

È possibile usare qualsiasi SDK per .NET, Java, JavaScript, Python e Go per abilitare la funzionalità di rilevamento duplicati durante la creazione di code e argomenti. È anche possibile modificare l'intervallo di tempo della cronologia di rilevamento duplicati.
Di seguito sono elencate le proprietà da aggiornare durante la creazione di code e argomenti:
- `RequiresDuplicateDetection`
- `DuplicateDetectionHistoryTimeWindow`

Si noti che, anche se i nomi delle proprietà vengono forniti in maiuscole e minuscole in pascal, gli SDK JavaScript e Python usano rispettivamente l'uso di maiuscole e minuscole camel e di maiuscole e minuscole.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Negli scenari in cui il codice client non è in grado di inviare nuovamente un messaggio con lo stesso *MessageId* di prima, è importante progettare messaggi che possono essere rielaborati in modo sicuro. Questo [post di blog sull'idempotence](https://particular.net/blog/what-does-idempotent-mean) descrive varie tecniche per eseguire questa operazione.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
