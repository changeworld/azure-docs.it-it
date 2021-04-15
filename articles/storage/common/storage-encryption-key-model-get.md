---
title: Determinare il modello di chiave di crittografia in uso per l'account di archiviazione
titleSuffix: Azure Storage
description: Usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per controllare come vengono gestite le chiavi di crittografia per l'account di archiviazione. Le chiavi possono essere gestite da Microsoft (impostazione predefinita) o dal cliente. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ef0f32ecc59bea6ee7a0f7ff12083fd2358c223c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478914"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determinare quale Archiviazione di Azure di chiave di crittografia è in uso per l'account di archiviazione

I dati nell'account di archiviazione vengono crittografati automaticamente Archiviazione di Azure. Archiviazione di Azure crittografia offre due opzioni per la gestione delle chiavi di crittografia a livello dell'account di archiviazione:

- **Chiavi gestite da Microsoft.** Per impostazione predefinita, Microsoft gestisce le chiavi usate per crittografare l'account di archiviazione.
- **Chiavi gestite dal cliente.** Facoltativamente, è possibile scegliere di gestire le chiavi di crittografia per l'account di archiviazione. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault.

È anche possibile fornire una chiave di crittografia a livello di singola richiesta per alcune operazioni di archiviazione BLOB. Quando viene specificata una chiave di crittografia nella richiesta, tale chiave esegue l'override della chiave di crittografia attiva nell'account di archiviazione. Per altre informazioni, vedere Specificare una chiave fornita dal cliente [in una richiesta all'archiviazione BLOB.](../blobs/storage-blob-customer-provided-key.md)

Per altre informazioni sulle chiavi di crittografia, vedere Crittografia [Archiviazione di Azure per i dati in stato di inalter.](storage-service-encryption.md)

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Controllare il modello di chiave di crittografia per l'account di archiviazione

Per determinare se un account di archiviazione usa chiavi gestite da Microsoft o chiavi gestite dal cliente per la crittografia, usare uno degli approcci seguenti.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per controllare il modello di crittografia per l'account di archiviazione usando il portale di Azure, seguire questa procedura:

1. Nel portale di Azure passare all'account di archiviazione.
1. Selezionare **l'impostazione** Crittografia e prendere nota dell'impostazione.

L'immagine seguente mostra un account di archiviazione crittografato con chiavi gestite da Microsoft:

![Visualizzare l'account crittografato con chiavi gestite da Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

L'immagine seguente mostra un account di archiviazione crittografato con chiavi gestite dal cliente:

![Screenshot che mostra l'impostazione della chiave di crittografia in portale di Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per controllare il modello di crittografia per l'account di archiviazione tramite PowerShell, chiamare il [comando Get-AzStorageAccount,](/powershell/module/az.storage/get-azstorageaccount) quindi controllare la **proprietà KeySource** per l'account.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se il valore della **proprietà KeySource** è `Microsoft.Storage` , l'account viene crittografato con chiavi gestite da Microsoft. Se il valore della **proprietà KeySource** è `Microsoft.Keyvault` , l'account viene crittografato con chiavi gestite dal cliente.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Per controllare il modello di crittografia per l'account di archiviazione usando l'interfaccia della riga di comando di Azure, chiamare il [comando az storage account show,](/cli/azure/storage/account#az-storage-account-show) quindi controllare la **proprietà keySource** per l'account.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se il valore della **proprietà keySource** è `Microsoft.Storage` , l'account viene crittografato con chiavi gestite da Microsoft. Se il valore della **proprietà keySource** è `Microsoft.Keyvault` , l'account viene crittografato con chiavi gestite dal cliente.

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per Archiviazione di Azure crittografia](customer-managed-keys-overview.md)
