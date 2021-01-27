---
title: Servizi di Azure che supportano le zone di disponibilità
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 01/26/2021
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 5883dc8b34cd1aa00ba1c5de25a09a017a130c0a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896885"
---
# <a name="azure-services-that-support-availability-zones"></a>Servizi di Azure che supportano le zone di disponibilità

Microsoft Azure infrastruttura globale è progettata e costruita a ogni livello per offrire ai clienti i massimi livelli di ridondanza e resilienza. L'infrastruttura di Azure è costituita da aree geografiche, aree e zone di disponibilità, che limitano il raggio di esplosione di un errore e quindi limitano il potenziale impatto sulle applicazioni e i dati dei clienti. Il costrutto di zone di disponibilità di Azure è stato sviluppato per offrire una soluzione software e di rete per la protezione da errori dei data center e per garantire una maggiore disponibilità elevata ai clienti.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center con alimentazione, raffreddamento e rete indipendenti. La separazione fisica di zone di disponibilità all'interno di un'area limita l'effetto sulle applicazioni e sui dati degli errori di zona, ad esempio l'alluvione su larga scala, le tempeste principali e i Superstorm e altri eventi che potrebbero compromettere l'accesso al sito, il passaggio sicuro, il tempo di attività delle utilità estese e la disponibilità delle risorse. Zone di disponibilità e i Data Center associati sono progettati in modo tale che, se una zona viene compromessa, i servizi, la capacità e la disponibilità sono supportati dagli altri zone di disponibilità nell'area.

Tutti i servizi di gestione di Azure sono progettati per essere resilienti da errori a livello di area. Nello spettro degli errori, uno o più errori della zona di disponibilità all'interno di un'area hanno un raggio di errore inferiore rispetto a un errore dell'intera area. Azure può eseguire il ripristino da un errore a livello di zona dei servizi di gestione all'interno di un'area. Azure esegue una manutenzione critica di una zona alla volta all'interno di un'area, per evitare errori che influiscano sulle risorse dei clienti distribuite tra zone di disponibilità all'interno di un'area.


