---
title: Mappe di Azure come origine di griglia di eventi
description: Descrive le proprietà e lo schema disponibili per gli eventi di Mappe di Azure con Griglia di eventi di Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 88cf0c8274d685a45862bc7b7884b5e4a686c22d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363680"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Mappe di Azure come origine di griglia di eventi

Questo articolo illustra le proprietà e lo schema per gli eventi di Mappe di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](./event-schema.md). Viene inoltre visualizzato un elenco di guide introduttive ed esercitazioni per l'uso di mappe di Azure come origine evento.

## <a name="available-event-types"></a>Tipi di evento disponibili

Un account di Mappe di Azure genera i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Generato quando le coordinate ricevute sono state spostate dall'esterno all'interno di un determinato recinto virtuale |
| Microsoft.Maps.GeofenceExited | Generato quando le coordinate ricevute sono state spostate dall'interno all'esterno di un determinato recinto virtuale |
| Microsoft.Maps.GeofenceResult | Generato ogni volta che una query di geofencing restituisce un risultato, indipendentemente dallo stato |

## <a name="example-events"></a>Eventi di esempio

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
L'esempio seguente illustra lo schema di un evento **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

L'esempio seguente illustra lo schema di **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)
L'esempio seguente illustra lo schema di un evento **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceEntered", 
   "time":"2018-11-08T00:54:17.6408601Z", 
   "specversion":"1.0" 
}
```

L'esempio seguente illustra lo schema di **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceResult", 
   "time":"2018-11-08T00:52:08.0954283Z", 
   "specversion":"1.0" 
}
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
| `data` | object | Dati dell'evento di geofencing. |
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
| `data` | object | Dati dell'evento di geofencing. |
| `specversion` | string | Versione della specifica dello schema CloudEvents. |

---

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `apiCategory` | string | Categoria API dell'evento. |
| `apiName` | string | Nome API dell'evento. |
| `issues` | object | Elenca i problemi verificatisi durante l'elaborazione. Se vengono restituiti problemi, la risposta non includerà geometrie. |
| `responseCode` | d'acquisto | Codice di risposta HTTP |
| `geometries` | object | Elenca le geometrie del recinto che contengono la posizione della coordinata o si sovrappongono a searchBuffer intorno alla posizione. |

L'oggetto error viene restituito quando si verifica un errore nell'API Mappe. L'oggetto error ha le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `error` | ErrorDetails |Questo oggetto viene restituito quando si verifica un errore nell'API Mappe  |

L'oggetto ErrorDetails viene restituito quando si verifica un errore nell'API Mappe. L'oggetto ErrorDetails ha le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `code` | string | Codice di stato HTTP. |
| `message` | string | Se disponibile, una descrizione leggibile dell'errore. |
| `innererror` | InnerError | Se disponibile, un oggetto contenente informazioni specifiche del servizio relative all'errore. |

InnerError è un oggetto contenente informazioni specifiche del servizio relative all'errore. L'oggetto InnerError ha le proprietà seguenti: 

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `code` | string | Messaggio di errore. |

L'oggetto geometries elenca gli ID delle geometrie dei recinti virtuali che sono scaduti rispetto all'ora utente nella richiesta. L'oggetto geometries include elementi di geometria con le proprietà seguenti: 

| Proprietà | Type | Descrizione |
|:-------- |:---- |:----------- |
| `deviceid` | string | ID del dispositivo. |
| `distance` | string | <p>Distanza dalla coordinata al bordo più vicino del recinto virtuale. Un valore positivo indica che la coordinata è all'esterno del recinto virtuale. Se la coordinata è all'esterno del recinto virtuale, ma più lontana del valore di searchBuffer dal bordo del recinto virtuale più vicino, il valore è 999. Un valore negativo indica che la coordinata è all'interno del recinto virtuale. Se la coordinata è all'interno del poligono, ma più lontana del valore di searchBuffer dal bordo del recinto virtuale più vicino, il valore è -999. Un valore pari a 999 indica che è molto probabile che la coordinata sia all'esterno del recinto virtuale. Un valore pari a -999 indica che è molto probabile che la coordinata sia all'interno del recinto virtuale.<p> |
| `geometryid` |string | L'ID univoco identifica la geometria georecinzione. |
| `nearestlat` | d'acquisto | Latitudine del punto più vicino della geometria. |
| `nearestlon` | d'acquisto | Longitudine del punto più vicino della geometria. |
| `udId` | string | ID univoco restituito dal servizio di caricamento dell'utente durante il caricamento di un geofence. Non verrà incluso nell'API post di geoschermatura. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `expiredGeofenceGeometryId` | string[] | Elenca gli ID delle geometrie del recinto virtuale che sono scaduti rispetto all'ora utente nella richiesta. |
| `geometries` | geometries[] |Elenca le geometrie del recinto che contengono la posizione della coordinata o si sovrappongono a searchBuffer intorno alla posizione. |
| `invalidPeriodGeofenceGeometryId` | string[]  | Elenca gli ID delle geometrie del recinto virtuale che sono inclusi in un periodo non valido rispetto all'ora utente nella richiesta. |
| `isEventPublished` | boolean | True se almeno un evento viene pubblicato nel sottoscrittore di eventi di Mappe di Azure, false non viene pubblicato alcun evento nel sottoscrittore di eventi di Mappe di Azure. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo  |Descrizione  |
|---------|---------|
| [Reagire agli eventi di Mappe di Azure con Griglia di eventi](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Panoramica dell'integrazione di Mappe di Azure con Griglia di eventi. |
| [Esercitazione: configurare una rete virtuale](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Questa esercitazione illustra i passaggi di base per la configurazione di un recinto virtuale con Mappe di Azure. Si usa Griglia di eventi di Azure per trasmettere i risultati del recinto virtuale e configurare una notifica in base ai risultati del recinto virtuale stesso. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
