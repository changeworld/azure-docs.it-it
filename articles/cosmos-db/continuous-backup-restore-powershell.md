---
title: Usare Azure PowerShell per configurare il backup continuo e il ripristino temporizzato nel Azure Cosmos DB.
description: Informazioni su come eseguire il provisioning di un account con backup continuo e ripristino dei dati usando Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381835"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Configurare e gestire il backup continuo e il ripristino temporizzato (anteprima)-uso di Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima) consente di eseguire il ripristino da una modifica accidentale all'interno di un contenitore, di ripristinare un account, un database o un contenitore eliminato o di eseguire il ripristino in qualsiasi area (in cui erano presenti backup). La modalità di backup continuo consente di eseguire il ripristino in qualsiasi momento negli ultimi 30 giorni.

Questo articolo descrive come eseguire il provisioning di un account con backup continuo e ripristino dei dati usando Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Installa Azure PowerShell

1. Eseguire il comando seguente da Azure PowerShell per installare il `Az.CosmosDB` modulo di anteprima, che contiene i comandi correlati al ripristino temporizzato:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Dopo aver installato i moduli, accedere ad Azure usando

   ```azurepowershell
   Connect-AzAccount
   ```

1. Selezionare una sottoscrizione specifica con il comando seguente:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Effettuare il provisioning di un account API SQL con backup continuo

Per eseguire il provisioning di un account con backup continuo, aggiungere un argomento `-BackupPolicyType Continuous` insieme al comando di provisioning regolare.

Il cmdlet seguente è un esempio di un account di scrittura in una singola area `pitracct2` con criteri di backup continuo creati nell'area *Stati Uniti occidentali* sotto il gruppo di risorse *Myrg* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Effettuare il provisioning di un account API MongoDB con backup continuo

Il cmdlet seguente è un esempio di account di backup continuo *pitracct2* creato nell'area *Stati Uniti occidentali* nel gruppo di risorse *Myrg* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Attivare un'operazione di ripristino

Il cmdlet seguente è un esempio di attivazione di un'operazione di ripristino con il comando Restore utilizzando l'account di origine, il percorso, il gruppo di risorse e il timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Esempio 1:** Ripristino dell'intero account:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Esempio 2:** Ripristino di raccolte e database specifici. Questo esempio Mostra come ripristinare le raccolte myCol1, myCol2 da myDB1 e l'intero database myDB2, che include tutti i contenitori sottoposti.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumerare le risorse ripristinabili per l'API SQL

I cmdlet di enumerazione consentono di individuare le risorse disponibili per il ripristino in diversi timestamp. Inoltre, forniscono anche un feed di eventi chiave per l'account ripristinabile, il database e le risorse del contenitore.

**Elencare tutti gli account che possono essere ripristinati nella sottoscrizione corrente**

Eseguire il `Get-AzCosmosDBRestorableDatabaseAccount` comando di PowerShell per elencare tutti gli account che possono essere ripristinati nella sottoscrizione corrente.

La risposta include tutti gli account di database (Live ed Deleted) che possono essere ripristinati e le aree da cui possono essere ripristinati.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Proprio come `CreationTime` o `DeletionTime` per l'account, esiste anche un oggetto `CreationTime` o `DeletionTime` per l'area. Questi tempi consentono di scegliere l'area corretta e un intervallo di tempo valido per il ripristino in tale area.

**Elencare tutte le versioni dei database SQL in un account di database attivo**

Elencando tutte le versioni dei database è possibile scegliere il database corretto in uno scenario in cui il tempo effettivo di esistenza del database è sconosciuto.

Eseguire il comando di PowerShell seguente per elencare tutte le versioni dei database. Questo comando funziona solo con gli account Live. I `DatabaseAccountInstanceId` parametri e `LocationName` vengono ottenuti dalle `name` proprietà e `location` nella risposta del `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet. L' `DatabaseAccountInstanceId` attributo fa riferimento alla `instanceId` proprietà dell'account del database di origine in fase di ripristino:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Elencare tutte le versioni dei contenitori SQL di un database in un account di database attivo.**

Usare il comando seguente per elencare tutte le versioni dei contenitori SQL. Questo comando funziona solo con gli account Live. Il `DatabaseRid` parametro è l'oggetto `ResourceId` del database che si desidera ripristinare. È il valore dell' `ownerResourceid` attributo trovato nella risposta del `Get-AzCosmosdbSqlRestorableDatabase` cmdlet. La risposta include anche un elenco di operazioni eseguite su tutti i contenitori all'interno del database.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Trovare i database o i contenitori che possono essere ripristinati in qualsiasi timestamp specificato**

Usare il comando seguente per ottenere l'elenco di database o contenitori che è possibile ripristinare in qualsiasi timestamp specificato. Questo comando funziona solo con gli account Live.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Enumerare le risorse ripristinabili per MongoDB

I comandi di enumerazione descritti di seguito consentono di individuare le risorse disponibili per il ripristino in diversi timestamp. Inoltre, forniscono anche un feed di eventi chiave per l'account ripristinabile, il database e le risorse del contenitore. Questi comandi funzionano solo per gli account Live e sono simili ai comandi dell'API SQL, ma con `MongoDB` il nome del comando anziché `sql` .

**Elencare tutte le versioni dei database MongoDB in un account di database attivo**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Elencare tutte le versioni delle raccolte MongoDB di un database in un account di database attivo**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Elencare tutte le risorse di un account di database MongoDB disponibili per il ripristino in un timestamp e in un'area specificati**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo usando l'interfaccia della riga di comando di [Azure](continuous-backup-restore-command-line.md), [Gestione risorse](continuous-backup-restore-template.md)o [portale di Azure](continuous-backup-restore-portal.md).
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
