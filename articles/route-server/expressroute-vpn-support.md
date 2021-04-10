---
title: Informazioni sul server di route di Azure (anteprima) supporta ExpressRoute e VPN di Azure
description: Informazioni sul modo in cui il server di routing di Azure interagisce con ExpressRoute e i gateway VPN di Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679970"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Informazioni sul supporto del server di route di Azure (anteprima) per ExpressRoute e VPN di Azure

Il server di route di Azure supporta non solo appliance di rete virtuale di terze parti in esecuzione in Azure, ma si integra facilmente con ExpressRoute e i gateway VPN di Azure. Non è necessario configurare o gestire il peering BGP tra il gateway e il server di route di Azure. È possibile abilitare lo scambio di route tra il gateway e il server di route di Azure con una semplice [modifica della configurazione](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Come funziona?

Quando si distribuisce un server di route di Azure insieme a un gateway ExpressRoute e un appliance virtuale di rete in una rete virtuale per impostazione predefinita, il server di route di Azure non propaga le route ricevute dall'appliance virtuale di rete e dal gateway ExpressRoute. Dopo aver abilitato lo scambio di route, ExpressRoute e l'appliance virtuale di dispositivo apprenderanno le route di ogni altro.

Ad esempio, nel diagramma seguente:

* L'appliance SDWAN riceverà dal server di route di Azure il percorso da "locale 2", che è connesso a ExpressRoute, insieme alla route di rete virtuale.

* Il gateway ExpressRoute riceverà la route da "on-premi 1", che è connessa al dispositivo SDWAN, insieme alla route di rete virtuale dal server di route di Azure.

    ![Diagramma che mostra ExpressRoute configurato con il server di route.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

È anche possibile sostituire l'appliance SDWAN con il gateway VPN di Azure. Poiché il gateway VPN di Azure e ExpressRoute sono completamente gestiti, è sufficiente abilitare lo scambio di route per le due reti locali per comunicare tra loro.

> [!IMPORTANT] 
> Il gateway VPN di Azure deve essere configurato in modalità Active [**-Active**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) .
>

![Diagramma che mostra ExpressRoute e gateway VPN configurati con il server di route.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [server di route di Azure](route-server-faq.md).
- Informazioni su come [configurare il server di route di Azure](quickstart-configure-route-server-powershell.md).
- Altre informazioni su [Azure ExpressRoute e la coesistenza VPN di Azure](../expressroute/expressroute-howto-coexist-resource-manager.md).
