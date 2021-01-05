---
title: Panoramica e distribuzione di VM GPU sul dispositivo Azure Stack Edge Pro
description: Viene descritto come creare e gestire macchine virtuali (VM) GPU in un dispositivo Azure Stack Edge Pro usando i modelli.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 0a7f54d991d31594a6ab522dbf9e73958cde3023
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900145"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>VM GPU per il dispositivo Azure Stack Edge Pro

Questo articolo fornisce una panoramica delle macchine virtuali (VM) GPU sul dispositivo Azure Stack Edge Pro. Questo articolo descrive come creare una VM GPU e quindi installare l'estensione driver GPU per installare i driver NVIDIA appropriati. Usare i modelli di Azure Resource Manager per creare la VM GPU e installare l'estensione driver GPU. 

Questo articolo si applica alla GPU Azure Stack Edge Pro e ai dispositivi Azure Stack R Edge Pro.

## <a name="about-gpu-vms"></a>Informazioni sulle VM GPU

I dispositivi Pro Azure Stack Edge sono dotati di una o due GPU di NVIDIA Tesla T4. Per distribuire carichi di lavoro delle VM con accelerazione GPU in questi dispositivi, usare le dimensioni delle macchine virtuali ottimizzate per GPU. Ad esempio, è consigliabile usare la serie NC T4 V3 per distribuire carichi di lavoro di inferenza con GPU T4. 

