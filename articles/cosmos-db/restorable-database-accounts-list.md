---
title: Elencare gli account di database ripristinabili usando Azure Cosmos DB API REST
description: Elenca tutti gli account di database Azure Cosmos DB ripristinabili disponibili in una sottoscrizione.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 71aafc756e5291e148c3b162f8946544b6e3c2d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537578"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Elencare gli account di database ripristinabili usando Azure Cosmos DB API REST

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Elenca tutti gli account di database Azure Cosmos DB ripristinabili disponibili nella sottoscrizione. Questa chiamata richiede l' `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` autorizzazione.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parametri URI

| Nome | In | Obbligatoria | Tipo | Descrizione |
| --- | --- | --- | --- | --- |
| **subscriptionId** | path | True | string| ID della sottoscrizione di destinazione. |
| **versione API** | query | True | string | Versione dell'API da usare per questa operazione. |

## <a name="responses"></a>Risposte

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| 200 - OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| Operazione riuscita. |
| Altri codici di stato | [DefaultErrorResponse](#defaulterrorresponse)| Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. |

## <a name="examples"></a>Esempio

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Richiesta di esempio**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Risposta di esempio**

Codice di stato: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definizioni

|Definizione  | Descrizione|
| --- | --- |
| [ApiType](#apitype) | Enum per indicare il tipo di API dell'account del database ripristinabile. |
| [DefaultErrorResponse](#defaulterrorresponse) | Risposta di errore dal servizio. |
| [ErrorResponse](#errorresponse) | Risposta di errore. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Un account di database Azure Cosmos DB ripristinabile. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | Risposta dell'operazione di elenco che contiene gli account di database ripristinabili e le relative proprietà. |
| [RestorableLocationResource](#restorablelocationresource) | Proprietà dell'account ripristinabile a livello di area. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enum per indicare il tipo di API dell'account del database ripristinabile.

| **Nome** | **Tipo** |
| --- | --- |
| Cassandra |string|
| Gremlin |string|
| GremlinV2 |string|
| MongoDB |string|
| Sql |string|
| Tabella |string|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Un account di database Azure Cosmos DB ripristinabile.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| ID |string| Identificatore della risorsa univoca della risorsa Azure Resource Manager. |
| posizione |string| Posizione del gruppo di risorse a cui appartiene la risorsa. |
| name |string| Nome della risorsa Gestione risorse. |
| Properties. AccountName |string| Nome dell'account del database globale |
| Properties. apiType |[ApiType](#apitype)| Tipo di API dell'account del database ripristinabile. |
| Properties. creationTime |string| Ora di creazione dell'account di database ripristinabile (formato ISO-8601). |
| Properties. deletionTime |string| Ora in cui è stato eliminato l'account del database ripristinabile (formato ISO-8601). |
| Properties. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Elenco di aree da cui è possibile ripristinare l'account del database. |
| tipo |string| Tipo di risorsa di Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

Risposta dell'operazione di elenco che contiene gli account di database ripristinabili e le relative proprietà.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Valore |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Elenco di account di database ripristinabili e relative proprietà. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Proprietà dell'account ripristinabile a livello di area.

| **Nome** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| creationTime |string| Data e ora di creazione dell'account del database ripristinabile a livello di area (formato ISO-8601). |
| deletionTime |string| Data e ora in cui l'account del database ripristinabile a livello di area è stato eliminato (formato ISO-8601). |
| locationName |string| Posizione dell'account ripristinabile a livello di area. |
| regionalDatabaseAccountInstanceId |string| ID istanza dell'account ripristinabile a livello di area. |

## <a name="next-steps"></a>Passaggi successivi

* [Account di database ripristinabili-elenco per località.](restorable-database-accounts-list-by-location.md)
* [Modello di risorse](continuous-backup-restore-resource-model.md) della modalità di backup continuo.