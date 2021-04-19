---
title: Creare una condivisione NFS - File di Azure (anteprima)
description: Informazioni su come creare una condivisione file di Azure che può essere montata usando il protocollo File System di rete.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b549c625f0a6ff0480eafc38f84d292e66350950
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717132"
---
# <a name="how-to-create-an-nfs-share"></a>Come creare una condivisione NFS
Le condivisioni file di Azure sono condivisioni file completamente gestite presenti nel cloud. Questo articolo illustra la creazione di una condivisione file che usa il protocollo NFS. Per altre informazioni su entrambi i protocolli, vedere Protocolli [di condivisione file di Azure.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Limitazioni
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prerequisiti
- Le condivisioni NFS sono accessibili solo da reti attendibili. Le connessioni alla condivisione NFS devono provenire da una delle origini seguenti:
    - Creare [un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint) (scelta consigliata) o limitare [l'accesso all'endpoint pubblico.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configurare una VPN da punto a sito (P2S) in Linux](storage-files-configure-p2s-vpn-linux.md)per l'uso con File di Azure .
    - [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).
    - Configurare [ExpressRoute.](../../expressroute/expressroute-introduction.md)

- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Registrare il protocollo NFS 4.1
Se si usa il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure, registrare la funzionalità usando i comandi seguenti:

# <a name="portal"></a>[Portale](#tab/azure-portal)
Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per registrare la funzionalità NFS 4.1 per File di Azure.

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
Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per verificare la registrazione della funzionalità NFS 4.1 per File di Azure. 

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

## <a name="create-a-filestorage-storage-account"></a>Creare un account di archiviazione FileStorage
Attualmente, le condivisioni NFS 4.1 sono disponibili solo come condivisioni file Premium. Per distribuire una condivisione file Premium con il supporto del protocollo NFS 4.1, è prima necessario creare un account di archiviazione FileStorage. Un account di archiviazione è un oggetto di primo livello in Azure che rappresenta un pool condiviso di archiviazione che può essere usato per distribuire più condivisioni file di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un account di archiviazione FileStorage, passare al portale di Azure.

1. Nella finestra portale di Azure selezionare **Account di archiviazione** nel menu a sinistra.

    ![portale di Azure pagina principale selezionare l'account di archiviazione.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Selezionare il gruppo di risorse in cui creare l'account di archiviazione
1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione o usare la località predefinita.
1. Per **Prestazioni** selezionare **Premium.**

    È necessario selezionare **Premium per** **Le condivisine file come** opzione disponibile nell'elenco a discesa Tipo **di** account.

1. Per **Tipo di account Premium** scegliere **Condivisizioni file.**

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Screenshot delle prestazioni Premium selezionate.":::

1. Lasciare **l'opzione** Replica impostata sul valore predefinito **Archiviazione con ridondanza locale**.
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Crea**.

Dopo aver creato la risorsa dell'account di archiviazione, passare a essa.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per creare un account di archiviazione FileStorage, aprire un prompt di PowerShell ed eseguire i comandi seguenti, ricordando di sostituire e con i `<resource-group>` `<storage-account>` valori appropriati per l'ambiente.

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
Per creare un account di archiviazione FileStorage, aprire il terminale ed eseguire i comandi seguenti, ricordando di sostituire e con i `<resource-group>` `<storage-account>` valori appropriati per l'ambiente.

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

Dopo aver creato un account FileStorage e configurato la rete, è possibile creare una condivisione file NFS. Il processo è simile alla creazione di una condivisione SMB. Durante la creazione della condivisione si seleziona **NFS** anziché **SMB.**

1. Passare all'account di archiviazione e selezionare **Condivisioni file**.
1. Selezionare **+ Condivisione file** per creare una nuova condivisione file.
1. Assegnare un nome alla condivisione file e selezionare una capacità di cui è stato effettuato il provisioning.
1. Per **Protocollo** selezionare **NFS (anteprima).**
1. Per **Root Squash (Squash** radice) effettuare una selezione.

    - Radice squash (impostazione predefinita): l'accesso per l'utente con privilegi avanzati remoti (radice) viene mappato a UID (65534) e GID (65534).
    - Nessun squash radice: l'utente con privilegi avanzati remoti (radice) riceve l'accesso come radice.
    - Tutti gli squash: viene eseguito il mapping di tutti gli accessi utente a UID (65534) e GID (65534).
    
1. Selezionare **Crea**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Screenshot del pannello di creazione della condivisione file.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Assicurarsi che .NET Framework sia installato. Vedere [Scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verificare che la versione di PowerShell installata sia o successiva `5.1` usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [Aggiornamento di Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Installare la versione più recente del modulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Chiudere e riaprire la console di PowerShell.

1. Installare il **modulo di anteprima Az.Storage** **versione 2.5.2-preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [Installare il Azure PowerShell modulo](/powershell/azure/install-az-ps)
   
1. Per creare una condivisione file Premium con il Azure PowerShell, usare il cmdlet [New-AzRmStorageShare.](/powershell/module/az.storage/new-azrmstorageshare)

    > [!NOTE]
    > Le condivisioni file Premium vengono fatturate usando un modello con provisioning. Le dimensioni di cui è stato effettuato il provisioning della condivisione sono specificate da `QuotaGiB` di seguito. Per altre informazioni, vedere [Understanding the provisioned model](understanding-billing.md#provisioned-model) and the File di Azure pricing [page](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per creare una condivisione file Premium con l'interfaccia della riga di comando di Azure, usare [il comando az storage share create.](/cli/azure/storage/share-rm)

> [!NOTE]
> Le condivisioni file Premium vengono fatturate usando un modello con provisioning. Le dimensioni di cui è stato effettuato il provisioning della condivisione sono specificate da `quota` di seguito. Per altre informazioni, vedere [Understanding the provisioned model](understanding-billing.md#provisioned-model) and the File di Azure pricing [page](https://azure.microsoft.com/pricing/details/storage/files/).

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
Dopo aver creato una condivisione NFS, per usarla è necessario montarla nel client Linux. Per informazioni dettagliate, [vedere Come montare una condivisione NFS.](storage-files-how-to-mount-nfs-shares.md)

Se si verificano problemi, vedere Risolvere i problemi [delle condivisioni file NFS di Azure.](storage-troubleshooting-files-nfs.md)