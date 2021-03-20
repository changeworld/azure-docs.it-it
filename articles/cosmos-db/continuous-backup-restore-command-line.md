---
title: Usare l'interfaccia della riga di comando di Azure per configurare il backup continuo e il ripristino temporizzato in Azure Cosmos DB.
description: Informazioni su come eseguire il provisioning di un account con backup continuo e ripristino dei dati usando l'interfaccia della riga
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377330"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Configurare e gestire il backup continuo e il ripristino temporizzato (anteprima) con l'interfaccia della riga di comando di Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima) consente di eseguire il ripristino da una modifica accidentale all'interno di un contenitore, di ripristinare un account, un database o un contenitore eliminato o di eseguire il ripristino in qualsiasi area (in cui erano presenti backup). La modalità di backup continuo consente di eseguire il ripristino in qualsiasi momento negli ultimi 30 giorni.

Questo articolo descrive come eseguire il provisioning di un account con backup continuo e ripristino dei dati usando l'interfaccia della riga di comando

## <a name="install-azure-cli"></a><a id="install"></a>Installa interfaccia della riga di comando di Azure

1. Installare l'ultima versione dell'interfaccia della riga di comando di Azure

   * Installare la versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o della versione successiva a 2.17.1.
   * Se è già stata installata `az upgrade` l'interfaccia della riga di comando, eseguire il comando per eseguire l'aggiornamento alla versione più recente. Questo comando funzionerà solo con la versione CLI superiore a 2,11. Se si dispone di una versione precedente, usare il collegamento precedente per installare la versione più recente.

1. Installare l' `cosmosdb-preview` estensione cli.

   * I comandi di ripristino temporizzato sono disponibili in `cosmosdb-preview` estensione.
   * È possibile installare questa estensione eseguendo il comando seguente: `az extension add --name cosmosdb-preview`
   * È possibile disinstallare questa estensione eseguendo il comando seguente: `az extension remove --name cosmosdb-preview`

1. Eseguire l'accesso e selezionare la sottoscrizione

   * Accedere al proprio account Azure con il `az login` comando.
   * Selezionare la sottoscrizione necessaria tramite il `az account set -s <subscriptionguid>` comando.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Effettuare il provisioning di un account API SQL con backup continuo

Per eseguire il provisioning di un account API SQL con backup continuo, `--backup-policy-type Continuous` è necessario passare un argomento aggiuntivo insieme al comando di provisioning normale. Il comando seguente è un esempio di un account di scrittura con area singola denominato `pitracct2` con criteri di backup continuo creati nell'area *Stati Uniti occidentali* sotto il gruppo di risorse *Myrg* :

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Effettuare il provisioning di un'API Azure Cosmos DB per l'account MongoDB con backup continuo

Il comando seguente mostra un esempio di un account di scrittura in una singola area denominato `pitracct3` con criteri di backup continuo creati nell'area *Stati Uniti occidentali* sotto il gruppo di risorse *Myrg* :

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Attivare un'operazione di ripristino con l'interfaccia della riga di comando

Il modo più semplice per attivare un ripristino consiste nell'emettere il comando Restore con il nome dell'account di destinazione, l'account di origine, il percorso, il gruppo di risorse, il timestamp (in UTC) e, facoltativamente, i nomi di database e di contenitori. Di seguito sono riportati alcuni esempi per attivare l'operazione di ripristino:

1. Consente di creare un nuovo account di Azure Cosmos DB ripristinando da un account esistente.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Consente di creare un nuovo account Azure Cosmos DB ripristinando solo i database e i contenitori selezionati da un account di database esistente.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumerare le risorse ripristinabili per l'API SQL

I comandi di enumerazione descritti di seguito consentono di individuare le risorse disponibili per il ripristino in diversi timestamp. Inoltre, forniscono anche un feed di eventi chiave per l'account ripristinabile, il database e le risorse del contenitore.

**Elencare tutti gli account che possono essere ripristinati nella sottoscrizione corrente**

Eseguire il comando CLI seguente per elencare tutti gli account che possono essere ripristinati nella sottoscrizione corrente

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

La risposta include tutti gli account di database (Live ed Deleted) che possono essere ripristinati e le aree da cui è possibile ripristinare:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Proprio come `CreationTime` o `DeletionTime` per l'account, esiste anche un oggetto `CreationTime` o `DeletionTime` per l'area. Questi tempi consentono di scegliere l'area corretta e un intervallo di tempo valido per il ripristino in tale area.

**Elencare tutte le versioni dei database in un account di database attivo**

Elencando tutte le versioni dei database è possibile scegliere il database corretto in uno scenario in cui il tempo effettivo di esistenza del database è sconosciuto.

Eseguire il comando CLI seguente per elencare tutte le versioni dei database. Questo comando funziona solo con gli account Live. I `instanceId` parametri e `location` vengono ottenuti dalle `name` proprietà e `location` nella risposta del `az cosmosdb restorable-database-account list` comando. L'attributo instanceId è anche una proprietà dell'account del database di origine in fase di ripristino:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Questo output del comando ora indica quando è stato creato ed eliminato un database.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Elencare tutte le versioni dei contenitori SQL di un database in un account di database attivo**

Usare il comando seguente per elencare tutte le versioni dei contenitori SQL. Questo comando funziona solo con gli account Live. Il `databaseRid` parametro è l'oggetto `ResourceId` del database che si desidera ripristinare. È il valore dell' `ownerResourceid` attributo trovato nella risposta del `az cosmosdb sql restorable-database list` comando.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Questo output del comando Mostra l'elenco delle operazioni eseguite su tutti i contenitori all'interno del database:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Trovare i database o i contenitori che possono essere ripristinati in qualsiasi timestamp specificato**

Usare il comando seguente per ottenere l'elenco di database o contenitori che è possibile ripristinare in qualsiasi timestamp specificato. Questo comando funziona solo con gli account Live.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Enumerare le risorse ripristinabili per l'account dell'API MongoDB

I comandi di enumerazione descritti di seguito consentono di individuare le risorse disponibili per il ripristino in diversi timestamp. Inoltre, forniscono anche un feed di eventi chiave per l'account ripristinabile, il database e le risorse del contenitore. Analogamente a quanto avviene con l'API SQL, è possibile usare il `az cosmosdb` comando ma con `mongodb` come parametro anziché con `sql` . Questi comandi funzionano solo per gli account Live.

**Elencare tutte le versioni dei database MongoDB in un account di database attivo**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Elencare tutte le versioni delle raccolte MongoDB di un database in un account di database attivo**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Elencare tutte le risorse di un account di database MongoDB disponibili per il ripristino in un timestamp e in un'area specificati**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
