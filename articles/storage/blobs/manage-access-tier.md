---
title: Gestire il livello di accesso di un BLOB in un account di archiviazione di Azure
description: Informazioni su come modificare il livello di un BLOB in un account di archiviazione BLOB o GPv2
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98166521"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Gestire il livello di accesso di un BLOB in un account di archiviazione di Azure

Ogni BLOB ha un livello di accesso predefinito, ad accesso frequente, sporadico o archivio. Un BLOB acquisisce il livello di accesso predefinito dell'account di archiviazione di Azure in cui viene creato. Gli account di archiviazione BLOB e GPv2 espongono l'attributo **livello di accesso** a livello di account. Questo attributo specifica il livello di accesso predefinito per qualsiasi BLOB che non è impostato in modo esplicito a livello di oggetto. Per gli oggetti con il livello impostato a livello di oggetto, il livello di account non verrà applicato. Il livello archivio può essere applicato solo a livello di oggetto. È possibile passare tra i livelli di accesso in qualsiasi momento attenendosi alla procedura descritta di seguito.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modificare il livello di un BLOB in un account di archiviazione BLOB o GPv2

Negli scenari seguenti viene usato il portale di Azure o PowerShell:

# <a name="portal"></a>[Portale](#tab/portal)

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel portale di Azure cercare e selezionare **Tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. Selezionare il contenitore e quindi il BLOB.

1. In **proprietà BLOB** selezionare **Modifica livello**.

1. Selezionare il **livello di accesso** ad accesso frequente, **ad** accesso sporadico o **Archivio** . Se il BLOB è attualmente in archivio e si vuole riattivarlo a un livello online, è anche possibile selezionare una priorità di reidratazione **standard** o **High**.

1. Nella parte inferiore selezionare **Salva**.

![Modificare il livello BLOB in portale di Azure](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per modificare il livello BLOB, è possibile usare lo script di PowerShell seguente. La variabile `$rgName` deve essere inizializzata con il nome del gruppo di risorse. La variabile `$accountName` deve essere inizializzata con il nome dell'account di archiviazione. La variabile `$containerName` deve essere inizializzata con il nome del contenitore. La variabile `$blobName` deve essere inizializzata con il nome del BLOB.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Come gestire il livello di accesso dell'account predefinito di un account di archiviazione di Azure](../common/manage-account-default-access-tier.md)
- [Informazioni sulla reidratazione dei dati BLOB dal livello archivio](storage-blob-rehydration.md)
- [Controllare i prezzi di accesso frequente, ad accesso sporadico e archivio nell'archiviazione BLOB e negli account GPv2 per area](https://azure.microsoft.com/pricing/details/storage/)
