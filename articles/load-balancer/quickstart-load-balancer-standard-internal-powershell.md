---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Questo argomento di avvio rapido illustra come creare un servizio di bilanciamento del carico interno con Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 8d94a784aba5995fd7e4645c3b56929874da2bcd
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056369"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Avvio rapido: Creare un servizio di bilanciamento del carico interno per le macchine virtuali con Azure PowerShell

Iniziare a usare Azure Load Balancer con Azure PowerShell per creare un servizio di bilanciamento del carico interno e due macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateIntLBQS-rg' -Location 'eastus'
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. 

Quando si crea un servizio di bilanciamento del carico interno, occorre configurare una rete virtuale in cui eseguire il servizio. 

Il diagramma seguente mostra le risorse create in questa Guida introduttiva:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Risorse di Load Balancer Standard create per l'avvio rapido." border="false":::

## <a name="configure-virtual-network---standard"></a>Configurare la rete virtuale - Standard

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

Creare una rete virtuale per le macchine virtuali back-end.

Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Creare una rete virtuale, un gruppo di sicurezza di rete e un host bastion

* Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Creare una regola di gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Creare un host Azure Bastion con [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
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
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```
## <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) per il pool di indirizzi IP front-end. Questo IP riceve il traffico in ingresso nel servizio di bilanciamento del carico

* Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) per il traffico inviato dal front-end del servizio di bilanciamento del carico. Questo pool è quello in cui vengono distribuite le macchine virtuali back-end.

* Usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) per creare un probe di integrità che determina l'integrità delle istanze di VM back-end.

* Usare [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) per creare una regola di bilanciamento del carico che definisce come viene distribuito il traffico alle VM.

* Creare un servizio di bilanciamento del carico pubblico con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-virtual-machines---standard"></a>Creare macchine virtuali - Standard

In questa sezione verranno create tre macchine virtuali per il pool back-end del servizio di bilanciamento del carico.

* Creare tre interfacce di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Creare le macchine virtuali con:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreateIntLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
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
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

Le distribuzioni delle macchine virtuali e dell'host bastion vengono inviate come processi di PowerShell. Per visualizzare lo stato dei processi, usare [Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. 

Quando si crea un servizio di bilanciamento del carico interno, occorre configurare una rete virtuale in cui eseguire il servizio. 

Il diagramma seguente mostra le risorse create in questa Guida introduttiva:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Risorse di Load Balancer Basic create per l'avvio rapido." border="false":::

## <a name="configure-virtual-network---basic"></a>Configurare la rete virtuale - Basic

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

Creare una rete virtuale per le macchine virtuali back-end.

Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Creare una rete virtuale, un gruppo di sicurezza di rete e un host bastion

* Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Creare una regola di gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Creare un host Azure Bastion con [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
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
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```
## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) per il pool di indirizzi IP front-end. Questo IP riceve il traffico in ingresso nel servizio di bilanciamento del carico

* Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) per il traffico inviato dal front-end del servizio di bilanciamento del carico. Questo pool è quello in cui vengono distribuite le macchine virtuali back-end.

* Usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) per creare un probe di integrità che determina l'integrità delle istanze di VM back-end.

* Usare [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) per creare una regola di bilanciamento del carico che definisce come viene distribuito il traffico alle VM.

* Creare un servizio di bilanciamento del carico pubblico con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-virtual-machines---basic"></a>Creare le macchine virtuali - Basic

In questa sezione verranno create le macchine virtuali per il pool back-end del servizio di bilanciamento del carico.

* Creare tre interfacce di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Usare [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) per creare un set di disponibilità per le macchine virtuali.

* Creare le macchine virtuali con:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreateIntLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvailabilitySet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
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
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

Le distribuzioni delle macchine virtuali e dell'host bastion vengono inviate come processi di PowerShell. Per visualizzare lo stato dei processi, usare [Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

---

## <a name="install-iis"></a>Installare IIS

Usare [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) per installare l'estensione per script personalizzati. 

L'estensione esegue `PowerShell Add-WindowsFeature Web-Server` per installare il server Web IIS e quindi aggiorna la pagina Default.htm per visualizzare il nome host della VM:

> [!IMPORTANT]
> Assicurarsi che le distribuzioni delle macchine virtuali siano state completate nel passaggio precedente prima di procedere.  Usare `Get-Job` per verificare lo stato dei processi di distribuzione delle macchine virtuali.

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreateIntLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

Le estensioni vengono distribuite come processi di PowerShell. Per visualizzare lo stato dei processi di installazione, usare [Get-Job](/powershell/module/microsoft.powershell.core/get-job):


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```


## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

### <a name="create-virtual-machine"></a>Creare macchina virtuale

Creare la macchina virtuale con:

* [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)
* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VM. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## Command to create network interface for VM ##
$nic = @{
    Name = "myNicTestVM"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myTestVM"
    VMSize = 'Standard_DS1_v2' 
}
$vmos = @{
    ComputerName = "myTestVM"
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
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm

```

### <a name="test"></a>Test

1. [Accedere](https://portal.azure.com) al portale di Azure.

1. Individuare l'indirizzo IP privato del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer**.

2. Annotare o copiare l'indirizzo accanto a **Indirizzo IP privato** nella pagina **Panoramica** di **myLoadBalancer**.

3. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myTestVM**, che si trova nel gruppo di risorse **CreateIntLBQS-rg**.

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere l'indirizzo IP del passaggio precedente nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Creare un servizio di bilanciamento del carico interno standard" border="true":::
   
Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e le risorse rimanenti.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateIntLBQS-rg'
```

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido:

* È stato creato un servizio di bilanciamento del carico interno Standard o Basic
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, passare a:
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)