![visualizzazione concettuale di un'area di Azure con 3 zone](./media/az-region/azure-region-availability-zones.png)


I servizi di Azure che supportano zone di disponibilità rientrano in tre categorie: i servizi **di zona,** con **ridondanza della zona** e **non a livello** di area. I carichi di lavoro dei clienti possono essere categorizzati per usare uno qualsiasi di questi scenari di architettura per soddisfare le prestazioni e la durabilità delle applicazioni.

- **Servizi di zona** : una risorsa può essere distribuita in una zona di disponibilità specifica e autonoma per ottenere requisiti di latenza o prestazioni più rigorosi.  La resilienza è progettata in modo autonomo replicando le applicazioni e i dati in una o più zone all'interno dell'area.  Le risorse possono essere aggiunte a una zona specifica. Ad esempio, le macchine virtuali, i dischi gestiti o gli indirizzi IP standard possono essere aggiunti a una zona specifica, consentendo una maggiore resilienza in presenza di una o più istanze di risorse distribuite tra le zone.

- **Servizi con ridondanza della zona** : la piattaforma Azure replica la risorsa e i dati tra le zone.  Microsoft gestisce il recapito della disponibilità elevata, poiché Azure replica e distribuisce automaticamente le istanze all'interno dell'area.  ZRS, ad esempio, replica i dati in tre zone, in modo che un errore di zona non influisca sulla disponibilità elevata dei dati. 

- **Servizi non** a livello di area: i servizi sono sempre disponibili da aree geografiche di Azure e sono resilienti a interruzioni a livello di zona, oltre a interruzioni a livello di area. 


Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza. Per altre informazioni, vedere [la pagina relativa alla creazione di soluzioni per la disponibilità elevata con zone di disponibilità](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Servizi di Azure che supportano zone di disponibilità

 - Le macchine virtuali di generazione precedenti non sono elencate. Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../virtual-machines/sizes-previous-gen.md).
 - Come indicato nelle [aree e zone di disponibilità in Azure](az-overview.md), alcuni servizi non sono regionali. Questi servizi non hanno dipendenze da un'area di Azure specifica, perché sono resilienti a interruzioni a livello di zona, oltre a interruzioni a livello di area.  L'elenco dei servizi non a livello di area è reperibile nei [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Aree di Azure con zone di disponibilità


| Americhe           | Europa         | Germania              | Africa              | Asia Pacifico   |
|--------------------|----------------|----------------------|---------------------|----------------|
|                    |                |                      |                     |                |
| Canada centrale     | Francia centrale | Germania centro-occidentale | Sudafrica settentrionale * | Giappone orientale     |
| Stati Uniti centrali         | Europa settentrionale   |                      |                     | Asia sud-orientale |
| Stati Uniti orientali            | Regno Unito meridionale       |                      |                     | Australia orientale |
| Stati Uniti orientali 2          | Europa occidentale    |                      |                     |                |
| Stati Uniti centro-meridionali |                |                      |                     |                |
| US Gov Virginia * |                |                      |                     |                |
| Stati Uniti occidentali 2        |                |                      |                     |                |


\* Per ulteriori informazioni sul supporto di zone di disponibilità e dei servizi disponibili in queste aree, contattare il rappresentante Microsoft Sales o Customer rappresentante. Per le aree future che supporteranno zone di disponibilità, vedere [geografie di Azure](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Servizi di Azure che supportano zone di disponibilità

- Le macchine virtuali di generazione meno recenti non sono elencate di seguito. Per altre informazioni, vedere [generazioni precedenti di dimensioni di macchine virtuali](../virtual-machines/sizes-previous-gen.md).

- Alcuni servizi non sono regionali, vedere [aree e zone di disponibilità in Azure](az-overview.md) per altre informazioni. Questi servizi non hanno una dipendenza da un'area di Azure specifica, rendendoli resilienti a interruzioni a livello di zona e interruzioni a livello di area.  L'elenco dei servizi non a livello di area è reperibile nei [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Servizi con resilienza della zona 

: globe_with_meridians: servizi non a livello di area: i servizi sono sempre disponibili da aree geografiche di Azure e sono resilienti a interruzioni a livello di zona, oltre a interruzioni a livello di area.

: large_blue_diamond: resilienza per le interruzioni a livello di zona 

**Servizi di base**

|     Prodotti                                                    | Resilienza             |
|-----------------------------------------------------------------|:----------------------------:|
|     Account di archiviazione                                           | : large_blue_diamond:  |
|     Gateway applicazione (v2)                                  | : large_blue_diamond:  |
|     Backup di Azure                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage generazione 2                             | : large_blue_diamond:  |
|     Azure Express Route                                       | : large_blue_diamond:  |
|     IP pubblico di Azure                                           | : large_blue_diamond:  |
|     Database SQL di Azure (livello per utilizzo generico)                 | : large_blue_diamond:  |
|     Database SQL di Azure (livello Premium & business critical)     | : large_blue_diamond:  |
|     Archiviazione su disco                                                | : large_blue_diamond:  |
|     Hub eventi                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Bus di servizio                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Archiviazione: livelli di archiviazione BLOB ad accesso frequente/sporadico                      | : large_blue_diamond:  |
|     Archiviazione: Managed Disks                                    | : large_blue_diamond:  |
|     Set di scalabilità di macchine virtuali                               | : large_blue_diamond:  |
|     Macchine virtuali                                          | : large_blue_diamond:  |
|     Macchine virtuali: Av2-Series                              | : large_blue_diamond:  |
|     Macchine virtuali: Bs-Series                               | : large_blue_diamond:  |
|     Macchine virtuali: DSv2-Series                             | : large_blue_diamond:  |
|     Macchine virtuali: DSv3-Series                             | : large_blue_diamond:  |
|     Macchine virtuali: Dv2-Series                              | : large_blue_diamond:  |
|     Macchine virtuali: Dv3-Series                              | : large_blue_diamond:  |
|     Macchine virtuali: ESv3-Series                             | : large_blue_diamond:  |
|     Macchine virtuali: Ev3-Series                              | : large_blue_diamond:  |
|     Rete virtuale                                           | : large_blue_diamond:  |
|     Gateway VPN                                                 | : large_blue_diamond:  |


**Servizi mainstream**

| Prodotti                                        | Resilienza |
|-------------------------------------------------|:------------:|
| Ambienti del servizio app                        |      : large_blue_diamond:  |
| Servizi di dominio Azure Active Directory          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Cache Redis di Azure                           |      : large_blue_diamond:  |
| Servizi cognitivi di Azure: Analisi del testo        |      : large_blue_diamond:  |
| Esplora dati di Azure                             |      : large_blue_diamond:  |
| Database di Azure per MySQL-server flessibile      |      : large_blue_diamond:  |
| Database di Azure per PostgreSQL-server flessibile |      : large_blue_diamond:  |
| Protezione DDoS di Azure                           |      : large_blue_diamond:  |
| Firewall di Azure                                  |      : large_blue_diamond:  |
| Gestione firewall di Azure                          |      : large_blue_diamond:  |
| Servizio Azure Kubernetes                  |      : large_blue_diamond:  |
| Collegamento privato di Azure                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Registro contenitori                              |      : large_blue_diamond:  |
| Griglia di eventi                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Archiviazione BLOB Premium                            |      : large_blue_diamond:  |
| Macchine virtuali: Ddsv4-Series                  |      : large_blue_diamond:  |
| Macchine virtuali: Ddv4-Series                   |      : large_blue_diamond:  |
| Macchine virtuali: Dsv4-Series                   |      : large_blue_diamond:  |
| Macchine virtuali: Dv4-Series                    |      : large_blue_diamond:  |
| Macchine virtuali: Edsv4-Series                  |      : large_blue_diamond:  |
| Macchine virtuali: Edv4-Series                   |      : large_blue_diamond:  |
| Macchine virtuali: Esv4-Series                   |      : large_blue_diamond:  |
| Macchine virtuali: Ev4-Series                    |      : large_blue_diamond:  |
| Macchine virtuali: Fsv2-Series                   |      : large_blue_diamond:  |
| Macchine virtuali: serie M                      |      : large_blue_diamond:  |
| Rete WAN virtuale                                     |      : large_blue_diamond:  |


**Non a livello di area**

|     Prodotti                                  |     Resilienza    |
|-----------------------------------------------|:-------------------:|
|     DNS di Azure                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Servizi Azure Bot                        |     : globe_with_meridians:             |
|     Azure Defender per l'it                  |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Faro di Azure                        |     : globe_with_meridians:             |
|     Applicazioni gestite di Azure              |     : globe_with_meridians:             |
|     Mappe di Azure                                |     : globe_with_meridians:             |
|     Criteri di Azure                              |     : globe_with_meridians:             |
|     Grafico delle risorse di Azure                    |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack Edge                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Customer Lockbox per Microsoft Azure    |     : globe_with_meridians:             |
|     Servizio di peering Microsoft Azure         |     : globe_with_meridians:             |
|     portale di Microsoft Azure                  |     : globe_with_meridians:             |
|     Centro sicurezza                         |     : globe_with_meridians:             |
|     Gestione traffico                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Prezzi per le macchine virtuali in zone di disponibilità

Per le macchine virtuali distribuite in una zona di disponibilità non sono previsti costi aggiuntivi. Per ulteriori informazioni, vedere la [pagina](https://azure.microsoft.com/pricing/details/bandwidth/)relativa ai prezzi per la larghezza di banda.


## <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità

- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md)
- [Livello utilizzo generico database SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Aggiungere un'area con ridondanza della zona per Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introduzione cache di Azure per Redis zone di disponibilità](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Creare un'istanza di Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Creare un cluster Azure Kubernetes Service (AKS) che usa zone di disponibilità](../aks/availability-zones.md)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aree e zone di disponibilità in Azure](az-overview.md)
