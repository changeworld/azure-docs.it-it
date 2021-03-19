---
title: Informazioni su Azure ExpressRoute FastPath
description: Informazioni su Azure ExpressRoute FastPath per inviare il traffico di rete ignorando il gateway
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: c953668d6b2e364e6e703b1769317f1c520317ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654374"
---
# <a name="about-expressroute-fastpath"></a>Informazioni su ExpressRoute FastPath

Il gateway di rete virtuale ExpressRoute è progettato per scambiare le route di rete e instradare il traffico di rete. FastPath è progettato per migliorare le prestazioni del percorso dati tra la rete locale e la rete virtuale. Se abilitata, FastPath invia il traffico di rete direttamente alle macchine virtuali nella rete virtuale, ignorando il gateway.

## <a name="requirements"></a>Requisiti

### <a name="circuits"></a>Circuiti

FastPath è disponibile in tutti i circuiti ExpressRoute.

### <a name="gateways"></a>Gateway

FastPath richiede ancora la creazione di un gateway di rete virtuale per scambiare le route tra la rete virtuale e la rete locale. Per altre informazioni sui gateway di rete virtuale e ExpressRoute, incluse le informazioni sulle prestazioni e gli SKU del gateway, vedere [gateway di rete virtuale ExpressRoute](expressroute-about-virtual-network-gateways.md).

Per configurare FastPath, il gateway di rete virtuale deve essere uno dei seguenti:

* Prestazioni ultra
* ErGw3AZ

> [!IMPORTANT]
> Se si prevede di usare FastPath con peering privato basato su IPv6 su ExpressRoute, assicurarsi di selezionare ErGw3AZ per **SKU**. Si noti che questa operazione è disponibile solo per i circuiti che usano ExpressRoute Direct.
> 
>

## <a name="limitations"></a>Limitazioni

Sebbene FastPath supporti la maggior parte delle configurazioni, non supporta le funzionalità seguenti:

* UDR sulla subnet del gateway: questo UDR non ha alcun effetto sul traffico di rete che FastPath Invia direttamente dalla rete locale alle macchine virtuali nella rete virtuale di Azure. 

* Peering VNet: se si dispone di altre reti virtuali con peering con quello connesso a ExpressRoute, il traffico di rete dalla rete locale alle altre reti virtuali, ad esempio il cosiddetto "spoke" reti virtuali, continuerà a essere inviato al gateway di rete virtuale. La soluzione alternativa consiste nel connettere direttamente tutte le reti virtuali al circuito ExpressRoute.

* Load Balancer di base: se si distribuisce un servizio di bilanciamento del carico interno di base nella rete virtuale o il servizio Azure PaaS distribuito nella rete virtuale usa un servizio di bilanciamento del carico interno di base, il traffico di rete proveniente dalla rete locale agli indirizzi IP virtuali ospitati nel servizio di bilanciamento del carico di base verrà inviato al gateway di rete virtuale. La soluzione consiste nell'aggiornare il servizio di bilanciamento del carico di base a un servizio di [bilanciamento del carico standard](../load-balancer/load-balancer-overview.md).

* Collegamento privato: se ci si connette a un [endpoint privato](../private-link/private-link-overview.md) nella rete virtuale dalla rete locale, la connessione passerà attraverso il gateway di rete virtuale.
 
## <a name="next-steps"></a>Passaggi successivi

Per abilitare FastPath, vedere [collegare una rete virtuale a ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
