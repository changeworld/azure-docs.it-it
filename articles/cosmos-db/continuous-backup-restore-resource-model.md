---
title: Modello di risorsa per la funzionalità di ripristino temporizzato Azure Cosmos DB.
description: Questo articolo illustra il modello di risorsa per la funzionalità di ripristino temporizzato di Azure Cosmos DB. Vengono illustrati i parametri che supportano il backup continuo e le risorse che possono essere ripristinate nell'API di Azure Cosmos DB per gli account SQL e MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 065127fbeaabc415dd9a5fbe74f90d5060909d5d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641042"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Modello di risorsa per la funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra il modello di risorse per la funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima). Vengono illustrati i parametri che supportano il backup continuo e le risorse che possono essere ripristinate nell'API di Azure Cosmos DB per gli account SQL e MongoDB.

## <a name="database-accounts-resource-model"></a>Modello di risorsa dell'account di database

Il modello di risorse dell'account del database viene aggiornato con alcune proprietà aggiuntive per supportare i nuovi scenari di ripristino. Queste proprietà sono **BackupPolicy, CreateMode e RestoreParameters.**

### <a name="backuppolicy"></a>BackupPolicy

Una nuova proprietà nel criterio di backup a livello di account denominato `Type` sotto `backuppolicy` parametro Abilita il backup continuo e le funzionalità di ripristino temporizzato. Questa modalità è denominata **backup continuo**. Nell'anteprima pubblica è possibile impostare questa modalità solo quando si crea l'account. Dopo l'abilitazione, tutti i contenitori e i database creati in questo account disporranno di funzionalità di backup continuo e ripristino temporizzato abilitate per impostazione predefinita.

> [!NOTE]
> Attualmente la funzionalità di ripristino temporizzato è disponibile in anteprima pubblica ed è disponibile per Azure Cosmos DB API per MongoDB e per gli account SQL. Dopo aver creato un account con la modalità continua, non è possibile passare a una modalità periodica.

### <a name="createmode"></a>CreateMode

Questa proprietà indica il modo in cui è stato creato l'account. I valori possibili sono *default* e *Restore*. Per eseguire un ripristino, impostare questo valore su *Ripristina* e specificare i valori appropriati nella `RestoreParameters` Proprietà.

### <a name="restoreparameters"></a>RestoreParameters

La `RestoreParameters` risorsa contiene i dettagli dell'operazione di ripristino, tra cui, l'ID dell'account, il tempo di ripristino e le risorse che devono essere ripristinate.

|Nome proprietà |Descrizione  |
|---------|---------|
|restoreMode  | La modalità di ripristino dovrebbe essere *PointInTime* |
|restoreSource   |  ID istanza dell'account di origine da cui verrà avviato il ripristino.       |
|restoreTimestampInUtc  | Data e ora in formato UTC in cui deve essere ripristinato l'account. |
|databasesToRestore   | Elenco di `DatabaseRestoreSource` oggetti per specificare i database e i contenitori da ripristinare. Se questo valore è vuoto, viene ripristinato l'intero account.   |

**DatabaseRestoreResource** : ogni risorsa rappresenta un singolo database e tutte le raccolte nel database.

|Nome proprietà |Descrizione  |
|---------|---------|
|databaseName | Il nome del database. |
| collectionNames| Elenco di contenitori in questo database |

### <a name="sample-resource"></a>Risorsa di esempio

Il codice JSON seguente è una risorsa account del database di esempio con backup continuo abilitato:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Risorse ripristinabili

È disponibile un set di nuove risorse e API che consentono di individuare le informazioni critiche sulle risorse, che possono essere ripristinate, le posizioni da cui possono essere ripristinate e i timestamp per l'esecuzione delle operazioni principali su queste risorse.

> [!NOTE]
> Per tutte le API usate per enumerare queste risorse sono necessarie le autorizzazioni seguenti:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Account del database ripristinabile

Questa risorsa contiene un'istanza dell'account di database che può essere ripristinata. L'account di database può essere un account eliminato o attivo. Contiene informazioni che consentono di trovare l'account del database di origine che si desidera ripristinare.

|Nome proprietà |Descrizione  |
|---------|---------|
| ID | Identificatore univoco della risorsa. |
| accountName | Nome dell'account del database globale. |
| creationTime | Ora in formato UTC in cui è stato creato l'account.  |
| deletionTime | Ora in formato UTC in cui l'account è stato eliminato.  Questo valore è vuoto se l'account è Live. |
| apiType | Tipo di API dell'account Azure Cosmos DB. |
| restorableLocations | Elenco di percorsi in cui è esistito l'account. |
| restorableLocations: LocationName | Nome dell'area geografica dell'account locale. |
| restorableLocations: regionalDatabaseAccountInstanceI | GUID dell'account a livello di area. |
| restorableLocations: creationTime | Ora in formato UTC in cui è stato creato l'account a livello di area.|
| restorableLocations: deletionTime | Ora in formato UTC in cui è stato eliminato l'account a livello di area. Questo valore è vuoto se l'account locale è Live.|

Per ottenere un elenco di tutti gli account ripristinabili, vedere [account di database ripristinabili-elenco](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorabledatabaseaccounts/list) o [account di database ripristinabili-elenchi per località](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorabledatabaseaccounts/listbylocation) .

### <a name="restorable-sql-database"></a>Database SQL ripristinabile

Ogni risorsa contiene informazioni di un evento di mutazione, ad esempio la creazione e l'eliminazione che si sono verificate nel database SQL. Queste informazioni possono essere utili negli scenari in cui il database è stato eliminato accidentalmente e se è necessario individuare il momento in cui si è verificato l'evento.

