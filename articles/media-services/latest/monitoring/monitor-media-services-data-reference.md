---
title: Riferimento ai dati di monitoraggio di servizi multimediali
description: Materiale di riferimento importante necessario per il monitoraggio di servizi multimediali
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 866b2faf473f06fc3f85cdb434d6555504a7f6a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598185"
---
# <a name="monitoring-media-services-data-reference"></a>Riferimento ai dati di monitoraggio di servizi multimediali

Questo articolo illustra i dati utili per il monitoraggio di servizi multimediali. Per altre informazioni su tutte le metriche della piattaforma supportate in monitoraggio di Azure, vedere [metriche supportate con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

## <a name="media-services-metrics"></a>Metriche di servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice.

Servizi multimediali supporta le metriche di monitoraggio per le risorse seguenti:

* Account
* Endpoint di streaming

### <a name="account"></a>Account

È possibile monitorare le metriche dell'account seguenti.

|Nome metrica|Nome visualizzato|Descrizione|
|---|---|---|
|AssetCount|Asset count (Conteggio asset)|Asset nell'account.|
|AssetQuota|Asset quota (Quota asset)|Quota di asset nell'account.|
|AssetQuotaUsedPercentage|Asset quota used percentage (Percentuale usata quota asset)|Percentuale della quota di asset già in uso.|
|ContentKeyPolicyCount|Content Key Policy count (Conteggio criteri chiave simmetrica)|Criteri chiave simmetrica nell'account.|
|ContentKeyPolicyQuota|Content Key Policy quota (Quota criteri chiave simmetrica)|Quota dei criteri chiave simmetrica nell'account.|
|ContentKeyPolicyQuotaUsedPercentage|Content Key Policy quota used percentage (Percentuale usata quota criteri chiave simmetrica)|Percentuale della quota dei criteri della chiave simmetrica già utilizzata.|
|StreamingPolicyCount|Streaming Policy count (Conteggio criteri di streaming)|Criteri di streaming nell'account.|
|StreamingPolicyQuota|Streaming Policy quota (Quota criteri di streaming)|Quota dei criteri di streaming nell'account.|
|StreamingPolicyQuotaUsedPercentage|Streaming Policy quota used percentage (Percentuale usata quota criteri di streaming)|Percentuale della quota dei criteri di flusso già utilizzata.|

È anche necessario esaminare [le quote e i limiti dell'account](../limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Endpoint di streaming

Sono supportate le metriche seguenti per gli [endpoint di streaming](/rest/api/media/streamingendpoints) di servizi multimediali:

|Nome metrica|Nome visualizzato|Descrizione|
|---|---|---|
|Requests|Requests|Specifica il numero totale di richieste HTTP gestite dall'endpoint di streaming.|
|Egress|Egress|Byte in uscita totali al minuto per endpoint di streaming.|
|SuccessE2ELatency|Success end to end Latency (Latenza end-to-end riuscita)|Durata del periodo di tempo durante il quale l'endpoint di streaming ha ricevuto la richiesta al momento dell'invio dell'ultimo byte della risposta.|
|Utilizzo della CPU| | Utilizzo della CPU per gli endpoint di streaming Premium. Questi dati non sono disponibili per gli endpoint di streaming standard. |
|Larghezza di banda in uscita | | Larghezza di banda in uscita in bit al secondo.|

## <a name="metric-dimensions"></a>Dimensioni metrica

Per altre informazioni sulle dimensioni della metrica, vedere [metriche multidimensionali](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Log risorse

## <a name="media-services-diagnostic-logs"></a>Log di diagnostica di servizi multimediali

I log di diagnostica forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure. Per ulteriori informazioni, vedere [come raccogliere e utilizzare i dati di log dalle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md).

Servizi multimediali supporta i log di diagnostica seguenti:

* Distribuzione delle chiavi

### <a name="key-delivery"></a>Distribuzione delle chiavi

|Nome|Descrizione|
|---|---|
|Richiesta del servizio di distribuzione delle chiavi|Log che mostrano le informazioni della richiesta del servizio di distribuzione delle chiavi. Per ulteriori informazioni, vedere [schemi](monitor-media-services-data-reference.md).|

## <a name="schemas"></a>Schemi

Per una descrizione dettagliata dello schema dei log di diagnostica di primo livello, vedere [servizi, schemi e categorie supportati per i log di diagnostica di Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Proprietà dello schema del log di distribuzione delle chiavi

Queste proprietà sono specifiche dello schema del log di distribuzione delle chiavi.

|Nome|Descrizione|
|---|---|
|keyId|ID della chiave richiesta.|
|keyType|Può essere uno dei valori seguenti: "Clear" (nessuna crittografia), "FairPlay", "PlayReady" o "Widevine".|
|policyName|Nome Azure Resource Manager del criterio.|
|tokenType|Tipo di token.|
|statusMessage|Messaggio di stato.|

### <a name="example"></a>Esempio

Proprietà dello schema delle richieste di recapito delle chiavi.

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
