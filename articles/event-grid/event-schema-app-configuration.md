---
title: Configurazione app Azure come origine griglia di eventi
description: Questo articolo descrive come usare la configurazione di app Azure come origine evento di griglia di eventi. Offre lo schema e i collegamenti ad articoli di esercitazione e procedure.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366773"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Configurazione app Azure come origine di griglia di eventi
Questo articolo fornisce le proprietà e lo schema per gli eventi di configurazione app Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Viene inoltre visualizzato un elenco di guide introduttive ed esercitazioni per utilizzare app Azure configurazione come origine evento.

## <a name="available-event-types"></a>Tipi di evento disponibili

App Azure configurazione genera i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Generato quando viene creato o sostituito un valore chiave. |
| Microsoft. AppConfiguration. KeyValueDeleted | Generato quando un valore di chiave viene eliminato. |

## <a name="example-event"></a>Evento di esempio

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
Nell'esempio seguente viene illustrato lo schema di un evento di modifica chiave-valore: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Lo schema per un evento di eliminazione chiave-valore è simile: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Nell'esempio seguente viene illustrato lo schema di un evento di modifica chiave-valore: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

Lo schema per un evento di eliminazione chiave-valore è simile: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Proprietà degli eventi

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `topic` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene specificato da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `eventType` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `eventTime` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati degli eventi di configurazione dell'app. |
| `dataVersion` | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| `metadataVersion` | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |


# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `source` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene specificato da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `type` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `time` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati degli eventi di configurazione dell'app. |
| `specversion` | string | Versione della specifica dello schema CloudEvents. |

---

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `key` | string | Chiave del valore della chiave che è stato modificato o eliminato. |
| `label` | string | Etichetta, se presente, del valore della chiave che è stato modificato o eliminato. |
| `etag` | string | Per `KeyValueModified` l'ETag del nuovo valore di chiave. Per `KeyValueDeleted` l'ETag del valore della chiave che è stato eliminato. |


## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure

|Titolo | Descrizione |
|---------|---------|
| [Reagire agli eventi di configurazione di app Azure tramite griglia di eventi](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Panoramica dell'integrazione di app Azure configurazione con griglia di eventi. |
| [Usa griglia di eventi per le notifiche di modifica dei dati](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Informazioni su come usare le sottoscrizioni di eventi di configurazione app Azure per inviare eventi di modifica chiave-valore a un endpoint Web. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso di eventi di configurazione app Azure, vedere [usare griglia di eventi per le notifiche di modifica dei dati](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 