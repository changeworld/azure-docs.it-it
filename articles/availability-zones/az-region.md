---
title: Servizi di Azure che supportano zone di disponibilità
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723040"
---
# <a name="azure-services-that-support-availability-zones"></a>Servizi di Azure che supportano zone di disponibilità

Zone di disponibilità è un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori dei data center. Per l'elenco delle aree esistenti e future che supportano zone di disponibilità, vedere [aree e zone di disponibilità in Azure](az-overview.md).  

Questa sezione elenca i servizi di Azure che supportano zone di disponibilità. 

I servizi disponibili in ogni area, insieme alla roadmap di disponibilità imminente, sono reperibili in [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).

Tutti i servizi di gestione di Azure sono progettati per essere resilienti da errori a livello di area. Nello spettro degli errori, uno o più errori della zona di disponibilità all'interno di un'area hanno un raggio di errore inferiore rispetto a un errore dell'intera area. Azure può eseguire il ripristino da un errore a livello di zona dei servizi di gestione all'interno di un'area. Azure esegue una manutenzione critica di una zona alla volta all'interno di un'area, per evitare errori che influiscano sulle risorse dei clienti distribuite tra zone di disponibilità all'interno di un'area.


![visualizzazione concettuale di un'area di Azure con 3 zone](./media/az-region/azure-region-availability-zones.png)


I servizi di Azure che supportano zone di disponibilità rientrano in tre categorie: i servizi **di zona,** con **ridondanza della zona** e **non a livello** di area. I carichi di lavoro dei clienti possono essere categorizzati per usare uno qualsiasi di questi scenari di architettura per soddisfare le prestazioni e la durabilità delle applicazioni.

- **Servizi di zona** : una risorsa può essere distribuita in una zona di disponibilità specifica e autonoma per ottenere requisiti di latenza o prestazioni più rigorosi.  La resilienza è progettata in modo autonomo replicando le applicazioni e i dati in una o più zone all'interno dell'area.  Le risorse possono essere aggiunte a una zona specifica. Ad esempio, le macchine virtuali, i dischi gestiti o gli indirizzi IP standard possono essere aggiunti a una zona specifica, consentendo una maggiore resilienza in presenza di una o più istanze di risorse distribuite tra le zone.

- **Servizi con ridondanza della zona** : la piattaforma Azure replica la risorsa e i dati tra le zone.  Microsoft gestisce il recapito della disponibilità elevata, poiché Azure replica e distribuisce automaticamente le istanze all'interno dell'area.  ZRS, ad esempio, replica i dati in tre zone, in modo che un errore di zona non influisca sulla disponibilità elevata dei dati. 

- **Servizi non** a livello di area: servizi che non hanno dipendenze da un'area di Azure specifica, rendendoli resilienti a interruzioni a livello di zona, oltre a interruzioni a livello di area.


Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza. Per altre informazioni, vedere [la pagina relativa alla creazione di soluzioni per la disponibilità elevata con zone di disponibilità](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Servizi di Azure che supportano zone di disponibilità

- Le macchine virtuali di generazione meno recenti non sono elencate di seguito. Per altre informazioni, vedere [generazioni precedenti di dimensioni di macchine virtuali](../virtual-machines/sizes-previous-gen.md).

- Alcuni servizi non sono regionali, vedere [aree e zone di disponibilità in Azure](az-overview.md) per altre informazioni. Questi servizi non hanno una dipendenza da un'area di Azure specifica, rendendoli resilienti a interruzioni a livello di zona e interruzioni a livello di area.  L'elenco dei servizi non a livello di area è reperibile nei [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Americhe

| **Prodotti** | **Stati Uniti centrali** | **Stati Uniti orientali** | **Stati Uniti orientali 2** | **Stati Uniti occidentali 2** | **Canada centrale** |
|--|--|--|--|--|--|
| **Calcolo** |  |  |  |  |  |
| [Ambienti del servizio app (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Contenitori** |  |  |  |
| [Servizio Azure Kubernetes](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Registro Container](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Archiviazione** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Archiviazione file Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Archiviazione BLOB](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rete** |  |  |  |  |  |
| [Gateway applicazione V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall di Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Indirizzo IP standard](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Bilanciamento del carico](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rete virtuale](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT di rete virtuale](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rete WAN virtuale](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Database** |  |  |  |  |  |
| [Cache Redis di Azure](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Database di Azure per MySQL-server flessibile](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Server flessibile di Database di Azure per PostgreSQL](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Database SQL di Azure (livello per utilizzo generico)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (anteprima) | : heavy_check_mark: (anteprima) | : heavy_check_mark: (anteprima) | :x: |
| [Database SQL di Azure (livelli Premium & business critical)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analisi** |  |  |  |  |  |
| [Hub eventi](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrazione** |  |  |  |  |  |
| [Griglia di eventi](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Bus di servizio](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestione e governance** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **Sicurezza** |  |  |  |  |  |
| [Servizi di dominio Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Prodotti** | **Francia centrale** | **Europa settentrionale** | **Regno Unito meridionale** | **Europa occidentale** |
|--|--|--|--|--|
| **Calcolo** |  |  |  |  |
| [Ambienti del servizio app (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Servizio Azure Kubernetes](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Archiviazione** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Archiviazione file Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Archiviazione BLOB](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rete** |  |  |  |  |
| [Gateway applicazione V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall di Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Indirizzo IP standard](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Bilanciamento del carico](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rete virtuale](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT di rete virtuale](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rete WAN virtuale](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Database** |  |  |  |  |
| [Cache Redis di Azure](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Database di Azure per MySQL-server flessibile](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Server flessibile di Database di Azure per PostgreSQL](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Database SQL di Azure (livello per utilizzo generico)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (anteprima) | :x: | : heavy_check_mark: (anteprima) |
| [Database SQL di Azure (livelli Premium & business critical)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analisi** |  |  |  |  |
| [Hub eventi](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrazione** |  |  |  |  |
| [Griglia di eventi](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Bus di servizio](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestione e governance** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **Sicurezza** |  |  |  |  |
| [Servizi di dominio Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Asia Pacifico



| **Prodotti** | **Giappone orientale** | **Asia sud-orientale** | **Australia orientale** |
|--|--|--|--|
| **Calcolo** |  |  |  |
| [Ambienti del servizio app (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Servizio Azure Kubernetes](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Archiviazione** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Archiviazione file Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Archiviazione BLOB](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rete** |  |  |  |
| [Gateway applicazione V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall di Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Indirizzo IP standard](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Bilanciamento del carico](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rete virtuale](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT di rete virtuale](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rete WAN virtuale](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Database** |  |  |  |
| [Cache Redis di Azure](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Database di Azure per MySQL-server flessibile](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Server flessibile di Database di Azure per PostgreSQL](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Database SQL di Azure (livello per utilizzo generico)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (anteprima) | : heavy_check_mark: (anteprima) |
| [Database SQL di Azure (livelli Premium & business critical)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analisi** |  |  |  |
| [Hub eventi](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrazione** |  |  |  |
| [Griglia di eventi](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Bus di servizio](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestione e governance** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **Sicurezza** |  |  |  |
| [Servizi di dominio Azure Active Directory](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>zone di disponibilità imminente 

Azure offre supporto zone di disponibilità nelle aree seguenti:
- US Gov Virginia
- Sudafrica settentrionale
- Stati Uniti centro-meridionali
- Germania centro-occidentale

L'elenco delle aree esistenti e future che supportano zone di disponibilità è disponibile [qui](https://azure.microsoft.com/global-infrastructure/geographies/).    

Per ulteriori informazioni sul supporto zone di disponibilità in queste aree, contattare il rappresentante Microsoft Sales o Customer.


## <a name="pricing-for-vms-in-availability-zones"></a>Prezzi per le macchine virtuali in zone di disponibilità

Per le macchine virtuali distribuite in una zona di disponibilità non sono previsti costi aggiuntivi. Per ulteriori informazioni, vedere la [pagina](https://azure.microsoft.com/pricing/details/bandwidth/)relativa ai prezzi per la larghezza di banda.


## <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità

- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md)
- [Livello utilizzo generico database SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Aggiungere un'area con ridondanza della zona per Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introduzione cache di Azure per Redis zone di disponibilità](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Creare un'istanza di Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Creare un cluster Azure Kubernetes Service (AKS) che usa zone di disponibilità](../aks/availability-zones.md)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aree e zone di disponibilità in Azure](az-overview.md)
