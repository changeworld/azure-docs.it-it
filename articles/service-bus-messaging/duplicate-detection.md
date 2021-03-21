---
title: Rilevamento di messaggi duplicati nel bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come è possibile rilevare i duplicati nei messaggi del bus di servizio di Azure. Il messaggio duplicato può essere ignorato e eliminato.
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 527c2dea34b02733907372b6e75a40a5ef5fc289
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711926"
---
# <a name="duplicate-detection"></a>Rilevamento duplicati

Se un'applicazione si arresta a causa di un errore irreversibile subito dopo l'invio di un messaggio e l'istanza riavviata dell'applicazione ritiene erroneamente che il recapito del messaggio precedente non sia stato eseguito, il successivo invio provoca la doppia visualizzazione del messaggio nel sistema.

È anche possibile che si verifichi un errore a livello di client o di rete in un momento precedente e che venga eseguito il commit di un messaggio inviato nella coda, con il riconoscimento non correttamente restituito al client. Questo scenario lascia il client in dubbio sull'esito dell'operazione di invio.

Il rilevamento dei duplicati consente di evitare queste situazioni, consentendo al mittente di inviare nuovamente lo stesso messaggio che, se duplicato, verrà rimosso automaticamente dalla coda o dall'argomento.

> [!NOTE]
> Il livello Basic del bus di servizio non supporta il rilevamento dei duplicati. I livelli standard e Premium supportano il rilevamento dei duplicati. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Funzionamento 
Abilitare il rilevamento dei duplicati consente di tenere traccia del *MessageId* controllato dall'applicazione di tutti i messaggi inviati a una coda o un argomento durante un intervallo di tempo specificato. Se viene inviato un nuovo messaggio con *MessageId* già registrato durante l'intervallo di tempo specificato, il messaggio viene segnalato come accettato (l'operazione di invio ha esito positivo), ma viene immediatamente ignorato ed eliminato. Viene presa in considerazione esclusivamente la parte *MessageId* del messaggio.

Il controllo dell'identificatore da parte dell'applicazione è essenziale, perché è solo questo che consente all'applicazione di associare il *MessageId* al contesto di un processo di business da cui possa essere ricostruito in modo prevedibile in caso di errore.

Per un processo di business in cui vengono inviati più messaggi durante la gestione di un contesto applicazione, il *MessageId* può essere composto dall'identificatore di contesto a livello di applicazione, ad esempio un numero d'ordine di acquisto, e dall'oggetto del messaggio, ad esempio **12345.2017/pagamento**.

Il *MessageID* può sempre essere un GUID, ma l'ancoraggio dell'identificatore al processo di business produce una ripetibilità prevedibile, che è necessario per l'utilizzo efficace della funzionalità di rilevamento dei duplicati.

> [!IMPORTANT]
>- Quando il **partizionamento** è **abilitato**, `MessageId+PartitionKey` viene usato per determinare l'univocità. Quando le sessioni sono abilitate, la chiave di partizione e l'ID di sessione devono essere uguali. 
>- Quando il **partizionamento** è **disabilitato** (impostazione predefinita), `MessageId` viene usato solo per determinare l'univocità.
>- Per informazioni su SessionId, PartitionKey e MessageId, vedere [uso delle chiavi di partizione](service-bus-partitioning.md#use-of-partition-keys).
>- Il [livello Premier](service-bus-premium-messaging.md) non supporta il partizionamento, quindi è consigliabile usare ID di messaggio univoci nelle applicazioni e non basarsi sulle chiavi di partizione per il rilevamento dei duplicati. 


## <a name="enable-duplicate-detection"></a>Abilitare il rilevamento dei duplicati

Nel portale la funzionalità si attiva durante la creazione di entità tramite la casella di controllo **Abilita rilevamento duplicati**, che per impostazione predefinita è deselezionata. L'impostazione per la creazione di nuovi argomenti è equivalente.

![Screenshot della finestra di dialogo Crea coda con l'opzione Abilita rilevamento duplicati selezionata e delineata in rosso.][1]

> [!IMPORTANT]
> È possibile abilitare o disabilitare il rilevamento dei duplicati dopo la creazione della coda. È possibile farlo solo al momento della creazione della coda. 

A livello di codice, si imposta il flag con la proprietà [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) sull'intera API .NET Framework. Con l'API di Azure Resource Manager, il valore viene impostato con la proprietà [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

Per le code e gli argomenti per la cronologia del tempo di rilevamento duplicato, il valore predefinito è 10 minuti, con un valore minimo di 20 secondi per il valore massimo di 7 giorni. È possibile modificare questa impostazione nella finestra delle proprietà di code e argomenti nel portale di Azure.

![Screenshot della funzionalità del bus di servizio con l'impostazione delle proprietà evidenziata e l'opzione per la cronologia dei duplicati di rilevamento descritta in rosso.][2]

A livello di codice, è possibile configurare le dimensioni della finestra temporale di rilevamento dei duplicati durante la quale vengono conservati gli ID di messaggio, usando la proprietà [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) con l'intera API .NET Framework. Con l'API di Azure Resource Manager, il valore viene impostato con la proprietà [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

L'abilitazione del rilevamento dei duplicati e le dimensioni della finestra temporale hanno un impatto diretto sulla velocità effettiva di code e argomenti, dal momento che tutti gli ID messaggio registrati devono essere confrontati con l'identificatore dei nuovi messaggi inviati.

Mantenendo una finestra temporale di dimensioni ridotte, occorre conservare e confrontare un numero inferiore di ID messaggio, con un minore impatto sulla velocità effettiva. Per le entità con velocità effettiva elevata che richiedono il rilevamento dei duplicati, è consigliabile ridurre la finestra al minimo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Negli scenari in cui il codice client non è in grado di inviare nuovamente un messaggio con lo stesso *MessageID* di prima, è importante progettare messaggi che possono essere rielaborati in modo sicuro. Questo [post di Blog su l'idempotenza](https://particular.net/blog/what-does-idempotent-mean) descrive le varie tecniche per eseguire questa operazione.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
