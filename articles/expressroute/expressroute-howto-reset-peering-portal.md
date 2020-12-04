---
title: 'Azure ExpressRoute: reimpostare il peering del circuito usando il portale di Azure'
description: Informazioni su come disabilitare e abilitare i peering di un circuito ExpressRoute di Azure usando il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582299"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Reimposta i peering del circuito ExpressRoute usare il portale di Azure

Questo articolo descrive come disabilitare e abilitare i peering di un circuito ExpressRoute usando il portale di Azure. Quando si disabilita un peering, la sessione BGP per la connessione primaria e secondaria del circuito ExpressRoute verrà arrestata. Si perderà la connettività tramite questo peering a Microsoft. Quando si Abilita un peering, viene attivata la sessione BGP sia nella connessione primaria che in quella secondaria del circuito ExpressRoute. Si otterrà nuovamente la connettività tramite questo peering a Microsoft. È possibile abilitare e disabilitare in modo indipendente il peering Microsoft e il peering privato di Azure in un circuito ExpressRoute. La prima volta che si configurano i peering sul circuito ExpressRoute, i peering sono abilitati per impostazione predefinita.

La reimpostazione dei peering ExpressRoute può essere utile in un paio di scenari.
* Esecuzione di test della progettazione e dell'implementazione del ripristino di emergenza. Ad esempio, sono presenti due circuiti ExpressRoute. È possibile disabilitare i peering di un circuito e forzare il failover del traffico di rete nell'altro circuito.
* Abilitare il rilevamento di inoltri bidirezionali (BFD) nel peering privato di Azure o nel peering Microsoft del circuito ExpressRoute. BFD viene abilitato per impostazione predefinita nel peering privato di Azure se il circuito ExpressRoute viene creato dopo il 1 2018 agosto e nel peering Microsoft. Se il circuito ExpressRoute viene creato dopo il 10 2020 gennaio. Se il circuito è stato creato prima di questa data, il rilevamento dell'inoltro bidirezionale non è stato abilitato. È possibile abilitarlo disabilitando il peering e riabilitandolo. 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

## <a name="reset-a-peering"></a>Reimpostare un peering

1. Selezionare il circuito per cui si vuole apportare modifiche alla configurazione del peering.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Elenco di circuiti ExpressRoute":::

1. Selezionare la configurazione del peering che si vuole abilitare o disabilitare.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Panoramica del circuito ExpressRoute":::

1. Deselezionare **Abilita peering** e selezionare **Salva** per disabilitare la configurazione del peering.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Disabilitare il peering privato":::

1. È possibile abilitare di nuovo il peering selezionando **Abilita peering** e selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
Per assistenza nella risoluzione di un problema ExpressRoute, vedere gli articoli seguenti:
* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)
