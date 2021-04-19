---
title: Abilitare SMB multicanale
description: Informazioni su come abilitare SMB Multichannel nelle condivisioni file Premium di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da4e1a58aef28e5c47100a0311ff81a5af04a918
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718981"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Abilitare SMB Multichannel in un account FileStorage (anteprima) 

Gli account FileStorage di Azure supportano SMB Multichannel (anteprima), che aumenta le prestazioni di un client SMB 3.x stabilendo più connessioni di rete alle condivisioni file Premium. Questo articolo fornisce indicazioni dettagliate per abilitare SMB Multichannel in un account di archiviazione esistente. Per informazioni dettagliate su File di Azure multicanale SMB, vedere Prestazioni multicanale SMB.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account FileStorage](./storage-how-to-create-file-share.md).
- Se si intende usare il modulo Azure PowerShell, installare la versione di anteprima [3.0.1 del modulo](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Guida introduttiva

Aprire una finestra di PowerShell e accedere alla sottoscrizione di Azure. Da qui è possibile eseguire la registrazione per l'anteprima SMB multicanale con i comandi seguenti.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> La registrazione può richiedere fino a un'ora.

### <a name="verify-that-feature-registration-is-complete"></a>Verificare che la registrazione delle funzionalità sia stata completata

Poiché l'abilitazione della funzionalità nell'account di archiviazione può richiedere fino a un'ora, è possibile usare il comando seguente per verificare che sia registrata per la sottoscrizione:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Abilitare SMB multicanale 
Dopo aver creato un account FileStorage, è possibile seguire le istruzioni per aggiornare le impostazioni SMB multicanale per l'account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al portale di Azure e passare all'account di archiviazione FileStorage in cui si vuole configurare SMB Multichannel.
1. Selezionare **Condivisioni file** in **Servizio file** e quindi selezionare Impostazioni **condivisione file**.
1. Attivare **o disattivare SMB Multichannel** **per** disabilitare e selezionare **Salva**. 

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Screenshot dell'account di archiviazione, smb multicanale attivato."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Se l'opzione SMB multicanale non è visibile in Impostazioni condivisione **file** o si verifica un errore di impostazione di aggiornamento [](#regional-availability) non riuscito durante l'aggiornamento della configurazione, assicurarsi che la sottoscrizione sia registrata e che l'account si trova in una delle aree supportate con il tipo di account e la replica supportati.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare SMB multicanale usando il modulo Azure PowerShell, è necessario installare la [versione 3.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) del modulo.

Impostare le variabili `$resourceGroupName` e sul gruppo di risorse e `$storageAccountName` sull'account di archiviazione prima di eseguire questi comandi di PowerShell.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
L'interfaccia della riga di comando di Azure non supporta ancora la configurazione di SMB multicanale. Vedere le istruzioni del portale per configurare SMB multicanale nell'account di archiviazione.

---

> [!NOTE]
> Tutte le modifiche alle impostazioni di configurazione SMB multicanale verranno applicate a tutte le condivisioni file nell'account di archiviazione. Sarà tuttavia necessario rimontare la condivisione nel client per l'applicazione delle modifiche.


## <a name="next-steps"></a>Passaggi successivi 

- [Rimontare la condivisione file](storage-how-to-use-files-windows.md) per sfruttare SMB multicanale.
- [Risolvere eventuali problemi relativi a SMB multicanale.](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)
- Per altre informazioni sui miglioramenti, vedere [Prestazioni SMB multicanale](storage-files-smb-multichannel-performance.md)
 - Per altre informazioni sulla funzionalità Multicanale SMB di Windows, vedere [Gestire SMB Mulitchannel.](/azure-stack/hci/manage/manage-smb-multichannel)