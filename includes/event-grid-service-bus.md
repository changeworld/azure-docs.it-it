---
title: includere file
description: includere file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 49e35687820679a1c06cf19e7a26b3b04a1e1318
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363246"
---
## <a name="available-event-types"></a>Tipi di evento disponibili

Il bus di servizio genera i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Generato quando in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Generato quando in una coda di messaggi non recapitabili sono presenti messaggi attivi e non ci sono listener attivi. |
| Microsoft. ServiceBus. ActiveMessagesAvailablePeriodicNotifications | Generato periodicamente se sono presenti messaggi attivi in una coda o una sottoscrizione, anche se sono presenti listener attivi nella coda o nella sottoscrizione specifica. |
| Microsoft. ServiceBus. DeadletterMessagesAvailablePeriodicNotifications | Generato periodicamente se sono presenti messaggi nell'entità dei messaggi non recapitabili di una coda o di una sottoscrizione, anche se sono presenti listener attivi sull'entità dei messaggi non recapitabili della coda o della sottoscrizione specifica. | 

## <a name="example-event"></a>Evento di esempio

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Messaggi attivi disponibili senza listener
Questo evento viene generato se in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Messaggi DeadLetter disponibili senza listener

Lo schema per un evento della coda di messaggi non recapitabili è simile. Si ottiene almeno un evento per ogni coda di messaggi non recapitabili che contiene messaggi e nessun ricevitore attivo.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Notifiche periodiche disponibili per i messaggi attivi
Questo evento viene generato periodicamente se sono presenti messaggi attivi nella coda o nella sottoscrizione specifica, anche se sono presenti listener attivi per la coda o la sottoscrizione specifica.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Notifiche periodiche disponibili per i messaggi DeadLetter
Questo evento viene generato periodicamente se sono presenti messaggi DeadLetter per la coda o la sottoscrizione specifica, anche se sono presenti listener attivi sull'entità DeadLetter della coda o della sottoscrizione specifica.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Messaggi attivi disponibili senza listener
Questo evento viene generato se in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Messaggi DeadLetter disponibili senza listener

Lo schema per un evento della coda di messaggi non recapitabili è simile. Si ottiene almeno un evento per ogni coda di messaggi non recapitabili che contiene messaggi e nessun ricevitore attivo.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Notifiche periodiche disponibili per i messaggi attivi
Questo evento viene generato periodicamente se sono presenti messaggi attivi nella coda o nella sottoscrizione specifica, anche se sono presenti listener attivi per la coda o la sottoscrizione specifica.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Notifiche periodiche disponibili per i messaggi DeadLetter
Questo evento viene generato periodicamente se sono presenti messaggi DeadLetter per la coda o la sottoscrizione specifica, anche se sono presenti listener attivi sull'entità DeadLetter della coda o della sottoscrizione specifica.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>Proprietà degli eventi

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `topic` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `eventType` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `eventTime` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati relativi all'evento di archiviazione BLOB. |
| `dataVersion` | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| `metadataVersion` | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |

# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `source` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `type` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `time` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati relativi all'evento di archiviazione BLOB. |
| `specversion` | string | Versione della specifica dello schema CloudEvents. |

---

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `namespaceName` | string | Spazio dei nomi del bus di servizio in cui esiste la risorsa. |
| `requestUri` | string | URI della coda o della sottoscrizione specifica che crea l'evento. |
| `entityType` | string | Tipo di entità del bus di servizio che crea gli eventi (coda o sottoscrizione). |
| `queueName` | string | Coda con messaggi attivi in caso di sottoscrizione di una coda. Valore null se si usano argomenti/sottoscrizioni. |
| `topicName` | string | Argomento a cui appartiene la sottoscrizione del bus di servizio con messaggi attivi. Valore null se si usa una coda. |
| `subscriptionName` | string | Sottoscrizione del bus di servizio con messaggi attivi. Valore null se si usa una coda. |