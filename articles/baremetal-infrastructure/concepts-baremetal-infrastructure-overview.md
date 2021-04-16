---
title: Che cos'è l'infrastruttura BareMetal in Azure?
description: Offre una panoramica dell'infrastruttura BareMetal in Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c0fd250a63ce93d3f8b62dfe76fe753c928801ce
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536886"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Che cos'è l'infrastruttura BareMetal in Azure?

Microsoft Azure un'infrastruttura cloud con un'ampia gamma di servizi cloud integrati per soddisfare le esigenze aziendali. In alcuni casi, tuttavia, potrebbe essere necessario eseguire servizi in bare metal server senza un livello di virtualizzazione. Potrebbe essere necessario l'accesso alla radice e il controllo del sistema operativo. Per soddisfare queste esigenze, Azure offre l'infrastruttura BareMetal per diverse applicazioni di alto valore e cruciali.

L'infrastruttura BareMetal è costituito da istanze BareMetal dedicate (istanze di calcolo), archiviazione ad alte prestazioni e appropriata per le applicazioni (NFS, ISCSI e Fiber Channel), nonché da un set di reti LAN virtuali (VLAN) specifiche della funzione in un ambiente isolato. L'archiviazione può essere condivisa tra istanze BareMetal per abilitare funzionalità come i cluster con scalabilità orizzontale o per la creazione di coppie di disponibilità elevata con STONITH.
 
Questo ambiente include anche VLAN speciali a cui è possibile accedere se si eseguono macchine virtuali (VM) in una o più reti virtuali di Azure nella sottoscrizione di Azure. L'intero ambiente è rappresentato come gruppo di risorse nella sottoscrizione di Azure.

L'infrastruttura BareMetal è disponibile in oltre 30 SKU da server a 2 socket a 24 socket e memoria compresa tra 1,5 TB e 24 TB. Con la memoria octane è disponibile anche un ampio set di SKU. Azure offre la gamma più ampia di istanze bare metal in un cloud iperscalabilità.

## <a name="why-baremetal-infrastructure"></a>Perché l'infrastruttura BareMetal?  

Alcuni carichi di lavoro centrali nell'organizzazione sono costituito da tecnologie che non sono progettate per l'esecuzione in una tipica impostazione cloud virtualizzata. Richiedono un'architettura speciale, hardware certificato o dimensioni estremamente grandi. Anche se queste tecnologie hanno le funzionalità di protezione dei dati e continuità aziendale più sofisticate, tali funzionalità non sono create per il cloud virtualizzato. Sono più sensibili alle latenze, ai vicini rumorosi e richiedono un maggiore controllo sulla gestione delle modifiche e sulle attività di manutenzione.

L'infrastruttura BareMetal viene compilata, certificata e testata per un set selezionato di tali applicazioni. Azure è stato il primo a offrire tali soluzioni e da allora è stato leader con il portfolio più ampio e i sistemi più sofisticati.

L'infrastruttura BareMetal offre i vantaggi seguenti: 

