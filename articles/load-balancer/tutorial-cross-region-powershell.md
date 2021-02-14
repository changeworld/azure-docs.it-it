---
title: 'Esercitazione: creare un servizio di bilanciamento del carico tra aree usando Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Introduzione a questa esercitazione distribuzione di una Azure Load Balancer tra aree con Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: b1a249bac4a1a46e52bf52eccd56649153eefe8e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360925"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Esercitazione: creare una Azure Load Balancer tra aree usando Azure PowerShell

Un servizio di bilanciamento del carico tra più aree garantisce che un servizio sia disponibile a livello globale in più aree di Azure. Se si verifica un errore in un'area, il traffico viene indirizzato al servizio di bilanciamento del carico a livello di area integro più vicino.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un servizio di bilanciamento del carico tra aree.
> * Creare una regola del servizio di bilanciamento del carico.
> * Creare un pool back-end contenente due servizi di bilanciamento del carico a livello di area.
> * Testare il servizio di bilanciamento del carico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Due istanze **standard** di SKU Azure Load Balancer con pool back-end distribuiti tra due diverse aree di Azure.
    - Per informazioni sulla creazione di un servizio di bilanciamento del carico standard a livello di area e delle macchine virtuali per i pool back-end, vedere [Guida introduttiva: creare un servizio di bilanciamento del carico pubblico per biAzure PowerShell lanciare il carico di](quickstart-load-balancer-standard-public-powershell.md)
        - Aggiungere il nome dei bilanciamenti del carico e delle macchine virtuali in ogni area con a **-R1** e **-R2**. 
- Azure PowerShell installato localmente o Azure Cloud Shell.


Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-cross-region-load-balancer"></a>Creare un servizio di bilanciamento del carico tra aree


### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Creare risorse di bilanciamento del carico tra aree

In questa sezione verranno create le risorse necessarie per il servizio di bilanciamento del carico tra aree.

Un IP pubblico con SKU standard globale viene usato per il front-end del servizio di bilanciamento del carico tra aree.

* Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare l'indirizzo IP pubblico.

* Creare una configurazione IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Creare un servizio di bilanciamento del carico tra aree con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Configurare il pool back-end

In questa sezione verranno aggiunti due servizi di bilanciamento del carico standard a livello di area al pool back-end del servizio di bilanciamento del carico tra più aree.

> [!IMPORTANT]
> Per completare questi passaggi, assicurarsi che nella sottoscrizione siano stati distribuiti due servizi di bilanciamento del carico a livello di area con pool back-end.  Per altre informazioni, vedere **[Guida introduttiva: creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine virtuali con Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)**.

* Usare [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) e [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) per archiviare le informazioni del servizio di bilanciamento del carico a livello di area nelle variabili.

* Usare [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) per creare la configurazione del pool di indirizzi back-end per il servizio di bilanciamento del carico.

* Usare [set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) per aggiungere il front-end del servizio di bilanciamento del carico a livello di area al pool back-end tra più aree.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

In questa sezione verrà testato il servizio di bilanciamento del carico tra più aree. Ci si connetterà all'indirizzo IP pubblico in un Web browser.  Si arresteranno le macchine virtuali in uno dei pool back-end del servizio di bilanciamento del carico a livello di area e si osserverà il failover.

1. Usare [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

3. Arrestare le macchine virtuali nel pool di back-end di uno dei servizi di bilanciamento del carico a livello di area.

4. Aggiornare il Web browser e osservare il failover della connessione all'altro servizio di bilanciamento del carico a livello di area.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e le risorse rimanenti.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

* È stato creato un indirizzo IP globale.
* È stato creato un servizio di bilanciamento del carico tra aree.
* È stata creata una regola di bilanciamento del carico.
* Sono stati aggiunti i servizi di bilanciamento del carico a livello di area al pool back-end del servizio di bilanciamento del carico tra più aree.
* È stato testato il servizio di bilanciamento del carico.


Passare all'articolo successivo per informazioni su come...
> [!div class="nextstepaction"]
> [Macchine virtuali del servizio di bilanciamento del carico tra zone di disponibilità](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
