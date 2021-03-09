---
title: Creare una condivisione NFS-File di Azure (anteprima)
description: Informazioni su come creare una condivisione file di Azure che può essere montata usando il protocollo Network File System.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521238"
---
# <a name="how-to-create-an-nfs-share"></a>Come creare una condivisione NFS
Le condivisioni file di Azure sono condivisioni file completamente gestite che si trovano nel cloud. Questo articolo illustra la creazione di una condivisione file che usa il protocollo NFS. Per altre informazioni su entrambi i protocolli, vedere [protocolli di condivisione file di Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitazioni
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prerequisiti
- È possibile accedere alle condivisioni NFS solo da reti attendibili. Le connessioni alla condivisione NFS devono avere origine da una delle origini seguenti:
    - È possibile [creare un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint) (scelta consigliata) o [limitare l'accesso all'endpoint pubblico](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configurare una VPN da punto a sito (P2S) in Linux per l'uso con file di Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configurare una VPN da sito a sito per l'uso con file di Azure](storage-files-configure-s2s-vpn.md).
    - Configurare [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Registrare il protocollo NFS 4,1
Se si usa il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure, registrare la funzionalità usando i comandi seguenti:

# <a name="portal"></a>[Portale](#tab/azure-portal)
Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per registrare la funzionalità NFS 4,1 per File di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

L'approvazione della registrazione può richiedere fino a un'ora. Per verificare che la registrazione sia stata completata, usare i comandi seguenti:

# <a name="portal"></a>[Portale](#tab/azure-portal)
Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per controllare la registrazione della funzionalità NFS 4,1 per File di Azure. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione filestorage
Attualmente, le condivisioni NFS 4,1 sono disponibili solo come condivisioni file Premium. Per distribuire una condivisione file Premium con il supporto del protocollo NFS 4,1, è necessario creare prima un account di archiviazione filestorage. Un account di archiviazione è un oggetto di livello superiore in Azure che rappresenta un pool condiviso di spazio di archiviazione che può essere usato per distribuire più condivisioni file di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un account di archiviazione filestorage, passare al portale di Azure.

1. Nella portale di Azure selezionare account di **archiviazione** nel menu a sinistra.

    ![portale di Azure pagina principale selezionare account di archiviazione](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
3. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
4. Selezionare il gruppo di risorse in cui creare l'account di archiviazione

5. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
6. Selezionare la località per l'account di archiviazione o usare la località predefinita.
7. Per **prestazioni** selezionare **Premium**.

    È necessario selezionare **Premium** per l' **archiviazione filestorage** come opzione disponibile nell'elenco a discesa **tipo di account** .

8. Selezionare **tipo di account** e scegliere **filestorage**.
9. Lasciare la **replica** impostata sul valore predefinito di **archiviazione con ridondanza locale (con ridondanza locale)**.

    ![Come creare un account di archiviazione per una condivisione file Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
11. Selezionare **Crea**.

Una volta creata la risorsa dell'account di archiviazione, passare a essa.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per creare un account di archiviazione filestorage, aprire un prompt di PowerShell ed eseguire i comandi seguenti, ricordando di sostituire `<resource-group>` e `<storage-account>` con i valori appropriati per l'ambiente in uso.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per creare un account di archiviazione filestorage, aprire il terminale ed eseguire i comandi seguenti, ricordando di sostituire `<resource-group>` e `<storage-account>` con i valori appropriati per l'ambiente.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>Creare una condivisione NFS

# <a name="portal"></a>[Portale](#tab/azure-portal)

Ora che è stato creato un account filestorage e configurato la rete, è possibile creare una condivisione file NFS. Il processo è simile alla creazione di una condivisione SMB. quando si crea la condivisione, è necessario selezionare **NFS** anziché **SMB** .

1. Passare all'account di archiviazione e selezionare **Condivisioni file**.
1. Selezionare **+ condivisione file** per creare una nuova condivisione file.
1. Assegnare un nome alla condivisione file e selezionare una capacità con provisioning.
1. Per **protocollo** selezionare **NFS (anteprima)**.
1. Per lo **squash radice** effettuare una selezione.

    - Squash radice (impostazione predefinita)-l'accesso per l'utente remoto (radice) viene mappato a UID (65534) e GID (65534).
    - Nessuno squash radice-utente remoto (radice) riceve l'accesso come radice.
    - Tutti gli accessi squash-all utente sono mappati a UID (65534) e GID (65534).
    
1. Selezionare **Crea**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Screenshot del pannello di creazione della condivisione file":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verificare che sia installato .NET Framework. Vedere [scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Installare la versione più recente del modulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Chiudere e riaprire la console di PowerShell.

1. Installare il modulo **AZ. storage** Preview versione **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps)
   
1. Per creare una condivisione file Premium con il modulo Azure PowerShell, usare il cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

    > [!NOTE]
    > Le condivisioni file Premium vengono fatturate usando un modello di cui è stato effettuato il provisioning. Le dimensioni del provisioning della condivisione sono specificate `QuotaGiB` sotto. Per ulteriori informazioni, vedere informazioni sul modello di cui è stato effettuato il [provisioning](understanding-billing.md#provisioned-model) e la [pagina dei prezzi file di Azure](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per creare una condivisione file Premium con l'interfaccia della riga di comando di Azure, usare il comando [AZ storage Share create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Le condivisioni file Premium vengono fatturate usando un modello di cui è stato effettuato il provisioning. Le dimensioni del provisioning della condivisione sono specificate `quota` sotto. Per ulteriori informazioni, vedere informazioni sul modello di cui è stato effettuato il [provisioning](understanding-billing.md#provisioned-model) e la [pagina dei prezzi file di Azure](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata creata una condivisione NFS, per usarla è necessario montarla nel client Linux. Per informazioni dettagliate, vedere [come montare una condivisione NFS](storage-files-how-to-mount-nfs-shares.md).

Se si verificano problemi, vedere [risolvere i problemi relativi alle condivisioni file NFS di Azure](storage-troubleshooting-files-nfs.md).