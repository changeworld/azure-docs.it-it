---
title: Elencare i contenitori di API SQL ripristinabili in Azure Cosmos DB usando l'API REST
description: Mostra il feed di eventi di tutte le mutazioni eseguite su tutti i Azure Cosmos DB contenitori SQL in un database specifico. Questo consente uno scenario in cui il contenitore è stato eliminato accidentalmente.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527713"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Elencare i contenitori di API SQL ripristinabili in Azure Cosmos DB usando l'API REST

Mostra il feed di eventi di tutte le mutazioni eseguite su tutti i Azure Cosmos DB contenitori SQL in un database specifico. Questo consente uno scenario in cui il contenitore è stato eliminato accidentalmente. Questa API richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` autorizzazione

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Parametri facoltativi:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Parametri URI

| Nome | In | Obbligatoria | Tipo | Descrizione |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| Il GUID instanceId di un account di database ripristinabile. |
| **location** | path | True | string| Azure Cosmos DB area, con spazi tra le parole e ogni parola maiuscola. |
| **subscriptionId** | path | True | string| ID della sottoscrizione di destinazione. |
| **versione API** | query | True | string | Versione dell'API da usare per questa operazione. |
| **restorableSqlDatabaseRid** | query | |string| ID risorsa del database SQL. |

## <a name="responses"></a>Risposte

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| 200 - OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. |

## <a name="examples"></a>Esempio

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definizioni

| Definizione | Descrizione | | --- || --- | | [Contenitore](#container) | Azure Cosmos DB oggetto risorsa contenitore SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. | | [ErrorResponse](#errorresponse) | Risposta di errore. | | [OperationType](#operationtype) | Enum per indicare il tipo di operazione dell'evento. | | [Risorsa](#resource) | Risorsa di un evento del contenitore SQL Azure Cosmos DB | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Evento del contenitore SQL Azure Cosmos DB | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Proprietà di un evento del contenitore SQL Azure Cosmos DB | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | Risposta dell'operazione di elenco che contiene gli eventi del contenitore SQL e le relative proprietà. |

### <a name="container"></a><a id="container"></a>Contenitore

Azure Cosmos DB oggetto risorsa contenitore SQL

| **Nome**  |  **Tipo**  |  di **Descrizione** | | --- || --- | ---| | _etag | stringa | Proprietà generata dal sistema che rappresenta il valore ETag della risorsa necessario per il controllo della concorrenza ottimistica. | | _rid | stringa | Proprietà generata dal sistema. Identificatore univoco. | | _self | stringa | Proprietà generata dal sistema che specifica il percorso indirizzabile della risorsa del contenitore. | | _ts | | Proprietà generata dal sistema che indica il timestamp dell'ultimo aggiornamento della risorsa. | | ID | stringa | Nome del contenitore SQL Azure Cosmos DB |

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
| Crea |string|evento di creazione del contenitore|
| Elimina |string|evento di eliminazione del contenitore|
| Sostituisci |string|evento di modifica del contenitore|
| SystemOperation |string|evento di modifica del contenitore attivato dal sistema. Questo evento non è stato avviato dall'utente|

### <a name="resource"></a><a id="resource"></a>Risorsa

Risorsa di un evento del contenitore SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| _rid |string| Proprietà generata dal sistema. Identificatore univoco. |
| contenitore |[Contenitore](#container)| Azure Cosmos DB oggetto risorsa contenitore SQL |
| eventTimestamp |string| Data e ora in cui si è verificato questo evento contenitore. |
| operationType |[Tipo operazione](#operationtype)| Tipo di operazione di questo evento contenitore. |
| ownerId |string| Nome del contenitore SQL. |
| ownerResourceId |string| ID risorsa del contenitore SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Evento del contenitore SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | ---
| ID |string| Identificatore della risorsa univoca della risorsa Azure Resource Manager. |
| name |string| Nome della risorsa Azure Resource Manager. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Proprietà di un evento del contenitore SQL. |
| tipo |string| Tipo di risorsa di Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Proprietà di un evento del contenitore SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| risorse |[Risorsa](#resource)| Risorsa di un evento del contenitore SQL Azure Cosmos DB |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

Risposta dell'operazione di elenco che contiene gli eventi del contenitore SQL e le relative proprietà.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Elenco di eventi del contenitore SQL e relative proprietà. |

## <a name="next-steps"></a>Passaggi successivi

* [Elencare i database ripristinabili](restorable-mongodb-databases-list.md)  nell'API Azure Cosmos DB per MongoDB usando l'API REST.
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.