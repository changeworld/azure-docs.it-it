---
title: Elencare i database ripristinabili nell'API Azure Cosmos DB per MongoDB usando l'API REST
description: Mostra il feed di eventi di tutte le mutazioni eseguite su tutti i Azure Cosmos DB database MongoDB con l'account ripristinabile. Questo consente uno scenario in cui il database è stato eliminato accidentalmente per ottenere l'ora di eliminazione.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537511"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Elencare i database ripristinabili nell'API Azure Cosmos DB per MongoDB usando l'API REST

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mostra il feed di eventi di tutte le mutazioni eseguite su tutti i Azure Cosmos DB database MongoDB con l'account ripristinabile. Questo consente uno scenario in cui il database è stato eliminato accidentalmente per ottenere l'ora di eliminazione. Questa API richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parametri URI

| Nome | In | Obbligatoria | Tipo | Descrizione |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| Il GUID instanceId di un account di database ripristinabile. |
| **location** | path | True | string| Azure Cosmos DB area, con spazi tra le parole e ogni parola maiuscola. |
| **subscriptionId** | path | True | string| ID della sottoscrizione di destinazione. |
| **versione API** | query | True | string | Versione dell'API da usare per questa operazione. |

## <a name="responses"></a>Risposte

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| 200 - OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita.|

## <a name="examples"></a>Esempio

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definizioni

| Definizione | Descrizione | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. | | [ErrorResponse](#errorresponse) | Risposta di errore. | | [OperationType](#operationtype) | Enum per indicare il tipo di operazione dell'evento. | | [Risorsa](#resource) | Risorsa di un evento di database Azure Cosmos DB API per MongoDB | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Un evento Azure Cosmos DB API per il database MongoDB | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | Proprietà di un evento di database Azure Cosmos DB API per MongoDB | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | Risposta dell'operazione di elenco che contiene l'API Azure Cosmos DB per gli eventi di database MongoDB e le relative proprietà. |

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
| Crea |string|evento di creazione del database|
| Elimina |string|evento di eliminazione del database|
| Sostituisci |string|evento di modifica del database|

### <a name="resource"></a><a id="resource"></a>Risorsa

La risorsa di un evento di database Azure Cosmos DB API per MongoDB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| _rid |string| Proprietà generata dal sistema. Identificatore univoco. |
| eventTimestamp |string| Ora in cui si è verificato l'evento di database. |
| operationType |[Tipo operazione](#operationtype)| Tipo di operazione di questo evento di database.  |
| ownerId |string| Nome dell'API Azure Cosmos DB per il database MongoDB.|
| ownerResourceId |string| ID risorsa Azure Cosmos DB API per il database MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Un evento di database Azure Cosmos DB API per MongoDB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| ID |string| Identificatore della risorsa univoca della risorsa Azure Resource Manager. |
| name |string| Nome della risorsa Gestione risorse. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| Proprietà di un evento di database Azure Cosmos DB API per MongoDB. |
| tipo |string| Tipo di risorsa di Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Proprietà di un evento di database Azure Cosmos DB API per MongoDB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| risorse |[Risorsa](#resource)| La risorsa di un evento di database Azure Cosmos DB API per MongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

Risposta dell'operazione di elenco che contiene gli eventi del database e le relative proprietà.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Elenco degli eventi del database e delle relative proprietà. |

## <a name="next-steps"></a>Passaggi successivi

* [Elencare le risorse ripristinabili](restorable-mongodb-resources-list.md)  nell'API Azure Cosmos DB per MongoDB usando l'API REST.
* [Elencare le raccolte ripristinabili](restorable-mongodb-collections-list.md)  nell'API Azure Cosmos DB per MongoDB usando l'API REST.
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.