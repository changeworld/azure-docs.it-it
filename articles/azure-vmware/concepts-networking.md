---
title: Concetti-interconnettività di rete
description: Informazioni sugli aspetti chiave e i casi d'uso di rete e interconnettività nella soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: cd62949c13b1f12e635d8d7bf07518a94c4e8d4b
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462585"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Concetti relativi alla rete e alla connettività della soluzione VMware di Azure

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Esistono due modi per eseguire l'interconnettività nel cloud privato della soluzione VMware di Azure:

1. L' [**interconnettività di base di Azure**](#azure-virtual-network-interconnectivity) consente di gestire e usare il cloud privato con una sola rete virtuale in Azure. Questa implementazione è più adatta per le valutazioni delle soluzioni VMware di Azure o per le implementazioni che non richiedono l'accesso da ambienti locali.

1. L' [**interconnettività completa da locale a cloud privato**](#on-premises-interconnectivity) estende l'implementazione di base di Azure per includere l'interconnettività tra cloud privati della soluzione VMware locale e Azure.
 
In questo articolo verranno illustrati i concetti chiave che stabiliscono la rete e l'interconnettività, inclusi i requisiti e le limitazioni. Questo articolo fornisce le informazioni necessarie per configurare la rete per l'uso con la soluzione VMware di Azure.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Casi d'uso del cloud privato della soluzione VMware di Azure

I casi d'uso per i cloud privati della soluzione VMware di Azure includono:
- Nuovi carichi di lavoro delle macchine virtuali VMware nel cloud
- Aumento del carico di lavoro della macchina virtuale nel cloud (solo per la soluzione VMware da sito locale ad Azure)
- Migrazione del carico di lavoro della macchina virtuale nel cloud (solo per la soluzione VMware da sito locale ad Azure)
- Ripristino di emergenza (soluzione VMware di Azure alla soluzione VMware di Azure o alla soluzione VMware da sito locale ad Azure)
- Utilizzo di servizi di Azure

> [!TIP]
> Tutti i casi d'uso per il servizio della soluzione VMware di Azure sono abilitati con la connettività da sito locale a cloud privato.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnettività rete virtuale di Azure

È possibile collegare la rete virtuale di Azure all'implementazione del cloud privato della soluzione VMware di Azure. È possibile gestire il cloud privato della soluzione VMware di Azure, utilizzare i carichi di lavoro nel cloud privato e accedere ad altri servizi di Azure.

Il diagramma seguente illustra l'interconnettività di rete di base stabilita al momento della distribuzione di un cloud privato. Mostra la rete logica tra una rete virtuale in Azure e un cloud privato. Questa connettività viene stabilita tramite un ExpressRoute back-end che fa parte del servizio della soluzione VMware di Azure. L'interconnettività soddisfa i casi d'uso principali seguenti:

- Accesso in ingresso al server vCenter e alla gestione NSX-T accessibili dalle macchine virtuali nella sottoscrizione di Azure.
- Accesso in uscita dalle macchine virtuali nel cloud privato ai servizi di Azure.
- Accesso in ingresso dei carichi di lavoro in esecuzione nel cloud privato.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connettività da rete virtuale di base a cloud privato" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnettività locale

Nello scenario completamente interconnesso è possibile accedere alla soluzione VMware di Azure dalle reti virtuali di Azure e in locale. Questa implementazione è un'estensione dell'implementazione di base descritta nella sezione precedente. Per la connessione da locale al cloud privato della soluzione VMware di Azure in Azure è necessario un circuito ExpressRoute.

Il diagramma seguente illustra l'interconnettività da locale a cloud privato, che consente i casi d'uso seguenti:

- VCenter vMotion a caldo/freddo tra la soluzione locale e la soluzione VMware di Azure.
- Accesso alla gestione cloud privata della soluzione VMware da sito locale ad Azure.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rete virtuale e connettività cloud privata completa locale" border="false":::

Per l'interconnettività completa con il cloud privato, è necessario abilitare ExpressRoute Copertura globale e quindi richiedere una chiave di autorizzazione e un ID di peering privato per Copertura globale nel portale di Azure. La chiave di autorizzazione e l'ID del peering vengono usati per stabilire Copertura globale tra un circuito ExpressRoute nella sottoscrizione e il circuito ExpressRoute per il cloud privato. Una volta collegati, i due circuiti ExpressRoute instradano il traffico di rete tra gli ambienti locali al cloud privato. Per ulteriori informazioni sulle procedure, vedere l' [esercitazione per la creazione di un ExpressRoute copertura globale peering a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Limitazioni
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Passaggi successivi 

Ora che sono stati illustrati i concetti relativi alla rete della soluzione VMware di Azure e all'interconnettività, è possibile acquisire familiarità con:

- [Concetti relativi all'archiviazione della soluzione VMware di Azure](concepts-storage.md).
- [Concetti di identità della soluzione VMware di Azure](concepts-identity.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
