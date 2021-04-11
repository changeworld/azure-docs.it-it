---
title: Aggiungere dati personalizzati agli eventi in hub eventi di Azure
description: Questo articolo illustra come aggiungere dati personalizzati agli eventi in hub eventi di Azure.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893473"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Aggiungere dati personalizzati agli eventi in hub eventi di Azure
Poiché un evento è costituito principalmente da un set opaco di byte, potrebbe essere difficile per gli utenti di tali eventi prendere decisioni informate su come elaborarli. Per consentire agli autori di eventi di offrire un contesto migliore per i consumer, gli eventi possono contenere anche metadati personalizzati, sotto forma di un set di coppie chiave-valore. Uno scenario comune per l'inclusione dei metadati consiste nel fornire un suggerimento sul tipo di dati contenuti in un evento, in modo che i consumer ne possano comprendere il formato e possano deserializzarli in modo appropriato.

> [!NOTE]
> Questi metadati non vengono usati da o in alcun modo significativo per il servizio Hub eventi; esiste solo per il coordinamento tra autori di eventi e consumer. 

Le sezioni seguenti illustrano come aggiungere dati personalizzati agli eventi in linguaggi di programmazione diversi. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Per l'esempio di codice completo, vedere [pubblicazione di eventi con metadati personalizzati](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Per l'esempio di codice completo, vedere [pubblicare eventi con metadati personalizzati](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Per l'esempio di codice completo, vedere [inviare un batch di dati evento con proprietà](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Passaggi successivi
Vedere le guide introduttive e gli esempi seguenti. 

- Guide introduttive: [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- Esempi su GitHub: [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [typescript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
