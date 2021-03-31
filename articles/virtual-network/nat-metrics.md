---
title: Metriche e avvisi per la rete virtuale di Azure NAT
titleSuffix: Azure Virtual Network
description: Informazioni sulle metriche e sugli avvisi di monitoraggio di Azure disponibili per la rete virtuale NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: e3c47a60a6cda074eba7b5c3292577c29f50c2ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87424052"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metriche NAT della rete virtuale di Azure

Le risorse del gateway NAT di rete virtuale di Azure forniscono metriche multidimensionali. È possibile usare queste metriche per osservare l'operazione e per la [risoluzione dei problemi](troubleshoot-nat.md).  Gli avvisi possono essere configurati per problemi critici, ad esempio l'esaurimento SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="NAT di rete virtuale per il flusso in uscita verso Internet">
</p>

*Figura: NAT di rete virtuale per il flusso in uscita verso Internet*

## <a name="metrics"></a>Metriche

Le risorse del gateway NAT forniscono le metriche multidimensionali seguenti in monitoraggio di Azure:

| Metrica | Descrizione | Aggregazione consigliata | Dimensioni |
|---|---|---|---|
| Byte | Byte elaborati in ingresso e in uscita | Sum | Direzione (in; Out), protocollo (6 TCP; 17 UDP) |
| Pacchetti | Pacchetti elaborati in ingresso e in uscita | Sum | Direzione (in; Out), protocollo (6 TCP; 17 UDP) |
| Pacchetti eliminati | Pacchetti eliminati dal gateway NAT | Sum | / |
| Numero di connessioni SNAT | Transizioni di stato per intervallo | Sum | Stato connessione, protocollo (6 TCP; 17 UDP) |
| Numero totale di connessioni SNAT | Connessioni SNAT attive correnti (~ porte SNAT in uso) | Sum | Protocollo (6 TCP; 17 UDP) |


## <a name="alerts"></a>Avvisi

È possibile configurare gli avvisi per le metriche in monitoraggio di Azure per ognuna delle [metriche](#metrics)precedenti.

## <a name="limitations"></a>Limitazioni

Integrità risorse non è supportato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [servizio NAT di rete virtuale](nat-overview.md)
* Informazioni sulla [risorsa gateway NAT](nat-gateway-resource.md)
* Informazioni su [monitoraggio di Azure](../azure-monitor/overview.md)
* Informazioni sulla [risoluzione dei problemi delle risorse gateway NAT](troubleshoot-nat.md).
* [Segnalare le nuove funzionalità richieste per NAT di rete virtuale in UserVoice](https://aka.ms/natuservoice).


