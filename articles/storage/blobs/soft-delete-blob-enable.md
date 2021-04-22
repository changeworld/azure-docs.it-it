---
title: Abilitare l'eliminazione temporanea per i BLOB
titleSuffix: Azure Storage
description: Abilitare l'eliminazione soft per i BLOB per proteggere i dati BLOB da eliminazioni o sovrascritte accidentali.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a8d1f872ca042429276b8f0e1112bc5837d8e38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869276"
---
# <a name="enable-soft-delete-for-blobs"></a>Abilitare l'eliminazione temporanea per i BLOB

L'eliminazione soft del BLOB protegge un singolo BLOB e le relative versioni, snapshot e metadati da eliminazioni o sovrascritte accidentali mantenendo i dati eliminati nel sistema per un periodo di tempo specificato. Durante il periodo di conservazione, è possibile ripristinare lo stato del BLOB in fase di eliminazione. Dopo la scadenza del periodo di conservazione, il BLOB viene eliminato definitivamente. Per altre informazioni sull'eliminazione blob soft, vedere [Eliminazione soft per i BLOB](soft-delete-blob-overview.md).

L'eliminazione blob soft fa parte di una strategia di protezione dei dati completa per i dati BLOB. Per altre informazioni sulle raccomandazioni di Microsoft per la protezione dei dati, vedere [Panoramica della protezione dei dati](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Abilitare l'eliminazione blob in modalità soft

L'eliminazione blob soft è disabilitata per impostazione predefinita per un nuovo account di archiviazione. È possibile abilitare o disabilitare l'eliminazione temporaneamente per un account di archiviazione in qualsiasi momento usando portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per abilitare l'eliminazione blob soft per l'account di archiviazione usando il portale di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/) passare all'account di archiviazione.
1. Individuare **l'opzione Protezione** dati in **Servizio BLOB**.
1. Nella sezione **Recovery** (Ripristino) selezionare **Turn on soft delete for BLOBs (Attiva eliminazione soft per i BLOB).**
1. Specificare un periodo di conservazione compreso tra 1 e 365 giorni. Microsoft consiglia un periodo di conservazione minimo di sette giorni.
1. Salvare le modifiche.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Screenshot che mostra come abilitare l'eliminazione soft nella portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare l'eliminazione blob soft con PowerShell, chiamare il comando [Enable-AzStorageBlobDeleteRetentionPolicy,](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) specificando il periodo di conservazione in giorni.

Nell'esempio seguente viene abilitata l'eliminazione blob soft e il periodo di conservazione viene impostato su sette giorni. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Per controllare le impostazioni correnti per l'eliminazione blob soft, chiamare il [comando Get-AzStorageBlobServiceProperty:](/powershell/module/az.storage/get-azstorageblobserviceproperty)

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Per abilitare l'eliminazione soft dei BLOB con l'interfaccia della riga di comando di Azure, chiamare il [comando az storage account blob-service-properties update,](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) specificando il periodo di conservazione in giorni.

Nell'esempio seguente viene abilitata l'eliminazione blob e il periodo di conservazione viene impostato su sette giorni. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Per controllare le impostazioni correnti per l'eliminazione blob soft, chiamare il [comando az storage account blob-service-properties show:](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show)

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Eliminazione temporanea per i BLOB](soft-delete-blob-overview.md)
- [Gestire e ripristinare i BLOB eliminati automaticamente](soft-delete-blob-manage.md)
