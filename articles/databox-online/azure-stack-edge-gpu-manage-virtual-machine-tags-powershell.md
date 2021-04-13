---
title: Gestire i tag della VM sul dispositivo GPU Azure Stack Edge Pro tramite Azure PowerShell
description: Viene descritto come creare e gestire i tag della macchina virtuale per le macchine virtuali in esecuzione in Azure Stack Edge utilizzando Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305557"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Gestire i tag delle macchine virtuali in Azure Stack Edge tramite Azure PowerShell

Questo articolo descrive come contrassegnare le macchine virtuali in esecuzione nei dispositivi GPU Azure Stack Edge Pro usando Azure PowerShell.

## <a name="about-tags"></a>Informazioni sui tag

I tag sono coppie chiave-valore definite dall'utente che possono essere assegnate a una risorsa o a un gruppo di risorse. È possibile applicare i tag alle macchine virtuali in esecuzione nel dispositivo per organizzarle in modo logico in una tassonomia. È possibile inserire i tag in una risorsa al momento della creazione o aggiungerli a una risorsa esistente. Ad esempio, è possibile applicare il nome `Organization` e il valore `Engineering` a tutte le macchine virtuali usate dal reparto tecnico dell'organizzazione.

Per altre informazioni sui tag, vedere come [gestire i tag tramite AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire una macchina virtuale nel dispositivo tramite PowerShell, verificare che:

- Si ha accesso a un client che verrà usato per connettersi al dispositivo.
    - Il client esegue un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Il client è configurato per connettersi alla Azure Resource Manager locale del dispositivo in base alle istruzioni riportate in [connettersi a Azure Resource Manager per il dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Verificare la connessione al Azure Resource Manager locale

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Aggiungere un tag a una macchina virtuale

1. Impostare alcuni parametri.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Ottenere l'oggetto macchina virtuale e i relativi tag.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Aggiungere il tag e aggiornare la macchina virtuale. L'aggiornamento della macchina virtuale può richiedere alcuni minuti.

    È possibile usare il flag facoltativo **-Force** per eseguire il comando senza conferma dell'utente.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Per ulteriori informazioni, vedere [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Visualizzare i tag di una macchina virtuale

È possibile visualizzare i tag applicati a una macchina virtuale specifica in esecuzione nel dispositivo. 

1. Definire i parametri associati alla VM di cui si desidera visualizzare i tag.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Ottenere l'oggetto VM e visualizzarne i tag.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Visualizza i tag per tutte le risorse

Per visualizzare l'elenco corrente di tag per tutte le risorse nella sottoscrizione di Azure Resource Manager locale (diversa dalla sottoscrizione di Azure) del dispositivo, usare il `Get-AzureRMTag` comando.


Di seguito è riportato un esempio di output quando più macchine virtuali sono in esecuzione nel dispositivo e si desidera visualizzare tutti i tag in tutte le macchine virtuali.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

L'output precedente indica che sono presenti tre `Organization` tag sulle macchine virtuali in esecuzione nel dispositivo.

Per visualizzare altri dettagli, usare il `-Detailed` parametro.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

L'output precedente indica che fuori dai tre tag, 2 macchine virtuali sono contrassegnate come `Engineering` e una è contrassegnata come appartenente a `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Rimuovere un tag da una macchina virtuale

1. Impostare alcuni parametri. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Ottenere l'oggetto macchina virtuale.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Rimuovere il tag e aggiornare la macchina virtuale. Usare il `-Force` flag facoltativo per eseguire il comando senza conferma dell'utente.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [gestire i tag tramite AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
