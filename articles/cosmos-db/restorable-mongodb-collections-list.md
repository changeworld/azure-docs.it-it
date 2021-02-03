---
title: Elencare le raccolte ripristinabili nell'API REST di Azure Cosmos DB MongoDB
description: Mostra il feed di eventi di tutte le mutazioni eseguite in tutte le raccolte Azure Cosmos DB MongoDB in un database specifico. Questo consente uno scenario in cui il contenitore è stato eliminato accidentalmente.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 83e1c7c27e8c5d179c4ec6aa4ba64b3367294a48
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527700"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Elencare le raccolte ripristinabili nell'API Azure Cosmos DB per MongoDB usando l'API REST

Mostra il feed di eventi di tutte le mutazioni eseguite in tutte le raccolte di Azure Cosmos DB API per MongoDB in un database specifico. Questo consente uno scenario in cui il contenitore è stato eliminato accidentalmente. Questa API richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Parametri facoltativi:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Parametri URI

| Nome | In | Obbligatoria | Tipo | Descrizione |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| Il GUID instanceId di un account di database ripristinabile. |
| **location** | path | True | string| Azure Cosmos DB area, con spazi tra le parole e ogni parola maiuscola. |
| **subscriptionId** | path | True | string| ID della sottoscrizione di destinazione. |
| **versione API** | query | True | string | Versione dell'API da usare per questa operazione. |
| **restorableMongodbDatabaseRid** | query | |string| ID risorsa dell'API Azure Cosmos DB per il database MongoDB. |

## <a name="responses"></a>Risposte

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| 200 - OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita.|

## <a name="examples"></a>Esempio

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definizioni

|Definizione  | Descrizione|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. |
| [ErrorResponse](#errorresponse) | Risposta di errore. |
| [Tipo operazione](#operationtype) | Enum per indicare il tipo di operazione dell'evento. |
| [Risorsa](#resource) | La risorsa di un'API Azure Cosmos DB per l'evento di raccolta MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Un evento Azure Cosmos DB API per la raccolta MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | Proprietà di un evento Azure Cosmos DB API per la raccolta MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | Risposta dell'operazione di elenco che contiene gli eventi di raccolta e le relative proprietà. |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Risposta di errore dal servizio.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| error | [ErrorResponse](#errorresponse)| Risposta di errore. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Risposta di errore.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| codice |string| Codice di errore. |
| message |string| Messaggio di errore che indica il motivo dell'esito negativo dell'operazione. |

### <a name="operationtype"></a><a id="operationtype"></a>Tipo operazione

Enum per indicare il tipo di operazione dell'evento.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Crea |string|evento di creazione raccolta|
| Elimina |string|evento di eliminazione della raccolta|
| Sostituisci |string|evento di modifica della raccolta|

### <a name="resource"></a><a id="resource"></a>Risorsa

La risorsa di un evento di raccolta Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| _rid |string| Proprietà generata dal sistema. Identificatore univoco. |
| eventTimestamp |string| Data e ora in cui si è verificato questo evento di raccolta. |
| operationType |[Tipo operazione](#operationtype)|  Tipo di operazione dell'evento della raccolta. |
| ownerId |string| Nome della raccolta MongoDB.|
| ownerResourceId |string| ID risorsa della raccolta MongoDB. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Evento della raccolta Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| ID |string| Identificatore della risorsa univoca della risorsa Azure Resource Manager. |
| name |string| Nome della risorsa Gestione risorse. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Proprietà di un evento di raccolta. |
| tipo |string| Tipo di risorsa di Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Proprietà di un evento di raccolta Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| risorse | [Risorsa](#resource)| La risorsa di un'API Azure Cosmos DB per l'evento di raccolta MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

Risposta dell'operazione di elenco che contiene gli eventi di raccolta e le relative proprietà.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Elenco di Azure Cosmos DB API per gli eventi di raccolta MongoDB e relative proprietà. |

## <a name="next-steps"></a>Passaggi successivi

* [Elencare i database ripristinabili](restorable-mongodb-databases-list.md)  nell'API Azure Cosmos DB per MongoDB usando l'API REST.
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.