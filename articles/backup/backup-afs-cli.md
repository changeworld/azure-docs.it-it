---
title: Eseguire il backup di condivisioni file di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per eseguire il backup di condivisioni file di Azure nell'insieme di credenziali di Servizi di ripristino
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773622"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Eseguire il backup di condivisioni file di Azure con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure offre un'esperienza da riga di comando per la gestione delle risorse di Azure. È un ottimo strumento per la creazione di automazione personalizzata per l'uso delle risorse di Azure. Questo articolo illustra in dettaglio come eseguire il backup di condivisioni file di Azure con l'interfaccia della riga di comando di Azure. È anche possibile eseguire questa procedura con [Azure PowerShell](./backup-azure-afs-automation.md) o nel [portale di Azure](backup-afs.md).

Al termine di questa esercitazione si apprenderà come eseguire le operazioni seguenti con l'interfaccia della riga di comando di Azure:

* Creare un insieme di credenziali di Servizi di ripristino
* Abilitare il backup per le condivisioni file di Azure
* Attivare un backup su richiesta per le condivisioni file

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0.18 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di Servizi di ripristino è un'entità che offre una visualizzazione e una funzionalità di gestione consolidate per tutti gli elementi di backup. Quando viene eseguito, il processo di backup per una risorsa protetta crea un punto di ripristino all'interno dell'insieme di credenziali dei servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino:

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [az group create](/cli/azure/group#az_group_create) . In questa esercitazione viene creato il nuovo gruppo di risorse *azurefiles nell'area* Stati Uniti orientali.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Usare il cmdlet [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    L'esempio seguente crea un insieme di credenziali di Servizi di ripristino denominato *azurefilesvault* nell'area Stati Uniti orientali.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Abilitare il backup per le condivisioni file di Azure

In questa sezione si presuppone che sia già presente una condivisione file di Azure per cui si vuole configurare il backup. Se non è disponibile, creare una condivisione file di Azure usando il [comando az storage share create.](/cli/azure/storage/share#az_storage_share_create)

Per abilitare il backup per le condivisioni file, è necessario creare criteri di protezione che definiscono quando viene eseguito un processo di backup e per quanto tempo vengono archiviati i punti di ripristino. È possibile creare un criterio di backup usando il cmdlet [az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create)

L'esempio seguente usa il cmdlet [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) per abilitare il backup per la condivisione file *azurefiles* nell'account di archiviazione *afsaccount* usando i criteri di backup schedule *1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

**L'attributo Name** nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione **di abilitazione del backup.** Per tenere traccia dello stato del processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Attivare un backup su richiesta per la condivisione file

Se si vuole attivare un backup su richiesta per la condivisione file anziché attendere l'esecuzione del processo da parte dei criteri di backup all'ora pianificata, usare il cmdlet [az backup protection backup-now.](/cli/azure/backup/protection#az_backup_protection_backup_now)

È necessario definire i parametri seguenti per attivare un backup su richiesta:

* **--container-name è** il nome dell'account di archiviazione che ospita la condivisione file. Per recuperare il **nome o** il **nome descrittivo** del contenitore, usare il [comando az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name** è il nome della condivisione file per cui si vuole attivare un backup su richiesta. Per recuperare il **nome o** il **nome descrittivo** dell'elemento di cui è stato eseguito il backup, usare [il comando az backup item list.](/cli/azure/backup/item#az_backup_item_list)
* **--retain-until specifica** la data fino a quando si vuole mantenere il punto di ripristino. Il valore deve essere impostato nel formato ora UTC (gg-mm-aaaa).

L'esempio seguente attiva un backup su richiesta per la condivisione file *azurefiles* nell'account di archiviazione *afsaccount* con conservazione fino al *20-01-2020.*

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

**L'attributo** Name nell'output corrisponde al nome del processo creato dal servizio di backup per l'operazione di "backup su richiesta". Per tenere traccia dello stato di un processo, usare il cmdlet [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come ripristinare [condivisioni file di Azure con l'interfaccia della riga di comando](restore-afs-cli.md)
* Informazioni su come gestire i [backup di condivisione file di Azure con l'interfaccia della riga di comando](manage-afs-backup-cli.md)
