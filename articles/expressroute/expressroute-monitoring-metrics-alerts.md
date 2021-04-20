---
title: 'Azure ExpressRoute: monitoraggio, metriche e avvisi'
description: Informazioni su Azure ExpressRoute monitoraggio, metriche e avvisi usando Monitoraggio di Azure, il negozio unico per tutte le metriche, gli avvisi e i log di diagnostica in Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725783"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoraggio, metriche e avvisi di ExpressRoute

Questo articolo illustra il monitoraggio, le metriche e gli avvisi di ExpressRoute usando Monitoraggio di Azure. Monitoraggio di Azure è uno strumento centralizzato per tutte le metriche, gli avvisi, i log di diagnostica in Azure.
 
>[!NOTE]
>L'uso delle **metriche classiche** non è consigliato.
>

## <a name="expressroute-metrics"></a>Metriche di ExpressRoute

Per visualizzare **Metriche,** passare *alla* pagina Monitoraggio di Azure e selezionare *Metriche*. Per visualizzare le **metriche di ExpressRoute,** filtrare in base al tipo di *risorsa Circuiti ExpressRoute*. Per visualizzare **Copertura globale** metriche, filtrare in base ai circuiti *ExpressRoute* del tipo di risorsa e selezionare una risorsa circuito ExpressRoute Copertura globale abilitata. Per visualizzare **ExpressRoute Direct** metriche, filtrare Tipo di risorsa in base *alle porte ExpressRoute.* 

Dopo aver selezionato una metrica, verrà applicata l'aggregazione predefinita. Facoltativamente, è possibile applicare la suddivisione, che mostrerà la metrica con dimensioni diverse.

### <a name="aggregation-types"></a>Tipi di aggregazione:

