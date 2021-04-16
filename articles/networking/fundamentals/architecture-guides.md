---
title: Rete di Azure dell'architettura
description: Informazioni sulla documentazione sull'architettura di riferimento disponibile per i servizi di rete di Azure.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484796"
---
# <a name="azure-networking-architecture-documentation"></a>Rete di Azure dell'architettura

Questo articolo fornisce informazioni sulle guide all'architettura che consentono di esplorare i diversi servizi di rete disponibili in Azure per la compilazione delle applicazioni.

## <a name="networking-overview"></a>Panoramica delle reti

La tabella seguente include articoli che forniscono una panoramica della rete di un data center virtuale e di una topologia hub e spoke in Azure.

|Titolo |Descrizione  |
|---------|---------|
|[Data center virtuali](/azure/architecture/vdc/networking-virtual-datacenter)   | Offre una prospettiva di rete di un data center virtuale in Azure.       |
|[Topologia hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Offre una panoramica della topologia di rete hub e spoke in Azure insieme alle informazioni sui limiti della sottoscrizione e su più hub.          |

## <a name="connect-to-azure-resources"></a>Connettersi alle risorse di Azure

La tabella seguente include articoli sui servizi Rete di Azure che forniscono connettività tra le risorse di Azure, connettività da una rete locale alle risorse di Azure e connettività da ramo a ramo in Azure.

|Titolo |Descrizione  |
|---------|---------|
|[Aggiungere spazi di indirizzi IP alle reti virtuali con peer](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Fornisce script che consentono di aggiungere spazi di indirizzi IP a reti virtuali con peer.        |
|[Connettere server autonomi tramite la scheda di rete di Azure](/azure/architecture/hybrid/azure-network-adapter)   | Viene illustrato come connettere un server autonomo locale Microsoft Azure reti virtuali usando la scheda di rete di Azure distribuita tramite Windows Admin Center.        |
|[Scegliere tra il peering di reti virtuali e i gateway VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Confronta due modi per connettere le reti virtuali in Azure: peering di rete virtuale e gateway VPN.        |
|[Connettere una rete locale ad Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Confronta le opzioni per la connessione di una rete locale a una rete virtuale di Azure. Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.        |
|[Architettura della connettività SD-WAN con la rete WAN virtuale di Azure](../../virtual-wan/sd-wan-connectivity-architecture.md)|Vengono descritte le diverse opzioni di connettività per l'interconnessione di una rete WAN definita dal software (SD-WAN) privata con rete WAN virtuale di Azure.|

## <a name="deploy-highly-available-applications"></a>Distribuire applicazioni a disponibilità elevata

La tabella seguente include articoli che descrivono come distribuire le applicazioni per la disponibilità elevata usando una combinazione di Rete di Azure servizi.

|Titolo |Descrizione  |
|---------|---------|
|[Applicazione a più aree a più livelli](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Descrive un'applicazione a più livelli in più aree che usa Gestione traffico per instradare le richieste in ingresso a un'area primaria e, se tale area non è più disponibile, Viene eseguito il fall over di Gestione traffico all'area secondaria.      |
| [SaaS multitenant in Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Usa una soluzione multi-tenant che include una combinazione di Front door e gateway applicazione.  Front door consente di bilanciare il carico del traffico tra aree e route del gateway applicazione e di bilanciare il carico del traffico internamente nell'applicazione ai vari servizi che soddisfano le esigenze aziendali dei client.  |
| [Applicazione Web multilivello creata per la disponibilità elevata e il ripristino di emergenza ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Distribuisce applicazioni multilivello resilienti create per la disponibilità elevata e il ripristino di emergenza. Se l'area primaria non è più disponibile, Gestione traffico viene eseguito il over nell'area secondaria.  |
|[IaaS: Applicazione Web con database relazionale](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Viene descritto come usare le risorse distribuite tra più zone per fornire un'architettura a disponibilità elevata per l'hosting di un'applicazione Web IaaS (Infrastructure as a Service) e di un database SQL Server servizio.     |
|[Condivisione della posizione in tempo reale con i servizi di Azure serverless a costi contenuti](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Usa Frontdoor di Azure per offrire una disponibilità più elevata per le applicazioni rispetto alla distribuzione in una singola area. Se un'interruzione a livello di area interessa l'area primaria, è possibile usare Frontdoor per effettuare il failover all'area secondaria.      |
|[Appliance virtuali di rete a disponibilità elevata](/azure/architecture/reference-architectures/dmz/nva-ha)     | Illustra come distribuire un set di appliance virtuali di rete per la disponibilità elevata in Azure.        |
|[Bilanciamento del carico in più aree con Gestione traffico e il gateway applicazione](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Descrive come distribuire applicazioni multilivello resilienti in più aree di Azure per ottenere disponibilità e un'infrastruttura di ripristino di emergenza affidabile.        |

## <a name="secure-your-network-resources"></a>Proteggere le risorse di rete

La tabella seguente include articoli che descrivono come proteggere le risorse di rete usando Rete di Azure servizi.

|Titolo |Descrizione  |
|---------|---------|
|[Procedure consigliate per la sicurezza di rete](../../security/fundamentals/network-best-practices.md) |Illustra una raccolta di procedure consigliate di Azure per migliorare la sicurezza di rete.         |
[Guida all'architettura di Firewall di Azure](/azure/architecture/example-scenario/firewalls/) | Offre un approccio strutturato per la progettazione di firewall a disponibilità elevata in Azure tramite appliance virtuali di terze parti.        |
|[Implementare una rete ibrida sicura](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Descrive un'architettura che implementa una rete perimetrale, detta anche rete perimetrale, tra la rete locale e una rete virtuale di Azure. Tutto il traffico in ingresso e in uscita passa attraverso Firewall di Azure.        |
|[Proteggere e regolamentare i carichi di lavoro con la segmentazione a livello di rete](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Descrive i tre modelli comuni usati per organizzare i carichi di lavoro in Azure dal punto di vista della rete.   Ognuno di questi modelli fornisce un tipo diverso di isolamento e connettività.      |
|[Firewall e gateway applicazione per le reti virtuali](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Descrive i servizi di sicurezza della rete virtuale di Azure come Firewall di Azure e gateway applicazione di Azure, quando usare ogni servizio e le opzioni di progettazione di rete che combinano entrambi.      |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [Rete virtuale di Azure.](../../virtual-network/virtual-networks-overview.md)
