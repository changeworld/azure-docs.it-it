---
title: Distribuire macchine virtuali nel dispositivo Azure Stack Edge Pro tramite modelli
description: Viene descritto come creare e gestire macchine virtuali (VM) in un dispositivo Azure Stack Edge Pro usando i modelli.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/16/2020
ms.author: alkohli
ms.openlocfilehash: 69d5a0a69bcd820fd59da0a18b3838b65a6a0460
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763431"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Distribuire macchine virtuali nel dispositivo GPU Azure Stack Edge Pro tramite modelli

Questa esercitazione descrive come creare e gestire una macchina virtuale nel dispositivo Azure Stack Edge Pro usando i modelli. Questi modelli sono file JavaScript Object Notation (JSON) che definiscono l'infrastruttura e la configurazione per la VM. In questi modelli si specificano le risorse da distribuire e le proprietà di tali risorse.

I modelli sono flessibili in ambienti diversi, in quanto possono assumere parametri come input in fase di esecuzione da un file. La struttura di denominazione standard è `TemplateName.json` per il modello e `TemplateName.parameters.json` per il file dei parametri. Per altre informazioni sui modelli ARM, vedere [che cosa sono i modelli di Azure Resource Manager?](../azure-resource-manager/templates/overview.md).

In questa esercitazione verranno usati modelli di esempio pre-scritti per la creazione di risorse. Non è necessario modificare il file modello ed è possibile modificare solo i `.parameters.json` file per personalizzare la distribuzione nel computer. 

## <a name="vm-deployment-workflow"></a>Flusso di lavoro di distribuzione della VM

Per distribuire le macchine virtuali Azure Stack Edge Pro in molti dispositivi, è possibile usare un singolo disco rigido virtuale preparata con Sysprep per la flotta completa, lo stesso modello per la distribuzione e apportare solo modifiche minime ai parametri del modello per ogni percorso di distribuzione (queste modifiche potrebbero essere manualmente come in questo caso o a livello di codice). 

Il riepilogo generale del flusso di lavoro di distribuzione con i modelli è il seguente:

1. **Configurare i prerequisiti** : sono disponibili tre tipi di prerequisiti: dispositivo, client e per la macchina virtuale.

    1. **Prerequisiti del dispositivo**

        1. Connettersi a Azure Resource Manager nel dispositivo.
        2. Abilitare il calcolo tramite l'interfaccia utente locale.

    2. **Prerequisiti client**

        1. Scaricare i modelli di macchina virtuale e i file associati nel client.
        1. Facoltativamente, configurare TLS 1,2 nel client.
        1. [Scaricare e installare Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) nel client.

    3. **Prerequisiti della macchina virtuale**

        1. Creare un gruppo di risorse nel percorso del dispositivo che conterrà tutte le risorse della macchina virtuale.
        1. Creare un account di archiviazione per caricare il disco rigido virtuale usato per creare l'immagine di macchina virtuale.
        1. Aggiungere l'URI dell'account di archiviazione locale a DNS o al file hosts nel client che accede al dispositivo.
        1. Installare il certificato di archiviazione BLOB nel dispositivo e nel client locale che accede al dispositivo. Facoltativamente, installare il certificato di archiviazione BLOB nella Storage Explorer.
        1. Creare e caricare un disco rigido virtuale nell'account di archiviazione creato in precedenza.

2. **Creare una macchina virtuale da modelli**

    1. Creare un'immagine di macchina virtuale usando il `CreateImage.parameters.json` file dei parametri e il `CreateImage.json` modello di distribuzione.
    1. Creare una VM con risorse create in precedenza usando il `CreateVM.parameters.json` file di parametri e il  `CreateVM.json` modello di distribuzione.

## <a name="device-prerequisites"></a>Prerequisiti del dispositivo

Configurare questi prerequisiti sul dispositivo Azure Stack Edge Pro.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Prerequisiti client

Configurare questi prerequisiti nel client che verranno usati per accedere al dispositivo Azure Stack Edge Pro.

