---
title: Creare immagini di VM da un'immagine specializzata del disco rigido virtuale Windows per il dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come creare immagini di VM da immagini specializzate a partire da un disco rigido virtuale Windows o da un VHDX. Usare questa immagine specializzata per creare immagini di VM da usare con le macchine virtuali distribuite nel dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556131"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Distribuire una macchina virtuale da un'immagine specializzata nel dispositivo Azure Stack Edge Pro tramite Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive i passaggi necessari per distribuire una macchina virtuale (VM) nel dispositivo Azure Stack Edge Pro da un'immagine specializzata. 

## <a name="about-specialized-images"></a>Informazioni sulle immagini specializzate

È possibile usare un disco rigido virtuale Windows o VHDX per creare un'immagine *specializzata* o un'immagine *generalizzata* . Nella tabella seguente sono riepilogate le differenze principali tra le immagini *specializzate* e quelle *generalizzate* .


|Tipo di immagine  |Generalizzata  |Specializzata  |
|---------|---------|---------|
|Destinazione     |Distribuito in qualsiasi sistema         | Destinato a un sistema specifico        |
|Installazione dopo l'avvio     | Il programma di installazione è necessario al primo avvio della macchina virtuale.          | Il programma di installazione non è necessario. <br> Piattaforma attiva la macchina virtuale.        |
|Configurazione     |Sono necessari il nome host, l'utente amministratore e altre impostazioni specifiche per le macchine virtuali.         |Pre-configurato.         |
|Utilizzato per     |Creare più macchine virtuali nuove dalla stessa immagine.         |Eseguire la migrazione di un computer specifico o il ripristino di una VM dal backup precedente.         |


Questo articolo illustra i passaggi necessari per eseguire la distribuzione da un'immagine specializzata. Per eseguire la distribuzione da un'immagine generalizzata, vedere [usare un disco rigido virtuale di Windows generalizzato](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) per il dispositivo.


## <a name="vm-image-workflow"></a>Flusso di lavoro delle immagini di VM

Il flusso di lavoro di alto livello per la distribuzione di una macchina virtuale da un'immagine specializzata è:

1. Copiare il disco rigido virtuale in un account di archiviazione locale sul dispositivo GPU Azure Stack Edge Pro.
1. Creare un nuovo disco gestito dal disco rigido virtuale.
1. Creare una nuova macchina virtuale dal disco gestito e alleghi il disco gestito.


## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire una macchina virtuale nel dispositivo tramite PowerShell, verificare che:

- Si ha accesso a un client che verrà usato per connettersi al dispositivo.
    - Il client esegue un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Il client è configurato per connettersi alla Azure Resource Manager locale del dispositivo in base alle istruzioni riportate in [connettersi a Azure Resource Manager per il dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>Verificare la connessione di Azure Resource Manager locale

Verificare che il client sia in grado di connettersi al Azure Resource Manager locale. 

1. Chiamare le API del dispositivo locale per l'autenticazione:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Specificare il nome utente `EdgeArmUser` e la password per la connessione tramite Azure Resource Manager. Se non si richiama la password, [reimpostare la password per Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) e usare questa password per accedere.
 

## <a name="deploy-vm-from-specialized-image"></a>Distribuire una macchina virtuale da un'immagine specializzata

Le sezioni seguenti contengono istruzioni dettagliate per la distribuzione di una macchina virtuale da un'immagine specializzata.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Copiare un disco rigido virtuale nell'account di archiviazione locale nel dispositivo

Per copiare il disco rigido virtuale nell'account di archiviazione locale, seguire questa procedura:

1. Copiare il disco rigido virtuale di origine in un account di archiviazione BLOB locale nell'Azure Stack Edge. 

1. Prendere nota dell'URI risultante. Questo URI verrà usato in un passaggio successivo.
    
    Per creare e accedere a un account di archiviazione locale, vedere le sezioni [creare un account di archiviazione](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) tramite il [caricamento di un disco rigido virtuale](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) nell'articolo: [distribuire macchine virtuali nel dispositivo Azure stack Edge tramite Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Creare un disco gestito dal disco rigido virtuale

Seguire questa procedura per creare un disco gestito da un disco rigido virtuale caricato in precedenza nell'account di archiviazione:

1. Impostare alcuni parametri.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Di seguito è riportato un esempio di output.

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

    Di seguito è riportato un esempio di output. Il percorso è impostato sul percorso dell'account di archiviazione locale ed è sempre `DBELocal` per tutti gli account di archiviazione locali sul dispositivo GPU Azure stack Edge Pro. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Creare una macchina virtuale da un disco gestito

Per creare una macchina virtuale da un disco gestito, seguire questa procedura:

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
    > `PrivateIP` è facoltativo. Usare questo parametro per assegnare un indirizzo IP statico. il valore predefinito è un indirizzo IP dinamico che usa DHCP.

    Di seguito è riportato un esempio di output. In questo esempio viene specificato lo stesso gruppo di risorse per tutte le risorse della macchina virtuale, anche se è possibile creare e specificare gruppi di risorse separati per le risorse, se necessario.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Ottenere le informazioni sulla rete virtuale e creare una nuova interfaccia di rete.

    Questo esempio presuppone che si stia creando una singola interfaccia di rete nella rete virtuale predefinita `ASEVNET` associata al gruppo di risorse predefinito `ASERG` . Se necessario, è possibile specificare una rete virtuale alternativa o creare più interfacce di rete. Per altre informazioni, vedere [aggiungere un'interfaccia di rete a una VM tramite il portale di Azure](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Di seguito è riportato un esempio di output.

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
    L'ultimo flag in questo comando sarà `-Windows` o `-Linux` a seconda del sistema operativo usato per la VM.

1. Creare la macchina virtuale

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Di seguito è riportato un esempio di output. 

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

## <a name="delete-vm-and-resources"></a>Eliminare VM e risorse

Questo articolo ha usato un solo gruppo di risorse per creare tutte le risorse della macchina virtuale. Eliminando il gruppo di risorse, la macchina virtuale e tutte le risorse associate vengono eliminate. 

1. Prima di tutto, visualizzare tutte le risorse create nel gruppo di risorse.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Di seguito è riportato un esempio di output.
    
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
    Di seguito è riportato un esempio di output.
    
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
    Di seguito è riportato un esempio di output.

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

A seconda della natura della distribuzione, è possibile scegliere una delle procedure riportate di seguito.

- [Distribuire una macchina virtuale da un'immagine generalizzata tramite Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Distribuire una macchina virtuale tramite portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
