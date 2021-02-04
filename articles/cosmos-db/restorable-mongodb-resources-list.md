---
title: Elencare le risorse restorable nell'API Azure Cosmos DB per MongoDB usando l'API REST
description: Restituisce un elenco di database e combo di raccolta presenti nell'account nel timestamp e nella posizione specificati. In questo modo è possibile convalidare le risorse esistenti nel timestamp e nella posizione specificati.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: b5800c2e9c1f3b36a3ac9afe6cd68f706505fbe0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537442"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Elencare le risorse restorable nell'API Azure Cosmos DB per MongoDB usando l'API REST

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Restituisce un elenco di database e combo di raccolta presenti nell'account nel timestamp e nella posizione specificati. In questo modo è possibile convalidare le risorse esistenti nel timestamp e nella posizione specificati. Questa API richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Parametri facoltativi:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 - OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. |


## <a name="examples"></a>Esempio

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
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

| Definizione | Descrizione | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Database specifici da ripristinare. | | [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. | | [ErrorResponse](#errorresponse) | Risposta di errore. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | Risposta dell'operazione di elenco che contiene le risorse SQL ripristinabili. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

Risposta dell'operazione list, che contiene l'API Azure Cosmos DB ripristinabile per le risorse MongoDB.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[DatabaseRestoreResource](#databaserestoreresource)[]| Elenco di Azure Cosmos DB di ripristino API per le risorse MongoDB, inclusi i nomi di database e di raccolta. |

## <a name="next-steps"></a>Passaggi successivi

* [Elencare i database ripristinabili](restorable-mongodb-databases-list.md)  nell'API Azure Cosmos DB per MongoDB usando l'API REST.
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.