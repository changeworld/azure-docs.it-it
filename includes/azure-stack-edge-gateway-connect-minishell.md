---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081099"
---
A seconda del sistema operativo del client, le procedure per connettersi in remoto al dispositivo sono diverse.

### <a name="remotely-connect-from-a-windows-client"></a>Connettersi in remoto da un client Windows


#### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

- Il client Windows esegue Windows PowerShell 5,0 o versione successiva.
- Il client Windows ha la catena di firma (certificato radice) corrispondente al certificato del nodo installato nel dispositivo. Per istruzioni dettagliate, vedere [installare un certificato nel client Windows](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- Il `hosts` file che si trova in `C:\Windows\System32\drivers\etc` per il client Windows ha una voce corrispondente al certificato del nodo nel formato seguente:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Ecco una voce di esempio per il `hosts` file:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Procedura dettagliata

Per connettersi in remoto da un client Windows, seguire questa procedura.

1. Eseguire una sessione di Windows PowerShell come amministratore.
2. Verificare che il servizio Gestione remota Windows sia in esecuzione nel client. Al prompt dei comandi digitare:

    `winrm quickconfig`

    Per altre informazioni, vedere [Installazione e configurazione di Gestione remota Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Assegnare una variabile all'indirizzo IP del dispositivo.

    $ip = "<device_ip>"

    Sostituire `<device_ip>` con l'indirizzo IP del dispositivo.

4. Per aggiungere l'indirizzo IP del dispositivo all'elenco di host attendibili del client, digitare il comando seguente:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Avviare una sessione di Windows PowerShell nel dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Se viene visualizzato un errore relativo alla relazione di trust, verificare se la catena di firma del certificato del nodo caricato nel dispositivo è installata anche sul client che accede al dispositivo.

    Se non si usano i certificati (si consiglia di usare i certificati!), è possibile ignorare questo controllo usando le opzioni di sessione: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > Quando si usa l' `-UseSSL` opzione, si esegue la comunicazione remota tramite PowerShell su *https*. Si consiglia di usare sempre *https* per connettersi in remoto tramite PowerShell. 

6. Fornire la password quando richiesto. Usare la stessa password usata per accedere all'interfaccia utente Web locale. La password dell'interfaccia utente Web locale predefinita è *Password1*. Quando ci si connette correttamente al dispositivo usando PowerShell remoto, viene visualizzato l'output di esempio seguente:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> Nella versione corrente è possibile connettersi all'interfaccia di PowerShell del dispositivo solo tramite un client Windows. L' `-UseSSL` opzione non funziona con i client Linux.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->