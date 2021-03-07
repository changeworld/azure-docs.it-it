---
title: Disponibilità e coerenza - Hub eventi di Azure | Microsoft Docs
description: Come fornire la quantità massima di disponibilità e coerenza con l'Hub eventi di Azure usando le partizioni.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425924"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilità e coerenza nell'Hub eventi
Questo articolo fornisce informazioni su disponibilità e coerenza supportate da Hub eventi di Azure. 

## <a name="availability"></a>Disponibilità
Hub eventi di Azure distribuisce il rischio di errori irreversibili dei singoli computer o anche di completare i rack tra i cluster che si estendono su più domini di errore all'interno di un Data Center. Implementa meccanismi di rilevamento e failover trasparenti, in modo che il servizio continuerà a funzionare entro i livelli di servizio assicurati e in genere senza interruzioni evidenti quando si verificano tali errori. 

Se è stato creato uno spazio dei nomi di hub eventi con le [zone di disponibilità](../availability-zones/az-overview.md) abilitate, il rischio di interruzione viene ulteriormente distribuito in tre strutture fisicamente separate e il servizio dispone di sufficienti riserve di capacità per affrontare immediatamente la perdita irreversibile completa dell'intera struttura. Per altre informazioni, vedere [Hub eventi di Azure-ripristino di emergenza geografico](event-hubs-geo-dr.md).

Quando un'applicazione client invia eventi a un hub eventi senza specificare una partizione, gli eventi vengono distribuiti automaticamente tra le partizioni nell'hub eventi. Se per qualche motivo non è disponibile una partizione, gli eventi vengono distribuiti tra le partizioni rimanenti. Questo comportamento consente la maggiore quantità di tempo di attività. Per i casi d'uso che richiedono il tempo massimo, questo modello è preferibile anziché inviare eventi a una partizione specifica. 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>Considerazioni sulla disponibilità quando si usa un ID o una chiave di partizione
L'uso di un ID partizione o di una chiave di partizione è facoltativo. Valutare attentamente se utilizzarne uno. Se non si specifica un ID/chiave di partizione durante la pubblicazione di un evento, Hub eventi bilancia il carico tra le partizioni. Quando si usa un ID/chiave di partizione, queste partizioni richiedono la disponibilità in un singolo nodo e le interruzioni possono verificarsi nel tempo. Ad esempio, potrebbe essere necessario riavviare o correggere i nodi di calcolo. Se quindi si imposta un ID/chiave di partizione e la partizione non è più disponibile per qualche motivo, un tentativo di accedere ai dati nella partizione avrà esito negativo. Se la disponibilità elevata è la più importante, non specificare un ID/chiave di partizione. In tal caso, gli eventi vengono inviati alle partizioni usando un algoritmo di bilanciamento del carico interno. In questo scenario si sta creando una scelta esplicita tra disponibilità (nessun ID/chiave di partizione) e coerenza (che blocca gli eventi a una partizione specifica). L'uso della chiave/ID di partizione esegue il downgrade della disponibilità di un hub eventi a livello di partizione. 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>Considerazioni sulla disponibilità durante la gestione dei ritardi nell'elaborazione degli eventi
Un'altra considerazione riguarda i ritardi di gestione delle applicazioni consumer nell'elaborazione degli eventi. In alcuni casi potrebbe essere preferibile per l'applicazione consumer eliminare i dati e riprovare anziché tentare di rimanere al passo con l'elaborazione, che può causare ritardi nell'elaborazione downstream. Con i titoli azionari, ad esempio, è consigliabile attendere il completamento dell'aggiornamento dei dati, ma nel caso di una chat in tempo reale o in uno scenario VOIP è preferibile ottenere i dati rapidamente, anche se non sono completi.

Considerate queste considerazioni sulla disponibilità, in questi scenari, l'applicazione consumer può scegliere una delle strategie di gestione degli errori seguenti:

