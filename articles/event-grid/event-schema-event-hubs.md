---
title: Hub eventi di Azure come origine di griglia di eventi
description: Descrive le proprietà disponibili per gli eventi degli hub eventi con Griglia di eventi di Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: e9bb4b5a27173181c7295e96a1eb0654a1a929e6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363510"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Hub eventi di Azure come origine di griglia di eventi

Questo articolo illustra le proprietà e lo schema per gli eventi degli hub eventi. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="available-event-types"></a>Tipi di evento disponibili

Hub eventi genera il tipo di evento **Microsoft.EventHub.CaptureFileCreated** quando viene creato un file di acquisizione.

## <a name="example-event"></a>Evento di esempio

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)

Questo evento di esempio mostra lo schema di un evento di hub eventi generato quando la funzionalità di acquisizione archivia un file: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Questo evento di esempio mostra lo schema di un evento di hub eventi generato quando la funzionalità di acquisizione archivia un file: 

```json
[
    {
        "source": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "type": "Microsoft.EventHub.CaptureFileCreated",
        "time": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "specversion": "1.0"
    }
]
```


---


## <a name="event-properties"></a>Proprietà degli eventi

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `topic` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `eventType` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `eventTime` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati dell'evento dell'hub eventi. |
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
| `data` | object | Dati dell'evento dell'hub eventi. |
| `specversion` | string | Versione della specifica dello schema CloudEvents. |

---

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `fileUrl` | string | Percorso del file di acquisizione. |
| `fileType` | string | Tipo di file del file di acquisizione. |
| `partitionId` | string | ID della partizione. |
| `sizeInBytes` | numero intero | Dimensioni del file. |
| `eventCount` | numero intero | Numero di eventi nel file. |
| `firstSequenceNumber` | numero intero | Numero di sequenza minore nella coda. |
| `lastSequenceNumber` | numero intero | Ultimo numero di sequenza nella coda. |
| `firstEnqueueTime` | string | Primo orario nella coda. |
| `lastEnqueueTime` | string | Ultimo orario nella coda. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure

|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per informazioni sulla gestione degli eventi dell'hub eventi, vedere [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md).