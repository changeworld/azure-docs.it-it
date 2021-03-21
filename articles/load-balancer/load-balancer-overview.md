---
title: Informazioni su Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Panoramica di funzionalità, architettura e implementazione di Azure Load Balancer. Informazioni sul funzionamento di Load Balancer e su come usarlo nel cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181211"
---
# <a name="what-is-azure-load-balancer"></a>Informazioni su Azure Load Balancer

Il *bilanciamento del carico* si riferisce alla distribuzione uniforme del carico o del traffico di rete in ingresso tra un gruppo di risorse o server back-end. 

Azure Load Balancer opera al livello 4 del modello OSI (Open Systems Interconnection). Rappresenta l'unico punto di contatto per i client. Il servizio di bilanciamento del carico distribuisce i flussi in ingresso che arrivano al front-end del servizio di bilanciamento del carico alle istanze del pool back-end. Questi flussi sono conformi alle regole di bilanciamento del carico e ai probe di integrità configurati. Le istanze del pool back-end possono essere macchine virtuali o istanze di Azure in un set di scalabilità di macchine virtuali.

Un **[servizio Load Balancer pubblico](./components.md#frontend-ip-configurations)** può offrire connettività in uscita per le macchine virtuali all'interno della rete virtuale. Queste connessioni vengono stabilite convertendo i relativi indirizzi IP privati in indirizzi IP pubblici. I servizi Load Balancer pubblici vengono usati per bilanciare il carico del traffico Internet inviato alle VM.

Un **[servizio Load Balancer interno (o privato)](./components.md#frontend-ip-configurations)** viene usato se gli indirizzi IP privati sono necessari solo sul front-end. I servizi Load Balancer interni vengono usati per bilanciare il carico all'interno di una rete virtuale. È possibile accedere al front-end di Load Balancer da una rete locale in uno scenario ibrido.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Figura: bilanciamento del carico di applicazioni multilivello tramite il servizio Load Balancer sia pubblico che interno*

Per altre informazioni sui singoli componenti del servizio Load Balancer, vedere [Componenti di Azure Load Balancer](./components.md).

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari. 
> * Se si sta cercando di eseguire il routing globale basato su DNS e **non** si hanno i requisiti per la terminazione del protocollo TLS (Transport Layer Security) ("offload SSL"), per la richiesta per HTTP/HTTPS o per l'elaborazione a livello di applicazione, vedere [Gestione traffico](../traffic-manager/traffic-manager-overview.md). 
> * Se si vuole bilanciare il carico tra i server in un'area a livello di applicazione, esaminare il [gateway applicazione](../application-gateway/overview.md).
> * Se è necessario ottimizzare il routing globale del traffico Web e ottimizzare le prestazioni e l'affidabilità dell'utente finale di livello superiore tramite il failover globale rapido, vedere la [porta anteriore](../frontdoor/front-door-overview.md).
> 
> Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.
> Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Perché usare Azure Load Balancer?
Azure Load Balancer consente di ridimensionare le applicazioni e di creare servizi a disponibilità elevata. Load Balancer supporta scenari in ingresso e in uscita. Load Balancer offre bassa latenza e velocità effettiva elevata, oltre a una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

Gli scenari principali che è possibile eseguire con Azure Load Balancer Standard includono:

- Bilanciare il carico del traffico **[interno](./quickstart-load-balancer-standard-internal-portal.md)** ed **[esterno](./quickstart-load-balancer-standard-public-portal.md)** inviato alle macchine virtuali di Azure.

- Aumentare la disponibilità distribuendo le risorse **[all'interno](./tutorial-load-balancer-standard-public-zonal-portal.md)** e **[tra](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** zone.

- Configurare la **[connettività in uscita](./load-balancer-outbound-connections.md)** per le macchine virtuali di Azure.

- Usare **[probe di integrità](./load-balancer-custom-probe-overview.md)** per monitorare le risorse con carico bilanciato.

- Adottare il **[port forwarding](./tutorial-load-balancer-port-forwarding-portal.md)** per accedere alle macchine virtuali in una rete virtuale tramite indirizzo IP pubblico e porta.

- Abilitare il supporto per il **[bilanciamento del carico](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** di **[IPv6](../virtual-network/ipv6-overview.md)** .

- Load Balancer standard fornisce metriche multidimensionali attraverso [monitoraggio di Azure](../azure-monitor/overview.md).  Queste metriche possono essere filtrate, raggruppate e suddivise in una determinata dimensione.  Forniscono informazioni dettagliate sulle prestazioni presenti e passate e sullo stato di integrità del servizio. [Insights for Azure Load Balancer](./load-balancer-insights.md) offre un dashboard preconfigurato con visualizzazioni utili per queste metriche.  Integrità risorse di Azure è supportato. Per altri dettagli, vedere diagnostica del servizio di **[bilanciamento del carico standard](load-balancer-standard-diagnostics.md)** .

- Bilanciare il carico dei servizi in **[più porte, più indirizzi IP o entrambi](./load-balancer-multivip-overview.md)** .

- Spostare le risorse di Load Balancer **[interne](./move-across-regions-internal-load-balancer-portal.md)** ed **[esterne](./move-across-regions-external-load-balancer-portal.md)** tra le aree di Azure.

- Bilanciare il carico del flusso TCP e UDP in tutte le porte contemporaneamente usando **[porte a disponibilità elevata](./load-balancer-ha-ports-overview.md)** .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Protezione per impostazione predefinita

* Load Balancer standard è basato sul modello di sicurezza di rete zero trust.

* Load Balancer Standard è sicuro per impostazione predefinita ed è incluso nella rete virtuale. La rete virtuale è una rete privata e isolata.  

* I bilanciamento del carico standard e gli indirizzi IP pubblici standard vengono chiusi alle connessioni in ingresso, a meno che non vengano aperti da gruppi di sicurezza di rete. I gruppi di sicurezza di rete vengono usati per permettere esplicitamente il traffico consentito.  Se non si dispone di un NSG in una subnet o una scheda di interfaccia di rete della risorsa della macchina virtuale, il traffico non è autorizzato a raggiungere questa risorsa. Per informazioni su gruppi e su come applicarli allo scenario, vedere [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).

* Per impostazione predefinita, il servizio di bilanciamento del carico di base è aperto a Internet. 

* Il servizio di bilanciamento del carico non archivia i dati dei clienti.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

Per informazioni sui prezzi di Load Balancer standard, vedere prezzi del servizio di [bilanciamento del carico](https://azure.microsoft.com/pricing/details/load-balancer/).
Il servizio di bilanciamento del carico Basic è disponibile gratuitamente.
Vedere [contratto di servizio per Load Balancer](https://aka.ms/lbsla). Il servizio di bilanciamento del carico Basic non prevede contratti di servizio.

## <a name="whats-new"></a>Novità

Sottoscrivere il feed RSS e visualizzare gli aggiornamenti più recenti di Azure Load Balancer nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare un servizio di bilanciamento del carico, vedere [Creare un servizio di bilanciamento del carico Standard pubblico](quickstart-load-balancer-standard-public-portal.md).

Per ulteriori informazioni sulle limitazioni e i componenti di Azure Load Balancer, vedere [Azure Load Balancer componenti](./components.md) e [concetti di Azure Load Balancer](./concepts.md)