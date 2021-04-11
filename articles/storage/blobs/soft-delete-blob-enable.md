---
title: Abilitare l'eliminazione temporanea per i BLOB
titleSuffix: Azure Storage
description: Abilitare l'eliminazione temporanea per i BLOB per proteggere i dati BLOB da eliminazioni accidentali o sovrascritture.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554119"
---
# <a name="enable-soft-delete-for-blobs"></a>Abilitare l'eliminazione temporanea per i BLOB

L'eliminazione temporanea BLOB protegge un singolo BLOB e le relative versioni, gli snapshot e i metadati da eliminazioni accidentali o sovrascritture gestendo i dati eliminati nel sistema per un periodo di tempo specificato. Durante il periodo di memorizzazione, è possibile ripristinare lo stato del BLOB al momento dell'eliminazione. Una volta scaduto il periodo di conservazione, il BLOB viene eliminato definitivamente. Per altre informazioni sull'eliminazione temporanea dei BLOB, vedere [eliminazione temporanea per i BLOB](soft-delete-blob-overview.md).

L'eliminazione temporanea dei BLOB fa parte di una strategia di protezione dei dati completa per i dati BLOB. Per ulteriori informazioni sulle raccomandazioni di Microsoft per la protezione dei dati, vedere [panoramica sulla protezione dei dati](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Abilita eliminazione temporanea BLOB

L'eliminazione temporanea BLOB è disabilitata per impostazione predefinita per un nuovo account di archiviazione. È possibile abilitare o disabilitare l'eliminazione temporanea per un account di archiviazione in qualsiasi momento usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per abilitare l'eliminazione temporanea del BLOB per l'account di archiviazione usando il portale di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/) passare all'account di archiviazione.
1. Individuare l'opzione **protezione dati** in **servizio BLOB**.
1. Nella sezione **ripristino** selezionare **Attiva eliminazione temporanea per i BLOB**.
1. Specificare un periodo di conservazione compreso tra 1 e 365 giorni. Microsoft consiglia un periodo di conservazione minimo di sette giorni.
1. Salvare le modifiche.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Screenshot che illustra come abilitare l'eliminazione temporanea nella portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare l'eliminazione temporanea BLOB con PowerShell, chiamare il comando [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) , specificando il periodo di conservazione in giorni.

L'esempio seguente abilita l'eliminazione temporanea di BLOB e imposta il periodo di memorizzazione su sette giorni. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Per verificare le impostazioni correnti per l'eliminazione temporanea del BLOB, chiamare il comando [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Per abilitare l'eliminazione temporanea BLOB con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account Blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) , specificando il periodo di conservazione in giorni.

L'esempio seguente abilita l'eliminazione temporanea di BLOB e imposta il periodo di memorizzazione su sette giorni. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Per verificare le impostazioni correnti per l'eliminazione temporanea del BLOB, chiamare il comando [AZ storage account Blob-Service-Properties Show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) :

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Eliminazione temporanea per i BLOB](soft-delete-blob-overview.md)
- [Gestire e ripristinare BLOB eliminati temporaneamente](soft-delete-blob-manage.md)