- Istanze dedicate
- Hardware certificato per carichi di lavoro specializzati
    - SAP (vedere [la nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (vedere [l'ID documento Oracle #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bare metal (nessuna virtualizzazione del calcolo)
- Bassa latenza tra le macchine virtuali dell'applicazione ospitata in Azure e le istanze BareMetal (0,35 ms)
- Tutte le unità Flash SSD e NVMe
    - Fino a 1 PB/tenant 
    - Operazioni di I/O al secondo fino a 1,2 milioni/tenant 
    - Larghezza di banda di rete di 40/100 GB
    - Accessibile tramite NFS, ISCSI e FC
- Alimentazione ridondante, alimentatori, schede di interfaccia di rete, tor, porte, reti WAN, archiviazione e gestione
- Riserva a caldo per la sostituzione in caso di errore (senza la necessità di riconfigurazione)
- Finestre di manutenzione coordinate dal cliente
- Snapshot con informazioni su applicazioni, archiviazione, mirroring e clonazione


## <a name="baremetal-benefits"></a>Vantaggi di BareMetal  

L'infrastruttura BareMetal è destinata a carichi di lavoro cruciali che richiedono la certificazione per eseguire le applicazioni aziendali. Le istanze di BareMetal sono dedicate solo all'utente e si di dispone dell'accesso completo (accesso radice) al sistema operativo. È possibile gestire l'installazione del sistema operativo e dell'applicazione in base alle proprie esigenze. Per motivi di sicurezza, viene effettuato il provisioning delle istanze all'interno della rete virtuale di Azure senza connettività Internet. Solo i servizi in esecuzione nelle macchine virtuali (VM) e altri servizi di Azure nella stessa rete di livello 2 possono comunicare con le istanze di BareMetal.  

L'infrastruttura BareMetal offre questi vantaggi: 

- Hardware certificato per carichi di lavoro specializzati
- SAP (fare riferimento alla [nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533))
- Oracle (fare riferimento [all'ID documento Oracle #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Istanza BareMetal non hypervised, proprietà di un tenant singolo
- Bassa latenza tra macchine virtuali dell'applicazione ospitata in Azure e istanze BareMetal (0,35 ms)
- Tutto il supporto di Flash SSD e NVMe
- Database fino a 1 PB/tenant 
- Operazioni di I/O al secondo fino a 1,2 milioni/tenant 
- Larghezza di banda di rete di 50 GB 

## <a name="sku-availability-in-azure-regions"></a>Disponibilità dello SKU nelle aree di Azure

BareMetal Infrastructure offre più SKU certificati per carichi di lavoro specializzati. Usare gli SKU specifici del carico di lavoro per soddisfare le proprie esigenze.

- Istanze di grandi dimensioni: da sistemi a due socket a quattro socket.  
- Istanze molto grandi: da sistemi a quattro socket a 20 socket. 

L'infrastruttura BareMetal per carichi di lavoro specializzati è disponibile nelle aree di Azure seguenti:
- Europa occidentale
- Europa settentrionale
- Supporto delle *zone" in Germania centro-occidentale
- Supporto delle *zone negli Stati Uniti orientali 2
- Supporto delle *zone negli Stati Uniti orientali
- Supporto delle *zone negli Stati Uniti occidentali
- Supporto delle *zone negli Stati Uniti occidentali 2
- Stati Uniti centro-meridionali

>[!NOTE]
>**Il supporto delle** zone si riferisce alle zone di disponibilità all'interno di un'area in cui le istanze BareMetal possono essere distribuite tra zone per garantire resilienza e disponibilità elevate. Questa funzionalità abilita il supporto per il ridimensionamento attivo-attivo multis sito.

## <a name="managing-baremetal-instances-in-azure"></a>Gestione di istanze BareMetal in Azure 

A seconda delle esigenze, le topologie dell'applicazione dell'infrastruttura BareMetal possono essere complesse. È possibile distribuire più istanze, in una o più posizioni, con archiviazione condivisa o dedicata e connessioni LAN e WAN specializzate. Pertanto, per l'infrastruttura BareMetal, Azure offre un'acquisizione consultiva di queste informazioni da parte di un CSA/GBB nel campo in un portale di provisioning. 

Al momento del provisioning dell'infrastruttura BareMetal, il sistema operativo, le reti, i volumi di archiviazione, i posizionamenti in zone e aree e le connessioni WAN tra le località sono già preconfigurati. Si è impostati per registrare le licenze del sistema operativo (BYOL), configurare il sistema operativo e installare il livello dell'applicazione.

Sarà possibile visualizzare tutte le risorse BareMetal e il relativo stato e attributi nella portale di Azure. Da qui è anche possibile gestire le istanze e aprire richieste di servizio e ticket di supporto. 

## <a name="operational-model"></a>Modello operativo
BareMetal Infrastructure è conforme a ISO 27001, ISO 27017, SOC 1 e SOC 2. Usa anche un modello BYOL (Bring Your Own License): sistema operativo, carico di lavoro specializzato e applicazioni di terze parti.  

Non appena si riceve l'accesso radice e il controllo completo, si assume la responsabilità di:
- Progettazione e implementazione di soluzioni di backup e ripristino, disponibilità elevata e ripristino di emergenza.
- Licenze, sicurezza e supporto per il sistema operativo e il software di terze parti.

Microsoft è responsabile di:
- Fornire l'hardware per carichi di lavoro specializzati. 
- Provisioning del sistema operativo.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagramma del modello di supporto dell'infrastruttura BareMetal." border="false":::

## <a name="baremetal-instance-stamp"></a>Indicatore di istanza BareMetal

Il timbro dell'istanza BareMetal combina i componenti seguenti:

- **Calcolo:** Server basati sulla generazione di processori Intel Xeon che forniscono la funzionalità di calcolo necessaria e sono certificati per il carico di lavoro specializzato.

- **Rete:** Un'infrastruttura di rete ad alta velocità unificata collega i componenti di elaborazione, archiviazione e LAN.

- **Archiviazione:** Infrastruttura a cui si accede tramite un'infrastruttura di rete unificata.

All'interno dell'infrastruttura multi-tenant del stamp BareMetal, i clienti vengono distribuiti in tenant isolati. Quando si distribuisce un tenant, si denota una sottoscrizione di Azure all'interno della registrazione di Azure. Questa sottoscrizione di Azure è quella fatturata per le istanze di BareMetal.

>[!NOTE]
>Un cliente che distribuisce un'istanza di BareMetal è isolato in un tenant. Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate a tenant diversi non possono vedersi o comunicare tra loro nelle istanze BareMetal.

## <a name="operating-system"></a>Sistema operativo
Durante il provisioning dell'istanza di BareMetal, è possibile selezionare il sistema operativo che si vuole installare nei computer. 

>[!NOTE]
>Tenere presente che l'infrastruttura BareMetal è un modello BYOL.

Le versioni del sistema operativo Linux disponibili sono:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Archiviazione
L'infrastruttura BareMetal offre archiviazione NFS con ridondanza elevata e archiviazione Fiber Channel. L'infrastruttura offre una profonda integrazione per carichi di lavoro aziendali come SAP, SQL e altro ancora. Offre anche funzionalità di protezione dei dati coerenti con l'applicazione e di gestione dei dati. Gli strumenti di gestione self-service offrono funzionalità di snapshot, clonazione e replica granulari efficienti a livello di spazio, oltre a un unico riquadro di monitoraggio. L'infrastruttura abilita zero funzionalità RPO e RTO per le esigenze di disponibilità dei dati e continuità aziendale. 

L'infrastruttura di archiviazione offre:
- Uplink fino a 4 x 100 GB.
- Fino a 32 GB di uplink fiber channel.
- Tutte le unità flash SSD e NVMe.
- Latenza estremamente bassa e velocità effettiva elevata.
- Ridimensiona fino a 4 PB di spazio di archiviazione non elaborato. 
- Fino a 11 milioni di operazioni di I/O al secondo.

Sono supportati i protocolli di accesso ai dati seguenti: 
- iSCSI 
- NFS (v3 o v4) 
- Fiber Channel 
- NVMe su FC  

## <a name="networking"></a>Rete
L'architettura dei servizi di rete di Azure è un componente chiave per una corretta distribuzione di carichi di lavoro specializzati nelle istanze BareMetal. È probabile che non tutti i sistemi IT si trovino già in Azure. Azure offre la tecnologia di rete per rendere Azure simile a un data center virtuale per le distribuzioni software locali. La funzionalità di rete di Azure necessaria per le istanze BareMetal include:

- Reti virtuali di Azure connesse Azure ExpressRoute circuito di rete che si connette alle risorse di rete locali.
- Il circuito ExpressRoute che si connette in locale ad Azure deve avere una larghezza di banda minima di 1 Gbps o superiore.
- Active Directory e DNS estesi in Azure o completamente in esecuzione in Azure.

ExpressRoute consente di estendere la rete locale nel cloud Microsoft tramite una connessione privata con l'aiuto di un provider di connettività. È possibile usare **ExpressRoute Local** per il trasferimento dei dati conveniente tra la località locale e l'area di Azure desiderata. Per estendere la connettività attraverso i limiti geopolitici, è possibile abilitare **ExpressRoute Premium.** 

Il provisioning delle istanze BareMetal viene eseguito all'interno dell'intervallo di indirizzi IP del server di rete virtuale di Azure.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagramma dell'architettura del diagramma dell'infrastruttura BareMetal di Azure." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

L'architettura illustrata è suddivisa in tre sezioni.
- **A sinistra:** Mostra l'infrastruttura locale del cliente che esegue applicazioni diverse, connettendosi tramite il partner o il router perimetrale locale come Equinix. Per altre informazioni, vedere [Provider di connettività e percorsi: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Centro:** Mostra [ExpressRoute di cui è stato](../expressroute/expressroute-introduction.md) effettuato il provisioning usando la sottoscrizione di Azure che offre connettività alla rete perimetrale di Azure.
- **A destra:** Mostra IaaS di Azure e, in questo caso, l'uso di macchine virtuali per ospitare le applicazioni di cui viene effettuato il provisioning all'interno della rete virtuale di Azure.
- **In basso:** Mostra l'uso del gateway ExpressRoute abilitato [con ExpressRoute FastPath](../expressroute/about-fastpath.md) per la connettività BareMetal che offre bassa latenza.   
   >[!TIP]
   >A tale scopo, il gateway ExpressRoute deve essere UltraPerformance. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale ExpressRoute.](../expressroute/expressroute-about-virtual-network-gateways.md)

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nell'apprendere come identificare e interagire con le istanze BareMetal tramite l'portale di Azure.

> [!div class="nextstepaction"]
> [Gestire le istanze BareMetal tramite il portale di Azure](connect-baremetal-infrastructure.md)