- Arrestare (arrestare la lettura dall'hub eventi fino a quando non vengono corretti i problemi)
- Eliminazione (i messaggi non sono importanti, eliminarli)
- Ripetizione (nuovo tentativo di invio dei messaggi quando possibile)


## <a name="consistency"></a>Consistenza
In alcuni scenari, l'ordinamento degli eventi può essere importante. È ad esempio, potrebbe essere necessario che il sistema back-end elabori un comando di aggiornamento prima di un comando di eliminazione. In questo scenario, un'applicazione client invia eventi a una partizione specifica in modo che l'ordinamento venga mantenuto. Quando un'applicazione consumer utilizza questi eventi dalla partizione, questi vengono letti nell'ordine. 

Con questa configurazione, tenere presente che se la partizione specifica alla quale si esegue l'invio non è disponibile, si riceverà una risposta di errore. Come punto di confronto, se non si ha un'affinità a una singola partizione, il servizio Hub eventi invia l'evento alla partizione successiva disponibile.


## <a name="appendix"></a>Appendice

### <a name="send-events-to-a-specific-partition"></a>Inviare eventi a una partizione specifica
Questa sezione illustra come inviare eventi a una partizione specifica usando C#, Java, Python e JavaScript. 

### <a name="net"></a>[.NET](#tab/dotnet)
Per il codice di esempio completo che illustra come inviare un batch di eventi a un hub eventi (senza impostare l'ID partizione/chiave), vedere [inviare eventi a e ricevere eventi da Hub eventi di Azure-.NET (Azure. Messaging. EventHubs)](event-hubs-dotnet-standard-getstarted-send.md).

Per inviare eventi a una partizione specifica, creare il batch usando il metodo [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) specificando `PartitionId` o `PartitionKey` in [CreateBatchOptions](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). Il codice seguente invia un batch di eventi a una partizione specifica specificando una chiave di partizione. 

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

È anche possibile usare il metodo [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) specificando **PartitionID** o **PartitionKey** in [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```

### <a name="java"></a>[Java](#tab/java)
Per il codice di esempio completo che illustra come inviare un batch di eventi a un hub eventi (senza impostare l'ID partizione/chiave), vedere [usare Java per inviare o ricevere eventi da Hub eventi di Azure (Azure-Messaging-Eventhubs)](event-hubs-java-get-started-send.md).

Per inviare eventi a una partizione specifica, creare il batch usando il metodo [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) specificando l' **ID partizione** o la **chiave di partizione** in [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). Il codice seguente invia un batch di eventi a una partizione specifica specificando una chiave di partizione. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

È anche possibile usare il metodo [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) specificando l' **ID partizione** o la **chiave di partizione** in [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```

### <a name="python"></a>[Python](#tab/python) 
Per il codice di esempio completo che illustra come inviare un batch di eventi a un hub eventi (senza impostare l'ID partizione/chiave), vedere [inviare eventi a o ricevere eventi da Hub eventi tramite Python (Azure-eventhub)](event-hubs-python-get-started-send.md).

Per inviare eventi a una partizione specifica, quando si crea un batch usando il [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) metodo, specificare `partition_id` o `partition_key` . Usare quindi il [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) metodo per inviare il batch alla partizione dell'hub eventi. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

È anche possibile usare il metodo [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) specificando i valori per i `partition_id` `partition_key` parametri o.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
Per il codice di esempio completo che illustra come inviare un batch di eventi a un hub eventi (senza impostare l'ID partizione/chiave), vedere [inviare eventi a o ricevere eventi da Hub eventi usando JavaScript (Azure/Event-Hub)](event-hubs-node-get-started-send.md).

Per inviare eventi a una partizione specifica, [creare un batch](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) usando l'oggetto [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) specificando `partitionId` o `partitionKey` . Inviare quindi il batch all'hub eventi usando il metodo [EventHubProducerClient. SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) . 

Vedere l'esempio seguente.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

È anche possibile usare il metodo [EventHubProducerClient. sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) specificando l' **ID partizione** o la **chiave di partizione** in [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica del servizio Hub eventi](./event-hubs-about.md)
* [Creare un hub eventi](event-hubs-create.md)
