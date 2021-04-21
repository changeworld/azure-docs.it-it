---
title: Ripristinare condivisioni file di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per ripristinare condivisioni file di Azure di cui è stato eseguito il backup nell'insieme di credenziali di Servizi di ripristino
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 2edc2281c29023bb8dfe0268f23eacfe081d413e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768514"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Ripristinare condivisioni file di Azure con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per la gestione delle risorse di Azure. È un ottimo strumento per la creazione di automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra come ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato Backup di Azure [tramite](./backup-overview.md) l'interfaccia della riga di comando di Azure. È anche possibile eseguire questa procedura con [Azure PowerShell](./backup-azure-afs-automation.md) o nel [portale di Azure](backup-afs.md).

Al termine di questo articolo si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:

* Visualizzare i punti di ripristino per una condivisione file di Azure di cui è stato eseguito il backup.
* Ripristinare una condivisione file di Azure completa.
* Ripristinare singoli file o cartelle.

>[!NOTE]
> Backup di Azure ora supporta il ripristino di più file o cartelle nel percorso originale o in un percorso alternativo tramite l'interfaccia della riga di comando di Azure. Per altre [informazioni, vedere](#restore-multiple-files-or-folders-to-original-or-alternate-location) la sezione Ripristinare più file o cartelle nel percorso originale o alternativo di questo documento.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già presente una condivisione file di Azure di cui è stato eseguito il backup Backup di Azure. Se non se ne ha una, vedere Eseguire il backup di condivisioni file di [Azure](backup-afs-cli.md) con l'interfaccia della riga di comando per configurare il backup per la condivisione file. Per questo articolo si usano le risorse seguenti:

| Condivisione file | Account di archiviazione | Region | Dettagli |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Origine originale di cui è stato eseguito il backup usando Backup di Azure |
| *azurefiles1* | *afaccount1* | EastUS | Origine di destinazione usata per il ripristino alternativo della posizione |

È possibile usare una struttura simile per le condivisioni file per provare i diversi tipi di ripristini illustrati in questo articolo.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Questa esercitazione richiede la versione 2.0.18 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Recuperare i punti di ripristino per la condivisione file di Azure

Usare il cmdlet [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) per elencare tutti i punti di ripristino per la condivisione file di cui è stato eseguito il backup.

L'esempio seguente recupera l'elenco dei punti di ripristino per la condivisione file *azurefiles* nell'account di archiviazione *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

È anche possibile eseguire il cmdlet precedente usando il nome descrittivo per il contenitore e l'elemento specificando i due parametri aggiuntivi seguenti:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Il set di risultati è un elenco di punti di ripristino con dettagli di tempo e coerenza per ogni punto di ripristino.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

**L'attributo Name** nell'output corrisponde al nome del punto di ripristino che può essere usato come valore per il **parametro --rp-name** nelle operazioni di ripristino.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Ripristino della condivisione completa tramite l'interfaccia della riga di comando di Azure

È possibile usare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o alternativo.

Definire i parametri seguenti per eseguire operazioni di ripristino:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il nome o il nome descrittivo del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nome della condivisione file originale di cui è stato eseguito il backup da usare per l'operazione di ripristino. Per recuperare il nome o il nome descrittivo dell'elemento di cui è stato eseguito il backup, usare [il comando az backup item list.](/cli/azure/backup/item#az_backup_item_list)

### <a name="restore-a-full-share-to-the-original-location"></a>Ripristinare una condivisione completa nel percorso originale

Quando si esegue il ripristino in un percorso originale, non è necessario specificare parametri correlati alla destinazione. È **necessario specificare solo Risolvi** conflitto.

L'esempio seguente usa il cmdlet [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) con la modalità di ripristino impostata su *originallocation* per ripristinare la condivisione file *azurefiles* nel percorso originale. Si usa il punto di ripristino 932883129628959823 ottenuto in Recuperare i punti di ripristino per la condivisione [file di Azure:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="restore-a-full-share-to-an-alternate-location"></a>Ripristinare una condivisione completa in un percorso alternativo

È possibile usare questa opzione per ripristinare una condivisione file in un percorso alternativo e mantenere la condivisione file originale così come è. Specificare i parametri seguenti per il ripristino in un percorso alternativo:

* **--target-storage-account:** account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share:** condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder:** cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **--resolve-conflict:** istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

L'esempio seguente usa [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) con modalità di ripristino *come alternatelocation* per ripristinare la condivisione file *azurefiles* nell'account di archiviazione *afsaccount* nella condivisione file *azurefiles1"* nell'account di archiviazione *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="item-level-recovery"></a>Ripristino a livello di elemento

È possibile usare questa opzione di ripristino per ripristinare singoli file o cartelle nel percorso originale o in un percorso alternativo.

Definire i parametri seguenti per eseguire operazioni di ripristino:

* **--container-name:** nome dell'account di archiviazione che ospita la condivisione file originale di cui è stato eseguito il backup. Per recuperare il nome o il nome descrittivo del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** nome della condivisione file originale di cui è stato eseguito il backup da usare per l'operazione di ripristino. Per recuperare il nome o il nome descrittivo dell'elemento di cui è stato eseguito il backup, usare [il comando az backup item list.](/cli/azure/backup/item#az_backup_item_list)

Specificare i parametri seguenti per gli elementi da ripristinare:

* **SourceFilePath:** percorso assoluto del file da ripristinare all'interno della condivisione file, sotto forma di stringa. Questo percorso è lo stesso percorso usato nei comandi [az storage file download](/cli/azure/storage/file#az_storage_file_download) o [az storage file show](/cli/azure/storage/file#az_storage_file_show) CLI.
* **SourceFileType:** scegliere se è selezionata una directory o un file. Accetta directory **o** **file**.
* **ResolveConflict:** istruzione in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Ripristinare singoli file o cartelle nel percorso originale

Usare il cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) con la modalità di ripristino impostata su *originallocation* per ripristinare file o cartelle specifici nel percorso originale.

L'esempio seguente ripristina *ilRestoreTest.txt* file nel percorso originale: la condivisione file *azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Ripristinare singoli file o cartelle in un percorso alternativo

Per ripristinare cartelle o file specifici in un percorso alternativo, usare il cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) con la modalità di ripristino impostata su *alternatelocation* e specificare i parametri correlati alla destinazione seguenti:

* **--target-storage-account:** account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **--target-file-share:** condivisione file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **--target-folder:** cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, assegnare il valore della cartella di destinazione come stringa vuota.

L'esempio seguente ripristina il file *RestoreTest.txt* originariamente presente nella condivisione file *azurefiles* in un percorso alternativo: la cartella *restoredata* nella condivisione file *azurefiles1* ospitata nell'account di archiviazione *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Ripristinare più file o cartelle nel percorso originale o alternativo

Per eseguire il ripristino per più elementi, passare il  valore per il parametro **source-file-path** come percorsi separati da spazi di tutti i file o le cartelle da ripristinare.

L'esempio seguente ripristina i *fileRestore.txt* e *AFS Report.docx* file nel percorso originale.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

L'output sarà simile al seguente:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di ripristino. Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

Se si desidera ripristinare più elementi in un percorso alternativo, usare il comando precedente specificando i parametri relativi alla destinazione, come illustrato nella sezione Ripristinare singoli file o cartelle in un [percorso](#restore-individual-files-or-folders-to-an-alternate-location) alternativo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire i [backup di condivisione file di Azure con l'interfaccia della riga di comando di Azure.](manage-afs-backup-cli.md)
