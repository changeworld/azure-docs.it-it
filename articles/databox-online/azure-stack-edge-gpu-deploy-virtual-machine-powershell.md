---
title: Distribuire macchine virtuali nel dispositivo Azure Stack Edge tramite Azure PowerShell
description: Viene descritto come creare e gestire le macchine virtuali in un dispositivo Azure Stack Edge utilizzando Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 90925f30e84ec3c06e715669ebd982c823dfaf5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568325"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Distribuire macchine virtuali nel dispositivo Azure Stack Edge tramite Azure PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come creare e gestire una macchina virtuale (VM) nel dispositivo Azure Stack Edge usando Azure PowerShell. Le informazioni si applicano a Azure Stack Edge Pro con GPU (unità di elaborazione grafica), a Azure Stack Edge Pro R e a dispositivi Mini R Azure Stack Edge.

## <a name="vm-deployment-workflow"></a>Flusso di lavoro di distribuzione della VM

Il flusso di lavoro di distribuzione viene visualizzato nel diagramma seguente:

![Diagramma del flusso di lavoro di distribuzione della macchina virtuale.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>Eseguire una query per una sottoscrizione incorporata nel dispositivo

Per Azure Resource Manager, è supportata una sola sottoscrizione fissa visibile dall'utente. Questa sottoscrizione è univoca per ogni dispositivo e non è possibile modificare il nome e l'ID della sottoscrizione.

La sottoscrizione contiene tutte le risorse necessarie per la creazione della macchina virtuale. 

> [!IMPORTANT]
> La sottoscrizione viene creata quando si abilitano le macchine virtuali dal portale di Azure e si trova localmente nel dispositivo.

La sottoscrizione viene usata per distribuire le macchine virtuali.

1.  Per elencare la sottoscrizione, eseguire il comando seguente:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Di seguito è riportato un esempio di output:

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Ottenere un elenco dei provider di risorse registrati in esecuzione nel dispositivo. L'elenco include in genere le risorse di calcolo, di rete e di archiviazione.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > I provider di risorse sono pre-registrati e non possono essere modificati o modificati.
    
    Di seguito è riportato un esempio di output:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure, ad esempio un account di archiviazione, un disco e un disco gestito.

> [!IMPORTANT]
> Tutte le risorse vengono create nella stessa posizione del dispositivo e la posizione è impostata su **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Di seguito è riportato un esempio di output:

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un nuovo account di archiviazione usando il gruppo di risorse creato nel passaggio precedente. Si tratta di un account di archiviazione locale usato per caricare l'immagine del disco virtuale per la VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Con Azure Resource Manager è possibile creare solo account di archiviazione locali, ad esempio l'archiviazione con ridondanza locale (standard o Premium). Per creare account di archiviazione a livelli, vedere [esercitazione: trasferire i dati tramite gli account di archiviazione con Azure stack Edge Pro con GPU](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

Di seguito è riportato un esempio di output:

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Per ottenere la chiave dell'account di archiviazione, eseguire il `Get-AzureRmStorageAccountKey` comando. Di seguito è riportato un esempio di output:

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Aggiungere l'URI del BLOB al file host

È già stato aggiunto l'URI del BLOB nel file degli host per il client usato per connettersi all'archiviazione BLOB di Azure in "passaggio 5: modificare il file host per la risoluzione dei nomi di endpoint" di [distribuire macchine virtuali nel dispositivo Azure stack Edge tramite Azure PowerShell](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Questa voce è stata usata per aggiungere l'URI del BLOB:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>

## <a name="install-certificates"></a>Installare i certificati

Se si usa HTTPS, è necessario installare i certificati appropriati nel dispositivo. Qui viene installato il certificato dell'endpoint BLOB. Per altre informazioni, vedere [usare i certificati con il dispositivo Azure stack Edge Pro con GPU](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Caricare il VHD

Copiare le immagini del disco da usare nei BLOB di pagine nell'account di archiviazione locale creato in precedenza. È possibile usare uno strumento come [AzCopy](../storage/common/storage-use-azcopy-v10.md) per caricare il disco rigido virtuale (VHD) nell'account di archiviazione. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you're using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For more information, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's some example output that uses AzCopy 7.3. For more information about this command, see [Upload VHD file to storage account by using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
Usare i comandi seguenti con AzCopy 10:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Di seguito è riportato un esempio di output: 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-a-managed-disk-from-the-vhd"></a>Creare un disco gestito dal disco rigido virtuale

Per creare un disco gestito dal disco rigido virtuale caricato, eseguire il comando seguente:

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Di seguito è riportato un esempio di output: 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Di seguito è riportato un esempio di output. Per ulteriori informazioni su questo cmdlet, vedere [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Creare un'immagine di VM dal disco gestito dell'immagine

Per creare un'immagine di macchina virtuale dal disco gestito, eseguire il comando seguente. Sostituire *\<Disk name>* , *\<OS type>* e *\<Disk size>* con i valori reali.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Di seguito è riportato un esempio di output. Per ulteriori informazioni su questo cmdlet, vedere [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-your-vm-with-previously-created-resources"></a>Creare la macchina virtuale con le risorse create in precedenza

Prima di creare e distribuire la VM, è necessario creare una rete virtuale e associarvi un'interfaccia di rete virtuale.

> [!IMPORTANT]
> Sono applicabili le regole seguenti:
> - È possibile creare una sola rete virtuale, anche tra gruppi di risorse. La rete virtuale deve avere esattamente lo stesso spazio di indirizzi della rete logica.
> - La rete virtuale può avere una sola subnet. La subnet deve avere esattamente lo stesso spazio di indirizzi della rete virtuale.
> - Quando si crea la scheda di interfaccia di rete virtuale, è possibile usare solo il metodo di allocazione statica. L'utente deve specificare un indirizzo IP privato.

### <a name="query-the-automatically-created-virtual-network"></a>Eseguire una query sulla rete virtuale creata automaticamente

Quando si Abilita il calcolo dall'interfaccia utente locale del dispositivo, viene creata automaticamente una rete virtuale denominata `ASEVNET` nel `ASERG` gruppo di risorse. 

Usare il comando seguente per eseguire una query sulla rete virtuale esistente:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Creare una scheda di interfaccia di rete virtuale

Per creare una scheda di interfaccia di rete virtuale usando l'ID subnet della rete virtuale, eseguire il comando seguente:

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Di seguito è riportato un esempio di output:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Facoltativamente, durante la creazione di una scheda di interfaccia di rete virtuale per una macchina virtuale, è possibile passare l'indirizzo IP pubblico. In questo caso, l'indirizzo IP pubblico restituisce l'indirizzo IP privato. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>Creare una macchina virtuale

È ora possibile usare l'immagine di macchina virtuale per creare una macchina virtuale e collegarla alla rete virtuale creata in precedenza.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

Dopo aver creato e alimentato la VM, si useranno il nome utente e la password seguenti per accedere a tale macchina virtuale.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-the-vm"></a>Connettersi alla VM

A seconda che sia stata creata una macchina virtuale Windows o una VM Linux, le istruzioni per la connessione possono essere diverse.

### <a name="connect-to-a-linux-vm"></a>Connettersi a una VM Linux

Per connettersi a una VM Linux, eseguire le operazioni seguenti:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Connettersi a una VM Windows

Per connettersi a una macchina virtuale Windows, eseguire le operazioni seguenti:

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this instance is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>Gestire la macchina virtuale

Le sezioni seguenti descrivono alcune delle operazioni comuni che è possibile creare nel dispositivo Azure Stack Edge Pro.

### <a name="list-vms-that-are-running-on-the-device"></a>Elencare le macchine virtuali in esecuzione nel dispositivo

Per restituire un elenco di tutte le macchine virtuali in esecuzione nel dispositivo Azure Stack Edge, eseguire questo comando:


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Accendere la macchina virtuale

Per attivare una macchina virtuale in esecuzione nel dispositivo, eseguire il cmdlet seguente:

`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

Per ulteriori informazioni su questo cmdlet, vedere [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>Sospendere o arrestare la VM

Per arrestare o arrestare una macchina virtuale in esecuzione nel dispositivo, eseguire il cmdlet seguente:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Per ulteriori informazioni su questo cmdlet, vedere [cmdlet Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Aggiungere un disco dati

Se i requisiti del carico di lavoro nella macchina virtuale aumentano, potrebbe essere necessario aggiungere un disco dati. A tale scopo, eseguire il comando seguente:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Per rimuovere una macchina virtuale dal dispositivo, eseguire il cmdlet seguente:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Per ulteriori informazioni su questo cmdlet, vedere il [cmdlet Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Passaggi successivi

[Cmdlet di Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)