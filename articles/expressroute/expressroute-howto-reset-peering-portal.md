---
title: 'Azure ExpressRoute: reimpostare i peering del circuito usando il portale di Azure'
description: Informazioni su come disabilitare e abilitare i peering di un circuito ExpressRoute di Azure usando il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680282"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Reimpostare i peering del circuito ExpressRoute usando il portale di Azure

Questo articolo descrive come disabilitare e abilitare i peering di un circuito ExpressRoute di Azure usando il portale di Azure. Quando si disabilita un peering, la sessione di Border Gateway Protocol (BGP) per la connessione primaria e secondaria del circuito ExpressRoute viene arrestata. Quando si Abilita un peering, viene ripristinata la sessione BGP nella connessione primaria e secondaria del circuito ExpressRoute.

> [!Note]
> La prima volta che si configurano i peering sul circuito ExpressRoute, i peering sono abilitati per impostazione predefinita.

La reimpostazione dei peering ExpressRoute può essere utile negli scenari seguenti:

* Si sta testando la progettazione e l'implementazione del ripristino di emergenza. Si supponga, ad esempio, di avere due circuiti ExpressRoute. È possibile disabilitare i peering di un circuito e forzare il traffico di rete a usare l'altro circuito.

* Si vuole abilitare il rilevamento di inoltri bidirezionale (BFD) nel peering privato di Azure o nel peering Microsoft. Se il circuito ExpressRoute è stato creato prima del 1 ° agosto 2018, nel peering privato di Azure o prima del 10 gennaio 2020, nel peering Microsoft, BFD non è stato abilitato per impostazione predefinita. Reimpostare il peering per abilitare BFD.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Da un browser passare alla [portale di Azure](https://portal.azure.com)e quindi accedere con l'account Azure.

## <a name="reset-a-peering"></a>Reimpostare un peering

È possibile reimpostare il peering Microsoft e il peering privato di Azure in un circuito ExpressRoute in modo indipendente.

1. Scegliere il circuito che si desidera modificare.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Screenshot che mostra la scelta di un circuito nell'elenco di circuiti ExpressRoute.":::

1. Scegliere la configurazione del peering che si vuole reimpostare.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Screenshot che mostra la scelta di un peering nella panoramica del circuito ExpressRoute.":::

1. Deselezionare la casella di controllo **Abilita peering** , quindi selezionare **Salva** per disabilitare la configurazione del peering.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Screenshot che Mostra come deselezionare la casella di controllo Abilita peering.":::

1. Selezionare la casella di controllo **Abilita peering** , quindi selezionare **Salva** per abilitare nuovamente la configurazione del peering.

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi relativi a ExpressRoute, vedere gli articoli seguenti:

* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)
