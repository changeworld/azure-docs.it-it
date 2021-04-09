---
title: Informazioni dettagliate su Azure ExpressRoute con Network Insights
description: Informazioni su Azure ExpressRoute Insights con Network Insights.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050565"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Informazioni dettagliate su Azure ExpressRoute con Network Insights

Questo articolo illustra in che modo Network Insights può aiutarti a visualizzare le metriche e le configurazioni di ExpressRoute in un'unica posizione. Con Network Insights è possibile visualizzare le mappe e i dashboard di integrità topologici contenenti informazioni importanti su ExpressRoute senza dover completare la configurazione aggiuntiva.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Screenshot della pagina di destinazione di monitoraggio ExpressRoute." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Visualizzare le dipendenze funzionali

Per visualizzare questa soluzione, passare alla pagina *monitoraggio di Azure* , selezionare *reti*, quindi selezionare la scheda *circuiti ExpressRoute* . Quindi, selezionare il pulsante topologia per il circuito che si desidera visualizzare.

La visualizzazione delle dipendenze funzionali fornisce un quadro chiaro dell'installazione di ExpressRoute, delineando la relazione tra diversi componenti di ExpressRoute (peering, connessioni, gateway).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Screenshot della visualizzazione topologia per informazioni dettagliate sulla rete." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Posizionare il puntatore del mouse su qualsiasi componente della mappa della topologia per visualizzare le informazioni di configurazione. Ad esempio, passare il mouse su un componente peering ExpressRoute per visualizzare i dettagli, ad esempio la larghezza di banda del circuito e l'abilitazione del Copertura globale.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Screenshot del passaggio del mouse sulle risorse della visualizzazione topologia." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Visualizzare un dashboard dettagliato e precaricato delle metriche

Una volta esaminata la topologia del programma di installazione di ExpressRoute usando la visualizzazione dipendenze funzionale, selezionare **Visualizza metriche dettagliate** per passare alla visualizzazione dettagliata delle metriche per comprendere le prestazioni del circuito. Questa visualizzazione offre un elenco organizzato di risorse collegate e un dashboard completo di importanti metriche di ExpressRoute.

La sezione **risorse collegate** elenca i gateway ExpressRoute connessi e i peering configurati, che è possibile selezionare per passare alla pagina delle risorse corrispondente.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Screenshot della risorsa collegata nella pagina monitoraggio.":::


La sezione relativa alle **metriche di ExpressRoute** include grafici di metriche del circuito importanti tra le categorie di **disponibilità**, **velocità effettiva**, **gocce di pacchetti** e **metriche del gateway**.

### <a name="availability"></a>Disponibilità

La scheda *disponibilità* tiene traccia della disponibilità di ARP e BGP, tracciando i dati per il circuito come un intero e una singola connessione (primaria e secondaria). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Screenshot dei grafici della metrica di disponibilità." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Velocità effettiva

Analogamente, la scheda *velocità effettiva* traccia la velocità effettiva totale del traffico in ingresso e in uscita per il circuito in bit al secondo. È anche possibile visualizzare la velocità effettiva per le singole connessioni e ogni tipo di peering configurato.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Screenshot dei grafici della metrica della velocità effettiva." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Rilascio del pacchetto

La scheda *gocce pacchetto* traccia i bit rilasciati al secondo per il traffico in ingresso e in uscita attraverso il circuito. Questa scheda fornisce un modo semplice per monitorare i problemi di prestazioni che possono verificarsi se è necessario o supera la larghezza di banda del circuito.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Screenshot dei grafici dei pacchetti eliminati." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Metriche del gateway

Infine, la scheda metriche del gateway viene popolata con i grafici delle metriche chiave per un gateway ExpressRoute selezionato (nella sezione risorse collegate). Usare questa scheda quando è necessario monitorare la connettività a reti virtuali specifiche.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Screenshot delle metriche della CPU e della velocità effettiva del gateway." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Passaggi successivi

Configurare la connessione ExpressRoute.
  
* Scopri di più su [Azure ExpressRoute](expressroute-introduction.md), [Network insights](../azure-monitor/insights/network-insights-overview.md)e [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Creare e modificare un circuito](expressroute-howto-circuit-arm.md)
* [Creare e modificare configurazioni di peering](expressroute-howto-routing-arm.md)
* [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
* [Personalizzare le metriche](expressroute-monitoring-metrics-alerts.md) e creare un [monitoraggio della connessione](../network-watcher/connection-monitor-overview.md)
