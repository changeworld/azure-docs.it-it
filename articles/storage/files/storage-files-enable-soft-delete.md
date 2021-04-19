---
title: Abilitare l'eliminazione temporanea - Condivisioni file di Azure
description: Informazioni su come abilitare la funzionalità di eliminazione temporanea per le condivisioni file di Azure per il ripristino dei dati e la prevenzione dell'eliminazione accidentale.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717024"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Abilitare l'eliminazione temporanea in condivisioni file di Azure

Archiviazione di Azure offre l'eliminazione temporanea per le condivisioni file, per consentire di ripristinare più facilmente i dati nel caso in cui vengano erroneamente eliminati da un'applicazione o da un utente con un altro account di archiviazione. Per altre informazioni sull'eliminazione temporanea, vedere [Come evitare l'eliminazione accidentale di condivisioni file di Azure](storage-files-prevent-file-share-deletion.md).

Le sezioni seguenti illustrano come abilitare e usare l'eliminazione temporanea per le condivisioni file di Azure in un account di archiviazione esistente:

# <a name="portal"></a>[Portale](#tab/azure-portal)

## <a name="getting-started"></a>Guida introduttiva

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione e selezionare **Condivisioni file** in **Archiviazione dati.**
1. Selezionare **Abilitato accanto** a Eliminazione **soft.**
1. Selezionare **Abilitato per** Eliminazione soft per tutte le **condivisioni file.**
1. Selezionare **Periodo di conservazione della condivisione file in giorni** e immettere una scelta numerica.
1. Selezionare **Salva** per confermare le impostazioni di conservazione dei dati.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Screenshot del riquadro delle impostazioni di eliminazione soft dell'account di archiviazione. Evidenziazione della sezione di eliminazione soft delle condivisioni file, abilitazione dell'attivazione/disattivazione, impostazione di un periodo di conservazione e salvataggio. In questo modo si abiliterà l'eliminazione per tutte le condivisioni file nell'account di archiviazione.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet di eliminazione software sono disponibili nella versione 2.1.3 e nelle versioni più nuove del modulo dell'interfaccia della riga [di comando di Azure.](/cli/azure/install-azure-cli)

## <a name="getting-started-with-cli"></a>Introduzione all'interfaccia della riga di comando

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client file. Nell'esempio seguente viene abilitata l'eliminazione temporanea per tutte le condivisioni file in un account di archiviazione:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Per verificare se l'eliminazione temporanea è abilitata e visualizzarne i criteri di conservazione, usare il comando seguente:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Prerequisito

I cmdlet di eliminazione software sono disponibili nelle versioni 4.8.0 e successive del modulo Az.Storage. 

## <a name="getting-started-with-powershell"></a>Introduzione a PowerShell

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client file. Nell'esempio seguente viene abilitata l'eliminazione temporanea per tutte le condivisioni file in un account di archiviazione:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Per verificare se l'eliminazione temporanea è abilitata e visualizzarne i criteri di conservazione, usare il comando seguente:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Ripristinare una condivisione file eliminata temporaneamente

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ripristinare una condivisione file eliminata temporaneamente:

1. Passare all'account di archiviazione e selezionare **Condivisioni file**.
1. Nel pannello delle condivisioni file, abilitare **Mostra condivisioni eliminate** per visualizzare le condivisioni eliminate temporaneamente.

    Verranno visualizzate le condivisioni attualmente con stato **Eliminata**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Se la colonna Stato, ovvero la colonna accanto alla colonna Nome, è impostata su Eliminata, la condivisione file si trova in uno stato di eliminazione temporanea e verrà eliminata definitivamente dopo il periodo di conservazione specificato.":::

1. Selezionare la condivisione, quindi selezionare **Annulla eliminazione**: la condivisione verrà ripristinata.

    È possibile verificare che la condivisione è stata ripristinata perché lo stato passa ad **Attiva**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Se la colonna Stato, ovvero la colonna accanto alla colonna Nome, è impostata su Attiva, la condivisione file è stata ripristinata.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet di eliminazione software sono disponibili nella versione 2.1.3 dell'interfaccia della riga di comando di Azure. Per ripristinare una condivisione file eliminata automaticamente, è prima necessario ottenere `--deleted-version` il valore della condivisione. Per ottenere tale valore, usare il comando seguente per elencare tutte le condivisioni eliminate per l'account di archiviazione:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Dopo aver identificato la condivisione da ripristinare, è possibile usarla con il comando seguente per ripristinarla:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet di eliminazione software sono disponibili nelle versioni 4.8.0 e successive del modulo Az.Storage. Per ripristinare una condivisione file eliminata automaticamente, è prima necessario ottenere `-DeletedShareVersion` il valore della condivisione. Per ottenere tale valore, usare il comando seguente per elencare tutte le condivisioni eliminate per l'account di archiviazione:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Dopo aver identificato la condivisione da ripristinare, è possibile usarla con il comando seguente per ripristinarla:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Disabilitare l'eliminazione temporanea

Se si vuole interrompere l'uso dell'eliminazione soft, seguire queste istruzioni. Per eliminare definitivamente una condivisione file eliminata temporaneamente, è necessario annullarla, disabilitarla e quindi eliminarla di nuovo. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione e selezionare **Condivisioni file** in **Archiviazione dati.**
1. Selezionare il collegamento accanto a **Eliminazione soft.**
1. Selezionare **Disabilitato per** Eliminazione soft per tutte le **condivisioni file.**
1. Selezionare **Salva** per confermare le impostazioni di conservazione dei dati.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Se si disabilita l'eliminazione temporanea, sarà possibile eliminare immediatamente e definitivamente tutte le condivisioni file nell'account di archiviazione.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet di eliminazione software sono disponibili nella versione 2.1.3 dell'interfaccia della riga di comando di Azure. Per disabilitare l'eliminazione temporanea nell'account di archiviazione è possibile usare il comando seguente:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet di eliminazione software sono disponibili nelle versioni 4.8.0 e successive del modulo Az.Storage. Per disabilitare l'eliminazione temporanea nell'account di archiviazione è possibile usare il comando seguente:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su un'altra forma di protezione e ripristino dei dati, vedere l'articolo Panoramica degli snapshot di [condivisione per File di Azure](storage-snapshots-files.md).