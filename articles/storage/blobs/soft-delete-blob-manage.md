---
title: Gestire e ripristinare BLOB eliminati temporaneamente
titleSuffix: Azure Storage
description: Gestire e ripristinare i BLOB e gli snapshot eliminati temporaneamente con la portale di Azure o con le librerie client di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556152"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Gestire e ripristinare BLOB eliminati temporaneamente

L'eliminazione temporanea BLOB protegge un singolo BLOB e le relative versioni, gli snapshot e i metadati da eliminazioni accidentali o sovrascritture gestendo i dati eliminati nel sistema per un periodo di tempo specificato. Durante il periodo di memorizzazione, è possibile ripristinare lo stato del BLOB al momento dell'eliminazione. Una volta scaduto il periodo di conservazione, il BLOB viene eliminato definitivamente. Per altre informazioni sull'eliminazione temporanea dei BLOB, vedere [eliminazione temporanea per i BLOB](soft-delete-blob-overview.md).

L'eliminazione temporanea dei BLOB fa parte di una strategia di protezione dei dati completa per i dati BLOB. Per ulteriori informazioni sulle raccomandazioni di Microsoft per la protezione dei dati, vedere [panoramica sulla protezione dei dati](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Gestire i BLOB eliminati temporaneamente con la portale di Azure

È possibile usare la portale di Azure per visualizzare e ripristinare i BLOB e gli snapshot eliminati temporaneamente.

### <a name="view-deleted-blobs"></a>Visualizza BLOB eliminati

Quando vengono eliminati temporaneamente, i BLOB sono invisibili nel portale di Azure per impostazione predefinita. Per visualizzare i BLOB eliminati temporaneamente, passare alla pagina **Panoramica** per il contenitore e impostare l'impostazione **Mostra BLOB eliminati** . I BLOB eliminati temporaneamente vengono visualizzati con lo stato **Deleted**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Screenshot che illustra come elencare i BLOB eliminati temporaneamente in portale di Azure":::

Selezionare quindi il BLOB eliminato dall'elenco di BLOB per visualizzarne le proprietà. Nella scheda **Panoramica** si noti che lo stato del BLOB è impostato su **eliminato**. Il portale Visualizza anche il numero di giorni trascorsi i quali il BLOB viene eliminato definitivamente.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Screenshot che mostra le proprietà del BLOB eliminato temporaneamente in portale di Azure":::

### <a name="view-deleted-snapshots"></a>Visualizza snapshot eliminati

L'eliminazione di un BLOB comporta anche l'eliminazione di eventuali snapshot associati al BLOB. Se un BLOB eliminato temporaneamente include snapshot, gli snapshot eliminati possono essere visualizzati anche nel portale. Visualizzare le proprietà del BLOB eliminato temporaneamente, quindi passare alla scheda **snapshot** e impostare **Mostra snapshot eliminati**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Schermata che Mostra ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Ripristinare oggetti eliminati temporaneamente quando il controllo delle versioni è disabilitato

Per ripristinare un BLOB eliminato temporaneamente nel portale di Azure quando il controllo delle versioni del BLOB non è abilitato, visualizzare prima le proprietà del BLOB, quindi selezionare il pulsante **Annulla eliminazione** nella scheda **Panoramica** . Il ripristino di un BLOB restituisce anche eventuali snapshot eliminati durante il periodo di memorizzazione dell'eliminazione temporanea.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Screenshot che illustra come ripristinare un BLOB eliminato temporaneamente in portale di Azure":::

Per innalzare di livello uno snapshot eliminato temporaneamente al BLOB di base, assicurarsi prima di tutto che gli snapshot eliminati temporaneamente del BLOB siano stati ripristinati. Selezionare il pulsante Annulla **eliminazione** per ripristinare gli snapshot eliminati temporaneamente del BLOB, anche se il BLOB di base non è stato eliminato temporaneamente. Quindi, selezionare lo snapshot da alzare di livello e usare il pulsante **Promote snapshot** per sovrascrivere il BLOB di base con il contenuto dello snapshot.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Screenshot che illustra come innalzare di livello uno snapshot al BLOB di base":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Ripristinare i BLOB eliminati temporaneamente quando è abilitata la funzionalità di controllo delle versioni

