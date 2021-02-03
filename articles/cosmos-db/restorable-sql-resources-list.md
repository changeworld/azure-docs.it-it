---
title: Elencare le risorse dell'API SQL ripristinabile in Azure Cosmos DB usando l'API REST
description: Restituisce un elenco di database e combinata di contenitori presenti nell'account nel timestamp e nella posizione specificati. In questo modo è possibile convalidare le risorse esistenti nel timestamp e nella posizione specificati.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dbee87098dcc712669c332deac656cf5656ef4c4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527512"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Elencare le risorse dell'API SQL ripristinabile in Azure Cosmos DB usando l'API REST

Restituisce un elenco di database e combinata di contenitori presenti nell'account nel timestamp e nella posizione specificati. In questo modo è possibile convalidare le risorse esistenti nel timestamp e nella posizione specificati. Questa API richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Parametri facoltativi:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Parametri URI

| Nome | In | Obbligatoria | Tipo | Descrizione |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| Il GUID instanceId di un account di database ripristinabile. |
| **location** | path | True | string| Azure Cosmos DB area, con spazi tra le parole e ogni parola maiuscola. |
| **subscriptionId** | path | True | string| ID della sottoscrizione di destinazione. |
| **versione API** | query | True | string | Versione dell'API da usare per questa operazione. |
| **restoreLocation** | query | |string| Posizione in cui si trovano le risorse ripristinabili. |
| **restoreTimestampInUtc** | query | |string| Timestamp del momento in cui sono esistite le risorse ripristinabili. |

## <a name="responses"></a>Risposte

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| 200 - OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. |

## <a name="examples"></a>Esempio

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definizioni

| Definizione | Descrizione | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Database specifici da ripristinare. | | [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. | | [ErrorResponse](#errorresponse) | Risposta di errore. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | Risposta dell'operazione di elenco che contiene le risorse SQL ripristinabili. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Database specifici da ripristinare.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| collectionNames |string[]| Nomi delle raccolte disponibili per il ripristino. |
| databaseName |string| Nome del database disponibile per il ripristino. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

Risposta dell'operazione di elenco che contiene le risorse SQL ripristinabili.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[DatabaseRestoreResource](#databaserestoreresource)[]| Elenco di risorse SQL ripristinabili, inclusi i nomi di database e di raccolta. |

## <a name="next-steps"></a>Passaggi successivi

* [Elencare i database ripristinabili](restorable-sql-databases-list.md) nell'API di Azure Cosmos SQL usando l'API REST.
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.