|Nome proprietà |Descrizione  |
|---------|---------|
| eventTimestamp | Ora in formato UTC in cui il database viene creato o eliminato. |
| ownerId | Nome del database SQL. |
| ownerResourceId | ID risorsa del database SQL|
| operationType | Tipo di operazione di questo evento di database. Ecco i valori possibili:<br/><ul><li>Creazione: evento di creazione del database</li><li>Elimina: evento di eliminazione del database</li><li>Sostituisci: evento di modifica del database</li><li>SystemOperation: evento di modifica del database attivato dal sistema. Questo evento non è stato avviato dall'utente</li></ul> |
| database |Proprietà del database SQL al momento dell'evento|

Per ottenere un elenco di tutte le mutazioni del database, vedere l'articolo relativo ai [database SQL ripristinabili-elenco](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqldatabases/list) .

### <a name="restorable-sql-container"></a>Contenitore SQL ripristinabile

Ogni risorsa contiene informazioni di un evento di mutazione, ad esempio la creazione e l'eliminazione che si sono verificate nel contenitore SQL. Queste informazioni possono essere utili negli scenari in cui il contenitore è stato modificato o eliminato e se è necessario individuare il momento in cui si è verificato l'evento.

|Nome proprietà |Descrizione  |
|---------|---------|
| eventTimestamp    | Ora in formato UTC in cui si è verificato questo evento contenitore.|
| ownerId| Nome del contenitore SQL.|
| ownerResourceId   | ID risorsa del contenitore SQL.|
| operationType | Tipo di operazione di questo evento contenitore. Ecco i valori possibili: <br/><ul><li>Creazione: evento di creazione del contenitore</li><li>Elimina: evento di eliminazione del contenitore</li><li>Sostituisci: evento di modifica del contenitore</li><li>SystemOperation: evento di modifica del contenitore attivato dal sistema. Questo evento non è stato avviato dall'utente</li></ul> |
| contenitore | Proprietà del contenitore SQL al momento dell'evento.|

Per ottenere un elenco di tutte le mutazioni del contenitore nello stesso database, vedere l'articolo relativo all' [elenco di contenitori SQL ripristinabili](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqlcontainers/list) .

### <a name="restorable-sql-resources"></a>Risorse SQL ripristinabili

Ogni risorsa rappresenta un singolo database e tutti i contenitori in tale database.

|Nome proprietà |Descrizione  |
|---------|---------|
| databaseName  | Nome del database SQL.
| collectionNames   | Elenco di contenitori SQL in questo database.|

Per ottenere un elenco di database SQL e di una combinazione di contenitori presenti nell'account nel timestamp e nella posizione specificati, vedere l'articolo [risorse SQL ripristinabili-elenco](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqlresources/list) .

### <a name="restorable-mongodb-database"></a>Database MongoDB ripristinabile

Ogni risorsa contiene informazioni di un evento di mutazione, ad esempio la creazione e l'eliminazione che si sono verificate nel database MongoDB. Queste informazioni possono essere utili nello scenario in cui il database è stato eliminato accidentalmente ed è necessario che l'utente scopra quando si è verificato l'evento.

|Nome proprietà |Descrizione  |
|---------|---------|
|eventTimestamp| Ora in formato UTC in cui si è verificato l'evento di database.|
| ownerId| Nome del database MongoDB. |
| ownerResourceId   | ID risorsa del database MongoDB. |
| operationType |   Tipo di operazione di questo evento di database. Ecco i valori possibili:<br/><ul><li> Creazione: evento di creazione del database</li><li> Elimina: evento di eliminazione del database</li><li> Sostituisci: evento di modifica del database</li><li> SystemOperation: evento di modifica del database attivato dal sistema. Questo evento non è stato avviato dall'utente </li></ul> |

Per ottenere un elenco di tutte le mutazioni del database, vedere l'articolo relativo ai [database MongoDB ripristinabili-elenco](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbdatabases/list) .

### <a name="restorable-mongodb-collection"></a>Raccolta MongoDB ripristinabile

Ogni risorsa contiene informazioni di un evento di mutazione, ad esempio la creazione e l'eliminazione che si sono verificate nella raccolta MongoDB. Queste informazioni possono essere utili negli scenari in cui la raccolta è stata modificata o eliminata e l'utente deve individuare il momento in cui si è verificato l'evento.

|Nome proprietà |Descrizione  |
|---------|---------|
| eventTimestamp |Ora in formato UTC in cui si è verificato questo evento di raccolta. |
| ownerId| Nome della raccolta MongoDB. |
| ownerResourceId   | ID risorsa della raccolta MongoDB. |
| operationType |Tipo di operazione dell'evento della raccolta. Ecco i valori possibili:<br/><ul><li>Creazione: evento di creazione raccolta</li><li>Elimina: evento di eliminazione della raccolta</li><li>Sostituisci: evento di modifica della raccolta</li><li>SystemOperation: evento di modifica della raccolta attivato dal sistema. Questo evento non è stato avviato dall'utente</li></ul> |

Per ottenere un elenco di tutte le mutazioni del contenitore nello stesso database, vedere l'articolo [raccolte MongoDB ripristinabili-elenco](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbcollections/list) .

### <a name="restorable-mongodb-resources"></a>Risorse MongoDB ripristinabili

Ogni risorsa rappresenta un singolo database e tutte le raccolte in tale database.

|Nome proprietà |Descrizione  |
|---------|---------|
| databaseName  |Nome del database MongoDB. |
| collectionNames | Elenco di raccolte MongoDB in questo database. |

Per ottenere un elenco di tutte le combinazioni di database e raccolte MongoDB presenti nell'account nel timestamp e nella posizione specificati, vedere l'articolo [risorse MongoDB ripristinabili-elenco](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbresources/list) .

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
