---
title: Domande frequenti sul server di route di Azure
description: Trovare le risposte alle domande frequenti sul server di route di Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485420"
---
# <a name="azure-route-server-preview-faq"></a>Domande frequenti sul server di route di Azure (anteprima)

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Che cos'è il server di route di Azure?

Il server di route di Azure è un servizio completamente gestito che consente di gestire facilmente il routing tra l'appliance virtuale di rete e la rete virtuale.

### <a name="is-azure-route-server-just-a-vm"></a>Il server di routing di Azure è semplicemente una VM?

No. Il server di route di Azure è un servizio progettato con disponibilità elevata. Se viene distribuito in un'area di Azure che supporta [zone di disponibilità](../availability-zones/az-overview.md), avrà una ridondanza a livello di zona.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Quali protocolli di routing supportano il server di routing di Azure?

Il server di route di Azure supporta solo Border Gateway Protocol (BGP). L'appliance virtuale di rete deve supportare il protocollo BGP esterno a più hop perché è necessario distribuire il server di route di Azure in una subnet dedicata nella rete virtuale. L' [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) scelto deve essere diverso da quello usato dal server di route di Azure quando si configura BGP nell'appliance virtuale di Azure.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Il server di routing di Azure instrada il traffico dati tra l'appliance virtuale di dispositivo e le VM?

No. Il server di route di Azure scambia solo le route BGP con l'appliance virtuale di Azure. Il traffico dati passa direttamente dall'appliance virtuale di dispositivo alla VM scelta e direttamente dalla macchina virtuale all'appliance virtuale di dispositivo.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Se il server di route di Azure riceve la stessa route da più di un appliance virtuale di rete, programma tutte le copie della route (ma ognuna con un hop successivo diverso) per le macchine virtuali nella rete virtuale?

Sì, solo se la route ha lo stesso nome della lunghezza del percorso. Quando le macchine virtuali inviano il traffico alla destinazione di questa route, gli host della macchina virtuale eseguiranno Equal-Cost il routing a percorsi multipli (ECMP). Tuttavia, se un'appliance virtuale di dispositivo invia la route con una lunghezza di percorso più breve rispetto ad altri appliance virtuali. Il server di route di Azure programmerà solo la route con l'hop successivo impostato sull'appliance virtuale di rete per le macchine virtuali nella rete virtuale.

### <a name="does-azure-route-server-support-vnet-peering"></a>Il server di routing di Azure supporta il peering VNet?

Sì. Se si esegue il peering di un VNet che ospita il server di route di Azure in un altro VNet e si Abilita l'uso di gateway remoto in tale VNet. Il server di route di Azure apprenderà gli spazi di indirizzi di VNet e li invierà a tutti i appliance virtuali con peering.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Quali numeri di sistema autonomi (ASN) è possibile usare?

È possibile usare il proprio ASN pubblico o ASN privato nell'appliance virtuale di rete. Non è possibile usare gli intervalli riservati da Azure o IANA.
I seguenti numeri ASN sono riservati da Azure o da IANA:

* Numeri ASN riservati da Azure:
    * ASN pubblici: 8074, 8075, 12076
    * ASN privati: 65515, 65517, 65518, 65519, 65520
* ASN [riservati da IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>È possibile usare ASN a 32 bit (4 byte)?

No, il server di route di Azure supporta solo ASN a 16 bit (2 byte).

## <a name="route-server-limits"></a><a name = "limitations"></a>Limiti del server di route

Il server di route di Azure presenta i limiti seguenti (per distribuzione).

| Risorsa | Limite |
|----------|-------|
| Numero di peer BGP supportati | 8 |
| Numero di route che ogni peer BGP può annunciare al server di route di Azure | 200 |
| Numero di route che il server di route di Azure può pubblicizzare per ExpressRoute o gateway VPN | 200 |

Se l'appliance virtuale di virtuali annuncia più route rispetto al limite, la sessione BGP verrà eliminata. Se si verifica questo problema per il gateway e il server di route di Azure, si perderà la connettività dalla rete locale ad Azure. Per altre informazioni, vedere [diagnosticare un problema di routing di una macchina virtuale di Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare il server di route di Azure](quickstart-configure-route-server-powershell.md).
