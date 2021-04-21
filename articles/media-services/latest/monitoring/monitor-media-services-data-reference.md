---
title: Informazioni di riferimento sui dati di Servizi multimediali di monitoraggio
description: Materiale di riferimento importante necessario per il monitoraggio di Servizi multimediali
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 4e4c65966ec8a6a5b47d5f68596f6d2d496fda17
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835507"
---
# <a name="monitoring-media-services-data-reference"></a>Informazioni di riferimento sui dati di Servizi multimediali di monitoraggio

Questo articolo illustra i dati utili per il monitoraggio di Servizi multimediali. Per altre informazioni su tutte le metriche della piattaforma supportate in Monitoraggio di Azure, vedere [Metriche supportate con Monitoraggio di Azure](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="media-services-metrics"></a>Metriche di Servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice.


Servizi multimediali supporta le metriche di monitoraggio per le risorse seguenti:

|Tipo di metrica | Spazio dei nomi del provider di risorse/tipo<br/> e collegamento a singole metriche |
|-------|-----|
| Generale di Servizi multimediali | [Generale](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Eventi live | [Microsoft.Media/mediaservices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Endpoint di streaming | [Microsoft.Media/mediaservices/streamingEndpoints,](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints)che sono rilevanti per [l'API REST degli endpoint di streaming](/rest/api/media/streamingendpoints). 


È anche necessario esaminare [le quote e i limiti dell'account.](../limits-quotas-constraints-reference.md)


## <a name="metric-dimensions"></a>Dimensioni delle metriche

Per altre informazioni sulle dimensioni delle metriche, vedere [Metriche multidimensionali.](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

<!--**PLACEHOLDER** for dimensions table.-->
OutputFormat, HttpStatusCode, ErrorCode, TrackName

## <a name="resource-logs"></a>Log risorse

## <a name="media-services-resource-logs"></a>Log delle risorse di Servizi multimediali

I log delle risorse forniscono dati dettagliati e frequenti sul funzionamento di una risorsa di Azure. Per altre informazioni, vedere [Come raccogliere e utilizzare i dati di log dalle risorse di Azure.](../../../azure-monitor/essentials/platform-logs-overview.md)

Servizi multimediali supporta i log delle risorse seguenti: [Microsoft.Media/mediaservices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Schemi

Per una descrizione dettagliata dello schema dei log di diagnostica di primo livello, vedere Servizi, schemi e categorie supportati [per i log di diagnostica di Azure.](../../../azure-monitor/essentials/resource-logs-schema.md)

## <a name="key-delivery-log-schema-properties"></a>Proprietà dello schema del log di recapito delle chiavi

Queste proprietà sono specifiche dello schema del log di distribuzione delle chiavi.

|Nome|Descrizione|
|---|---|
|keyId|ID della chiave richiesta.|
|keyType|Può essere uno dei valori seguenti: "Clear" (nessuna crittografia), "FairPlay", "PlayReady" o "Widevine".|
|policyName|Nome Azure Resource Manager del criterio.|
|tokenType|Tipo di token.|
|messaggio di stato|Messaggio di stato.|

### <a name="example"></a>Esempio

Proprietà dello schema delle richieste di distribuzione delle chiavi.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
