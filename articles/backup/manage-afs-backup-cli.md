---
title: Gestire i backup di condivisione file di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire e monitorare le condivisioni file di Azure di cui è stato eseguito il backup Backup di Azure.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773604"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Gestire i backup di condivisione file di Azure con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per la gestione delle risorse di Azure. È un ottimo strumento per la creazione di automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra come eseguire attività per la gestione e il monitoraggio delle condivisioni file di Azure di cui viene eseguito il backup [Backup di Azure](./backup-overview.md). È anche possibile eseguire questi passaggi con [l'portale di Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già presente una condivisione file di Azure di cui è stato eseguito il backup [Backup di Azure](./backup-overview.md). Se non se ne ha una, vedere Eseguire il backup di condivisioni file di [Azure](backup-afs-cli.md) con l'interfaccia della riga di comando per configurare il backup per le condivisioni file. Per questo articolo si usano le risorse seguenti:
   -  **Gruppo di risorse:** *azurefiles*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Account di archiviazione**: *afsaccount*
   -  **Condivisione file**: *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Questa esercitazione richiede la versione 2.0.18 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="monitor-jobs"></a>Monitorare i processi

Quando si attivano operazioni di backup o ripristino, il servizio di backup crea un processo per il rilevamento. Per monitorare i processi completati o attualmente in esecuzione, usare il cmdlet [az backup job list.](/cli/azure/backup/job#az_backup_job_list) Con l'interfaccia della riga di comando è anche possibile [sospendere un processo attualmente in esecuzione](/cli/azure/backup/job#az_backup_job_stop) o attendere il completamento di un [processo](/cli/azure/backup/job#az_backup_job_wait).

Nell'esempio seguente viene visualizzato lo stato dei processi di backup per l'insieme di credenziali di Servizi di ripristino *azurefilesvault:*

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modifica dei criteri

È possibile modificare i criteri di backup per modificare la frequenza di backup o l'intervallo di conservazione usando [az backup item set-policy](/cli/azure/backup/item#az_backup_item_set_policy).

Per modificare i criteri, definire i parametri seguenti:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome o** il **nome descrittivo** del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--name:** nome della condivisione file per cui si desidera modificare i criteri. Per recuperare il **nome o** il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare [il comando az backup item list.](/cli/azure/backup/item#az_backup_item_list)
* **--policy-name:** nome dei criteri di backup da impostare per la condivisione file. È possibile usare [az backup policy list per](/cli/azure/backup/policy#az_backup_policy_list) visualizzare tutti i criteri per l'insieme di credenziali.

L'esempio seguente imposta i criteri di backup *schedule2* per la condivisione file *azurefiles* presente nell'account di archiviazione *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

È anche possibile eseguire il comando precedente usando i nomi descrittivi per il contenitore e l'elemento specificando i due parametri aggiuntivi seguenti:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di modifica dei criteri. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="stop-protection-on-a-file-share"></a>Interrompere la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri *ed eliminare tutti* i punti di ripristino.
* Arrestare tutti i processi di backup *futuri, ma lasciare* i punti di ripristino.

L'uscita dei punti di ripristino nell'archiviazione potrebbe essere associata a un costo, perché gli snapshot sottostanti creati da Backup di Azure verranno mantenuti. Il vantaggio di lasciare i punti di ripristino è l'opzione per ripristinare la condivisione file in un secondo momento, se necessario. Per informazioni sul costo di uscita dei punti di ripristino, vedere i dettagli [sui prezzi.](https://azure.microsoft.com/pricing/details/storage/files) Se si sceglie di eliminare tutti i punti di ripristino, non è possibile ripristinare la condivisione file.

Per arrestare la protezione per la condivisione file, definire i parametri seguenti:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome o** il **nome descrittivo** del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nome della condivisione file per cui si vuole arrestare la protezione. Per recuperare il **nome o** il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare [il comando az backup item list.](/cli/azure/backup/item#az_backup_item_list)

### <a name="stop-protection-and-retain-recovery-points"></a>Arrestare la protezione e mantenere i punti di ripristino

Per arrestare la protezione durante la conservazione dei dati, usare il cmdlet [az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

L'esempio seguente arresta la protezione per la condivisione file *azurefiles,* ma mantiene tutti i punti di ripristino.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

È anche possibile eseguire il comando precedente usando il nome descrittivo per il contenitore e l'elemento specificando i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**L'attributo Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di arresto della protezione. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="stop-protection-without-retaining-recovery-points"></a>Arrestare la protezione senza mantenere i punti di ripristino

Per arrestare la protezione senza mantenere i punti di ripristino, usare il cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) con l'opzione **delete-backup-data** impostata su **true.**

L'esempio seguente arresta la protezione per la condivisione file *azurefiles* senza mantenere i punti di ripristino.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

È anche possibile eseguire il comando precedente usando il nome descrittivo per il contenitore e l'elemento specificando i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Riprendere la protezione in una condivisione file

Se la protezione per una condivisione file di Azure è stata arrestata ma i punti di ripristino sono stati mantenuti, è possibile riprendere la protezione in un secondo momento. Se non si mantengono i punti di ripristino, non è possibile riprendere la protezione.

Per riprendere la protezione per la condivisione file, definire i parametri seguenti:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome o** il **nome descrittivo** del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nome della condivisione file per cui si vuole riprendere la protezione. Per recuperare il **nome o** il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare [il comando az backup item list.](/cli/azure/backup/item#az_backup_item_list)
* **--policy-name:** nome dei criteri di backup per cui si vuole riprendere la protezione per la condivisione file.

L'esempio seguente usa il cmdlet [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) per riprendere la protezione per la condivisione file *azurefiles* usando i criteri di backup *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

È anche possibile eseguire il comando precedente usando il nome descrittivo per il contenitore e l'elemento specificando i due parametri aggiuntivi seguenti:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripresa della protezione. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="unregister-a-storage-account"></a>Annullare la registrazione di un account di archiviazione

Se si vogliono proteggere le condivisioni file in un determinato account di archiviazione usando un insieme di credenziali di Servizi di ripristino diverso, arrestare prima di tutto la protezione per tutte le condivisioni [file](#stop-protection-on-a-file-share) in tale account di archiviazione. Annullare quindi la registrazione dell'account dall'insieme di credenziali di Servizi di ripristino attualmente usato per la protezione.

È necessario specificare un nome di contenitore per annullare la registrazione dell'account di archiviazione. Per recuperare il **nome o** il **nome descrittivo** del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)

L'esempio seguente annulla la registrazione dell'account di archiviazione *afsaccount* da *azurefilesvault* usando il cmdlet [az backup container unregister.](/cli/azure/backup/container#az_backup_container_unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

È anche possibile eseguire il cmdlet precedente usando il nome descrittivo per il contenitore specificando il parametro aggiuntivo seguente:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere Risolvere i [problemi relativi al backup delle condivisioni file di Azure.](troubleshoot-azure-files.md)
