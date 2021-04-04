---
title: 'Griglia di eventi di Azure: log di diagnostica per argomenti o domini'
description: Questo articolo fornisce informazioni concettuali sui log di diagnostica per un argomento di griglia di eventi di Azure o un dominio.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 36ade14932b5d25c7a1fe05632da671de68ba3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574987"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Log di diagnostica per argomenti/domini di griglia di eventi di Azure
Le impostazioni di diagnostica consentono agli utenti di griglia di eventi di acquisire e visualizzare i log degli **errori di pubblicazione e recapito** in un account di archiviazione, un hub eventi o un'area di lavoro log Analytics. Questo articolo fornisce uno schema per i log e una voce di log di esempio.


## <a name="schema-for-publishdelivery-failure-logs"></a>Schema per i log degli errori di pubblicazione/recapito

| Nome proprietà | Tipo di dati | Descrizione |
| ------------- | --------- | ----------- | 
| Tempo | Datetime | Ora di generazione della voce di log <p>**Valore di esempio:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | string | Nome della sottoscrizione di eventi <p>**Valore di esempio:** "EVENTSUB1"</p> <p>Questa proprietà esiste solo per i log degli errori di recapito.</p>  |
| Category | string | Nome della categoria di log. <p>**Valori di esempio:** "DeliveryFailures" o "PublishFailures" | 
| OperationName | string | Il nome dell'operazione ha causato l'errore.<p>**Valori di esempio:** "Recapita" per gli errori di recapito. |
| Message | string | Messaggio di log per l'utente che descrive il motivo dell'errore e altri dettagli aggiuntivi. |
| ResourceId | string | ID risorsa per la risorsa argomento/dominio<p>**Valori di esempio:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Esempio

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come abilitare i log di diagnostica per argomenti o domini, vedere [abilitare i log di diagnostica](enable-diagnostic-logs-topic.md).
