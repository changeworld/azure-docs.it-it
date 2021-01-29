---
title: Cache di Azure per Redis come origine griglia di eventi
description: Descrive le proprietà disponibili per gli eventi di cache di Azure per Redis con griglia di eventi di Azure
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055971"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Cache di Azure per Redis come origine di griglia di eventi

Questo articolo fornisce le proprietà e lo schema per la cache di Azure per gli eventi Redis. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). 

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Elenco di eventi per le API REST di cache di Azure per Redis

Questi eventi vengono attivati quando un client Esporta, importa o ridimensiona chiamando la cache di Azure per le API REST di Redis. L'evento di applicazione di patch viene attivato dall'aggiornamento di Redis.

 |Nome evento |Descrizione|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |Attivato quando i dati della cache vengono esportati. |
 |**Microsoft. cache. ImportRDBCompleted** |Attivato durante l'importazione dei dati della cache. |
 |**Microsoft. cache. PatchingCompleted** |Attivato al termine dell'applicazione di patch. |
 |**Microsoft. cache. ScalingCompleted** |Attivato quando il ridimensionamento viene completato. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Contenuto della risposta di un evento

Quando viene attivato un evento, il servizio Griglia di eventi invia i dati relativi all'evento all'endpoint di sottoscrizione.

Questa sezione contiene un esempio dell'aspetto dei dati per ogni evento di cache di Azure per Redis.

### <a name="microsoftcachepatchingcompleted-event"></a>Evento Microsoft. cache. PatchingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Evento Microsoft. cache. ImportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Evento Microsoft. cache. ExportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Cache di Azure per i dati dell'evento Redis. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| timestamp | string | L'ora in cui si è verificato l'evento. |
| name | string | Nome dell'evento. |
| status | string | Lo stato dell'evento. Operazione non riuscita o completata. |


## <a name="quickstarts"></a>Avvi rapidi

Se si vuole provare la cache di Azure per gli eventi Redis, vedere gli articoli introduttivi seguenti:

|Se si desidera utilizzare questo strumento:    |Vedere questo articolo: |
|--|-|
|Portale di Azure    |[Guida introduttiva: instradare la cache di Azure per gli eventi Redis all'endpoint Web con il portale di Azure](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Guida introduttiva: eseguire il routing della cache di Azure per gli eventi Redis all'endpoint Web con PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Interfaccia della riga di comando di Azure    |[Guida introduttiva: eseguire il routing di cache di Azure per eventi Redis all'endpoint Web con l'interfaccia della riga](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).

