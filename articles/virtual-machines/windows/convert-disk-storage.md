---
title: Convertire l'archiviazione di dischi gestiti tra diversi tipi di dischi usando Azure PowerShell
description: Come convertire i dischi gestiti di Azure tra i diversi tipi di dischi usando Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.openlocfilehash: e2541607b116159e4f6ec4028c83ecc9a45eded8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550740"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aggiornare il tipo di archiviazione di un disco gestito

Sono disponibili quattro tipi di dischi di Azure Managed disks: Azure ultra disks, SSD Premium, SSD standard e HDD standard. È possibile passare da unità SSD Premium, unità SSD standard e HDD standard in base alle esigenze di prestazioni. Non si è ancora in grado di passare da o a un disco Ultra, è necessario distribuirne uno nuovo.

Questa funzionalità non è supportata per i dischi non gestiti. Tuttavia, è possibile [convertire facilmente un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) per poter passare da un tipo di disco all'altra.



## <a name="before-you-begin"></a>Prima di iniziare

* Poiché la conversione richiede un riavvio della macchina virtuale (VM), è consigliabile pianificare la migrazione dell'archiviazione su disco durante una finestra di manutenzione preesistente.
* Se il disco non è gestito, [convertirlo in un disco gestito, in](convert-unmanaged-to-managed-disks.md) modo da poter passare tra le opzioni di archiviazione.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Passa tutti i dischi gestiti di una macchina virtuale tra un account e un altro

Questo esempio illustra come convertire tutti i dischi di una macchina virtuale in archiviazione Premium. Tuttavia, modificando la variabile $storageType in questo esempio, è possibile convertire il tipo di dischi della macchina virtuale in unità SSD standard o HDD standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](../sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passa singoli dischi gestiti tra standard e Premium

Per il carico di lavoro di sviluppo/test, potrebbe essere necessario un insieme di dischi standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che necessitano di prestazioni migliori. Questo esempio illustra come convertire un singolo disco della macchina virtuale dall'archiviazione standard a Premium. Tuttavia, modificando la variabile $storageType in questo esempio, è possibile convertire il tipo di dischi della macchina virtuale in unità SSD standard o HDD standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](../sizes.md) da supportare l'archiviazione Premium. Questo esempio mostra anche come passare a una dimensione che supporta archiviazione Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Passare i dischi gestiti da un tipo di disco a un altro

Seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la macchina virtuale dall'elenco di **macchine virtuali**.
3. Se la macchina virtuale non viene arrestata, selezionare **Arresta** nella parte superiore del riquadro di **Panoramica** della macchina virtuale e attendere l'arresto della macchina virtuale.
4. Nel riquadro della macchina virtuale selezionare **dischi** dal menu.
5. Selezionare il disco che si desidera convertire.
6. Selezionare **configurazione** dal menu.
7. Modificare il tipo di **account** dal tipo di disco originale al tipo di disco desiderato.
8. Selezionare **Save (Salva**) e chiudere il riquadro del disco.

La conversione del tipo di disco è immediata. È possibile avviare la macchina virtuale dopo la conversione.

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una macchina virtuale usando [snapshot](snapshot-copy-managed-disk.md).