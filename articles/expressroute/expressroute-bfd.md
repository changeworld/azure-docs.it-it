---
title: 'Azure ExpressRoute: configurare BFD'
description: Questo articolo contiene istruzioni su come configurare il rilevamento dell'inoltro bidirezionale sul peering privato di un circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511264"
---
# <a name="configure-bfd-over-expressroute"></a>Configurare il rilevamento dell'inoltro bidirezionale su ExpressRoute

ExpressRoute supporta il rilevamento di inoltri bidirezionale (BFD) sia sul peering privato che sul peering Microsoft. Quando si Abilita BFD su ExpressRoute, è possibile velocizzare il rilevamento degli errori di collegamento tra i dispositivi Microsoft Enterprise Edge (MSEE) e i router che il circuito ExpressRoute viene configurato (CE/PE). È possibile configurare ExpressRoute sui dispositivi di routing perimetrale o sui dispositivi di routing Edge del partner (se si è scelto di usare il servizio di connessione managed Layer 3). Questo documento illustra i vantaggi offerti dal rilevamento dell'inoltro bidirezionale e mostra come abilitare questa funzionalità su ExpressRoute.

## <a name="need-for-bfd"></a>Vantaggi del rilevamento dell'inoltro bidirezionale

Il diagramma seguente illustra i vantaggi offerti dall'abilitazione del rilevamento dell'inoltro bidirezionale sul circuito ExpressRoute: [![1]][1]

È possibile abilitare un circuito ExpressRoute tramite connessioni di livello 2 o connessioni gestite di livello 3. In entrambi i casi, se sono presenti più dispositivi di livello 2 nel percorso di connessione ExpressRoute, la responsabilità del rilevamento di eventuali errori di collegamento nel percorso è la sessione BGP sovrapposta.

Nei dispositivi MSEE, i tempi di attesa e di conservazione BGP sono in genere configurati rispettivamente come 60 e 180 secondi. Per questo motivo, quando si verifica un errore di collegamento, possono essere necessari fino a tre minuti per rilevare eventuali errori di collegamento e passare il traffico alla connessione alternativa.

È possibile controllare i timer BGP configurando un livello di conservazione e di conservazione BGP inferiore sul dispositivo di peering perimetrale. Se i timer BGP non sono gli stessi tra i due dispositivi peering, la sessione BGP stabilirà usando il valore di tempo più basso. Il Keep-Alive BGP può essere impostato su un minimo di tre secondi e il tempo di attesa è inferiore a 10 secondi. Tuttavia, l'impostazione di un timer BGP molto aggressivo non è consigliata perché il protocollo è a elevato utilizzo di processi.

In questo scenario, il rilevamento dell'inoltro bidirezionale può essere di aiuto poiché consente di rilevare gli errori di collegamento con un sovraccarico ridotto in un intervallo di frazioni di secondo. 


## <a name="enabling-bfd"></a>Abilitazione del rilevamento dell'inoltro bidirezionale

Il rilevamento dell'inoltro bidirezionale è configurato per impostazione predefinita in tutte le nuove interfacce di peering privato ExpressRoute create sui dispositivi MSEE. Per abilitare BFD, è necessario configurare BFD solo nei dispositivi primari e secondari. La configurazione di BFD è un processo in due passaggi. Il BFD viene configurato sull'interfaccia e quindi collegato alla sessione BGP.

Di seguito è riportata una configurazione di esempio CE/PE (usando Cisco IOS XE). 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>Per abilitare il rilevamento dell'inoltro bidirezionale per un peering privato già esistente è necessario reimpostare il peering. Vedere [Reimpostare i peering ExpressRoute][ResetPeering].
>

## <a name="bfd-timer-negotiation"></a>Negoziazione dei timer di rilevamento dell'inoltro bidirezionale

Tra i due peer di rilevamento dell'inoltro bidirezionale, quello più lento determina la velocità di trasmissione. Gli intervalli di trasmissione/ricezione del rilevamento dell'inoltro bidirezionale su dispositivi MSEE sono impostati su 300 millisecondi. In alcuni scenari, l'intervallo può essere impostato su un valore più elevato di 750 millisecondi. Configurando un valore più elevato, è possibile forzare gli intervalli in modo che siano più lunghi, ma non è possibile renderli più brevi.

>[!NOTE]
>Se sono stati configurati circuiti ExpressRoute con ridondanza geografica o si usa la connettività VPN IPSec da sito a sito come backup. L'abilitazione di BFD consente il failover più rapido dopo un errore di connettività di ExpressRoute. 
>

## <a name="next-steps"></a>Passaggi successivi

Per maggiori informazioni o assistenza, consultare i collegamenti seguenti:

- [Creare e modificare un circuito ExpressRoute][CreateCircuit]
- [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "Il rilevamento dell'inoltro bidirezionale riduce il tempo di individuazione degli errori di collegamento"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md