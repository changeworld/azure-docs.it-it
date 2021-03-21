---
title: Disponibilità e coerenza - Hub eventi di Azure | Microsoft Docs
description: Come fornire la quantità massima di disponibilità e coerenza con l'Hub eventi di Azure usando le partizioni.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd446cf86c22b851bae9cb9d8535a8e5234e08b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722532"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilità e coerenza nell'Hub eventi
Questo articolo fornisce informazioni su disponibilità e coerenza supportate da Hub eventi di Azure. 

## <a name="availability"></a>Disponibilità
Hub eventi di Azure distribuisce il rischio di errori irreversibili dei singoli computer o anche di completare i rack tra i cluster che si estendono su più domini di errore all'interno di un Data Center. Implementa meccanismi di rilevamento e failover trasparenti, in modo che il servizio continuerà a funzionare entro i livelli di servizio assicurati e in genere senza interruzioni evidenti quando si verificano tali errori. 

Se è stato creato uno spazio dei nomi di hub eventi con le [zone di disponibilità](../availability-zones/az-overview.md) abilitate, il rischio di interruzione viene ulteriormente distribuito in tre strutture fisicamente separate e il servizio dispone di sufficienti riserve di capacità per affrontare immediatamente la perdita irreversibile completa dell'intera struttura. Per altre informazioni, vedere [Hub eventi di Azure-ripristino di emergenza geografico](event-hubs-geo-dr.md).

Quando un'applicazione client invia eventi a un hub eventi senza specificare una partizione, gli eventi vengono distribuiti automaticamente tra le partizioni nell'hub eventi. Se per qualche motivo non è disponibile una partizione, gli eventi vengono distribuiti tra le partizioni rimanenti. Questo comportamento consente la maggiore quantità di tempo di attività. Per i casi d'uso che richiedono il tempo massimo, questo modello è preferibile anziché inviare eventi a una partizione specifica. 

## <a name="consistency"></a>Consistenza
In alcuni scenari, l'ordinamento degli eventi può essere importante. È ad esempio, potrebbe essere necessario che il sistema back-end elabori un comando di aggiornamento prima di un comando di eliminazione. In questo scenario, un'applicazione client invia eventi a una partizione specifica in modo che l'ordinamento venga mantenuto. Quando un'applicazione consumer utilizza questi eventi dalla partizione, questi vengono letti nell'ordine. 

Con questa configurazione, tenere presente che se la partizione specifica alla quale si esegue l'invio non è disponibile, si riceverà una risposta di errore. Come punto di confronto, se non si ha un'affinità a una singola partizione, il servizio Hub eventi invia l'evento alla partizione successiva disponibile.

Di conseguenza, se la disponibilità elevata è la più importante, non destinare una partizione specifica (usando l'ID partizione/chiave). L'uso della chiave/ID di partizione esegue il downgrade della disponibilità di un hub eventi a livello di partizione. In questo scenario si sta creando una scelta esplicita tra disponibilità (nessun ID/chiave di partizione) e coerenza (che blocca gli eventi a una partizione specifica). Per informazioni dettagliate sulle partizioni in hub eventi, vedere [partizioni](event-hubs-features.md#partitions).

## <a name="appendix"></a>Appendice

### <a name="send-events-without-specifying-a-partition"></a>Inviare eventi senza specificare una partizione
Si consiglia di inviare eventi a un hub eventi senza impostare le informazioni sulla partizione per consentire al servizio Hub eventi di bilanciare il carico tra le partizioni. Per informazioni su come eseguire questa operazione in diversi linguaggi di programmazione, vedere le guide introduttive seguenti. 

- [Inviare eventi con .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Inviare eventi con Java](event-hubs-java-get-started-send.md)
- [Inviare eventi tramite JavaScript](event-hubs-python-get-started-send.md)
- [Inviare eventi con Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Inviare eventi a una partizione specifica
In questa sezione si apprenderà come inviare eventi a una partizione specifica usando linguaggi di programmazione diversi. 

### <a name="net"></a>[.NET](#tab/dotnet)
Per inviare eventi a una partizione specifica, creare il batch usando il metodo [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) specificando `PartitionId` o `PartitionKey` in [CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions?view=azure-dotnet). Il codice seguente invia un batch di eventi a una partizione specifica specificando una chiave di partizione. Hub eventi garantisce che tutti gli eventi che condividono un valore di chiave di partizione vengano archiviati insieme e recapitati in ordine di arrivo.

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
Per inviare eventi a una partizione specifica, quando si crea un batch usando il [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) metodo, specificare `partition_id` o `partition_key` . Usare quindi il [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) metodo per inviare il batch alla partizione dell'hub eventi. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

È anche possibile usare il metodo [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) specificando i valori per i `partition_id` `partition_key` parametri o.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
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

- [Panoramica del servizio Hub eventi](./event-hubs-about.md)
- [Terminologia relativa a hub eventi](event-hubs-features.md)
