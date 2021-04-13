---
title: Servizi di Azure che supportano le zone di disponibilità
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: fd4443e4ef6bcecacc03ce190bbf438288c9ec60
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364057"
---
# <a name="azure-services-that-support-availability-zones"></a>Servizi di Azure che supportano le zone di disponibilità

Microsoft Azure'infrastruttura globale è progettata e costruita a ogni livello per offrire ai clienti i livelli più elevati di ridondanza e resilienza. L'infrastruttura di Azure è costituita da aree geografiche, aree e zone di disponibilità, che limitano il raggio di esplosione di un errore e quindi limitano il potenziale impatto sulle applicazioni e sui dati dei clienti. Il zone di disponibilità di Azure è stato sviluppato per fornire una soluzione software e di rete per la protezione da errori dei data center e per offrire maggiore disponibilità elevata ai clienti.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituito da uno o più data center con alimentazione, raffreddamento e rete indipendenti. La separazione fisica dei zone di disponibilità all'interno di un'area limita l'impatto sulle applicazioni e sui dati dagli errori di zona, ad esempio alluvioni su larga scala, tempeste e superstorm e altri eventi che potrebbero compromettere l'accesso al sito, il passaggio sicuro, il tempo di attività esteso delle utilità e la disponibilità delle risorse. zone di disponibilità e i data center associati sono progettati in modo che, se una zona viene compromessa, i servizi, la capacità e la disponibilità sono supportati dagli altri zone di disponibilità nell'area.

Tutti i servizi di gestione di Azure sono progettati per essere resilienti dagli errori a livello di area. Nella gamma di errori, uno o più errori della zona di disponibilità all'interno di un'area hanno un raggio di errore inferiore rispetto a un errore dell'intera area. Azure può eseguire il ripristino da un errore a livello di zona dei servizi di gestione all'interno di un'area. Azure esegue la manutenzione critica una zona alla volta all'interno di un'area, per evitare eventuali errori che influiscono sulle risorse dei clienti zone di disponibilità all'interno di un'area.


