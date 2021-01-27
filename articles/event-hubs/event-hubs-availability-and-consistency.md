---
title: Disponibilità e coerenza - Hub eventi di Azure | Microsoft Docs
description: Come fornire la quantità massima di disponibilità e coerenza con l'Hub eventi di Azure usando le partizioni.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882196"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilità e coerenza nell'Hub eventi
Questo articolo fornisce informazioni su disponibilità e coerenza supportate da Hub eventi di Azure. 

## <a name="availability"></a>Disponibilità
Hub eventi di Azure distribuisce il rischio di errori irreversibili dei singoli computer o anche di completare i rack tra i cluster che si estendono su più domini di errore all'interno di un Data Center. Implementa meccanismi di rilevamento e failover trasparenti, in modo che il servizio continuerà a funzionare entro i livelli di servizio assicurati e in genere senza interruzioni evidenti quando si verificano tali errori. Se è stato creato uno spazio dei nomi di hub eventi con l'opzione Enabled per le [zone di disponibilità](../availability-zones/az-overview.md), il rischio di interruzione viene ulteriormente distribuito in tre strutture fisicamente separate e il servizio dispone di un numero sufficiente di riserve di capacità per far fronte immediatamente alla perdita irreversibile completa dell'intera struttura. Per altre informazioni, vedere [Hub eventi di Azure-ripristino di emergenza geografico](event-hubs-geo-dr.md).

Quando un'applicazione client invia eventi a un hub eventi, gli eventi vengono distribuiti automaticamente tra le partizioni nell'hub eventi. Se per qualche motivo non è disponibile una partizione, gli eventi vengono distribuiti tra le partizioni rimanenti. Questo comportamento consente la maggiore quantità di tempo di attività. Per i casi d'uso che richiedono il tempo massimo, questo modello è preferibile anziché inviare eventi a una partizione specifica. Per altre informazioni, vedere [Partizioni](event-hubs-scalability.md#partitions).

## <a name="consistency"></a>Consistenza
In alcuni scenari, l'ordinamento degli eventi può essere importante. È ad esempio, potrebbe essere necessario che il sistema back-end elabori un comando di aggiornamento prima di un comando di eliminazione. In questo scenario, un'applicazione client invia eventi a una partizione specifica in modo che l'ordinamento venga mantenuto. Quando un'applicazione consumer utilizza questi eventi dalla partizione, questi vengono letti nell'ordine. 

Con questa configurazione, tenere presente che se la partizione specifica alla quale si esegue l'invio non è disponibile, si riceverà una risposta di errore. Come punto di confronto, se non si ha un'affinità a una singola partizione, il servizio Hub eventi invia l'evento alla partizione successiva disponibile.

Una possibile soluzione per garantire l'ordinamento ottimizzando allo stesso tempo i tempi di attività sarebbe l'aggregazione di eventi come parte dell'applicazione di elaborazione di eventi. Il modo più semplice per eseguire questa operazione è contrassegnare l'evento con una proprietà del numero di sequenza personalizzato.

In questo scenario, il client producer invia eventi a una delle partizioni disponibili nell'hub eventi e imposta il numero di sequenza corrispondente dall'applicazione. Questa soluzione richiede che l'applicazione di elaborazione mantenga lo stato, ma propone ai mittenti un endpoint che ha maggiori probabilità di essere disponibile.

## <a name="appendix"></a>Appendice

### <a name="net-examples"></a>Esempi .NET

#### <a name="send-events-to-a-specific-partition"></a>Inviare eventi a una partizione specifica
Impostare la chiave di partizione su un evento oppure usare un `PartitionSender` oggetto (se si usa la libreria Microsoft. Azure. Messaging precedente) per inviare solo gli eventi a una determinata partizione. In tal modo, quando questi eventi vengono letti dalla partizione, vengono letti nell'ordine. 

Se si usa la libreria **Azure. Messaging. EventHubs** più recente, vedere [migrazione del codice da PartitionSender a EventHubProducerClient per la pubblicazione di eventi in una partizione](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 o versione successiva)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 o versioni precedenti)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>Imposta un numero di sequenza
Nell'esempio seguente viene timbrato l'evento con una proprietà con numero di sequenza personalizzato. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 o versione successiva)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 o versioni precedenti)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

In questo esempio l'evento viene inviato a una delle partizioni disponibili nell'hub eventi e il numero di sequenza corrispondente viene impostato dall'applicazione. Questa soluzione richiede che l'applicazione di elaborazione mantenga lo stato, ma propone ai mittenti un endpoint che ha maggiori probabilità di essere disponibile.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica del servizio Hub eventi](./event-hubs-about.md)
* [Creare un hub eventi](event-hubs-create.md)
