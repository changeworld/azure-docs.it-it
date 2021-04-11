---
title: Criteri di Azure come origine di griglia di eventi
description: Questo articolo descrive come usare i criteri di Azure come origine evento di griglia di eventi. Offre lo schema e i collegamenti ad articoli di esercitazione e procedure.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735080"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Criteri di Azure come origine di griglia di eventi

Questo articolo fornisce le proprietà e lo schema per gli eventi di [criteri di Azure](../governance/policy/index.yml) . Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](./event-schema.md). Viene inoltre visualizzato un elenco di guide introduttive ed esercitazioni per l'uso di criteri di Azure come origine evento.

## <a name="available-event-types"></a>Tipi di evento disponibili

Criteri di Azure genera i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Generato quando viene creato uno stato di conformità dei criteri. |
| Microsoft. PolicyInsights. PolicyStateChanged | Generato quando viene modificato lo stato di conformità di un criterio. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Generato quando lo stato di conformità di un criterio viene eliminato. |

## <a name="example-event"></a>Evento di esempio

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
Nell'esempio seguente viene illustrato lo schema di un evento creato da uno stato dei criteri: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Lo schema per un evento di modifica dello stato dei criteri è simile: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Nell'esempio seguente viene illustrato lo schema di un evento creato da uno stato dei criteri: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Lo schema per un evento di modifica dello stato dei criteri è simile: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
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
| `subject` | string | ID completo della risorsa a cui è destinata la modifica dello stato di conformità, inclusi il nome della risorsa e il tipo di risorsa. Usa il formato, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `eventTime` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati degli eventi di criteri di Azure. |
| `dataVersion` | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| `metadataVersion` | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |

# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `source` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene specificato da Griglia di eventi. |
| `subject` | string | ID completo della risorsa a cui è destinata la modifica dello stato di conformità, inclusi il nome della risorsa e il tipo di risorsa. Usa il formato, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `time` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati degli eventi di criteri di Azure. |
| `specversion` | string | Versione della specifica dello schema CloudEvents. |

---

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `timestamp` | string | Ora (UTC) in cui la risorsa è stata analizzata da criteri di Azure. Per gli eventi di ordinamento, usare questa proprietà anziché le proprietà o di primo livello `eventTime` `time` . |
| `policyAssignmentId` | string | ID risorsa dell'assegnazione dei criteri. |
| `policyDefinitionId` | string | ID risorsa della definizione dei criteri. |
| `policyDefinitionReferenceId` | string | ID di riferimento per la definizione dei criteri all'interno della definizione dell'iniziativa, se l'assegnazione dei criteri è per un'iniziativa. Può essere vuoto. |
| `complianceState` | string | Stato di conformità della risorsa rispetto all'assegnazione dei criteri. |
| `subscriptionId` | string | L'ID sottoscrizione della risorsa. |
| `complianceReasonCode` | string | Codice motivo di conformità. Può essere vuoto. |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sugli eventi di modifica dello stato dei criteri di Azure, vedere [usare griglia di eventi per le notifiche di modifica dello stato dei criteri](../governance/policy/tutorials/route-state-change-events.md).
- Per una panoramica dell'integrazione di criteri di Azure con griglia di eventi, vedere [reagire agli eventi di criteri di Azure tramite griglia di eventi](../governance/policy/concepts/event-overview.md).
- Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](./overview.md)
- Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](./subscription-creation-schema.md).