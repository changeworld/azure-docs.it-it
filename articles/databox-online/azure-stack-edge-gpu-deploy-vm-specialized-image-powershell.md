---
title: Creare immagini di vm da un'immagine specializzata del disco rigido virtuale Windows per il Azure Stack Edge Pro GPU
description: Viene descritto come creare immagini di vm da immagini specializzate a partire da un disco rigido virtuale Windows o da un VHDX. Usare questa immagine specializzata per creare immagini di VM da usare con le macchine virtuali distribuite nel dispositivo AZURE STACK EDGE PRO GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575948"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Distribuire una macchina virtuale da un'immagine specializzata nel Azure Stack Edge Pro GPU tramite Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive i passaggi necessari per distribuire una macchina virtuale (VM) nel dispositivo GPU Azure Stack Edge Pro da un'immagine specializzata. 

Per preparare un'immagine generalizzata per la distribuzione di macchine virtuali Azure Stack Edge Pro GPU, vedere Preparare un'immagine [generalizzata](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) dal disco rigido virtuale Windows o Preparare un'immagine generalizzata da [un'immagine ISO.](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)

## <a name="about-vm-images"></a>Informazioni sulle immagini delle macchine virtuali

Un disco rigido virtuale Windows o VHDX può essere usato per creare *un'immagine specializzata* o *un'immagine generalizzata.* La tabella seguente riepiloga le differenze principali tra *le immagini specializzate* e *generalizzate.*

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro di alto livello per distribuire una macchina virtuale da un'immagine specializzata è:

1. Copiare il disco rigido virtuale in un account di archiviazione locale nel Azure Stack Edge Pro GPU.
1. Creare un nuovo disco gestito dal disco rigido virtuale.
1. Creare una nuova macchina virtuale dal disco gestito e collegare il disco gestito.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire una macchina virtuale nel dispositivo tramite PowerShell, assicurarsi che:

- Si ha accesso a un client che verrà utilizzato per connettersi al dispositivo.
    - Il client esegue un [sistema operativo supportato.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - Il client è configurato per connettersi al Azure Resource Manager locale del dispositivo in base alle istruzioni riportate in Connettersi a Azure Resource Manager [per il dispositivo.](azure-stack-edge-gpu-connect-resource-manager.md)

## <a name="verify-the-local-azure-resource-manager-connection"></a>Verificare la connessione Azure Resource Manager locale

Verificare che il client sia in grado di connettersi al Azure Resource Manager. 

1. Chiamare le API del dispositivo locale per l'autenticazione:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Specificare il nome utente `EdgeArmUser` e la password per connettersi tramite Azure Resource Manager. Se non si richiama la password, [reimpostare la password](azure-stack-edge-gpu-set-azure-resource-manager-password.md) per Azure Resource Manager e usarla per accedere.

## <a name="deploy-vm-from-specialized-image"></a>Distribuire una macchina virtuale da un'immagine specializzata

Le sezioni seguenti contengono istruzioni dettagliate per distribuire una macchina virtuale da un'immagine specializzata.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Copiare il disco rigido virtuale nell'account di archiviazione locale nel dispositivo

Seguire questa procedura per copiare il disco rigido virtuale nell'account di archiviazione locale:

1. Copiare il disco rigido virtuale di origine in un account di archiviazione BLOB locale nel Azure Stack Edge.

1. Prendere nota dell'URI risultante. Questo URI verrà utilizzato in un passaggio successivo.

    Per creare e accedere a un account di archiviazione locale, vedere le sezioni Creare un [account](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) di archiviazione tramite Caricare un [disco](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) rigido virtuale nell'articolo Distribuire macchine virtuali nel dispositivo Azure Stack Edge [tramite Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Creare un disco gestito dal disco rigido virtuale

Seguire questa procedura per creare un disco gestito da un disco rigido virtuale caricato in precedenza nell'account di archiviazione:

1. Impostare alcuni parametri.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Di seguito è riportato un output di esempio.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Creare un nuovo disco gestito.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Di seguito è riportato un output di esempio. La località qui è impostata sulla posizione dell'account di archiviazione locale ed è sempre per tutti gli account di archiviazione locali nel dispositivo `DBELocal` Azure Stack Edge Pro GPU. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Creare una macchina virtuale da un disco gestito

Seguire questa procedura per creare una macchina virtuale da un disco gestito:

1. Impostare alcuni parametri.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP` è facoltativo. Usare questo parametro per assegnare un indirizzo IP statico, altrimenti l'impostazione predefinita è un indirizzo IP dinamico che usa DHCP.

    Di seguito è riportato un output di esempio. In questo esempio viene specificato lo stesso gruppo di risorse per tutte le risorse della macchina virtuale, anche se è possibile creare e specificare gruppi di risorse separati per le risorse, se necessario.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Ottenere le informazioni sulla rete virtuale e creare una nuova interfaccia di rete.

    Questo esempio presuppone che si crei una singola interfaccia di rete nella rete virtuale `ASEVNET` predefinita associata al gruppo di risorse predefinito `ASERG` . Se necessario, è possibile specificare una rete virtuale alternativa o creare più interfacce di rete. Per altre informazioni, vedere [Aggiungere un'interfaccia di rete a una macchina virtuale tramite portale di Azure](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Di seguito è riportato un output di esempio.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Creare un nuovo oggetto di configurazione della macchina virtuale.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Aggiungere l'interfaccia di rete alla macchina virtuale.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Impostare le proprietà del disco del sistema operativo nella macchina virtuale.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    L'ultimo flag in questo comando sarà o a seconda del sistema operativo `-Windows` in uso per la macchina `-Linux` virtuale.

1. Creare la macchina virtuale

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Di seguito è riportato un output di esempio. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Eliminare macchine virtuali e risorse

Questo articolo ha usato un solo gruppo di risorse per creare tutte le risorse della macchina virtuale. L'eliminazione di tale gruppo di risorse eliminerà la macchina virtuale e tutte le risorse associate. 

1. Visualizzare prima di tutto tutte le risorse create nel gruppo di risorse.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Di seguito è riportato un output di esempio.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Eliminare il gruppo di risorse e tutte le risorse associate.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Di seguito è riportato un output di esempio.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Verificare che il gruppo di risorse sia stato eliminato. Ottenere tutti i gruppi di risorse presenti nel dispositivo. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Di seguito è riportato un output di esempio.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Preparare un'immagine generalizzata da un disco rigido virtuale Windows per distribuire macchine virtuali Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Preparare un'immagine generalizzata da un'immagine ISO per distribuire macchine virtuali in Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md) d