![Visualizzazione concettuale di un'area di Azure con 3 zone](./media/az-region/azure-region-availability-zones.png)


I servizi di Azure che zone di disponibilità rientrano in tre categorie: **servizi di zona,** con ridondanza della zona e **non regionali.** I carichi di lavoro dei clienti possono essere classificati in modo da usare uno di questi scenari di architettura per soddisfare le prestazioni e la durabilità delle applicazioni.

- **Servizi di zona:** una risorsa può essere distribuita in una zona di disponibilità specifica e selezionata autonomamente per ottenere requisiti di latenza o prestazioni più stringenti.  La resilienza è auto-progettata replicando applicazioni e dati in una o più zone all'interno dell'area.  Le risorse possono essere aggiunte a una zona specifica. Ad esempio, le macchine virtuali, i dischi gestiti o gli indirizzi IP standard possono essere aggiunti a una zona specifica, che consente una maggiore resilienza grazie alla presenza di una o più istanze di risorse distribuite tra le zone.

- **Servizi con ridondanza della** zona: la piattaforma Azure replica le risorse e i dati tra le zone.  Microsoft gestisce la distribuzione della disponibilità elevata perché Azure replica e distribuisce automaticamente le istanze all'interno dell'area.  L'ZRS, ad esempio, replica i dati in tre zone in modo che un errore di zona non influisca sulla disponibilità a disponibilità maggiore dei dati. 

- **Servizi non a livello** di area: i servizi sono sempre disponibili dalle aree geografiche di Azure e sono resilienti alle interruzioni a livello di zona e alle interruzioni a livello di area. 


Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza. Per altre informazioni, vedere [Creazione di soluzioni per la disponibilità elevata con zone di disponibilità](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Servizi di Azure che supportano zone di disponibilità

 - Le macchine virtuali di generazione precedente non sono elencate. Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../virtual-machines/sizes-previous-gen.md).
 - Come accennato in [Aree e zone di disponibilità in Azure,](az-overview.md)alcuni servizi non sono regionali. Questi servizi non hanno dipendenze da un'area di Azure specifica, in quanto sono resilienti alle interruzioni a livello di zona e alle interruzioni a livello di area.  L'elenco dei servizi non regionali è disponibile in [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Aree di Azure con zone di disponibilità
 

| Americhe           | Europa               | Africa              | Asia Pacifico   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brasile meridionale       | Francia centrale       | Sudafrica settentrionale* | Australia orientale |
| Canada centrale     | Germania centro-occidentale |                     | India centrale* |
| Stati Uniti centrali         | Europa settentrionale         |                     | Giappone orientale     |
| Stati Uniti orientali            | Regno Unito meridionale             |                     | Corea centrale* |
| Stati Uniti orientali 2          | Europa occidentale          |                     | Asia sud-orientale |
| Stati Uniti centro-meridionali |                      |                     |                |
| US Gov Virginia    |                      |                     |                |
| Stati Uniti occidentali 2        |                      |                     |                |
| Stati Uniti occidentali 3*       |                      |                     |                |

\* Per altre informazioni sul supporto zone di disponibilità e sui servizi disponibili in queste aree, contattare il rappresentante microsoft per le vendite o il cliente. Per le aree future che supporteranno zone di disponibilità, vedere [Aree geografiche di Azure.](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)


## <a name="azure-services-supporting-availability-zones"></a>Servizi di Azure che supportano zone di disponibilità

- Le macchine virtuali di generazione precedente non sono elencate di seguito. Per altre informazioni, vedere [le generazioni precedenti di dimensioni delle macchine virtuali.](../virtual-machines/sizes-previous-gen.md)

- Per altre informazioni, vedere Aree e zone di disponibilità [in Azure.](az-overview.md) Questi servizi non hanno dipendenza da un'area di Azure specifica, rendendoli resilienti alle interruzioni a livello di zona e alle interruzioni a livello di area.  L'elenco dei servizi non regionali è disponibile in [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Servizi resilienti della zona 

:globe_with_meridians: servizi non regionali: i servizi sono sempre disponibili dalle aree geografiche di Azure e sono resilienti alle interruzioni a livello di zona e alle interruzioni a livello di area.

:large_blue_diamond: resiliente alle interruzioni a livello di zona 

**Servizi di base**

|     Prodotti                                                    | Resilienza             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Gateway applicazione (V2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Backup di Azure](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [IP pubblico di Azure](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     database SQL di Azure ([per utilizzo generico )](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                 | :large_blue_diamond:  |
|     database SQL di Azure([Livello & business critical Premium](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | :large_blue_diamond:  |
|     [Archiviazione su disco](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Insieme di credenziali di chiave](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [Account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     Archiviazione:   [livelli di archiviazione BLOB ad accesso hot/cool](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     Archiviazione:   [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Macchine virtuali: [serie Av2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Macchine virtuali: [serie Bs](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [serie DSv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Macchine virtuali: [serie DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Macchine virtuali: [serie Dv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Macchine virtuali: [serie Dv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Macchine virtuali: [serie ESv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Macchine virtuali: [serie Ev3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Macchine virtuali: [serie F](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Macchine virtuali: [serie FS](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [Raccolta immagini condivise](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**Servizi mainstream**


|     Prodotti                                                    | Resilienza             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Ambienti del servizio app](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Servizi di dominio Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Cache Redis di Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Servizi cognitivi di Azure: [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Database di Azure per MySQL - [Server flessibile](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Database di Azure per PostgreSQL - [Server flessibile](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Protezione DDoS di Azure](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Firewall di Azure](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Gestione firewall di Azure](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Collegamento privato di Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [Macchina virtuale](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Web application firewall di Azure](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [Registro Container](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Griglia di eventi](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Network Watcher: [Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Archiviazione BLOB Premium](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     Archiviazione: [File Premium di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     Macchine virtuali: [host dedicato di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Macchine virtuali: [serie Ddsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Macchine virtuali: [serie Ddv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [serie Dsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [serie Dv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Macchine virtuali: [Serie Edsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Macchine virtuali: [serie Edv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [serie Esv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [serie Ev4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Macchine virtuali: [Serie Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Macchine virtuali: [serie M](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [Rete WAN virtuale](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Rete WAN virtuale: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Rete WAN virtuale: [gateway VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     Rete WAN virtuale: [gateway VPN da](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) sito a sito                       | :large_blue_diamond:  |


**Servizi specializzati**

|     Prodotti                                                    | Resilienza             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Servizi cognitivi: Rilevamento anomalie                        | :large_blue_diamond:  |
|     Servizi cognitivi: riconoscimento modulo                         | :large_blue_diamond:  |
|     Archiviazione: disco Ultra                                         | :large_blue_diamond:  |


**Non a livello di regione**

|     Prodotti                                                    | Resilienza             |
|-----------------------------------------------------------------|:----------------------------:|
|     DNS di Azure                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Servizi Azure Bot                                          | :globe_with_meridians: |
|     Frontdoor di Azure                                            | :globe_with_meridians: |
|     Azure Defender per IoT                                    | :globe_with_meridians: |
|     Frontdoor di Azure                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Applicazioni gestite di Azure                                | :globe_with_meridians: |
|     Mappe di Azure                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Criteri di Azure                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Rete CDN                                    | :globe_with_meridians: |
|     Gestione costi                                             | :globe_with_meridians: |
|     Customer Lockbox per Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Servizio di peering di Microsoft Azure                           | :globe_with_meridians: |
|     portale di Microsoft Azure                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Centro sicurezza                                           | :globe_with_meridians: |
|     Gestione traffico                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Prezzi per le macchine virtuali in zone di disponibilità

zone di disponibilità di Azure disponibili con la sottoscrizione di Azure. Per altre informazioni, vedere la [pagina dei prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità

- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md)
- [Livello utilizzo generico del database SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Aggiungere un'area con ridondanza della zona per Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Attività iniziali cache di Azure per Redis zone di disponibilità](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Creare un'istanza di Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Creare un cluster servizio Azure Kubernetes (AKS) che usa zone di disponibilità](../aks/availability-zones.md)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aree e zone di disponibilità in Azure](az-overview.md)
