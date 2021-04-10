---
title: 'Azure ExpressRoute: aggiungere il supporto IPv6 usando Azure PowerShell'
description: Informazioni su come aggiungere il supporto IPv6 per connettersi alle distribuzioni di Azure usando Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 7a9ac98a9566986767016720fda245712197b27f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566541"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Aggiungere il supporto IPv6 per il peering privato con Azure PowerShell (anteprima)

Questo articolo descrive come aggiungere il supporto IPv6 per la connessione tramite ExpressRoute alle risorse in Azure usando Azure PowerShell.

> [!Note]
> Questa funzionalità è attualmente disponibile per l'anteprima nelle [aree di Azure con zone di disponibilità](../availability-zones/az-region.md#azure-regions-with-availability-zones). È quindi possibile creare il circuito ExpressRoute usando qualsiasi località di peering, ma le distribuzioni basate su IPv6 a cui si connette devono trovarsi in un'area con zone di disponibilità.

## <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Registrarsi per l'anteprima pubblica
Prima di aggiungere il supporto per IPv6, è necessario prima registrare la sottoscrizione. Per eseguire la registrazione, eseguire le operazioni seguenti tramite Azure PowerShell:
1.  Accedere ad Azure e selezionare la sottoscrizione. È necessario eseguire questa operazione per la sottoscrizione contenente il circuito ExpressRoute, nonché la sottoscrizione contenente le distribuzioni di Azure (se sono diverse).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Richiedere la registrazione della sottoscrizione per l'anteprima pubblica usando il comando seguente:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

La richiesta verrà quindi approvata dal team di ExpressRoute entro 2-3 giorni lavorativi.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Aggiungere peering privato IPv6 al circuito ExpressRoute

1. [Creare un circuito ExpressRoute](./expressroute-howto-circuit-arm.md) o usare un circuito esistente. Recuperare il circuito eseguendo il comando **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Recuperare la configurazione del peering privato per il circuito eseguendo **Get-AzExpressRouteCircuitPeeringConfig**:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Aggiungere un peering privato IPv6 alla configurazione del peering privato IPv4 esistente. Fornire una coppia di subnet IPv6/126 di cui si è proprietari per il collegamento primario e i collegamenti secondari. Da ognuna di queste subnet si assegnerà al router il primo indirizzo IP utilizzabile, poiché il secondo indirizzo IP utilizzabile per il router viene usato da Microsoft.

    > [!Note]
    > Il peer ASN e VlanId devono corrispondere a quelli della configurazione del peering privato IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Dopo che la configurazione è stata salvata, ottenere di nuovo il circuito eseguendo il comando **Get-AzExpressRouteCircuit** . La risposta dovrebbe essere simile all'esempio seguente:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aggiornare la connessione a una rete virtuale esistente

Seguire questa procedura se si dispone di un ambiente esistente di risorse di Azure in un'area con zone di disponibilità a cui si vuole usare il peering privato IPv6 con.

1. Recuperare la rete virtuale a cui è connesso il circuito ExpressRoute.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Aggiungere uno spazio di indirizzi IPv6 alla rete virtuale.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Aggiungere lo spazio di indirizzi IPv6 alla subnet del gateway. La subnet IPv6 del gateway deve essere/64 o superiore.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Se si dispone di un gateway con ridondanza della zona esistente, eseguire il comando seguente per abilitare la connettività IPv6. In caso contrario, [creare il gateway di rete virtuale](./expressroute-howto-add-gateway-resource-manager.md) usando uno SKU con ridondanza della zona (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Creare una connessione a una nuova rete virtuale

Se si prevede di connettersi a un nuovo set di risorse di Azure in un'area con zone di disponibilità usando il peering privato IPv6, seguire questa procedura.

1. Creare una rete virtuale a doppio stack con lo spazio degli indirizzi IPv4 e IPv6. Per altre informazioni, vedere [creare una rete virtuale](../virtual-network/quick-create-portal.md#create-a-virtual-network).

2. [Creare la subnet del gateway dual stack](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. [Creare il gateway di rete virtuale](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) usando uno SKU con ridondanza della zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se si prevede di usare FastPath, usare ErGw3AZ (si noti che questo è disponibile solo per i circuiti che usano ExpressRoute Direct).

4. [Collegare la rete virtuale al circuito ExpressRoute](./expressroute-howto-linkvnet-arm.md).

## <a name="limitations"></a>Limitazioni
Sebbene il supporto per IPv6 sia disponibile per le connessioni alle distribuzioni in aree con zone di disponibilità, non supporta i casi d'uso seguenti:

* Connessioni alle distribuzioni in Azure tramite uno SKU del gateway non AZ ExpressRoute
* Connessioni alle distribuzioni in aree non AZ
* Copertura globale le connessioni tra circuiti ExpressRoute
* Uso di ExpressRoute con la rete WAN virtuale
* FastPath con circuiti diretti non ExpressRoute
* FastPath con circuiti nei seguenti percorsi di peering: Dubai
* Coesistenza con il gateway VPN

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi relativi a ExpressRoute, vedere gli articoli seguenti:

* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)