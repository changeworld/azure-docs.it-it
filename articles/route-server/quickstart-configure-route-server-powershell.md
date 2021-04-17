---
title: 'Guida introduttiva: Creare e configurare il server di route usando Azure PowerShell'
description: In questa guida introduttiva si apprenderà come creare e configurare un server di route usando Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538705"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Guida introduttiva: Creare e configurare il server di route usando Azure PowerShell

Questo articolo illustra come configurare il server di route di Azure per il peer con un'appliance virtuale di rete nella rete virtuale tramite PowerShell. Il server di route di Azure apprenderà le route dall'NVA e le programma nelle macchine virtuali nella rete virtuale. Il server di route di Azure annuncia anche le route di rete virtuale all'NVA. Per altre informazioni, vedere Server [di route di Azure.](overview.md)

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Assicurarsi di avere i moduli di PowerShell più recenti oppure è possibile usare Azure Cloud Shell nel portale.
* Esaminare i limiti [del servizio per il server di route di Azure.](route-server-faq.md#limitations)

## <a name="create-a-route-server"></a>Creare un server di route

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale

Prima di poter creare un server di route di Azure, è necessaria una rete virtuale per ospitare la distribuzione. Usare il comando seguente per creare un gruppo di risorse e una rete virtuale. Se si ha già una rete virtuale, è possibile passare alla sezione successiva.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Aggiungere una subnet

1. Aggiungere una subnet denominata *RouteServerSubnet in* cui distribuire il server di route di Azure. Questa subnet è una subnet dedicata solo per il server di route di Azure. RouteServerSubnet deve essere /27 o un prefisso più breve (ad esempio /26, /25) oppure verrà visualizzato un messaggio di errore quando si aggiunge il server di route di Azure.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Ottenere l'ID RouteServerSubnet. Per visualizzare l'ID risorsa di tutte le subnet nella rete virtuale, usare questo comando:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

L'ID RouteServerSubnet è simile al seguente:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Creare il server di route

Creare il server di route con questo comando:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

Il percorso deve corrispondere al percorso della rete virtuale. HostedSubnet è l'ID RouteServerSubnet ottenuto nella sezione precedente.

## <a name="create-peering-with-an-nva"></a>Creare un peering con un'istanza di NVA

Usare il comando seguente per stabilire il peering BGP dal server di route all'istanza di NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" è l'INDIRIZZO IP della rete virtuale assegnato all'NVA. "nva_asn" è il numero di sistema autonomo (ASN) configurato nell'NVA. L'ASN può essere qualsiasi numero a 16 bit diverso da quelli nell'intervallo 65515-65520. Questo intervallo di asn è riservato da Microsoft.

Per configurare il peering con un'altra NVA o un'altra istanza della stessa NVA per la ridondanza, usare questo comando:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Completare la configurazione nell'NVA

Per completare la configurazione nell'infrastruttura di rete e abilitare le sessioni BGP, sono necessari l'INDIRIZZO IP e l'ASN del server di route di Azure. È possibile ottenere queste informazioni usando questo comando:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

L'output contiene le informazioni seguenti:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Configurare lo scambio di route

Se si dispone di un gateway ExpressRoute e di un gateway VPN di Azure nella stessa rete virtuale e si vuole che scambino route, è possibile abilitare lo scambio di route nel server di route di Azure.

1. Per abilitare lo scambio di route tra il server di route di Azure e i gateway, usare questo comando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Per disabilitare lo scambio di route tra il server di route di Azure e i gateway, usare questo comando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

È possibile visualizzare le route annunciate e ricevute dal server di route di Azure con questo comando:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Pulire le risorse

Se il server di route di Azure non è più necessario, usare questi comandi per rimuovere il peering BGP e quindi il server di route. 

1. Rimuovere il peering BGP tra il server di route di Azure e un'istanza di NVA con questo comando:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Rimuovere il server di route di Azure con questo comando:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il server di route di Azure, continuare a scoprire come il server di route di Azure interagisce con ExpressRoute e i gateway VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute e il supporto VPN di Azure](expressroute-vpn-support.md)
