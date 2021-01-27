---
title: Uso di estensioni di script personalizzate per le macchine virtuali nel dispositivo Azure Stack Edge Pro
description: Viene descritto come installare estensioni di script personalizzate in macchine virtuali (VM) in esecuzione in un dispositivo Azure Stack Edge Pro usando i modelli.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 8b233211f47250d4742d35cd0782cdd241839496
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804866"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Distribuire un'estensione di script personalizzata in macchine virtuali in esecuzione nel dispositivo Azure Stack Edge Pro

L'estensione script personalizzata Scarica ed esegue script o comandi in macchine virtuali in esecuzione nei dispositivi Azure Stack Edge Pro. Questo articolo illustra come installare ed eseguire l'estensione script personalizzata usando un modello di Azure Resource Manager. 

Questo articolo si applica a Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e a dispositivi Mini R Azure Stack Edge.

## <a name="about-custom-script-extension"></a>Informazioni sull'estensione script personalizzato

L'estensione dello script personalizzata è utile per la configurazione post-distribuzione, l'installazione del software o qualsiasi altra attività di configurazione o gestione. È possibile scaricare gli script da archiviazione di Azure o da un altro percorso Internet accessibile oppure è possibile fornire script o comandi al runtime dell'estensione.

L'estensione per script personalizzati è integrabile con i modelli di Azure Resource Manager. È anche possibile eseguirla tramite l'interfaccia della riga di comando di Azure, PowerShell o l'API REST di Macchine virtuali di Azure.

## <a name="os-for-custom-script-extension"></a>Sistema operativo per l'estensione di script personalizzata

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Sistema operativo supportato per l'estensione di script personalizzata in Windows

L'estensione di script personalizzata per Windows viene eseguita nei sistemi operativi seguenti. Altre versioni possono funzionare, ma non sono state testate internamente in macchine virtuali in esecuzione su dispositivi Azure Stack Edge Pro.

| Distribuzione | Versione |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Sistema operativo supportato per l'estensione di script personalizzati in Linux

L'estensione dello script personalizzata per Linux viene eseguita nei sistemi operativi seguenti. Altre versioni possono funzionare, ma non sono state testate internamente in macchine virtuali in esecuzione su dispositivi Azure Stack Edge Pro.

| Distribuzione | Versione |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Prerequisiti

1. [Scaricare i modelli di macchina virtuale e i file di parametri](https://aka.ms/ase-vm-templates) nel computer client. Decomprimere il download in una directory che verrà usata come directory di lavoro.

1. È necessario che una macchina virtuale sia stata creata e distribuita nel dispositivo. Per creare macchine virtuali, seguire tutti i passaggi in [distribuire una macchina virtuale in Azure stack Edge Pro usando i modelli](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Se è necessario scaricare uno script, ad esempio da GitHub o da archiviazione di Azure esternamente, durante la configurazione della rete di calcolo, abilitare la porta connessa a Internet per il calcolo. In questo modo è possibile scaricare lo script.

    Nell'esempio seguente la porta 2 è stata connessa a Internet ed è stata usata per abilitare la rete di calcolo. Se nel passaggio precedente è stato rilevato che Kubernetes non è necessario, è possibile ignorare l'IP del nodo Kubernetes e l'assegnazione IP del servizio esterno.

    ![Abilitare le impostazioni di calcolo sulla porta connessa a Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Installare l'estensione script personalizzata

A seconda del sistema operativo per la macchina virtuale, è possibile installare l'estensione di script personalizzata per Windows o per Linux.
 

### <a name="custom-script-extension-for-windows"></a>Estensione Script personalizzato per Windows

Per distribuire l'estensione di script personalizzata per Windows per una macchina virtuale in esecuzione nel dispositivo, modificare il `addCSExtWindowsVM.parameters.json` file dei parametri e quindi distribuire il modello `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Modificare il file dei parametri

Il file `addCSExtWindowsVM.parameters.json` accetta i parametri seguenti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Specificare il nome della macchina virtuale, il nome dell'estensione e il comando che si vuole eseguire.

Ecco il file di parametri di esempio usato in questo articolo.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Distribuire il modello

Distribuire il modello `addCSextensiontoVM.json` . Questo modello consente di distribuire l'estensione in una macchina virtuale esistente. Eseguire il comando seguente:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> La distribuzione dell'estensione è un processo con esecuzione prolungata e richiede circa 10 minuti per il completamento.

Di seguito è riportato un esempio di output:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Tenere traccia della distribuzione

Per controllare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Di seguito è riportato un esempio di output:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Al termine della distribuzione, viene `ProvisioningState` modificato in `Succeeded` .

L'output dell'estensione viene registrato nei file presenti nella cartella seguente nella macchina virtuale di destinazione. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

I file specificati vengono scaricati nella cartella seguente nella macchina virtuale di destinazione.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
dove <n> è un numero intero decimale che può variare nelle diverse esecuzioni dell'estensione. Il valore 1. * corrisponde al valore effettivo corrente `typeHandlerVersion` dell'estensione. Ad esempio, la directory effettiva in questa istanza era `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


In questo esempio, il comando da eseguire per l'estensione personalizzata è: `md C:\\Users\\Public\\Documents\\test` . Quando l'estensione viene installata correttamente, è possibile verificare che la directory sia stata creata nella macchina virtuale nel percorso specificato nel comando. 


### <a name="custom-script-extension-for-linux"></a>Estensione script personalizzata per Linux

Per distribuire l'estensione di script personalizzata per Windows per una macchina virtuale in esecuzione nel dispositivo, modificare il `addCSExtLinuxVM.parameters.json` file dei parametri e quindi distribuire il modello `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Modificare il file dei parametri

Il file `addCSExtLinuxVM.parameters.json` accetta i parametri seguenti:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Specificare il nome della macchina virtuale, il nome dell'estensione e il comando che si vuole eseguire.

Ecco un file di parametri di esempio usato in questo articolo:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> La distribuzione dell'estensione è un processo con esecuzione prolungata e richiede circa 10 minuti per il completamento.

Di seguito è riportato un esempio di output:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`È stato impostato per creare un file `file2.txt` nella `/home/Administrator` Directory e il contenuto del file è `some text` . In questo caso, è possibile verificare che il file sia stato creato nel percorso specificato.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Tenere traccia dello stato della distribuzione    
    
Distribuzione modelli è un processo con esecuzione prolungata. Per controllare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, aprire un'altra sessione di PowerShell (Esegui come amministratore). Eseguire il comando seguente: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Di seguito è riportato un esempio di output: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Al termine della distribuzione, viene `ProvisioningState` modificato in `Succeeded` .

L'output dell'esecuzione dell'estensione viene registrato nel file seguente: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Rimuovi estensione script personalizzato

Per rimuovere l'estensione dello script personalizzato, usare il comando seguente:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Di seguito è riportato un esempio di output:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Passaggi successivi

[Cmdlet di Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
