---
title: Usare il modello ARM per configurare il backup continuo e il ripristino temporizzato in Azure Cosmos DB.
description: Informazioni su come eseguire il provisioning di un account con backup continuo e ripristino dei dati usando modelli di Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 642c61414d882b9cfe83f585fda8ff5404e8834a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538477"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Configurare e gestire il backup continuo e il ripristino temporizzato (anteprima)-uso dei modelli di Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima) consente di eseguire il ripristino da una modifica accidentale all'interno di un contenitore, di ripristinare un account, un database o un contenitore eliminato o di eseguire il ripristino in qualsiasi area (in cui erano presenti backup). La modalità di backup continuo consente di eseguire il ripristino in qualsiasi momento negli ultimi 30 giorni.

Questo articolo descrive come eseguire il provisioning di un account con backup continuo e ripristino dei dati usando modelli di Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Effettuare il provisioning di un account con backup continuo

È possibile usare i modelli di Azure Resource Manager per distribuire un account Azure Cosmos DB con la modalità continua. Quando si definisce il modello per il provisioning di un account, includere il parametro "backupPolicy", come illustrato nell'esempio seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Successivamente, distribuire il modello usando Azure PowerShell o l'interfaccia della riga di comando. Nell'esempio seguente viene illustrato come distribuire il modello con un comando dell'interfaccia della riga di comando:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Eseguire il ripristino usando il modello di Gestione risorse

È anche possibile ripristinare un account usando Gestione risorse modello. Quando si definisce il modello, includere i parametri seguenti:

* Impostare il parametro "createMode" su "Restore"
* Definire "restoreParameters". si noti che il valore "restoreSource" viene estratto dall'output del `az cosmosdb restorable-database-account list` comando per l'account di origine. L'attributo ID istanza per il nome dell'account viene usato per eseguire il ripristino.
* Impostare il parametro "restoreMode" su "PointInTime" e configurare il valore "restoreTimestampInUtc".

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Successivamente, distribuire il modello usando Azure PowerShell o l'interfaccia della riga di comando. Nell'esempio seguente viene illustrato come distribuire il modello con un comando dell'interfaccia della riga di comando:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo usando l'interfaccia della riga di comando di [Azure](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md)o [portale di Azure](continuous-backup-restore-portal.md).
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.