---
title: Visualizzare le metriche e lo stato BGP
titleSuffix: Azure VPN Gateway
description: Visualizzare importanti informazioni correlate a BGP per la risoluzione dei problemi.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103148847"
---
# <a name="view-bgp-metrics-and-status"></a>Visualizzare lo stato e le metriche BGP

È possibile visualizzare le metriche e lo stato BGP usando il portale di Azure o Azure PowerShell.

## <a name="azure-portal"></a>Portale di Azure

Nella portale di Azure è possibile visualizzare i peer BGP, le route apprese e le route annunciate. È anche possibile scaricare i file con estensione CSV che contengono questi dati.

1. Nella [portale di Azure](https://portal.azure.com)passare al gateway di rete virtuale.
1. In **monitoraggio** selezionare **peer BGP** per aprire la pagina dei peer BGP.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Screenshot delle metriche nell'portale di Azure.":::

### <a name="learned-routes"></a>Route acquisite

1. È possibile visualizzare fino a 50 Route apprese nel portale.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Screenshot delle route acquisite.":::

1. È anche possibile scaricare il file delle route apprese. Se sono presenti più di 50 Route apprese, l'unico modo per visualizzarle è scaricare e visualizzare il file con estensione CSV. Per scaricare, selezionare **Scarica le route apprese**.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Screenshot del download delle route acquisite.":::
1. Quindi, visualizzare il file.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Screenshot delle route acquisite scaricate.":::

### <a name="advertised-routes"></a>Route annunciate

1. Per visualizzare le route annunciate, selezionare il **...** alla fine della rete che si desidera visualizzare, quindi fare clic su **Visualizza route annunciate**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Screenshot che illustra come visualizzare le route annunciate." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Nella pagina **route annunciate al peer** è possibile visualizzare fino a 50 route annunciate.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Screenshot delle route annunciate.":::
1. È anche possibile scaricare il file di route annunciate. Se sono presenti più di 50 route annunciate, l'unico modo per visualizzarle è scaricare e visualizzare il file con estensione CSV. Per scaricare, selezionare **Scarica route annunciate**.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Screenshot della selezione delle route annunciate scaricate.":::
1. Quindi, visualizzare il file.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Screenshot delle route annunciate scaricate.":::

### <a name="bgp-peers"></a>Peer BGP

1. È possibile visualizzare fino a 50 peer BGP nel portale.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Screenshot dei peer BGP.":::
1. È anche possibile scaricare il file dei peer BGP. Se sono presenti più di 50 peer BGP, l'unico modo per visualizzarli è scaricare e visualizzare il file con estensione CSV. Per scaricare, selezionare **Scarica peer BGP** nella pagina del portale.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Screenshot del download dei peer BGP.":::
1. Quindi, visualizzare il file.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Screenshot dei peer BGP scaricati.":::

## <a name="powershell"></a>PowerShell

Usare **Get-AzVirtualNetworkGatewayBGPPeerStatus** per visualizzare tutti i peer BGP e lo stato.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Usare **Get-AzVirtualNetworkGatewayLearnedRoute** per visualizzare tutte le route apprese dal gateway tramite BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Usare **Get-AzVirtualNetworkGatewayAdvertisedRoute** per visualizzare tutte le route che il gateway annuncia ai propri peer tramite BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su BGP, vedere [configurare BGP per il gateway VPN](bgp-howto.md).
