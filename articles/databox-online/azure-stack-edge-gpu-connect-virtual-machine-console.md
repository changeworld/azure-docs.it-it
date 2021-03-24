---
title: Connettersi alla console della macchina virtuale nel dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come connettersi alla console della macchina virtuale in una VM in esecuzione nel dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962694"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Connettersi a una console della macchina virtuale in un dispositivo GPU Pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack soluzione GPU Pro Edge esegue carichi di lavoro non in contenitori tramite le macchine virtuali. Questo articolo descrive come connettersi alla console di una macchina virtuale distribuita nel dispositivo. 

La console macchine virtuali consente di accedere alle macchine virtuali con funzionalità di tastiera, mouse e schermo usando gli strumenti di desktop remoto comunemente disponibili. È possibile accedere alla console di e risolvere eventuali problemi riscontrati durante la distribuzione di una macchina virtuale nel dispositivo. È possibile connettersi alla console della macchina virtuale anche se non è stato possibile eseguire il provisioning della VM.


## <a name="workflow"></a>Flusso di lavoro

Il flusso di lavoro di alto livello prevede i passaggi seguenti:

1. Connettersi all'interfaccia di PowerShell nel dispositivo.
1. Abilitare l'accesso alla console alla macchina virtuale.
1. Connettersi alla macchina virtuale usando Remote Desktop Protocol (RDP).
1. Revocare l'accesso alla console alla macchina virtuale.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver completato i prerequisiti seguenti:

#### <a name="for-your-device"></a>Per il dispositivo

È necessario avere accesso a un dispositivo GPU Pro Azure Stack Edge attivato. Nel dispositivo deve essere distribuita una o più macchine virtuali. È possibile distribuire le macchine virtuali tramite Azure PowerShell, tramite i modelli o tramite il portale di Azure.

#### <a name="for-client-accessing-the-device"></a>Per l'accesso client al dispositivo

Assicurarsi di avere accesso a un sistema client che:

- Può accedere all'interfaccia di PowerShell del dispositivo. Il client esegue un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
- Il client esegue PowerShell 7,0 o versione successiva. Questa versione di PowerShell funziona per i client Windows, Mac e Linux. Vedere le istruzioni per [installare PowerShell 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Dispone di funzionalità desktop remoto. A seconda che si usi Windows, macOS o Linux, è necessario installare uno di questi [client desktop remoto](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Questo articolo fornisce istruzioni con [Windows Desktop remoto](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) e [freerdp](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Connetti alla console VM

Seguire questa procedura per connettersi alla console della macchina virtuale nel dispositivo.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Connettersi all'interfaccia di PowerShell nel dispositivo

Il primo passaggio consiste nel [connettersi all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) del dispositivo. 

### <a name="enable-console-access-to-the-vm"></a>Abilitare l'accesso alla console alla macchina virtuale

1.  Nell'interfaccia di PowerShell eseguire il comando seguente per abilitare l'accesso alla console VM.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Nell'output dell'esempio prendere nota dell'ID della macchina virtuale. Questa operazione sarà necessaria per un passaggio successivo.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Connettersi alla VM

È ora possibile usare un client Desktop remoto per connettersi alla console della macchina virtuale.

#### <a name="use-windows-remote-desktop"></a>USA Desktop remoto di Windows

1. Creare un nuovo file di testo e immettere il testo seguente.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Salvare il file con estensione *RDP* nel sistema client. Questo profilo verrà usato per connettersi alla macchina virtuale.
1. Fare doppio clic sul profilo per connettersi alla macchina virtuale. Fornire le credenziali seguenti:

    - **Nome utente**: accedere come EdgeARMUser.
    - **Password**: specificare la password di Azure Resource Manager locale per il dispositivo. Se si dimentica la password, [reimpostare Azure Resource Manager password tramite il portale di Azure](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Usare FreeRDP

Se si usa FreeRDP nel client Linux, eseguire il comando seguente: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Revocare l'accesso alla console VM

Per revocare l'accesso alla console VM, tornare all'interfaccia di PowerShell del dispositivo. Eseguire il comando seguente:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Di seguito è riportato un esempio di output:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Dopo aver terminato di usare la console VM, è consigliabile revocare l'accesso o chiudere la finestra di PowerShell per uscire dalla sessione. 

## <a name="next-steps"></a>Passaggi successivi

- Risolvere i problemi relativi a [Azure stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) nella portale di Azure.