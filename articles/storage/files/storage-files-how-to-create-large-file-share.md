---
title: Abilitare e creare condivisioni file di grandi dimensioni - File di Azure
description: Questo articolo illustra come abilitare e creare condivisioni file di grandi dimensioni.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 20f9aaf73fe0cb30b136254d57e6c9b960c16af4
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716983"
---
# <a name="enable-and-create-large-file-shares"></a>Abilitare e creare condivisioni file di grandi dimensioni

Le condivisioni file di Azure possono aumentare fino a 100 TiB dopo aver abilitato condivisioni file di grandi dimensioni nell'account di archiviazione. Quando si abilitano condivisioni file di grandi dimensioni, è anche possibile aumentare i limiti di operazioni di I/O al secondo e velocità effettiva della condivisione file. È anche possibile abilitare questa scalabilità per gli account di archiviazione esistenti per le condivisioni file nuove e esistenti. Per informazioni dettagliate sulle differenze di prestazioni, vedere Destinazioni [di condivisione file e scalabilità di file.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli).
- Se si intende usare il modulo Azure PowerShell, [installare la versione più recente](/powershell/azure/install-az-ps)di .

## <a name="restrictions"></a>Restrizioni

Per il momento, è possibile usare solo l'archiviazione con ridondanza locale (LRS) o l'archiviazione con ridondanza della zona (ZRS) sugli account di archiviazione con condivisioni file di grandi dimensioni abilitate. Non è possibile usare l'archiviazione con ridondanza geografica della zona (GZRS), l'archiviazione con ridondanza geografica (GRS), l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

L'abilitazione di condivisioni file di grandi dimensioni in un account è un processo irreversibile. Dopo l'abilitazione, non sarà possibile convertire l'account in GZRS, GRS, RA-GRS o RA-GZRS.

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure fare clic su **Tutti i servizi**. 
1. Nell'elenco delle risorse immettere **Account di archiviazione**. Mentre si digita, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nel pannello **Account di** archiviazione visualizzato selezionare **+ Nuovo.**
1. Nel pannello Informazioni di base compilare le selezioni nel modo desiderato.
1. Assicurarsi che **l'opzione Prestazioni** sia impostata su **Standard.**
1. Impostare **Ridondanza su** Archiviazione **con ridondanza locale o** Archiviazione con **ridondanza della zona.**
1. Selezionare il **pannello** Avanzate e quindi selezionare il pulsante di opzione **Abilitato** a destra di **Condivisioni file di grandi dimensioni.**
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Crea**.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Installare prima di [tutto la versione più recente dell'interfaccia della riga di](/cli/azure/install-azure-cli) comando di Azure in modo che sia possibile abilitare condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitate, usare il comando seguente. Sostituire `<yourStorageAccountName>` , e con le `<yourResourceGroup>` `<yourDesiredRegion>` informazioni.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installare prima [di tutto la versione più recente di PowerShell](/powershell/azure/install-az-ps) in modo da poter abilitare condivisioni file di grandi dimensioni.

Per creare un account di archiviazione con condivisioni file di grandi dimensioni abilitate, usare il comando seguente. Sostituire `<yourStorageAccountName>` , e con le `<yourResourceGroup>` `<yourDesiredRegion>` informazioni.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Abilitare condivisioni file di grandi dimensioni in un account esistente

È anche possibile abilitare condivisioni file di grandi dimensioni per gli account esistenti. Se si abilitano condivisioni file di grandi dimensioni, non sarà possibile eseguire la conversione in GZRS, GRS, RA-GRS o RA-GZRS. L'abilitazione di condivisioni file di grandi dimensioni è irreversibile in questo account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Aprire il [portale di Azure](https://portal.azure.com)e passare all'account di archiviazione in cui si desidera abilitare condivisioni file di grandi dimensioni.
1. Aprire l'account di archiviazione e selezionare **Condivisioni file**.
1. Selezionare **Abilitato nelle** **condivisioni file di grandi** dimensioni e quindi selezionare **Salva**.
1. Selezionare **Panoramica** e quindi **Aggiorna**.
1. Selezionare **Condividi capacità** e quindi selezionare **100 TiB** e **Salva.**

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Screenshot dell'account di archiviazione di Azure, pannello Condivisioni file con 100 condivisioni tib evidenziate.":::

Sono ora abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario aggiornare la quota della condivisione esistente per sfruttare [l'aumento](#expand-existing-file-shares) della capacità e della scalabilità.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per abilitare condivisioni file di grandi dimensioni nell'account esistente, usare il comando seguente. Sostituire `<yourStorageAccountName>` e con le `<yourResourceGroup>` informazioni.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Sono ora abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario aggiornare la quota della condivisione esistente per sfruttare [l'aumento](#expand-existing-file-shares) della capacità e della scalabilità.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare condivisioni file di grandi dimensioni nell'account esistente, usare il comando seguente. Sostituire `<yourStorageAccountName>` e con le `<yourResourceGroup>` informazioni.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Sono ora abilitate condivisioni file di grandi dimensioni nell'account di archiviazione. Successivamente, è necessario aggiornare la quota della condivisione esistente per sfruttare [l'aumento](#expand-existing-file-shares) della capacità e della scalabilità.

---

## <a name="create-a-large-file-share"></a>Creare una condivisione file di grandi dimensioni

Dopo aver abilitato le condivisioni file di grandi dimensioni nell'account di archiviazione, è possibile creare condivisioni file in tale account con quote più elevate. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

La creazione di una condivisione file di grandi dimensioni è quasi identica alla creazione di una condivisione file standard. La differenza principale è che è possibile impostare una quota fino a 100 TiB.

1. Nell'account di archiviazione selezionare **Condivisioni file.**
1. Selezionare **+ Condivisione file**.
1. Immettere un nome per la condivisione file. È anche possibile impostare le dimensioni della quota desiderate, fino a 100 TiB. Quindi selezionare **Crea** 

![Interfaccia portale di Azure che mostra le caselle Nome e Quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare una condivisione file di grandi dimensioni, usare il comando seguente. Sostituire `<yourStorageAccountName>` , e con le `<yourStorageAccountKey>` `<yourFileShareName>` informazioni.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare una condivisione file di grandi dimensioni, usare il comando seguente. Sostituire `<YourStorageAccountName>` , e con le `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Espandere le condivisioni file esistenti

Dopo aver abilitato le condivisioni file di grandi dimensioni nell'account di archiviazione, è anche possibile espandere le condivisioni file esistenti in tale account alla quota superiore. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nell'account di archiviazione selezionare **Condivisioni file.**
1. Fare clic con il pulsante destro del mouse sulla condivisione file e quindi **scegliere Quota.**
1. Immettere le nuove dimensioni desiderate e quindi selezionare **OK.**

![Interfaccia portale di Azure interfaccia utente con quota di condivisioni file esistenti](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impostare la quota sulle dimensioni massime, usare il comando seguente. Sostituire `<yourStorageAccountName>` , e con le `<yourStorageAccountKey>` `<yourFileShareName>` informazioni.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per impostare la quota sulle dimensioni massime, usare il comando seguente. Sostituire `<YourStorageAccountName>` , e con le `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` informazioni.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Passaggi successivi

* [Connettere e montare una condivisione file in Windows](storage-how-to-use-files-windows.md)
* [Connettere e montare una condivisione file in Linux](storage-how-to-use-files-linux.md)
* [Connettere e montare una condivisione file in macOS](storage-how-to-use-files-mac.md)