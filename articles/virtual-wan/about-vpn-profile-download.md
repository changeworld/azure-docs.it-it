---
title: 'Rete WAN virtuale di Azure: profili client VPN utente'
description: Consente di usare il file del profilo client
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980916"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Uso dei file del profilo client VPN utente

I file del profilo contengono informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN utente.

## <a name="download-the-profile"></a>Scarica il profilo

Per scaricare il file zip del profilo client, è possibile seguire la procedura descritta nell'articolo [scaricare i profili](global-hub-profile.md) .

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato perché includa la chiave e il certificato. Per altre informazioni, vedere [configurare i client OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla VPN utente WAN virtuale, vedere [creare una connessione VPN utente](virtual-wan-point-to-site-portal.md).
