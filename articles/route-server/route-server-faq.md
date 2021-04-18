---
title: Domande frequenti sul server di route di Azure
description: Risposte alle domande frequenti sul server di route di Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 0bbe16fb63a4546b4b4745df16074f6a4b0cb26b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599537"
---
# <a name="azure-route-server-preview-faq"></a>Domande frequenti sul server di route di Azure (anteprima)

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Che cos'è il server di route di Azure?

Il server di route di Azure è un servizio completamente gestito che consente di gestire facilmente il routing tra l'appliance virtuale di rete e la rete virtuale.

### <a name="is-azure-route-server-just-a-vm"></a>Il server di route di Azure è solo una macchina virtuale?

No. Il server di route di Azure è un servizio progettato con disponibilità elevata. Se viene distribuito in un'area di Azure che supporta zone di disponibilità [,](../availability-zones/az-overview.md)avrà ridondanza a livello di zona.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Quanti server di route è possibile creare in una rete virtuale?

È possibile creare un solo server di route in una rete virtuale. Deve essere distribuito in una subnet designata denominata *RouteServerSubnet.*

### <a name="does-azure-route-server-support-vnet-peering"></a>Il server di route di Azure supporta il peering reti virtuali?

Sì. Se si peer di una rete virtuale che ospita il server di route di Azure a un'altra rete virtuale e si abilita Usa gateway remoto nella seconda rete virtuale, il server di route di Azure apprenderà gli spazi di indirizzi di tale rete virtuale e li invierà a tutte le appliance virtuali di rete con peer. Verranno inoltre programmate le route dalle appliance virtuali di rete nella tabella di routing delle macchine virtuali nella rete virtuale con peer. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Quali protocolli di routing sono supportati dal server di route di Azure?

Il server di route di Azure supporta Border Gateway Protocol (BGP). L'NVA deve supportare BGP esterno multi hop perché sarà necessario distribuire il server di route di Azure in una subnet dedicata nella rete virtuale. [L'ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) scelto deve essere diverso da quello utilizzato dal server di route di Azure quando si configura il protocollo BGP nell'NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Il server di route di Azure instrada il traffico dati tra l'NVA e le macchine virtuali?

No. Il server di route di Azure scambia solo route BGP con l'infrastruttura di rete. Il traffico dati passa direttamente dall'NVA alla macchina virtuale di destinazione e direttamente dalla macchina virtuale all'NVA.

### <a name="does-azure-route-server-store-customer-data"></a>Il server di route di Azure archivia i dati dei clienti?
No. Il server di route di Azure scambia solo route BGP con l'infrastruttura di rete e quindi le propaga alla rete virtuale.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Se il server di route di Azure riceve la stessa route da più macchine virtuali di rete, come vengono gestite?

Se la route ha la stessa lunghezza del percorso AS, il server di route di Azure programma più copie della route, ognuna con un hop successivo diverso, alle macchine virtuali nella rete virtuale. Quando le macchine virtuali inviano il traffico alla destinazione di questa route, gli host di macchine virtuali Equal-Cost routing ECMP (Multi-Path). Tuttavia, se un'appliance virtuale di rete invia la route con una lunghezza del percorso AS più breve rispetto ad altre appliance virtuali di rete, il server di route di Azure programma solo la route con l'hop successivo impostato su questa appliance virtuale di rete per le macchine virtuali nella rete virtuale.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Il server di route di Azure mantiene le community BGP della route che riceve?

Sì, il server di route di Azure propaga la route con le community BGP così come sono.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Quali numeri di sistema autonomi (ASN) è possibile usare?

È possibile usare asn pubblici o asn privati nell'appliance virtuale di rete. Non è possibile usare gli intervalli riservati da Azure o IANA.
I seguenti numeri ASN sono riservati da Azure o da IANA:

* Numeri ASN riservati da Azure:
    * ASN pubblici: 8074, 8075, 12076
    * ASN privati: 65515, 65517, 65518, 65519, 65520
* ASN [riservati da IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>È possibile usare asn a 32 bit (4 byte) ?

No, il server di route di Azure supporta solo asn a 16 bit (2 byte).

## <a name="route-server-limits"></a><a name = "limitations"></a>Limiti del server di route

Il server di route di Azure ha i limiti seguenti (per ogni distribuzione).

| Risorsa | Limite |
|----------|-------|
| Numero di peer BGP supportati | 8 |
| Numero di route che ogni peer BGP può annunciare al server di route di Azure | 200 |
| Numero di route che il server di route di Azure può annunciare a ExpressRoute o al gateway VPN | 200 |

Se l'NVA annuncia più route rispetto al limite, la sessione BGP verrà eliminata. Se ciò si verifica nel gateway e nel server di route di Azure, si perderà la connettività dalla rete locale ad Azure. Per altre informazioni, vedere Diagnosticare [un problema di routing di una macchina virtuale di Azure.](../virtual-network/diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare [il server di route di Azure.](quickstart-configure-route-server-powershell.md)
