---
title: 'Esercitazione: creare un gateway NAT-PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Per iniziare, creare un gateway NAT con Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620237"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Esercitazione: Creare un gateway NAT usando Azure PowerShell

Questa esercitazione illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale.
> * Creare una macchina virtuale.
> * Creare un gateway NAT e associarlo alla rete virtuale.
> * Connettersi alla macchina virtuale e verificare l'indirizzo IP NAT.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroupNAT** nella località **Stati Uniti orientali 2**:

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>Creare il gateway NAT

In questa sezione vengono creati il gateway NAT e le risorse di supporto.

* Per accedere a Internet, sono necessari uno o più indirizzi IP pubblici per il gateway NAT. Usare il comando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare una risorsa indirizzo IP pubblico denominata **myPublicIP** in **myResourceGroupNAT**. 

* Creare un gateway NAT di Azure globale con [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Il risultato di questo comando creerà una risorsa gateway denominata **myNATgateway** che usa l'indirizzo IP pubblico **myPublicIP**. Il timeout di inattività è impostato su 10 minuti.  

* Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) in **myResourceGroup** con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Lo spazio degli indirizzi IP per la rete virtuale è **10.1.0.0/16**. La subnet all'interno della rete virtuale è **10.1.0.0/24**.

* Creare un host Bastion di Azure denominato **myBastionHost** per accedere alla macchina virtuale. Usare [New-AzBastion](/powershell/module/az.network/new-azbastion) per creare l'host Bastion. Creare un indirizzo IP pubblico per l'host Bastion con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Macchina virtuale

In questa sezione si creerà una macchina virtuale per testare il gateway NAT e si verificherà l'indirizzo IP pubblico della connessione in uscita.

* Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Impostare un nome utente e una password dell'amministratore per la macchina virtuale con [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Creare la macchina virtuale con:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Attendere il completamento della creazione della macchina virtuale prima di procedere alla sezione successiva.

## <a name="test-nat-gateway"></a>Testare il gateway NAT

In questa sezione verrà testato il gateway NAT. Si scoprirà prima l'indirizzo IP pubblico del gateway NAT. Ci si connette quindi alla macchina virtuale di test e si verifica la connessione in uscita tramite il gateway NAT.
    
1. Accedere al [portale di Azure](https://portal.azure.com)

1. Trovare l'indirizzo IP pubblico per il gateway NAT nella schermata **Panoramica** . Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP**.

2. Prendere nota dell'indirizzo IP pubblico:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Individuare l'indirizzo IP pubblico del gateway NAT" border="true":::

3. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi nell'elenco delle risorse selezionare **myVM** che si trova nel gruppo di risorse **myResourceGroupNAT** .

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere **https://whatsmyip.com** nella barra degli indirizzi.

9. Verificare che l'indirizzo IP visualizzato corrisponda all'indirizzo del gateway NAT annotato nel passaggio precedente:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer che mostra l'indirizzo IP in uscita esterno" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e il gateway NAT con i passaggi seguenti:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete virtuale di Azure NAT, vedere:
> [!div class="nextstepaction"]
> [Panoramica NAT della rete virtuale](nat-overview.md)
