---
title: Guida introduttiva Azure Cloud Shell-PowerShell
description: Informazioni su come usare PowerShell nel browser con Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 7ff58c4e463b4ad47680b9140403e9ae5e22b057
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045281"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Guida introduttiva a PowerShell in Azure Cloud Shell

Questo documento illustra dettagliatamente come usare PowerShell in Cloud Shell nel [portale di Azure](https://portal.azure.com/).

> [!NOTE]
> È disponibile anche una guida introduttiva a [Bash in Azure Cloud Shell](quickstart.md).

## <a name="start-cloud-shell"></a>Avviare Cloud Shell

1. Fare clic sul pulsante **Cloud Shell** dalla barra di navigazione in alto nel portale di Azure

   ![Screenshot che illustra come avviare Azure Cloud Shell dalla portale di Azure.](media/quickstart-powershell/shell-icon.png)

2. Selezionare l'ambiente PowerShell dall'elenco a discesa per accedere all’unità Azure `(Azure:)`

   ![Screenshot che illustra come selezionare l'ambiente di PowerShell per la Azure Cloud Shell.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Esecuzione dei comandi di PowerShell

Eseguire i comandi regolari di PowerShell in Cloud Shell, tra cui:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>Interazione con macchine virtuali

È possibile trovare tutte le macchine virtuali della sottoscrizione corrente tramite la directory `VirtualMachines`.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Richiamare uno script PowerShell tra le macchine virtuali remote

 > [!WARNING]
 > Consultare [Risoluzione dei problemi di gestione remota delle macchine virtuali di Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Se si dispone di una macchina virtuale, MyVM1, usare `Invoke-AzVMCommand` per richiamare un blocco di script di PowerShell nel computer remoto.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  È anche possibile passare prima alla directory VirtualMachines e poi eseguire `Invoke-AzVMCommand` come indicato di seguito.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Accedere in modo interattivo a una macchina virtuale remota

È possibile utilizzare `Enter-AzVM` per accedere in modo interattivo a una macchina virtuale in esecuzione in Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

È anche possibile prima passare alla directory `VirtualMachines` e poi eseguire `Enter-AzVM` come indicato di seguito.

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Scoprire WebApp

Accedendo alla directory `WebApps`, è possibile esplorare facilmente le risorse app Web

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Per eseguire l'autenticazione a server o macchine virtuali tramite SSH, generare la coppia di chiavi pubblica-privata in Cloud Shell e pubblicare la chiave pubblica in `authorized_keys` nel computer remoto, ad esempio `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> È possibile creare chiavi SSH pubblica-privata usando `ssh-keygen` e pubblicarle in `$env:USERPROFILE\.ssh` in Cloud Shell.

### <a name="using-ssh"></a>Uso di SSH

Seguire le istruzioni riportate [qui](../virtual-machines/linux/quick-create-powershell.md) per creare una nuova configurazione di macchina virtuale usando i cmdlet di Azure PowerShell.
Prima di chiamare `New-AzVM` per avviare la distribuzione, aggiungere la chiave pubblica SSH alla configurazione di macchina virtuale.
La nuova macchina virtuale conterrà la chiave pubblica nel percorso `~\.ssh\authorized_keys`, abilitando quindi le sessioni SSH senza credenziali nella macchina virtuale.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Elenco dei comandi disponibili

Nell'unità `Azure` digitare `Get-AzCommand` per ottenere i comandi di Azure specifici per il contesto.

In alternativa, è sempre possibile usare `Get-Command *az* -Module Az.*` per scoprire i comandi di Azure disponibili.

## <a name="install-custom-modules"></a>Installazione di moduli personalizzati

È possibile eseguire `Install-Module` per installare moduli dalla [PowerShell Gallery][gallery].

## <a name="get-help"></a>Get-Help

Digitare `Get-Help` per ottenere informazioni su PowerShell in Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Per un comando specifico, è comunque possibile eseguire `Get-Help` e subito dopo un cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Usare File di Azure per archiviare i dati

È possibile creare uno script, ad esempio `helloworld.ps1`, e salvarlo nel `clouddrive` personale per usarlo in diverse sessioni di shell.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Quando si usa PowerShell in Cloud Shell la volta successiva, il file `helloworld.ps1` sarà presente nella directory `$HOME\clouddrive` che monta la condivisione file di Azure.

## <a name="use-custom-profile"></a>Usare un profilo personalizzato

È possibile personalizzare l'ambiente PowerShell creando un profilo o più profili PowerShell `profile.ps1` (o `Microsoft.PowerShell_profile.ps1`).
Salvarlo in `$profile.CurrentUserAllHosts` (o `$profile.CurrentUserAllHosts`) in modo che possa essere caricato in ogni sessione di PowerShell in Cloud Shell.

Per informazioni su come creare un profilo, fare riferimento a [Informazioni sui profili][profile].

## <a name="use-git"></a>Usare Git

Per clonare un repository git nella Cloud Shell, è necessario creare un [token di accesso personale][githubtoken] e usarlo come nome utente. Dopo avere ottenuto il token, clonare il repository come indicato di seguito:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Chiudere la shell

Digitare `exit` per terminare la sessione.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
