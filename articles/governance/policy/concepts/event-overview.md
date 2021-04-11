---
title: Reazione agli eventi di modifica dello stato dei criteri di Azure
description: Usare griglia di eventi di Azure per sottoscrivere gli eventi dei criteri per le app, che consentono alle applicazioni di reagire alle modifiche dello stato senza la necessità di codice complesso.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735101"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Reazione agli eventi di modifica dello stato dei criteri di Azure

Gli eventi di criteri di Azure consentono alle applicazioni di reagire alle modifiche di stato. Questa integrazione viene eseguita senza la necessità di codice complesso o servizi di polling costosi e inefficienti. Al contrario, gli eventi vengono inviati tramite [griglia di eventi di Azure](../../../event-grid/index.yml) ai sottoscrittori, ad esempio funzioni di [Azure](../../../azure-functions/index.yml), app per la [logica di Azure](../../../logic-apps/index.yml)o anche al listener HTTP personalizzato.
In modo critico, paghi solo per ciò che usi.

Gli eventi di criteri di Azure vengono inviati alla griglia di eventi di Azure, che offre servizi di recapito affidabili alle applicazioni tramite criteri avanzati per i tentativi e il recapito di messaggi non recapitabili. Per altre informazioni, vedere [recapito dei messaggi di griglia di eventi e riprovare](../../../event-grid/delivery-and-retry.md).

Lo scenario di eventi comuni di criteri di Azure tiene traccia del momento in cui lo stato di conformità di una risorsa cambia durante la valutazione dei criteri. L'architettura basata su eventi è un modo efficiente per rispondere a queste modifiche anziché analizzare lo stato di conformità delle risorse in base a una pianificazione fissa.

> [!NOTE]
> Gli eventi di modifica dello stato dei criteri di Azure vengono inviati a griglia di eventi al termine della valutazione delle risorse da un [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers)

Per un'esercitazione completa, vedere [indirizzare gli eventi di modifica dello stato dei criteri a griglia di eventi con CLI di Azure](../tutorials/route-state-change-events.md) .

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Modello di Griglia di eventi per origini e gestori" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Eventi criteri di Azure disponibili

Griglia di eventi usa le [sottoscrizioni di eventi](../../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Le sottoscrizioni di eventi criteri di Azure possono includere tre tipi di eventi:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Generato quando viene creato uno stato di conformità dei criteri. |
| Microsoft. PolicyInsights. PolicyStateChanged | Generato quando viene modificato lo stato di conformità di un criterio. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Generato quando lo stato di conformità di un criterio viene eliminato. |

## <a name="event-schema"></a>Schema di eventi

Gli eventi di criteri di Azure contengono tutte le informazioni necessarie per rispondere alle modifiche apportate ai dati. È possibile identificare un evento di criteri di Azure quando la `eventType` proprietà inizia con "Microsoft. PolicyInsights".
Informazioni aggiuntive sull'utilizzo delle proprietà degli eventi di griglia di eventi sono documentate in  
[Schema di eventi di griglia di eventi](../../../event-grid/event-schema.md).

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `id` | string | Identificatore univoco dell'evento. |
| `topic` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene specificato da Griglia di eventi. |
| `subject` | string | ID completo della risorsa a cui è destinata la modifica dello stato di conformità, inclusi il nome della risorsa e il tipo di risorsa. Usa il formato, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | object | Dati degli eventi di criteri di Azure. |
| `data.timestamp` | string | Ora (UTC) in cui la risorsa è stata analizzata da criteri di Azure. Per gli eventi di ordinamento, usare questa proprietà anziché le proprietà o di primo livello `eventTime` `time` . |
| `data.policyAssignmentId` | string | ID risorsa dell'assegnazione dei criteri. |
| `data.policyDefinitionId` | string | ID risorsa della definizione dei criteri. |
| `data.policyDefinitionReferenceId` | string | ID di riferimento per la definizione dei criteri all'interno della definizione dell'iniziativa, se l'assegnazione dei criteri è per un'iniziativa. Può essere vuoto. |
| `data.complianceState` | string | Stato di conformità della risorsa rispetto all'assegnazione dei criteri. |
| `data.subscriptionId` | string | L'ID sottoscrizione della risorsa. |
| `data.complianceReasonCode` | string | Codice motivo di conformità. Può essere vuoto. |
| `eventType` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `eventTime` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `dataVersion` | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| `metadataVersion` | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |

Di seguito è riportato un esempio di un evento di modifica dello stato dei criteri:

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

Per altre informazioni, vedere [schema degli eventi di criteri di Azure](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi di criteri di Azure devono seguire le seguenti procedure consigliate:

> [!div class="checklist"]
> - È possibile configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi, quindi non presupporre che gli eventi provengano da un'origine particolare. Al contrario, controllare l'argomento del messaggio per verificare l'assegnazione dei criteri, la definizione dei criteri e la risorsa per l'evento di modifica dello stato.
> - Controllare `eventType` e non presupporre che tutti gli eventi ricevuti siano i tipi previsti.
> - Usare `data.timestamp` per determinare l'ordine degli eventi nei criteri di Azure, anziché le proprietà o di primo `eventTime` livello `time` .
> - Usare il campo oggetto per accedere alla risorsa con modifica dello stato dei criteri.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su griglia di eventi e concedi agli eventi di modifica dello stato dei criteri di Azure un tentativo:

- [Instradare gli eventi di modifica dello stato dei criteri a griglia di eventi con l'interfaccia](../tutorials/route-state-change-events.md)
- [Dettagli dello schema di criteri di Azure per griglia di eventi](../../../event-grid/event-schema-policy.md)
- [Informazioni sulla griglia di eventi](../../../event-grid/overview.md)