Esplora metriche supporta SUM, MAX, MIN, AVG e COUNT come tipi [di aggregazione](../azure-monitor/essentials/metrics-charts.md#aggregation). È consigliabile usare il tipo di aggregazione consigliato quando si esaminano le informazioni dettagliate per ogni metrica di ExpressRoute.

* Somma: somma di tutti i valori acquisiti durante l'intervallo di aggregazione. 
* Conteggio: numero di misurazioni acquisite durante l'intervallo di aggregazione. 
* Media: media dei valori delle metriche acquisiti durante l'intervallo di aggregazione. 
* Min: il valore più piccolo acquisito durante l'intervallo di aggregazione. 
* Max: valore massimo acquisito durante l'intervallo di aggregazione. 

### <a name="available-metrics"></a>Metriche disponibili

|**Metrica**|**Categoria**|**Dimensioni**|**Funzionalità**|
| --- | --- | --- | --- |
|Disponibilità ARP|Disponibilità|<ui><li>Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering (privato/pubblico/Microsoft)</ui></li>|ExpressRoute|
|Disponibilità BGP|Disponibilità|<ui><li> Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Traffico|<ui><li> Tipo di peering (ExpressRoute)</ui></li><ui><li>Collegamento (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>Connessione gateway ExpressRoute</ui></li>|
|BitsOutPerSecond|Traffico| <ui><li>Tipo di peering (ExpressRoute)</ui></li><ui><li> Collegamento (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>Connessione gateway ExpressRoute</ui></li>|
|Utilizzo CPU|Prestazioni| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|Pacchetti al secondo|Prestazioni| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|Conteggio delle route annunciate al peer |Disponibilità| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|Conteggio delle route apprese dal peer |Disponibilità| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|Frequenza di modifica delle route |Disponibilità| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|Numero di macchine virtuali nella rete virtuale |Disponibilità| N/D |Gateway di rete virtuale ExpressRoute|
|GlobalReachBitsInPerSecond|Traffico|<ui><li>Skey del circuito con peered (chiave del servizio)</ui></li>|Copertura globale|
|GlobalReachBitsOutPerSecond|Traffico|<ui><li>Skey del circuito con peered (chiave del servizio)</ui></li>|Copertura globale|
|AdminState|Connettività fisica|Collegamento|ExpressRoute Direct|
|LineProtocol|Connettività fisica|Collegamento|ExpressRoute Direct|
|RxLightLevel|Connettività fisica|<ui><li>Collegamento</ui></li><ui><li>corsia</ui></li>|ExpressRoute Direct|
|TxLightLevel|Connettività fisica|<ui><li>Collegamento</ui></li><ui><li>corsia</ui></li>|ExpressRoute Direct|
>[!NOTE]
>L'uso di *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* sarà visibile solo se viene stabilita almeno una Copertura globale connessione.
>

## <a name="circuits-metrics"></a>Metriche dei circuiti

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bit in e out - Metriche in tutti i peering

Tipo di aggregazione: *Avg*

È possibile visualizzare le metriche in tutti i peering in un circuito ExpressRoute specificato.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metriche del circuito":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bit in e out - Metriche per peering

Tipo di aggregazione: *Avg*

È possibile visualizzare le metriche in base al tipo di peering privato, pubblico e Microsoft in bit al secondo.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metriche per peering":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilità BGP - Suddivisione per peer  

Tipo di *aggregazione: Media*

È possibile visualizzare la disponibilità quasi in tempo reale di BGP (connettività di livello 3) tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra che lo stato della sessione BGP primaria è impostato per il peering privato e che lo stato della seconda sessione BGP è in stato incesto per il peering privato. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilità BGP per peer":::

### <a name="arp-availability---split-by-peering"></a>Disponibilità ARP - Suddivisione in base al peering  

Tipo di *aggregazione: Media*

È possibile visualizzare la disponibilità quasi in tempo reale di [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (connettività di livello 3) tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra che lo stato della sessione ARP di peering privato è impostato su entrambi i peer, ma in basso per il peering Microsoft per entrambi i peer. L'aggregazione predefinita (Media) è stata utilizzata in entrambi i peer.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilità ARP per peer":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct metriche

### <a name="admin-state---split-by-link"></a>Stato amministratore - Dividi per collegamento

Tipo di *aggregazione: Media*

È possibile visualizzare lo stato Amministratore per ogni collegamento della coppia ExpressRoute Direct porta. Lo stato Admin indica se la porta fisica è on o off. Questo stato è necessario per passare il traffico attraverso la ExpressRoute Direct connessione.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Stato dell'amministratore di Direct ER":::

### <a name="bits-in-per-second---split-by-link"></a>Bit al secondo - Dividi per collegamento

Tipo di *aggregazione: Media*

È possibile visualizzare i bit al secondo in entrambi i collegamenti della coppia ExpressRoute Direct porta. Monitorare questo dashboard per confrontare la larghezza di banda in ingresso per entrambi i collegamenti.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Bit diretti ER in al secondo":::

### <a name="bits-out-per-second---split-by-link"></a>Bit out al secondo - Divisione per collegamento

Tipo di aggregazione: *Avg*

È anche possibile visualizzare i bit al secondo in entrambi i collegamenti della coppia ExpressRoute Direct porta. Monitorare questo dashboard per confrontare la larghezza di banda in uscita per entrambi i collegamenti.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Bit diretti al secondo":::

### <a name="line-protocol---split-by-link"></a>Protocollo di linea - Divisione per collegamento

Tipo di aggregazione: *Avg*

È possibile visualizzare il protocollo di linea in ogni collegamento della coppia ExpressRoute Direct porta. Il protocollo line indica se il collegamento fisico è in esecuzione su ExpressRoute Direct. Monitorare questo dashboard e impostare gli avvisi per sapere quando la connessione fisica è stata erta.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocollo ER Direct Line":::

### <a name="rx-light-level---split-by-link"></a>Livello di luce Rx - Dividi per collegamento

Tipo di aggregazione: *Avg*

È possibile visualizzare il livello di luce Rx (il livello di luce che la ExpressRoute Direct sta ricevendo **)** per ogni porta. I livelli di luce Rx integri rientrano in genere in un intervallo compreso tra -10 dBm e 0 dBm. Impostare gli avvisi per ricevere una notifica se il livello di luce Rx non rientra nell'intervallo integro.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx Light Level":::

### <a name="tx-light-level---split-by-link"></a>Livello di luce Tx - Dividi per collegamento

Tipo di *aggregazione: Media*

È possibile visualizzare il livello di luce Tx (il livello di luce che la porta ExpressRoute Direct **trasmette)** per ogni porta. I livelli di luce Tx integri rientrano in genere in un intervallo compreso tra -10 dBm e 0 dBm. Impostare avvisi per ricevere una notifica se il livello di luce Tx non rientra nell'intervallo integro.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line Tx Light Level":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metriche del gateway di rete virtuale ExpressRoute

Tipo di *aggregazione: Media*

Quando si distribuisce un gateway ExpressRoute, Azure gestisce il calcolo e le funzioni del gateway. Sono disponibili sei metriche del gateway per comprendere meglio le prestazioni del gateway:

* Utilizzo CPU
* Pacchetti al secondo
* Numero di route annunciate ai peer
* Conteggio delle route apprese dai peer
* Frequenza delle route modificate
* Numero di macchine virtuali nella rete virtuale  

È consigliabile impostare avvisi per ognuna di queste metriche in modo da essere consapevoli del momento in cui il gateway potrebbe visualizzare problemi di prestazioni.

### <a name="cpu-utilization---split-instance"></a>Utilizzo CPU - Istanza suddivisa

Tipo di *aggregazione: Media*

È possibile visualizzare l'utilizzo della CPU di ogni istanza del gateway. L'utilizzo della CPU può verificarsi brevemente durante la manutenzione di routine dell'host, ma un utilizzo elevato prolungato della CPU potrebbe indicare che il gateway sta raggiungendo un collo di bottiglia delle prestazioni. L'aumento delle dimensioni del gateway ExpressRoute può risolvere il problema. Impostare un avviso per la frequenza con cui l'utilizzo della CPU supera una determinata soglia.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Screenshot dell'utilizzo della CPU : metriche suddivise.":::

### <a name="packets-per-second---split-by-instance"></a>Pacchetti al secondo - Divisione per istanza

Tipo di aggregazione: *Avg*

Questa metrica acquisisce il numero di pacchetti in ingresso che attraversano il gateway ExpressRoute. Dovrebbe essere visualizzato un flusso coerente di dati se il gateway riceve traffico dalla rete locale. Impostare un avviso per quando il numero di pacchetti al secondo scende al di sotto di una soglia che indica che il gateway non riceve più traffico.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Screenshot dei pacchetti al secondo: metriche suddivise.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Conteggio delle route annunciate al peer - Suddivisione per istanza

Tipo di aggregazione: *Conteggio*

Questa metrica è il numero di route che il gateway ExpressRoute sta pubblicizzando verso il circuito. Gli spazi indirizzi possono includere reti virtuali connesse tramite peering reti virtuali e che usano il gateway ExpressRoute remoto. È consigliabile prevedere che il numero di route rimanga coerente a meno che non vengano apportate modifiche frequenti agli spazi di indirizzi della rete virtuale. Impostare un avviso per quando il numero di route annunciate scende al di sotto della soglia per il numero di spazi di indirizzi di rete virtuale di cui si è a conoscenza.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Screenshot del numero di route annunciate al peer.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Conteggio delle route apprese dal peer - Divisione per istanza

Tipo di aggregazione: *Max*

Questa metrica mostra il numero di route che il gateway ExpressRoute sta imparando dai peer connessi al circuito ExpressRoute. Queste route possono essere da un'altra rete virtuale connessa allo stesso circuito o apprese dall'ambiente locale. Impostare un avviso per quando il numero di route apprese scende al di sotto di una determinata soglia. Ciò potrebbe indicare che il gateway sta verificando un problema di prestazioni o che i peer remoti non annuncino più route al circuito ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Screenshot del conteggio delle route apprese dal peer.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Frequenza di modifica delle route - Divisione per istanza

Tipo di aggregazione: *Somma*

Questa metrica mostra la frequenza delle route apprese o annunciate ai peer remoti. È consigliabile esaminare prima di tutto i dispositivi locali per capire perché la rete cambia con una frequenza così frequente. Una frequenza elevata nella modifica delle route potrebbe indicare un problema di prestazioni nel gateway ExpressRoute in cui il ridimensionamento dello SKU del gateway può risolvere il problema. Impostare un avviso per una soglia di frequenza da tenere presente quando il gateway ExpressRoute vede modifiche di route anomale.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Screenshot della frequenza delle metriche modificate delle route.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Numero di macchine virtuali nella rete virtuale

Tipo di aggregazione: *Max*

Questa metrica indica il numero di macchine virtuali che usano il gateway ExpressRoute. Il numero di macchine virtuali può includere macchine virtuali da reti virtuali con peered che usano lo stesso gateway ExpressRoute. Impostare un avviso per questa metrica se il numero di macchine virtuali supera una determinata soglia che potrebbe influire sulle prestazioni del gateway. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Screenshot del numero di macchine virtuali nella metrica della rete virtuale.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connessioni gateway di ExpressRoute in bit al secondo

Tipo di *aggregazione: Media*

Questa metrica mostra l'utilizzo della larghezza di banda per una connessione specifica a un circuito ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Screenshot della metrica di utilizzo della larghezza di banda della connessione del gateway.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Avvisi per le connessioni gateway di ExpressRoute

1. Per configurare gli avvisi, passare **Monitoraggio di Azure** e quindi selezionare **Avvisi.**

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="avvisi":::
2. Selezionare **+Seleziona destinazione e** selezionare la risorsa di connessione gateway ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="target":::
3. Definire i dettagli dell'avviso.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="gruppo di azioni":::
4. Definire e aggiungere il gruppo di azioni.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="aggiungere un gruppo di azioni":::

## <a name="alerts-based-on-each-peering"></a>Avvisi basati su ciascun peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="ogni peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurare avvisi per i log attività sui circuiti

In **Criteri di avviso** è possibile selezionare **Log attività** per il tipo di segnale e selezionare il segnale.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="log attività":::

## <a name="more-metrics-in-log-analytics"></a>Altre metriche in Log Analytics

È anche possibile visualizzare le metriche di ExpressRoute passando alla risorsa del circuito ExpressRoute e selezionando la *scheda* Log. Per le metriche su cui si esegue una query, l'output conterrà le colonne seguenti.

|**Colonna**|**Tipo**|**Descrizione**|
| --- | --- | --- |
|TimeGrain|string|PT1M (i valori delle metriche vengono inseriti ogni minuto)|
|Conteggio|real|In genere uguale a 2 (ogni MSEE invia un singolo valore di metrica ogni minuto)|
|Minima|real|Valore minimo dei due valori di metrica inseriti dai due MSEE|
|Massimo|real|Numero massimo di due valori di metrica inseriti dai due MSEE|
|Media|real|Uguale a (Minimo + Massimo)/2|
|Totale|real|Somma dei due valori delle metriche da entrambi gli MSEE (il valore principale su cui concentrarsi per la metrica su cui viene eseguita la query)|
  
## <a name="next-steps"></a>Passaggi successivi

Configurare la connessione ExpressRoute.
  
* [Creare e modificare un circuito](expressroute-howto-circuit-arm.md)
* [Creare e modificare configurazioni di peering](expressroute-howto-routing-arm.md)
* [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
