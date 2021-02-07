---
title: Come configurare OpenVPN nel gateway VPN di Azure
description: Informazioni su come usare PowerShell per abilitare il protocollo OpenVPN nel gateway VPN di Azure per un ambiente da punto a sito.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 34f24b8fbdb28e1b1f73e9db428c510d3f4661ce
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804845"
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
1. Per **tipo di tunnel** selezionare **OpenVPN (SSL)** o **IKEv2 e OpenVPN (SSL)** nell'elenco a discesa.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Selezionare OpenVPN SSL dall'elenco a discesa":::
1. Salvare le modifiche e continuare con i **passaggi successivi**.

Abilitare OpenVPN nel gateway

1. Abilitare OpenVPN sul gateway usando l'esempio seguente:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Continuare con i **passaggi successivi**.

## <a name="next-steps"></a>Passaggi successivi

Per configurare i client per OpenVPN, vedere [Configurare i client OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" è un marchio di OpenVPN Inc.**
