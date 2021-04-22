---
title: Concetti - Interconnettività di rete
description: Informazioni sugli aspetti chiave e sui casi d'uso della rete e dell'interconnettività in soluzione Azure VMware.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a6c86c0a9eb8e07a91a094ddf5d6fb77b0eddf67
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871688"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>soluzione Azure VMware di rete e interconnettività

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Esistono due modi per l'interconnettività nel cloud soluzione Azure VMware privato:

- [**L'interconnettività di**](#azure-virtual-network-interconnectivity) base solo per Azure consente di gestire e usare il cloud privato con una sola rete virtuale in Azure. Questa implementazione è più adatta per soluzione Azure VMware o implementazioni che non richiedono l'accesso da ambienti locali.

- [**L'interconnettività**](#on-premises-interconnectivity) completa da locale a cloud privato estende l'implementazione di base solo di Azure per includere l'interconnettività tra i cloud locali e soluzione Azure VMware cloud privati.
 
Questo articolo illustra i concetti chiave che stabiliscono la rete e l'interconnettività, inclusi i requisiti e le limitazioni. Questo articolo fornisce le informazioni necessarie per configurare la rete per l'soluzione Azure VMware.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>soluzione Azure VMware casi d'uso del cloud privato

I casi d'uso soluzione Azure VMware cloud privati includono:
- Nuovi carichi di lavoro delle macchine virtuali VMware nel cloud
- Burst del carico di lavoro della macchina virtuale nel cloud (solo da soluzione Azure VMware locale)
- Migrazione del carico di lavoro delle macchine virtuali al cloud (solo da soluzione Azure VMware locale)
- Ripristino di emergenza (soluzione Azure VMware in soluzione Azure VMware o da locale a soluzione Azure VMware)
- Utilizzo di servizi di Azure

> [!TIP]
> Tutti i casi d'uso soluzione Azure VMware servizio sono abilitati con connettività da locale a cloud privato.

## <a name="azure-virtual-network-interconnectivity"></a>Interconnessione della rete virtuale di Azure

È possibile interconnettere la rete virtuale di Azure con l'soluzione Azure VMware di cloud privato. È possibile gestire il soluzione Azure VMware cloud privato, utilizzare i carichi di lavoro nel cloud privato e accedere ad altri servizi di Azure.

Il diagramma seguente illustra l'interconnettività di rete di base stabilita al momento della distribuzione di un cloud privato. Mostra la rete logica tra una rete virtuale in Azure e un cloud privato. Questa connettività viene stabilita tramite expressroute back-end che fa parte del soluzione Azure VMware servizio. L'interconnettività soddisfa i casi d'uso principali seguenti:

- Accesso in ingresso al server vCenter e al gestore NSX-T accessibile dalle macchine virtuali nella sottoscrizione di Azure.
- Accesso in uscita dalle macchine virtuali nel cloud privato ai servizi di Azure.
- Accesso in ingresso dei carichi di lavoro in esecuzione nel cloud privato.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Connettività da rete virtuale di base a cloud privato" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconnettività locale

Nello scenario completamente interconnesso è possibile accedere al soluzione Azure VMware dalla rete virtuale di Azure e dall'ambiente locale. Questa implementazione è un'estensione dell'implementazione di base descritta nella sezione precedente. È necessario un circuito ExpressRoute per connettersi dall'ambiente locale soluzione Azure VMware cloud privato in Azure.

Il diagramma seguente illustra l'interconnettività da locale a cloud privato, che consente i casi d'uso seguenti:

- vMotion a caldo/freddo vCenter tra locale e soluzione Azure VMware.
- Locale per l'accesso soluzione Azure VMware di gestione del cloud privato.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rete virtuale e connettività cloud privata completa locale" border="false":::

Per l'interconnettività completa al cloud privato, è necessario abilitare ExpressRoute Copertura globale e quindi richiedere una chiave di autorizzazione e un ID peering privato per Copertura globale nel portale di Azure. La chiave di autorizzazione e l'ID peering vengono usati per stabilire Copertura globale tra un circuito ExpressRoute nella sottoscrizione e il circuito ExpressRoute per il cloud privato. Una volta collegati, i due circuiti ExpressRoute instradano il traffico di rete tra gli ambienti locali al cloud privato. Per altre informazioni sulle procedure, vedere l'esercitazione per la creazione di [un'istanza di ExpressRoute Copertura globale peering a un cloud privato.](tutorial-expressroute-global-reach-private-cloud.md)

## <a name="limitations"></a>Limitazioni
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Passaggi successivi 

Dopo aver trattato i concetti soluzione Azure VMware rete e interconnettività, è possibile ottenere informazioni su:

- [soluzione Azure VMware concetti relativi all'archiviazione](concepts-storage.md)
- [soluzione Azure VMware di identità](concepts-identity.md)
- [Come abilitare la soluzione Azure VMware risorsa](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
