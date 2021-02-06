---
title: 'Come configurare OpenVPN nel gateway VPN di Azure: PowerShell'
description: Informazioni su come usare PowerShell per abilitare il protocollo OpenVPN nel gateway VPN di Azure per un ambiente da punto a sito.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 1e2f7f754ae9a1547d6543dba65c69511ab7ceb1
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624913"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurare OpenVPN per il gateway VPN da punto a sito di Azure

Questo articolo consente di configurare il **protocollo OpenVPN®** nel gateway VPN di Azure. L'articolo presuppone che si disponga già di un ambiente di lavoro da punto a sito. In caso contrario, usare le istruzioni nel passaggio 1 per creare una VPN da punto a sito.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. creare una VPN da punto a sito

Se non è già presente un ambiente da punto a sito in funzione, seguire le istruzioni per crearne uno. Visualizzare [Creare una VPN Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per creare e configurare un gateway VPN da punto a sito con autenticazione del certificato di Azure nativa. 

> [!IMPORTANT]
> Lo SKU Basic non è supportato per OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. abilitare OpenVPN sul gateway

Abilitare OpenVPN nel gateway

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare i client per OpenVPN, vedere [Configurare i client OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" è un marchio di OpenVPN Inc.**
