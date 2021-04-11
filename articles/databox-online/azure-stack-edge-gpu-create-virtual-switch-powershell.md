---
title: Creare un nuovo Commuter virtuale in Azure Stack Edge tramite PowerShell
description: Viene descritto come creare un commutire virtuale in un dispositivo Azure Stack Edge usando PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556177"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Creare un nuovo Commuter virtuale nella GPU di Azure Stack Edge Pro tramite PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come creare un nuovo Commuti virtuale sul dispositivo GPU Azure Stack Edge Pro. Ad esempio, è possibile creare un nuovo Commuti virtuale se si desidera che le macchine virtuali si connettano tramite una porta di rete fisica diversa.

## <a name="vm-deployment-workflow"></a>Flusso di lavoro di distribuzione della VM

1. Connettersi all'interfaccia di PowerShell nel dispositivo.
2. Eseguire query sulle interfacce di rete fisiche disponibili.
3. Creare un commutatore virtuale.
4. Verificare la rete virtuale e la subnet create automaticamente.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

- Si ha accesso a un computer client in grado di accedere all'interfaccia di PowerShell del dispositivo. Vedere [connettersi all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    Nel computer client deve essere in esecuzione un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).

- Usare l'interfaccia utente locale per abilitare il calcolo in una delle interfacce di rete fisiche del dispositivo in base alle istruzioni riportate in [abilitare la rete di calcolo](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) nel dispositivo. 


## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[Connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Eseguire query sulle interfacce di rete disponibili

1. Usare il comando seguente per visualizzare un elenco di interfacce di rete fisiche in cui è possibile creare un nuovo Commuter virtuale. Viene selezionata una di queste interfacce di rete.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Di seguito è riportato un esempio di output:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Scegliere un'interfaccia di rete:

    - Nello stato **up** . 
    - Non usato da alcun commutatore virtuale esistente. Attualmente, è possibile configurare un solo vswitch per ogni interfaccia di rete. 
    
    Per verificare il Commuter virtuale esistente e l'associazione dell'interfaccia di rete, eseguire il `Get-HcsExternalVirtualSwitch` comando.
 
    Di seguito è riportato un esempio di output.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    In questo caso, la porta 2 è associata a un commutire virtuale esistente e non deve essere utilizzata.

## <a name="create-a-virtual-switch"></a>Creare un commutatore virtuale

Usare il cmdlet seguente per creare un nuovo commutire virtuale nell'interfaccia di rete specificata. Al termine di questa operazione, le istanze di calcolo possono usare la nuova rete virtuale.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Usare il `Get-HcsExternalVirtualSwitch` comando per identificare l'opzione appena creata. La nuova opzione creata viene denominata come `vswitch-<InterfaceAlias>` . 

Di seguito è riportato un esempio di output:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Verificare la rete, la subnet 

Dopo aver creato il nuovo Commuter virtuale, Azure Stack GPU Pro Edge crea automaticamente una rete virtuale e una subnet corrispondenti. È possibile usare questa rete virtuale durante la creazione di macchine virtuali.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite il portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
