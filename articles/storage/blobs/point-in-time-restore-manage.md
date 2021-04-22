---
title: Eseguire un ripristino tempormente sui dati BLOB in blocchi
titleSuffix: Azure Storage
description: Informazioni su come usare il ripristino temporico per ripristinare uno stato precedente di un set di BLOB in blocchi in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e8c926c2fbc5b19f67fb78d321ee3293c73be939
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869348"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Eseguire un ripristino tempormente sui dati BLOB in blocchi

È possibile usare il ripristino temporico per ripristinare uno o più set di BLOB in blocchi a uno stato precedente. Questo articolo descrive come abilitare il ripristino temporato per un account di archiviazione e come eseguire un'operazione di ripristino.

Per altre informazioni sul ripristino tempormente, vedere [Ripristino tempormente per i BLOB in blocchi.](point-in-time-restore-overview.md)

> [!CAUTION]
> Il ripristino temporizzato supporta solo il ripristino di operazioni su BLOB in blocchi. Non è possibile ripristinare le operazioni su contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando [l'operazione Elimina](/rest/api/storageservices/delete-container) contenitore, tale contenitore non può essere ripristinato con un'operazione di ripristino. Invece di eliminare un intero contenitore, eliminare singoli BLOB se si vuole ripristinarli in un secondo momento. Microsoft consiglia inoltre di abilitare l'eliminazione automatica per contenitori e BLOB per proteggersi dall'eliminazione accidentale. Per altre informazioni, vedere [Eliminazione automatica per i contenitori (anteprima)](soft-delete-container-overview.md) e Eliminazione automatica per i [BLOB.](soft-delete-blob-overview.md)

## <a name="enable-and-configure-point-in-time-restore"></a>Abilitare e configurare il ripristino temporizzato

Prima di abilitare e configurare il ripristino tempormente, abilitare i prerequisiti per l'account di archiviazione: eliminazione software, feed di modifiche e controllo delle versioni dei BLOB. Per altre informazioni su come abilitare queste funzionalità, vedere gli articoli seguenti:

- [Abilitare l'eliminazione temporanea per i BLOB](./soft-delete-blob-enable.md)
- [Abilitare e disabilitare il feed di modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)

> [!IMPORTANT]
> L'abilitazione dell'eliminazione software, del feed di modifiche e del controllo delle versioni dei BLOB può comportare costi aggiuntivi. Per altre informazioni, vedere [Eliminazione software per i](soft-delete-blob-overview.md)BLOB, Supporto dei feed di modifiche in [Archiviazione BLOB di Azure](storage-blob-change-feed.md)e Controllo delle versioni [dei BLOB.](versioning-overview.md)

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per configurare il ripristino temporato con il portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione nel portale di Azure.
1. In **Impostazioni** scegliere **Protezione dati.**
1. Selezionare **Turn on point-in-time** restore (Attiva ripristino tempormente). Quando si seleziona questa opzione, vengono abilitati anche l'eliminazione software per BLOB, controllo delle versioni e feed di modifiche.
1. Impostare il punto di ripristino massimo per il ripristino tempormente, in giorni. Questo numero deve essere inferiore di almeno un giorno al periodo di memorizzazione specificato per l'eliminazione blob.
1. Salvare le modifiche.

L'immagine seguente mostra un account di archiviazione configurato per il ripristino temporizzazione con un punto di ripristino di sette giorni fa e un periodo di conservazione per l'eliminazione blob soft di 14 giorni.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Screenshot che mostra come configurare il ripristino temporato nel portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare il ripristino temporizzato con PowerShell, installare prima il modulo [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) versione 2.6.0 o successiva. Chiamare quindi il [comando Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) per abilitare il ripristino temporizzato per l'account di archiviazione.

L'esempio seguente abilita l'eliminazione software e imposta il periodo di conservazione dell'eliminazione software, abilita il feed di modifiche e il controllo delle versioni e quindi abilita il ripristino temporizzazione. Quando si esegue l'esempio, assicurarsi di sostituire i valori tra parentesi uncinate con i propri valori:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare il ripristino temporato con l'interfaccia della riga di comando di Azure, installare prima l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Chiamare quindi il [comando az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) per abilitare il ripristino temporizzato e le altre impostazioni di protezione dei dati necessarie per l'account di archiviazione.

L'esempio seguente abilita l'eliminazione software e imposta il periodo di conservazione dell'eliminazione software su 14 giorni, abilita il feed di modifiche e il controllo delle versioni e abilita il ripristino temporizzazione con un periodo di ripristino di 7 giorni. Quando si esegue l'esempio, assicurarsi di sostituire i valori tra parentesi uncinate con i propri valori:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Scegliere un punto di ripristino

Il punto di ripristino è la data e l'ora in cui vengono ripristinati i dati. Archiviazione di Azure usa sempre un valore di data/ora UTC come punto di ripristino. Tuttavia, il portale di Azure consente di specificare il punto di ripristino nell'ora locale e quindi converte tale valore di data/ora in un valore di data/ora UTC per eseguire l'operazione di ripristino.

Quando si esegue un'operazione di ripristino con PowerShell o l'interfaccia della riga di comando di Azure, è necessario specificare il punto di ripristino come valore di data/ora UTC. Se il punto di ripristino viene specificato con un valore di ora locale anziché con un valore di ora UTC, l'operazione di ripristino può comunque comportarsi come previsto in alcuni casi. Ad esempio, se l'ora locale è UTC meno cinque ore, se si specifica un valore di ora locale, il punto di ripristino sarà cinque ore prima del valore specificato. Se non sono state apportate modifiche ai dati nell'intervallo da ripristinare durante il periodo di cinque ore, l'operazione di ripristino produrrà gli stessi risultati indipendentemente dal valore dell'ora specificato. È consigliabile specificare un'ora UTC per il punto di ripristino per evitare risultati imprevisti.

## <a name="perform-a-restore-operation"></a>Eseguire un'operazione di ripristino

È possibile ripristinare tutti i contenitori nell'account di archiviazione oppure è possibile ripristinare un intervallo di BLOB in uno o più contenitori. Un intervallo di BLOB viene definito lessicograficamente, vale a dire nell'ordine del dizionario. Sono supportati fino a dieci intervalli lessicografici per ogni operazione di ripristino. L'inizio dell'intervallo è inclusivo e la fine dell'intervallo è esclusiva.

Il modello di contenitore specificato per l'intervallo iniziale e l'intervallo finale deve includere un minimo di tre caratteri. La barra (/) usata per separare il nome di un contenitore dal nome di un BLOB non viene conteggiata ai fini di questo valore minimo.

I caratteri jolly non sono supportati in un intervallo lessicografico. Tutti i caratteri jolly vengono considerati come caratteri standard.

È possibile ripristinare i BLOB nei contenitori `$root` e `$web` specificandoli esplicitamente in un intervallo passato a un'operazione di ripristino. I contenitori `$root` e `$web` vengono ripristinati solo se vengono specificati esplicitamente. Gli altri contenitori di sistema non possono essere ripristinati.

Vengono ripristinati solo i BLOB in blocchi. I BLOB di pagine e i BLOB di accodamento non sono inclusi in un'operazione di ripristino. Per altre informazioni sulle limitazioni relative ai BLOB di accodamento, vedere [Ripristino tempormente per i BLOB in blocchi.](point-in-time-restore-overview.md)

> [!IMPORTANT]
> Quando si esegue un'operazione di ripristino, Archiviazione di Azure le operazioni sui dati nei BLOB negli intervalli ripristinati per la durata dell'operazione. Le operazioni di lettura, scrittura ed eliminazione sono bloccate nella posizione primaria. Per questo motivo, operazioni come l'elenco di contenitori nel portale di Azure potrebbero non essere eseguite come previsto mentre è in corso l'operazione di ripristino.
>
> Le operazioni di lettura dalla posizione secondaria possono continuare durante l'operazione di ripristino se l'account di archiviazione viene replicato geograficamente.
>
> Il tempo necessario per ripristinare un set di dati è basato sul numero di operazioni di scrittura ed eliminazione eseguite durante il periodo di ripristino. Ad esempio, un account con un milione di oggetti con 3.000 oggetti aggiunti al giorno e 1.000 oggetti eliminati al giorno richiederà circa due ore per il ripristino fino a un punto di 30 giorni nel passato. Un periodo di conservazione e il ripristino di più di 90 giorni nel passato non sono consigliati per un account con questa frequenza di modifica.

### <a name="restore-all-containers-in-the-account"></a>Ripristinare tutti i contenitori nell'account

È possibile ripristinare tutti i contenitori nell'account di archiviazione per ripristinarne lo stato precedente in un determinato momento.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione con portale di Azure, seguire questa procedura:

1. Passare all'elenco di contenitori per l'account di archiviazione.
1. Sulla barra degli strumenti scegliere **Ripristina contenitori**, quindi **Ripristina tutto**.
1. Nel riquadro **Ripristina tutti i contenitori** specificare il punto di ripristino specificando una data e un'ora.
1. Verificare di voler procedere selezionando la casella .
1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Screenshot che mostra come ripristinare tutti i contenitori in un punto di ripristino specificato":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione con PowerShell, chiamare il comando **Restore-AzStorageBlobRange** e specificare il punto di ripristino come valore di data/ora UTC. Per impostazione predefinita, il comando **Restore-AzStorageBlobRange** viene eseguito in modo asincrono e restituisce un oggetto di tipo **PSBlobRestoreStatus** che è possibile usare per controllare lo stato dell'operazione di ripristino.

L'esempio seguente ripristina in modo asincrono i contenitori nell'account di archiviazione allo stato 12 ore prima del momento attuale e controlla alcune delle proprietà dell'operazione di ripristino:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Per eseguire l'operazione di ripristino in modo sincrono, includere il **parametro -WaitForComplete** nel comando. Quando è **presente il parametro -WaitForComplete,** PowerShell visualizza un messaggio che include l'ID di ripristino per l'operazione e quindi si blocca all'esecuzione fino al completamento dell'operazione di ripristino. Tenere presente che il tempo necessario per un'operazione di ripristino dipende dalla quantità di dati da ripristinare e il completamento di un'operazione di ripristino di grandi dimensioni può richiedere fino a un'ora.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare il [comando az storage BLOB restore](/cli/azure/storage/blob#az_storage_blob_restore) e specificare il punto di ripristino come valore di data/ora UTC.

L'esempio seguente ripristina in modo asincrono tutti i contenitori nell'account di archiviazione allo stato 12 ore prima di una data e un'ora specificate. Per controllare lo stato dell'operazione di ripristino, chiamare [az storage account show:](/cli/azure/storage/account#az_storage_account_show)

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Per controllare le proprietà di un'operazione di ripristino, chiamare [az storage account show](/cli/azure/storage/account#az_storage_account_show) ed espandere la **proprietà blobRestoreStatus.** Nell'esempio seguente viene illustrato come controllare la **proprietà status.**

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Per eseguire il **comando az storage blob restore** in modo sincrono e bloccare l'esecuzione fino al completamento dell'operazione di ripristino, omettere il parametro `--no-wait` .

---

### <a name="restore-ranges-of-block-blobs"></a>Ripristinare intervalli di BLOB in blocchi

È possibile ripristinare uno o più intervalli lessicografici di BLOB all'interno di un singolo contenitore o in più contenitori per ripristinare lo stato precedente di tali BLOB in un determinato momento.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per ripristinare un intervallo di BLOB in uno o più contenitori con il portale di Azure, seguire questa procedura:

1. Passare all'elenco di contenitori per l'account di archiviazione.
1. Selezionare il contenitore o i contenitori da ripristinare.
1. Sulla barra degli strumenti scegliere **Ripristina contenitori** e quindi **Ripristina selezionato.**
1. Nel riquadro **Ripristina contenitori selezionati** specificare il punto di ripristino specificando una data e un'ora.
1. Specificare gli intervalli da ripristinare. Usare una barra (/) per delineare il nome del contenitore dal prefisso del BLOB.
1. Per impostazione **predefinita, il riquadro Ripristina contenitori** selezionati specifica un intervallo che include tutti i BLOB nel contenitore. Eliminare questo intervallo se non si vuole ripristinare l'intero contenitore. L'intervallo predefinito è illustrato nell'immagine seguente.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Screenshot che mostra l'intervallo BLOB predefinito da eliminare prima di specificare un intervallo personalizzato":::

1. Confermare che si vuole procedere selezionando la casella.
1. Selezionare **Ripristina per** avviare l'operazione di ripristino.

L'immagine seguente mostra un'operazione di ripristino in un set di intervalli.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Screenshot che mostra come ripristinare intervalli di BLOB in uno o più contenitori":::

L'operazione di ripristino illustrata nell'immagine esegue le azioni seguenti:

- Ripristina il contenuto completo di *container1.*
- Ripristina i BLOB nell'intervallo lessicografico *da BLOB1* a *BLOB5* in *container2.* Questo intervallo ripristina i BLOB con nomi quali *blob1,* *blob11,* *blob100,* *blob2* e così via. Poiché la fine dell'intervallo è esclusiva, ripristina i BLOB i cui nomi iniziano con *BLOB4,* ma non ripristina i BLOB i cui nomi iniziano con *blob5*.
- Ripristina tutti i BLOB in *container3* e *container4.* Poiché la fine dell'intervallo è esclusiva, questo intervallo non ripristina *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per ripristinare un singolo intervallo di BLOB, chiamare il **comando Restore-AzStorageBlobRange** e specificare un intervallo lessicografico di nomi di contenitori e BLOB per il `-BlobRestoreRange` parametro. Ad esempio, per ripristinare i BLOB in un singolo contenitore denominato *container1,* è possibile specificare un intervallo che inizia con *container1* e termina con *container2*. Non è necessario che i contenitori denominati nell'intervallo iniziale e finale esistano. Poiché la fine dell'intervallo è esclusiva, anche se l'account di archiviazione include un contenitore denominato *container2,* verrà ripristinato solo il contenitore denominato *container1:*

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Per specificare un subset di BLOB in un contenitore da ripristinare, usare una barra (/) per separare il nome del contenitore dal modello di prefisso BLOB. L'intervallo seguente, ad esempio, seleziona i BLOB in un singolo contenitore i cui nomi iniziano con le lettere da *d* fino a *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Specificare quindi l'intervallo **per il comando Restore-AzStorageBlobRange.** Specificare il punto di ripristino fornendo un valore **DateTime** UTC per il parametro `-TimeToRestore`. L'esempio seguente ripristina i BLOB inclusi nell'intervallo specificato allo stato di 3 giorni precedenti al momento attuale:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Per impostazione predefinita, il **comando Restore-AzStorageBlobRange** viene eseguito in modo asincrono. Quando si avvia un'operazione di ripristino in modo asincrono, PowerShell visualizza immediatamente una tabella di proprietà per l'operazione:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Per ripristinare più intervalli di BLOB in blocchi, specificare una matrice di intervalli per il parametro `-BlobRestoreRange`. L'esempio seguente specifica due intervalli per ripristinare il contenuto completo di *container1* e *container4* allo stato di 24 ore fa e salva il risultato in una variabile :

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Per eseguire l'operazione di ripristino in modo sincrono e bloccare l'esecuzione fino al completamento, includere il **parametro -WaitForComplete** nel comando.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per ripristinare un intervallo di BLOB, chiamare il [comando az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) e specificare un intervallo lessicografico di nomi di contenitore e BLOB per il parametro `--blob-range` . Per specificare più intervalli, specificare il `--blob-range` parametro per ogni intervallo distinto.

Ad esempio, per ripristinare i BLOB in un singolo contenitore denominato *container1*, è possibile specificare un intervallo che inizia con *container1* e termina con *container2*. Non è necessario che i contenitori denominati nell'intervallo iniziale e finale esistano. Poiché la fine dell'intervallo è esclusiva, anche se l'account di archiviazione include un contenitore denominato *container2*, verrà ripristinato solo il contenitore denominato *container1.*

Per specificare un subset di BLOB in un contenitore da ripristinare, usare una barra (/) per separare il nome del contenitore dal modello di prefisso BLOB. L'esempio illustrato di seguito ripristina in modo asincrono un intervallo di BLOB in un contenitore i cui nomi iniziano con le lettere `d` fino a `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Per eseguire il **comando az storage blob restore** in modo sincrono e bloccare l'esecuzione fino al completamento dell'operazione di ripristino, omettere il parametro `--no-wait` .

---

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino temporato per i BLOB in blocchi](point-in-time-restore-overview.md)
- [Eliminazione temporanea](./soft-delete-blob-overview.md)
- [Feed di modifiche](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)