1. [Scaricare Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se viene usato per caricare un disco rigido virtuale. In alternativa, è possibile scaricare AzCopy per caricare un disco rigido virtuale. Potrebbe essere necessario configurare TLS 1,2 nel computer client se si eseguono versioni precedenti di AzCopy. 
1. [Scaricare i modelli di macchina virtuale e i file di parametri](https://aka.ms/ase-vm-templates) nel computer client. Decomprimerlo in una directory che verrà usata come directory di lavoro.


## <a name="vm-prerequisites"></a>Prerequisiti della macchina virtuale

Configurare questi prerequisiti per creare risorse che saranno necessarie per la creazione di macchine virtuali. 

    
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure, ad esempio account di archiviazione, disco, disco gestito.

> [!IMPORTANT]
> Tutte le risorse vengono create nella stessa posizione del dispositivo e la posizione è impostata su **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Di seguito è riportato un output di esempio.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un nuovo account di archiviazione usando il gruppo di risorse creato nel passaggio precedente. Si tratta di un **account di archiviazione locale** che verrà usato per caricare l'immagine del disco virtuale per la VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Solo gli account di archiviazione locali, ad esempio l'archiviazione con ridondanza locale (Standard_LRS o Premium_LRS), possono essere creati tramite Azure Resource Manager. Per creare account di archiviazione a più livelli, vedere la procedura in [aggiungere, connettersi agli account di archiviazione in Azure stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Di seguito è riportato un output di esempio.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Per ottenere la chiave dell'account di archiviazione, eseguire il `Get-AzureRmStorageAccountKey` comando. Di seguito è riportato un esempio di output di questo comando.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Aggiungere l'URI del BLOB al file hosts

Assicurarsi di aver già aggiunto l'URI del BLOB nel file hosts per il client usato per connettersi all'archiviazione BLOB. **Eseguire blocco note come amministratore** e aggiungere la voce seguente per l'URI del BLOB in `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

In un ambiente tipico, il DNS è configurato in modo che tutti gli account di archiviazione puntino al dispositivo Azure Stack Edge Pro con una `*.blob.devicename.domainname.com` voce.

### <a name="optional-install-certificates"></a>Opzionale Installare i certificati

Ignorare questo passaggio se si effettuerà la connessione tramite Storage Explorer tramite *http*. Se si usa *https*, è necessario installare i certificati appropriati in Storage Explorer. In questo caso, installare il certificato dell'endpoint BLOB. Per ulteriori informazioni, vedere How to create and upload Certificates in [Manage Certificates](azure-stack-edge-j-series-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Creare e caricare un disco rigido virtuale

Assicurarsi di disporre di un'immagine del disco virtuale che è possibile usare per caricare il passaggio successivo. Seguire la procedura descritta in [creare un'immagine di macchina virtuale](azure-stack-edge-gpu-create-virtual-machine-image.md). 

Copiare le immagini del disco da usare nei BLOB di pagine nell'account di archiviazione locale creato nei passaggi precedenti. È possibile usare uno strumento come [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) o [AzCopy per caricare il disco rigido virtuale nell'account di archiviazione](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) creato nei passaggi precedenti. 

### <a name="use-storage-explorer-for-upload"></a>USA Storage Explorer per il caricamento

1. Aprire Esplora archivi. Passare a **Edit (modifica** ) e assicurarsi che l'applicazione sia impostata su **target Azure stack API**.

    ![Impostare la destinazione su API Azure Stack](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Installare il certificato client in formato PEM. Passare a **modificare > certificati SSL > importare i certificati**. Puntare al certificato client.

    ![Importa certificato dell'endpoint di archiviazione BLOB](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Se si usano i certificati generati dal dispositivo, scaricare e convertire il certificato dell'endpoint di archiviazione BLOB `.cer` in un `.pem` formato. Eseguire il comando seguente. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Se si sta portando un certificato, usare il certificato radice della catena di firma in `.pem` formato.

3. Dopo aver importato il certificato, riavviare Storage Explorer per rendere effettive le modifiche.

    ![Riavviare Storage Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. Nel riquadro sinistro fare clic con il pulsante destro del mouse su **account di archiviazione** e selezionare **Connetti ad archiviazione di Azure**. 

    ![Connettersi ad archiviazione di Azure 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Selezionare **Usare un nome e una chiave dell'account di archiviazione**. Selezionare **Avanti**.

    ![Connettersi ad archiviazione di Azure 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Nella pagina **Connetti con nome e chiave** specificare il **nome visualizzato**, il **nome dell'account di archiviazione** e la chiave dell' **account** di archiviazione di Azure. Selezionare **altro** dominio di archiviazione e quindi specificare la `<device name>.<DNS domain>` stringa di connessione. Se non è stato installato un certificato in Storage Explorer, selezionare l'opzione **USA http** . Selezionare **Avanti**.

    ![Connetti con nome e chiave](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Esaminare il **Riepilogo della connessione** e selezionare **Connetti**.

8. L'account di archiviazione viene visualizzato nel riquadro sinistro. Selezionare ed espandere l'account di archiviazione. Selezionare **contenitori BLOB**, fare clic con il pulsante destro del mouse e scegliere **Crea contenitore BLOB**. Specificare un nome per il contenitore BLOB.

9. Selezionare il contenitore appena creato e fare clic su **carica > carica file** nel riquadro di destra. 

    ![Carica file VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Individuare e puntare al disco rigido virtuale che si desidera caricare nei **file selezionati**. Selezionare **tipo di BLOB** come **BLOB di pagine** e selezionare **carica**.

    ![Carica file VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Una volta caricato il disco rigido virtuale nel contenitore BLOB, selezionare il disco rigido virtuale, fare clic con il pulsante destro del mouse e scegliere **Proprietà**. 

    ![Carica file VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Copiare e salvare l' **URI** come verrà usato nei passaggi successivi.

    ![Copia URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Creare un'immagine per la macchina virtuale

Per creare un'immagine per la macchina virtuale, modificare il `CreateImage.parameters.json` file dei parametri e quindi distribuire il modello `CreateImage.json` che usa questo file di parametri.


### <a name="edit-parameters-file"></a>Modificare il file dei parametri

Il file `CreateImage.parameters.json` accetta i parametri seguenti: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Modificare il file `CreateImage.parameters.json` in modo da includere quanto segue per il dispositivo Azure stack Edge Pro:

1. Specificare il tipo di sistema operativo corrispondente al disco rigido virtuale che si desidera caricare. Il tipo di sistema operativo può essere Windows o Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Modificare l'URI dell'immagine nell'URI dell'immagine caricata nel passaggio precedente:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Se si usa *http* con Storage Explorer, modificare questo valore in un URI *http* .

3. Consente di specificare un nome univoco per l'immagine. Questa immagine viene usata per creare una VM nei passaggi successivi. 

    Di seguito è riportato un esempio JSON usato in questo articolo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```
5. Salvare il file dei parametri.


### <a name="deploy-template"></a>Distribuire il modello 

Distribuire il modello `CreateImage.json` . Questo modello consente di distribuire le risorse immagine che verranno usate per creare le macchine virtuali nel passaggio successivo.

> [!NOTE]
> Quando si distribuisce il modello se viene ricevuto un errore di autenticazione, è possibile che le credenziali di Azure per questa sessione siano scadute. Eseguire `login-AzureRM` nuovamente il comando per connettersi di nuovo a Azure Resource Manager sul dispositivo Azure stack Edge Pro.

1. Eseguire il comando seguente: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Questo comando distribuisce una risorsa immagine. Per eseguire una query sulla risorsa, eseguire il comando seguente:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Di seguito è riportato un esempio di output di un'immagine creata correttamente.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>Creare una macchina virtuale

### <a name="edit-parameters-file-to-create-vm"></a>Modificare il file dei parametri per creare una macchina virtuale
 
Per creare una macchina virtuale, usare il file di parametri `CreateVM.parameters.json`. Accetta i parametri seguenti.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Assegnare i parametri appropriati in `CreateVM.parameters.json` per il dispositivo Azure stack Edge Pro.

1. Specificare un nome univoco, il nome dell'interfaccia di rete e il nome ipconfig. 
1. Immettere un nome utente, una password e una dimensione di macchina virtuale supportata.
1. Quando è stata abilitata l'interfaccia di rete per il calcolo, un commutire virtuale e una rete virtuale sono stati creati automaticamente sull'interfaccia di rete. È possibile eseguire una query sulla rete virtuale esistente per ottenere il nome VNET, il nome della subnet e il nome del gruppo di risorse vnet.

    Eseguire il comando seguente:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Di seguito è riportato l'output di esempio:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Usare ASEVNET per nome VNET, ASEVNETsubNet per nome subnet e ASERG per il nome del gruppo di risorse vnet.
    
1. A questo punto è necessario un indirizzo IP statico da assegnare alla VM che si trova nella rete subnet definita in precedenza. Sostituire **PrivateIPAddress** con questo indirizzo nel file dei parametri. Per fare in modo che la macchina virtuale ottenga un indirizzo IP dal server DCHP locale, lasciare `privateIPAddress` vuoto il valore.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Salvare il file dei parametri.

    Di seguito è riportato un esempio JSON usato in questo articolo.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Distribuire un modello per creare una macchina virtuale

Distribuire il modello di creazione della macchina virtuale `CreateVM.json` . Questo modello crea un'interfaccia di rete dal VNet esistente e crea una macchina virtuale dall'immagine distribuita.

1. Eseguire il comando seguente: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    La creazione della macchina virtuale può richiedere 15-20 minuti. Di seguito è riportato un esempio di output di una macchina virtuale creata correttamente.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    È anche possibile eseguire il `New-AzureRmResourceGroupDeployment` comando in modo asincrono con il `–AsJob` parametro. Di seguito è riportato un esempio di output quando il cmdlet viene eseguito in background. È quindi possibile eseguire una query sullo stato del processo creato utilizzando il `Get-Job` cmdlet.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Controllare se è stato eseguito correttamente il provisioning della macchina virtuale. Eseguire il comando seguente:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Connettersi a una macchina virtuale

A seconda del fatto che sia stata creata una macchina virtuale Windows o Linux, la procedura per la connessione può essere diversa.

### <a name="connect-to-windows-vm"></a>Connettersi a una macchina virtuale Windows

Per connettersi a una macchina virtuale Windows, seguire questa procedura.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Connettersi a una VM Linux

Seguire questa procedura per connettersi a una VM Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Passaggi successivi

[Cmdlet di Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)