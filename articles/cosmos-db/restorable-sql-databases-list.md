---
title: Elencare i database delle API SQL ripristinabili in Azure Cosmos DB usando l'API REST
description: Mostra il feed di eventi di tutte le mutazioni eseguite su tutti i database SQL di Azure Cosmos DB con l'account ripristinabile. Questo consente uno scenario in cui il database è stato eliminato accidentalmente per ottenere l'ora di eliminazione.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: d3d72cff5fcfeed17d60e2f856be4adc1a983819
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527517"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Elencare i database delle API SQL ripristinabili in Azure Cosmos DB usando l'API REST

Mostra il feed di eventi di tutte le mutazioni eseguite su tutti i database SQL di Azure Cosmos DB con l'account ripristinabile. Questo consente uno scenario in cui il database è stato eliminato accidentalmente per ottenere l'ora di eliminazione. Questa API richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
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
| 200 - OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. |

## <a name="examples"></a>Esempio

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definizioni

| Definizione | Descrizione | | --- || --- | | [Database](#database) | Azure Cosmos DB oggetto risorsa database SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. | | [ErrorResponse](#errorresponse) | Risposta di errore. | | [OperationType](#operationtype) | Enum per indicare il tipo di operazione dell'evento. | | [Risorsa](#resource) | La risorsa di un evento di database SQL Azure Cosmos DB | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Evento Azure Cosmos DB database SQL | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Proprietà di un evento di database SQL Azure Cosmos DB | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | Risposta dell'operazione di elenco che contiene gli eventi del database SQL e le relative proprietà. |

### <a name="database"></a><a id="database"></a>Database

Azure Cosmos DB oggetto risorsa database SQL

| **Nome**  |  **Tipo**  |  di **Descrizione** | | --- || --- | ---| | _colls | stringa | Proprietà generata dal sistema che ha specificato il percorso indirizzabile della risorsa delle raccolte. | | _etag | stringa | Proprietà generata dal sistema che rappresenta il valore ETag della risorsa necessario per il controllo della concorrenza ottimistica. | | _rid | stringa | Proprietà generata dal sistema. Identificatore univoco. | | _self | stringa | Proprietà generata dal sistema che specifica il percorso indirizzabile della risorsa del database. | | _ts | | Proprietà generata dal sistema che indica il timestamp dell'ultimo aggiornamento della risorsa. | | _users | stringa | Proprietà generata dal sistema che specifica il percorso indirizzabile della risorsa dell'utente. | | ID | stringa | Nome del database SQL di Azure Cosmos DB |

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
| SystemOperation |string|evento di modifica del database attivato dal sistema. Questo evento non è stato avviato dall'utente|

### <a name="resource"></a><a id="resource"></a>Risorsa

Risorsa di un evento Azure Cosmos DB database SQL

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| _rid |string| Proprietà generata dal sistema. Identificatore univoco. |
| database |[Database](#database)| Azure Cosmos DB oggetto risorsa database SQL |
| eventTimestamp |string| Ora in cui si è verificato l'evento di database. |
| operationType |[Tipo operazione](#operationtype)| Tipo di operazione di questo evento di database. |
| ownerId |string| Nome del database SQL. |
| ownerResourceId |string| ID risorsa del database SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Evento Azure Cosmos DB database SQL

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| ID |string| Identificatore della risorsa univoca della risorsa Azure Resource Manager. |
| name |string| Nome della risorsa Azure Resource Manager. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Proprietà di un evento del database SQL. |
| tipo |string| Tipo di risorsa di Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Proprietà di un evento di database SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| risorse |[Risorsa](#resource)| Risorsa di un evento Azure Cosmos DB database SQL |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

Risposta dell'operazione di elenco che contiene gli eventi del database SQL e le relative proprietà.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Elenco degli eventi del database SQL e delle relative proprietà. |

## <a name="next-steps"></a>Passaggi successivi

* [Elencare i contenitori ripristinabili](restorable-sql-containers-list.md) in Azure Cosmos DB API SQL tramite l'API REST.
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.