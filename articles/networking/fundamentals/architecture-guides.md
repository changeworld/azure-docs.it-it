---
title: Documentazione sull'architettura di rete di Azure
description: Informazioni sulla documentazione relativa all'architettura di riferimento disponibile per i servizi di rete di Azure.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080395"
---
# <a name="azure-networking-architecture-documentation"></a>Documentazione sull'architettura di rete di Azure

Questo articolo fornisce informazioni sulle guide di architettura che consentono di esplorare i diversi servizi di rete disponibili in Azure per la creazione delle applicazioni.

## <a name="networking-overview"></a>Panoramica delle reti

La tabella seguente include articoli che forniscono una panoramica della rete di un data center virtuale e una topologia hub e spoke in Azure.

|Titolo |Descrizione  |
|---------|---------|
|[Data center virtuali](/azure/architecture/vdc/networking-virtual-datacenter)   | Fornisce una prospettiva di rete di un data center virtuale in Azure.       |
|[Topologia hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Viene fornita una panoramica della topologia di rete hub-spoke in Azure insieme alle informazioni sui limiti della sottoscrizione e su più hub.          |

## <a name="connect-to-azure-resources"></a>Connettersi alle risorse di Azure

La tabella seguente include articoli sui servizi di rete di Azure che forniscono la connettività tra le risorse di Azure, la connettività da una rete locale alle risorse di Azure e la creazione di Branch per la connettività in Azure.

|Titolo |Descrizione  |
|---------|---------|
|[Aggiungere spazi di indirizzi IP alle reti virtuali con peering](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Fornisce script che consentono di aggiungere spazi di indirizzi IP alle reti virtuali con peering.        |
|[Connettere server autonomi tramite la scheda di rete di Azure](/azure/architecture/hybrid/azure-network-adapter)   | Mostra come connettere un server autonomo locale a Microsoft Azure reti virtuali tramite la scheda di rete di Azure distribuita tramite l'interfaccia di amministrazione di Windows.        |
|[Scegliere tra il peering di reti virtuali e i gateway VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Vengono confrontati due modi per connettere le reti virtuali in Azure: peering di rete virtuale e gateway VPN.        |
|[Connettere una rete locale ad Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Vengono confrontate le opzioni per la connessione di una rete locale a una rete virtuale di Azure (VNet). Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.        |
|[Architettura della connettività SD-WAN con la rete WAN virtuale di Azure](../../virtual-wan/sd-wan-connectivity-architecture.md)|Descrive le diverse opzioni di connettività per l'interconnessione di una rete WAN (software defined WAN) privata con la rete WAN virtuale di Azure.|

## <a name="deploy-highly-available-applications"></a>Distribuire applicazioni a disponibilità elevata

La tabella seguente include articoli che descrivono come distribuire le applicazioni per la disponibilità elevata usando una combinazione di servizi di rete di Azure.

|Titolo |Descrizione  |
|---------|---------|
|[Applicazione a più livelli a più aree](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Descrive un'applicazione a più livelli a più aree che usa Gestione traffico per instradare le richieste in ingresso a un'area primaria e se tale area non è più disponibile, gestione traffico esegue il failover nell'area secondaria.      |
| [SaaS multitenant in Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Usa una soluzione multi-tenant che include una combinazione di porta anteriore e gateway applicazione.  La porta anteriore consente di bilanciare il carico del traffico tra le aree e le route del gateway applicazione e di bilanciare il carico del traffico internamente nell'applicazione ai vari servizi che soddisfano le esigenze aziendali del cliente.  |
| [Applicazione Web multilivello compilata per la disponibilità elevata e il ripristino di emergenza ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Distribuisce applicazioni multilivello resilienti compilate per la disponibilità elevata e il ripristino di emergenza. Se l'area primaria non è più disponibile, viene eseguito il failover di gestione traffico nell'area secondaria.  |
|[IaaS: applicazione Web con database relazionale](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Viene descritto come usare le risorse distribuite in più zone per offrire un'architettura a disponibilità elevata per l'hosting di un'applicazione Web IaaS (Infrastructure as a Service) e SQL Server database.     |
|[Condivisione della posizione in tempo reale con i servizi di Azure serverless a costi contenuti](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   USA lo sportello anteriore di Azure per offrire una disponibilità più elevata per le applicazioni rispetto alla distribuzione in una singola area. Se un'interruzione a livello di area interessa l'area primaria, è possibile usare Frontdoor per effettuare il failover all'area secondaria.      |
|[Appliance virtuali di rete a disponibilità elevata](/azure/architecture/reference-architectures/dmz/nva-ha)     | Viene illustrato come distribuire un set di appliance virtuali di rete (appliance virtuali) per la disponibilità elevata in Azure.        |

## <a name="secure-your-network-resources"></a>Proteggere le risorse di rete

La tabella seguente include articoli che descrivono come proteggere le risorse di rete usando i servizi di rete di Azure.

|Titolo |Descrizione  |
|---------|---------|
|[Procedure consigliate per la sicurezza di rete](../../security/fundamentals/network-best-practices.md) |Viene illustrata una raccolta di procedure consigliate di Azure per migliorare la sicurezza della rete.         |
[Guida all'architettura di Firewall di Azure](/azure/architecture/example-scenario/firewalls/) | Offre un approccio strutturato per la progettazione di firewall a disponibilità elevata in Azure usando appliance virtuali di terze parti.        |
|[Implementare una rete ibrida sicura](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Descrive un'architettura che implementa una rete perimetrale, definita anche rete perimetrale, tra la rete locale e una rete virtuale di Azure. Tutto il traffico in ingresso e in uscita passa attraverso il firewall di Azure.        |
|[Proteggere e regolamentare i carichi di lavoro con la segmentazione a livello di rete](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Vengono descritti i tre modelli comuni usati per organizzare i carichi di lavoro in Azure dal punto di vista della rete.   Ognuno di questi modelli fornisce un tipo diverso di isolamento e connettività.      |
|[Firewall e gateway applicazione per le reti virtuali](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Descrive i servizi di sicurezza della rete virtuale di Azure, ad esempio il firewall di Azure e applicazione Azure gateway, quando usare ogni servizio e le opzioni di progettazione della rete che combinano entrambe.      |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md).
