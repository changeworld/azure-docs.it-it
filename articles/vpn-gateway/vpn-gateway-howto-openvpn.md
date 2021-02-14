---
title: Come configurare OpenVPN nel gateway VPN di Azure
description: Informazioni su come usare PowerShell per abilitare il protocollo OpenVPN nel gateway VPN di Azure per un ambiente da punto a sito.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 4b48e98ab35b620030b15165d4c9341c0f11b440
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393276"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurare OpenVPN per il gateway VPN da punto a sito di Azure

Questo articolo consente di configurare il **protocollo OpenVPN®** nel gateway VPN di Azure. È possibile usare il portale o le istruzioni di PowerShell.

## <a name="prerequisites"></a>Prerequisiti

* L'articolo presuppone che si disponga già di un ambiente di lavoro da punto a sito. In caso contrario, crearne uno usando uno dei metodi seguenti.

  * [Portale-crea da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell-creare da punto a sito](vpn-gateway-howto-point-to-site-rm-ps.md)

* Verificare che il gateway VPN non usi lo SKU Basic. Lo SKU Basic non è supportato per OpenVPN.

## <a name="portal"></a>Portale

1. Nel portale passare al **gateway di rete virtuale-> configurazione da punto a sito**.
1. Per **tipo di tunnel** selezionare **OpenVPN (SSL)** nell'elenco a discesa.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Selezionare OpenVPN SSL dall'elenco a discesa":::
1. Salvare le modifiche e continuare con i **passaggi successivi**.

## <a name="enable-openvpn-on-your-gateway-using-powershell"></a>Abilitare OpenVPN nel gateway usando PowerShell.

1. Abilitare OpenVPN sul gateway usando l'esempio seguente:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Continuare con i **passaggi successivi**.

## <a name="next-steps"></a>Passaggi successivi

Per configurare i client per OpenVPN, vedere [Configurare i client OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" è un marchio di OpenVPN Inc.**