Per ripristinare un BLOB eliminato temporaneamente nel portale di Azure quando è abilitata la funzionalità di controllo delle versioni, selezionare il BLOB eliminato temporaneamente per visualizzarne le proprietà, quindi selezionare la scheda **versioni** . Selezionare la versione che si desidera innalzare di livello come versione corrente, quindi selezionare **Crea versione corrente**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Screenshot che illustra come alzare di livello una versione per ripristinare un BLOB in portale di Azure":::

Per ripristinare le versioni o gli snapshot eliminati quando è abilitata la funzionalità di controllo delle versioni, visualizzare le proprietà del BLOB, quindi selezionare il pulsante **Annulla eliminazione** nella scheda **Panoramica** .

> [!NOTE]
> Quando è abilitata la funzionalità di controllo delle versioni, la selezione del pulsante **Annulla eliminazione** in un BLOB eliminato consente di ripristinare le versioni o gli snapshot eliminati temporaneamente, ma non di ripristinare il BLOB di base. Per ripristinare il BLOB di base, è necessario alzare di livello una versione precedente.

## <a name="manage-soft-deleted-blobs-with-code"></a>Gestire BLOB eliminati temporaneamente con il codice

È possibile usare le librerie client di archiviazione di Azure per ripristinare un BLOB o uno snapshot eliminato temporaneamente. Gli esempi seguenti illustrano come usare la libreria client .NET.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Ripristinare oggetti eliminati temporaneamente quando il controllo delle versioni è disabilitato

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Per ripristinare i BLOB eliminati quando il controllo delle versioni non è abilitato, chiamare l'operazione [Undelete BLOB](/rest/api/storageservices/undelete-blob) su tali BLOB. L'operazione **Undelete BLOB** Ripristina i BLOB eliminati temporaneamente ed eventuali snapshot eliminati associati a tali BLOB.

La chiamata di **Undelete BLOB** su un BLOB che non è stato eliminato non ha alcun effetto. Nell'esempio seguente viene chiamato **Undelete BLOB** su tutti i BLOB in un contenitore e vengono ripristinati i BLOB eliminati temporaneamente e i relativi snapshot:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Per ripristinare una versione specifica, chiamare prima l'operazione **Undelete BLOB** sul BLOB o sulla versione di base, quindi copiare la versione desiderata sul BLOB di base. Nell'esempio seguente viene ripristinato un BLOB in blocchi per la versione salvata più di recente:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Per ripristinare i BLOB eliminati quando il controllo delle versioni non è abilitato, chiamare l'operazione [Undelete BLOB](/rest/api/storageservices/undelete-blob) su tali BLOB. L'operazione **Undelete BLOB** Ripristina i BLOB eliminati temporaneamente ed eventuali snapshot eliminati associati a tali BLOB.

La chiamata di **Undelete BLOB** su un BLOB che non è stato eliminato non ha alcun effetto. Nell'esempio seguente viene chiamato **Undelete BLOB** su tutti i BLOB in un contenitore e vengono ripristinati i BLOB eliminati temporaneamente e i relativi snapshot:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Per ripristinare uno snapshot specifico, chiamare prima l'operazione **Undelete BLOB** sul BLOB di base, quindi copiare lo snapshot desiderato sul BLOB di base. Nell'esempio seguente viene ripristinato un BLOB in blocchi per lo snapshot generato più di recente:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Ripristinare i BLOB eliminati temporaneamente quando è abilitata la funzionalità di controllo delle versioni

Per ripristinare un BLOB eliminato temporaneamente quando è abilitata la funzionalità di controllo delle versioni, copiare una versione precedente tramite il BLOB di base con un'operazione [copia BLOB](/rest/api/storageservices/copy-blob) o [copia BLOB da URL](/rest/api/storageservices/copy-blob-from-url) .  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Non applicabile. Il controllo delle versioni dei BLOB è supportato solo nelle librerie client di archiviazione di Azure versione 12. x e successive.

---

## <a name="next-steps"></a>Passaggi successivi

- [Eliminazione temporanea per l'archiviazione BLOB](./soft-delete-blob-overview.md)
- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-blob-enable.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
