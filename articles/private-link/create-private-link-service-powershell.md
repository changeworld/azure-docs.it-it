---
title: 'Guida introduttiva: creare un servizio di collegamento privato di Azure usando Azure PowerShell'
description: Informazioni su come creare un servizio di collegamento privato di Azure usando Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/20/2021
ms.author: allensu
ms.openlocfilehash: 66ad5aae9f8175d154bb07a8b112dada175a205a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610064"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Creare un servizio di collegamento privato utilizzando Azure PowerShell

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
---
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
$fe = Get-AzLoadBalancer -Name 'myLoadBalancer' | Get-AzLoadBalancerFrontendIpConfig

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