Per altre informazioni, vedere [macchine virtuali della serie NC T4 V3](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Driver del sistema operativo e GPU supportati 

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie N di Azure, è necessario installare i driver GPU NVIDIA. 

L'estensione driver GPU NVIDIA installa i driver NVIDIA CUDA o GRID appropriati. È possibile installare o gestire l'estensione utilizzando i modelli di Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Sistema operativo supportato per l'estensione GPU per Windows

Questa estensione supporta i sistemi operativi (OSs) seguenti. Altre versioni possono funzionare, ma non sono state testate internamente nelle VM GPU in esecuzione nei dispositivi Azure Stack Edge Pro.

| Distribuzione | Versione |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Sistema operativo supportato per l'estensione GPU per Linux

Questa estensione supporta le distribuzioni del sistema operativo seguenti, a seconda del supporto dei driver per una versione specifica del sistema operativo. Altre versioni possono funzionare, ma non sono state testate internamente nelle VM GPU in esecuzione nei dispositivi Azure Stack Edge Pro.


| Distribuzione | Versione |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>VM GPU e Kubernetes

Prima di distribuire le VM GPU nel dispositivo, verificare le seguenti considerazioni se Kubernetes è configurato nel dispositivo.

#### <a name="for-1-gpu-device"></a>Per un dispositivo GPU 1: 

- **Creare una VM GPU seguita dalla configurazione di Kubernetes nel dispositivo**: in questo scenario, la creazione della VM GPU e la configurazione Kubernetes saranno entrambe riuscite. In questo caso, Kubernetes non avrà accesso alla GPU.

- **Configurare Kubernetes nel dispositivo seguito dalla creazione di una VM GPU**: in questo scenario, il Kubernetes richiederà la GPU sul dispositivo e la creazione della macchina virtuale avrà esito negativo perché non sono disponibili risorse GPU.

#### <a name="for-2-gpu-device"></a>Per il dispositivo GPU 2

- **Creare una VM GPU seguita dalla configurazione di Kubernetes nel dispositivo**: in questo scenario la VM GPU creata richiederà una GPU sul dispositivo e la configurazione di Kubernetes avrà esito positivo e reclamerà la restante GPU. 

- **Creare due VM GPU seguite dalla configurazione di Kubernetes nel dispositivo**: in questo scenario le due VM GPU attestano le due GPU sul dispositivo e il Kubernetes è configurato correttamente senza GPU. 

- **Configurare Kubernetes nel dispositivo seguito dalla creazione di una VM GPU**: in questo scenario, il Kubernetes reclamerà sia le GPU sul dispositivo che la creazione della macchina virtuale avrà esito negativo perché non sono disponibili risorse GPU.

Se nel dispositivo sono in esecuzione VM GPU e viene configurato anche Kubernetes, ogni volta che la macchina virtuale viene deallocata (quando si arresta o si rimuove una VM con Stop-AzureRmVM o Remove-AzureRmVM), esiste il rischio che il cluster Kubernetes ritenga tutte le GPU disponibili sul dispositivo. In tal caso, non sarà possibile riavviare le VM GPU distribuite nel dispositivo o creare VM GPU.


## <a name="create-gpu-vms"></a>Creare VM GPU

Per la distribuzione di VM GPU nel dispositivo, seguire questa procedura:

1. Identificare se il dispositivo eseguirà anche Kubernetes. Se il dispositivo eseguirà Kubernetes, è necessario creare prima la VM GPU e quindi configurare Kubernetes. Se Kubernetes è configurato per primo, verranno richieste tutte le risorse GPU disponibili e la creazione della VM GPU avrà esito negativo.

1. [Scaricare i modelli di macchina virtuale e i file di parametri](https://aka.ms/ase-vm-templates) nel computer client. Decomprimerlo in una directory che verrà usata come directory di lavoro.

1. Per creare VM GPU, seguire tutti i passaggi della distribuzione di una [macchina virtuale in Azure stack Edge Pro usando i modelli](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) tranne per le differenze seguenti: 

    1. Durante la configurazione della rete di calcolo, abilitare la porta connessa a Internet per il calcolo. In questo modo è possibile scaricare i driver GPU necessari per le estensioni GPU per le VM GPU.

        Di seguito è riportato un esempio in cui la porta 2 è stata connessa a Internet ed è stata usata per abilitare la rete di calcolo. Se è stato rilevato che Kubernetes non è necessario nel passaggio precedente, è possibile ignorare l'IP del nodo Kubernetes e l'assegnazione IP del servizio esterno.

        ![Abilitare le impostazioni di calcolo sulla porta connessa a Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Creare una VM usando i modelli. Quando si specificano le dimensioni delle VM GPU, assicurarsi di usare la serie NCasT4-V3 in `CreateVM.parameters.json` perché sono supportate per le VM GPU. Per altre informazioni, vedere [dimensioni delle VM supportate per le VM GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Dopo aver creato la VM GPU, è possibile visualizzare questa VM nell'elenco di macchine virtuali nella risorsa di Azure Stack Edge nel portale di Azure.

        ![VM GPU nell'elenco delle macchine virtuali in portale di Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Selezionare la macchina virtuale ed eseguire il drill-down ai dettagli. Copiare l'indirizzo IP allocato nella macchina virtuale.

    ![IP allocato alla VM GPU in portale di Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Dopo aver creato la macchina virtuale, distribuire l'estensione della GPU usando il modello di estensione. Per le macchine virtuali Linux, vedere [Install GPU Extension for Linux](#gpu-extension-for-linux) and for Windows VMS, vedere [Install GPU Extension for Windows](#gpu-extension-for-windows).

1. Per verificare l'installazione dell'estensione GPU, connettersi alla VM GPU:
    1. Se si usa una macchina virtuale Windows, attenersi alla procedura descritta in [connettersi a una macchina virtuale Windows](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm). [Verificare l'installazione](#verify-windows-driver-installation).
    1. Se si usa una VM Linux, seguire la procedura descritta in [connettersi a una VM Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). [Verificare l'installazione](#verify-linux-driver-installation).

1. Se necessario, è possibile riportare la rete di calcolo in base a qualsiasi esigenza. 


> [!NOTE]
> Quando si aggiorna la versione del software del dispositivo da 2012 a successiva, è necessario arrestare manualmente le VM GPU.


## <a name="install-gpu-extension"></a>Installare l'estensione della GPU

A seconda del sistema operativo per la macchina virtuale, è possibile installare l'estensione della GPU per Windows o per Linux.

> [!NOTE]
> Prima di installare l'estensione della GPU, assicurarsi che la porta abilitata per la rete di calcolo nel dispositivo sia connessa a Internet e abbia accesso. I driver GPU vengono scaricati tramite l'accesso a Internet.

### <a name="gpu-extension-for-windows"></a>Estensione GPU per Windows

Per distribuire driver GPU NVIDIA per una macchina virtuale esistente, modificare il `addGPUExtWindowsVM.parameters.json` file dei parametri e quindi distribuire il modello `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Modificare il file dei parametri

Il file `addGPUExtWindowsVM.parameters.json` accetta i parametri seguenti:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Ecco un file di parametri di esempio usato in questo articolo:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Distribuire il modello

Distribuire il modello `addGPUextensiontoVM.json` . Questo modello consente di distribuire l'estensione in una macchina virtuale esistente. Eseguire il comando seguente:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> La distribuzione dell'estensione è un processo con esecuzione prolungata e richiede circa 10 minuti per il completamento.

Di seguito è riportato un output di esempio:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
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
Di seguito è riportato un output di esempio:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente. Per `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` tenere traccia dello stato dell'installazione, fare riferimento a questo file. 


Una corretta installazione è indicata da `message` come `Enable Extension` e da `status` `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Verificare l'installazione del driver Windows

Accedere alla macchina virtuale ed eseguire l'utilità da riga di comando nvidia-smi installata con il driver. `nvidia-smi.exe`Si trova in `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Se il file non viene visualizzato, è possibile che l'installazione del driver sia ancora in esecuzione in background. Attendere 10 minuti e riprovare.

Se il driver è installato, viene visualizzato un output simile all'esempio seguente: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Per ulteriori informazioni, vedere [estensione driver GPU NVIDIA per Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>Estensione GPU per Linux

Per distribuire driver GPU NVIDIA per una macchina virtuale esistente, modificare il file dei parametri e quindi distribuire il modello `addGPUextensiontoVM.json` . Sono disponibili file di parametri specifici per Ubuntu e Red Hat Enterprise Linux (RHEL), come illustrato nelle sezioni seguenti.

#### <a name="edit-parameters-file"></a>Modificare il file dei parametri

Se si usa Ubuntu, il `addGPUExtLinuxVM.parameters.json` file accetta i parametri seguenti:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Se si usa Red Hat Enterprise Linux (RHEL), il `addGPUExtensionRHELVM.parameters.json` file accetta i parametri seguenti:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Ecco un file di parametri Ubuntu di esempio usato in questo articolo:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Distribuire il modello

Distribuire il modello `addGPUextensiontoVM.json` . Questo modello consente di distribuire l'estensione in una macchina virtuale esistente. Eseguire il comando seguente:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> La distribuzione dell'estensione è un processo con esecuzione prolungata e richiede circa 10 minuti per il completamento.

Di seguito è riportato un output di esempio:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Tenere traccia dello stato della distribuzione    
    
Distribuzione modelli è un processo con esecuzione prolungata. Per controllare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, aprire un'altra sessione di PowerShell (Esegui come amministratore). Eseguire il comando seguente: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Di seguito è riportato un output di esempio: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

L'output dell'esecuzione dell'estensione viene registrato nel file seguente: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Verificare l'installazione del driver Linux

Per verificare l'installazione del driver, attenersi alla procedura seguente:

1. Connettersi alla VM GPU. Seguire le istruzioni riportate in [connettersi a una VM Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). 

    Di seguito è riportato un output di esempio:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Eseguire l'utilità della riga di comando nvidia-smi installata con il driver. Se il driver è stato installato correttamente, sarà possibile eseguire l'utilità e visualizzare l'output seguente:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Per ulteriori informazioni, vedere [estensione driver GPU NVIDIA per Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>Rimuovi estensione GPU

Per rimuovere l'estensione della GPU, utilizzare il comando seguente:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Di seguito è riportato un output di esempio:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Passaggi successivi

[Cmdlet di Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)