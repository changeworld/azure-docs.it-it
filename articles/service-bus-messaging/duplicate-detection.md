---
title: Rilevamento di messaggi duplicati nel bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come rilevare i duplicati nei bus di servizio di Azure messaggi. Il messaggio duplicato può essere ignorato ed eliminato.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751275"
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


## <a name="duplicate-detection-window-size"></a>Dimensioni della finestra di rilevamento duplicati

Oltre a abilitare solo il rilevamento duplicati, è anche possibile configurare le dimensioni dell'intervallo di tempo della cronologia di rilevamento duplicati durante il quale vengono conservati gli ID messaggio.
Il valore predefinito è 10 minuti per code e argomenti, con un valore minimo di 20 secondi al valore massimo di 7 giorni.

L'abilitazione del rilevamento dei duplicati e le dimensioni della finestra temporale hanno un impatto diretto sulla velocità effettiva di code e argomenti, dal momento che tutti gli ID messaggio registrati devono essere confrontati con l'identificatore dei nuovi messaggi inviati.

Mantenendo una finestra temporale di dimensioni ridotte, occorre conservare e confrontare un numero inferiore di ID messaggio, con un minore impatto sulla velocità effettiva. Per le entità con velocità effettiva elevata che richiedono il rilevamento dei duplicati, è consigliabile ridurre la finestra al minimo.

## <a name="next-steps"></a>Passaggi successivi
È possibile abilitare il rilevamento dei messaggi duplicati usando portale di Azure, PowerShell, interfaccia della riga di comando, Resource Manager modello, .NET, Java, Python e JavaScript. Per altre informazioni, vedere Abilitare [il rilevamento dei messaggi duplicati.](enable-duplicate-detection.md) 

Negli scenari in cui il codice client non è in grado di inviare nuovamente un messaggio con lo stesso *MessageId* di prima, è importante progettare messaggi che possono essere rielaborati in modo sicuro. Questo [post di blog sull'idempotenza](https://particular.net/blog/what-does-idempotent-mean) descrive varie tecniche per eseguire questa operazione.

Provare gli esempi nel linguaggio preferito per esplorare le bus di servizio di Azure funzionalità. 

- [bus di servizio di Azure di librerie client per Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [bus di servizio di Azure di libreria client per Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [bus di servizio di Azure di libreria client per JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [bus di servizio di Azure di libreria client per TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Esempi di Azure.Messaging.ServiceBus per .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Di seguito sono riportati esempi per le librerie client .NET e Java precedenti:
- [Esempi di Microsoft.Azure.ServiceBus per .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Esempi di azure-servicebus per Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

