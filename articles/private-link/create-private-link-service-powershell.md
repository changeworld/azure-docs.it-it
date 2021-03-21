---
title: 'Guida introduttiva: creare un servizio di collegamento privato di Azure usando Azure PowerShell'
description: In questa Guida introduttiva si apprenderà come creare un servizio di collegamento privato di Azure usando Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: allensu
ms.openlocfilehash: 366be37135808a6d3d5cc1a277e2de3e3d6da8ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566403"
---
# <a name="quickstart-create-a-private-link-service-using-azure-powershell"></a>Guida introduttiva: creare un servizio di collegamento privato utilizzando Azure PowerShell

Introduzione alla creazione di un servizio Collegamento privato che fa riferimento al proprio servizio.  Assegnare l'accesso con collegamento privato al servizio o alla risorsa distribuita dietro un'istanza di Azure Load Balancer Standard.  Gli utenti del servizio hanno accesso privato dalla loro rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```

## <a name="create-an-internal-load-balancer"></a>Creare un bilanciamento del carico interno

In questa sezione si crea una rete virtuale e un'istanza interna di Azure Load Balancer.

### <a name="virtual-network"></a>Rete virtuale

In questa sezione si creano la rete virtuale e la subnet che ospiteranno l'istanza di Load Balancer usata per accedere al servizio Collegamento privato.

* Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) per il pool di indirizzi IP front-end. Questo IP riceve il traffico in ingresso nel servizio di bilanciamento del carico

* Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) per il traffico inviato dal front-end del servizio di bilanciamento del carico. Questo pool è quello in cui vengono distribuite le macchine virtuali back-end.

* Usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) per creare un probe di integrità che determina l'integrità delle istanze di VM back-end.

* Usare [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) per creare una regola di bilanciamento del carico che definisce come viene distribuito il traffico alle VM.

* Creare un servizio di bilanciamento del carico pubblico con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

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
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato

In questa sezione viene creato un servizio di collegamento privato che usa il Azure Load Balancer standard creato nel passaggio precedente.

* Creare la configurazione IP del servizio di collegamento privato con [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig).

* Creare il servizio di collegamento privato con [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice).

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

Il servizio di collegamento privato viene creato e può ricevere traffico. Per visualizzare i flussi di traffico, configurare l'applicazione in base al servizio di bilanciamento del carico standard.

## <a name="create-private-endpoint"></a>Creare un endpoint privato

In questa sezione si eseguirà il mapping del servizio di collegamento privato a un endpoint privato. Una rete virtuale contiene l'endpoint privato per il servizio di collegamento privato. Questa rete virtuale contiene le risorse che accederanno al servizio di collegamento privato.

### <a name="create-private-endpoint-virtual-network"></a>Crea rete virtuale endpoint privato

* Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>Crea endpoint e connessione

* Usare [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) per inserire la configurazione del servizio di collegamento privato creato prima in una variabile per un uso successivo.

* Usare [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) per creare la configurazione della connessione.

* Usare [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) per creare l'endpoint.



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>Approva la connessione all'endpoint privato

In questa sezione verrà approvata la connessione creata nei passaggi precedenti.

* Usare [Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) per approvare la connessione.

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>Indirizzo IP dell'endpoint privato

In questa sezione è possibile trovare l'indirizzo IP dell'endpoint privato che corrisponde al servizio di bilanciamento del carico e al servizio di collegamento privato.

* Usare [Get-AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint) per recuperare l'indirizzo IP.

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e le risorse rimanenti.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido:

* Sono state create una rete virtuale e un'istanza interna di Azure Load Balancer.
* È stato creato un servizio Collegamento privato

Per altre informazioni sull'endpoint privato di Azure, passare a:
> [!div class="nextstepaction"]
> [Guida introduttiva: creare un endpoint privato con Azure PowerShell](create-private-endpoint-powershell.md)

