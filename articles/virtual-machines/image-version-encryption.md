---
title: Anteprima - Creare una versione di immagine crittografata con le chiavi personali
description: Creare una versione dell'immagine in una raccolta di immagini condivise usando chiavi di crittografia gestite dal cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 601b8236ca413dd510585bdfffddc3e892caa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759670"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Anteprima: Usare le chiavi gestite dal cliente per crittografare le immagini

Le immagini in una raccolta di immagini condivise vengono archiviate come snapshot, quindi vengono crittografate automaticamente tramite la crittografia lato server. La crittografia lato server usa la crittografia [AES a](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)256 bit, una delle crittografie a blocchi più potenti disponibili. La crittografia lato server è anche conforme a FIPS 140-2. Per altre informazioni sui moduli crittografici sottostanti azure managed disks, vedere [Cryptography API: Next Generation (API di crittografia: generazione successiva).](/windows/desktop/seccng/cng-portal)

È possibile usare chiavi gestite dalla piattaforma per la crittografia delle immagini o usare chiavi personalizzate. È anche possibile usare entrambi insieme, per la crittografia doppia. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dischi nelle immagini. 

La crittografia lato server tramite chiavi gestite dal cliente usa Azure Key Vault. È possibile importare [le chiavi RSA nell'insieme](../key-vault/keys/hsm-protected-keys.md) di credenziali delle chiavi o generare nuove chiavi RSA in Azure Key Vault.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo richiede che sia già impostata una crittografia del disco in ogni area in cui si vuole replicare l'immagine:

- Per usare solo una chiave gestita dal cliente, vedere gli articoli sull'abilitazione delle chiavi gestite dal cliente con la crittografia lato server [usando](./disks-enable-customer-managed-keys-portal.md) il portale di Azure [o PowerShell.](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)

- Per usare sia le chiavi gestite dalla piattaforma che le chiavi gestite dal cliente (per la crittografia doppia), vedere gli articoli sull'abilitazione della crittografia doppia dei dati in pausa [usando](./disks-enable-double-encryption-at-rest-portal.md) il portale di Azure [o PowerShell.](./windows/disks-enable-double-encryption-at-rest-powershell.md)

   > [!IMPORTANT]
   > È necessario usare il collegamento [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) per accedere al portale di Azure. La crittografia doppia dei dati in stato di inquieto non è attualmente visibile nel portale di Azure a meno che non si usi tale collegamento.

## <a name="limitations"></a>Limitazioni

Quando si usano chiavi gestite dal cliente per crittografare le immagini in una raccolta di immagini condivise, si applicano queste limitazioni:   

- I set di chiavi di crittografia devono essere nella stessa sottoscrizione dell'immagine.

- I set di chiavi di crittografia sono risorse a livello di area, quindi ogni area richiede un set di chiavi di crittografia diverso.

- Non è possibile copiare o condividere immagini che usano chiavi gestite dal cliente. 

- Dopo aver usato le proprie chiavi per crittografare un disco o un'immagine, non è possibile tornare all'uso di chiavi gestite dalla piattaforma per crittografare tali dischi o immagini.


> [!IMPORTANT]
> La crittografia tramite chiavi gestite dal cliente è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Per l'anteprima pubblica, è prima necessario registrare la funzionalità:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Il completamento della registrazione richiede alcuni minuti. Usare `Get-AzProviderFeature` per controllare lo stato della registrazione della funzionalità:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando `RegistrationState` restituisce , è possibile passare al passaggio `Registered` successivo.

Controllare la registrazione del provider. Verificare che restituisca `Registered`.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se non restituisce , usare `Registered` il codice seguente per registrare i provider:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Per specificare un set di crittografia del disco per una versione dell'immagine,  [usare New-AzGalleryImageDefinition con](/powershell/module/az.compute/new-azgalleryimageversion) il `-TargetRegion` parametro : 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Creare una macchina virtuale

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è la stessa della creazione di [una macchina virtuale generalizzata](vm-generalized-image-version-powershell.md) [o specializzata](vm-specialized-image-version-powershell.md) da un'immagine. Usare il set di parametri estesi e aggiungere `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` alla configurazione della macchina virtuale.

Per i dischi dati, aggiungere il `-DiskEncryptionSetId $setID` parametro quando si usa [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Per l'anteprima pubblica, è prima necessario registrarsi per la funzionalità. La registrazione richiede circa 30 minuti.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Controllare lo stato della registrazione della funzionalità:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando questo codice restituisce `"state": "Registered"` , è possibile passare al passaggio successivo.

Controllare la registrazione:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se non è registrato, eseguire il comando seguente:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Per specificare un set di crittografia del disco per una versione dell'immagine, usare [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) con il `--target-region-encryption` parametro . Il formato per è un elenco delimitato da virgole di chiavi per la crittografia `--target-region-encryption` del sistema operativo e dei dischi dati. L'aspetto dovrebbe risultare simile al seguente: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Se l'origine per il disco del sistema operativo è un disco gestito o una macchina virtuale, usare `--managed-image` per specificare l'origine per la versione dell'immagine. In questo esempio l'origine è un'immagine gestita con un disco del sistema operativo e un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se l'origine per il disco del sistema operativo è uno snapshot, usare `--os-snapshot` per specificarlo. Se sono presenti snapshot del disco dati che devono far parte anche della versione dell'immagine, aggiungerli. Usare `--data-snapshot-luns` per specificare il LUN e usare per specificare gli `--data-snapshots` snapshot.

In questo esempio le origini sono costituite da snapshot del disco. Nel LUN 0 sono presenti un disco del sistema operativo e un disco dati. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Creare la VM

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è la stessa della creazione di una [macchina virtuale generalizzata](vm-generalized-image-version-cli.md) [o specializzata](vm-specialized-image-version-cli.md) da un'immagine. È sufficiente aggiungere `--os-disk-encryption-set` il parametro con l'ID del set di crittografia. Per i dischi dati, aggiungere con un elenco delimitato da spazi dei set di crittografia dei dischi `--data-disk-encryption-sets` per i dischi dati.


## <a name="portal"></a>Portale

Quando si crea la versione dell'immagine nel portale, è possibile usare la **scheda Crittografia** per applicare i set di crittografia di archiviazione.

> [!IMPORTANT]
> Per usare la crittografia doppia, è necessario usare il collegamento [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) per accedere al portale di Azure. La crittografia doppia dei dati in stato di inquieto non è attualmente visibile nel portale di Azure a meno che non si usi tale collegamento.


1. Nella pagina **Crea una versione dell'immagine** selezionare la **scheda** Crittografia.
2. In **Tipo di crittografia** selezionare Crittografia **dei** dati in pausa con una chiave gestita dal cliente o Crittografia doppia con chiavi gestite dalla piattaforma e gestite **dal cliente.** 
3. Per ogni disco nell'immagine, selezionare un set di crittografia dall'elenco a discesa **Set** di crittografia del disco. 

### <a name="create-the-vm"></a>Creare la macchina virtuale

È possibile creare una macchina virtuale da una versione dell'immagine e usare chiavi gestite dal cliente per crittografare i dischi. Quando si crea la macchina virtuale  nel portale,  nella scheda Dischi selezionare Crittografia  dei dati in pausa con chiavi gestite dal cliente o Doppia crittografia con chiavi gestite dalla piattaforma e gestite dal cliente per Tipo di **crittografia**. È quindi possibile selezionare il set di crittografia dall'elenco a discesa.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sulla [crittografia dei dischi sul lato server](./disk-encryption.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere Fornire Azure Marketplace informazioni sul piano di acquisto durante [la creazione di immagini.](marketplace-images.md)