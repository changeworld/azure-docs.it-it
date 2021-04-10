---
title: Panoramica dell'infrastruttura BareMetal in Azure
description: Viene fornita una panoramica dell'infrastruttura BareMetal in Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: bccb171ce364a5129489c437f2f18156cc563a1b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579048"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Che cos'è l'infrastruttura BareMetal in Azure?

Microsoft Azure offre un'infrastruttura cloud con un'ampia gamma di servizi cloud integrati per soddisfare le esigenze aziendali. In alcuni casi, tuttavia, potrebbe essere necessario eseguire servizi su Server Bare Metal senza un livello di virtualizzazione. Potrebbe essere necessario l'accesso alla radice e il controllo del sistema operativo. Per soddisfare queste esigenze, Azure offre un'infrastruttura BareMetal per diverse applicazioni di importanza critica e di alto valore.

L'infrastruttura BareMetal è costituita da istanze di BareMetal dedicate (istanze di calcolo), da risorse di archiviazione appropriate per le prestazioni e dalle applicazioni (NFS, dNFS, ISCSI e Fiber Channel), nonché da un set di LAN virtuali (VLAN) specifiche per le funzioni in un ambiente isolato. L'archiviazione può essere condivisa tra le istanze di BareMetal per abilitare funzionalità come cluster con scalabilità orizzontale o per la creazione di coppie a disponibilità elevata con STONITH.
 
Questo ambiente dispone anche di VLAN speciali a cui è possibile accedere se vengono eseguite macchine virtuali (VM) in una o più reti virtuali di Azure (reti virtuali) nella sottoscrizione di Azure. L'intero ambiente viene rappresentato come gruppo di risorse nella sottoscrizione di Azure.

L'infrastruttura BareMetal è disponibile in oltre 30 SKU da 2 socket a server a 24 socket e memoria da 1,5 TB fino a 24 TB. Un ampio set di SKU è disponibile anche con la memoria di ottanta. Azure offre la più ampia gamma di istanze bare metal in un cloud con iperscalabilità.

## <a name="why-baremetal-infrastructure"></a>Vantaggi dell'infrastruttura BareMetal  

Alcuni carichi di lavoro centrali nell'azienda sono costituiti da tecnologie che non sono progettate per essere eseguite in una tipica impostazione cloud virtualizzata. Richiedono un'architettura speciale, hardware certificato o dimensioni eccezionalmente elevate. Sebbene tali tecnologie dispongano delle funzionalità di protezione dei dati e di continuità aziendale più sofisticate, queste funzionalità non sono compilate per il cloud virtualizzato. Sono più sensibili alle latenze, ai vicini rumorosi e richiedono un maggiore controllo sulle attività di gestione e manutenzione delle modifiche.

L'infrastruttura BareMetal è stata compilata, certificata e testata per un set selezionato di tali applicazioni. Azure è stato il primo a offrire soluzioni di questo tipo ed è stato portato dal principale insieme al più ampio portfolio e ai sistemi più sofisticati.

L'infrastruttura BareMetal offre i vantaggi seguenti: 

- Istanze dedicate
- Hardware certificato per carichi di lavoro specializzati
    - SAP (vedere la [Nota sap #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (fare riferimento all' [ID documento oracle #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bare Metal (nessuna virtualizzazione di calcolo)
- Bassa latenza tra le macchine virtuali dell'applicazione ospitata in Azure e le istanze BareMetal (0,35 ms)
- Tutte le unità SSD Flash e NVMe
    - Fino a 1 PB/tenant 
    - IOPS fino a 1,2 milioni/tenant 
    - larghezza di banda di rete da 40/100 GB
    - Accessibile tramite NFS, dNFS, ISCSI e FC
- Alimentazione ridondante, alimentatori, schede di rete, tori, porte, WAN, archiviazione e gestione
- Riserve a caldo per la sostituzione in caso di errore (senza la necessità di riconfigurare)
- Finestre di manutenzione coordinate del cliente
- Snapshot, archiviazione, mirroring e clonazione compatibili con l'applicazione


## <a name="sku-availability-in-azure-regions"></a>Disponibilità degli SKU nelle aree di Azure

L'infrastruttura BareMetal offre più SKU certificati per carichi di lavoro specializzati. Usare gli SKU specifici del carico di lavoro per soddisfare le proprie esigenze.

- Istanze large, che vanno da due socket a sistemi a quattro socket.  
- Istanze di dimensioni molto grandi, da quattro socket a sistemi a venti socket. 

L'infrastruttura BareMetal per carichi di lavoro specializzati è disponibile nelle aree di Azure seguenti:
- Europa occidentale
- Europa settentrionale
- Supporto per Germania centro-occidentale * zone
- Supporto per le zone Stati Uniti orientali 2 *
- Supporto per aree Stati Uniti orientali *
- Supporto per zone Stati Uniti occidentali *
- Supporto per le zone Stati Uniti occidentali 2 *
- Stati Uniti centro-meridionali

>[!NOTE]
>Il **supporto delle zone** si riferisce alle zone di disponibilità all'interno di un'area in cui le istanze di Baremetal possono essere distribuite tra zone per garantire resilienza e disponibilità Questa funzionalità consente il supporto per il ridimensionamento attivo-attivo multisito.

## <a name="managing-baremetal-instances-in-azure"></a>Gestione di istanze di BareMetal in Azure 

A seconda delle esigenze, le topologie di applicazioni dell'infrastruttura BareMetal possono essere complesse. È possibile distribuire più istanze di in una o più posizioni, con archiviazione condivisa o dedicata e connessioni LAN e WAN specializzate. Per l'infrastruttura di BareMetal, Azure offre quindi un'acquisizione consultiva delle informazioni da parte di un CSA/GBB nel campo di un portale di provisioning. 

Nel momento in cui viene eseguito il provisioning dell'infrastruttura di BareMetal, il sistema operativo, le reti, i volumi di archiviazione, le posizioni in zone e aree e le connessioni WAN tra le località sono già preconfigurate. Si è impostati per registrare le licenze del sistema operativo (BYOL), configurare il sistema operativo e installare il livello dell'applicazione.

Nel portale di Azure sarà possibile visualizzare tutte le risorse dell'infrastruttura BareMetal e il relativo stato e gli attributi. È anche possibile usare le istanze e aprire richieste di assistenza e ticket di supporto da questa posizione. 

## <a name="operational-model"></a>Modello operativo
L'infrastruttura BareMetal è conforme a ISO 27001, ISO 27017, SOC 1 e SOC 2. USA anche un modello BYOL (Bring your own License): sistema operativo, carico di lavoro specializzato e applicazioni di terze parti.  

Non appena si riceve l'accesso alla radice e il controllo completo, si presuppone la responsabilità di:
- Progettazione e implementazione di soluzioni di backup e ripristino, disponibilità elevata e ripristino di emergenza.
- Licenze, sicurezza e supporto per il sistema operativo e il software di terze parti.

Microsoft è responsabile di:
- Fornire l'hardware per carichi di lavoro specializzati. 
- Provisioning del sistema operativo.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagramma del modello di supporto dell'infrastruttura BareMetal." border="false":::

## <a name="baremetal-instance-stamp"></a>Indicatore dell'istanza di BareMetal

Il timbro dell'istanza di BareMetal stesso combina i componenti seguenti:

- **Calcolo:** Server basati sulla generazione di processori Intel Xeon che forniscono la funzionalità di elaborazione necessaria e sono certificati per il carico di lavoro specifico.

- **Rete:** Un'infrastruttura di rete ad alta velocità unificata interconnette i componenti di calcolo, archiviazione e LAN.

- **Archiviazione:** Infrastruttura a cui si accede tramite un'infrastruttura di rete unificata.

All'interno dell'infrastruttura multi-tenant del timbro BareMetal, i clienti vengono distribuiti in tenant isolati. Quando si distribuisce un tenant, è necessario assegnare un nome a una sottoscrizione di Azure nella registrazione di Azure. Questa sottoscrizione di Azure è quella fatturata per le istanze di BareMetal.

>[!NOTE]
>Un cliente che distribuisce un'istanza di BareMetal è isolato in un tenant. Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate a tenant diversi non possono vedersi reciprocamente o comunicare tra loro nelle istanze BareMetal.

## <a name="operating-system"></a>Sistema operativo
Durante il provisioning dell'istanza di BareMetal, è possibile selezionare il sistema operativo che si vuole installare nei computer. 

>[!NOTE]
>Tenere presente che l'infrastruttura BareMetal è un modello BYOL.

Le versioni del sistema operativo Linux disponibili sono:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Archiviazione
L'infrastruttura BareMetal fornisce archiviazione NFS con ridondanza elevata e archiviazione Fiber Channel. L'infrastruttura offre un'integrazione completa per i carichi di lavoro aziendali come SAP, SQL e altro ancora. Fornisce inoltre funzionalità di protezione dei dati e di gestione dei dati coerenti con l'applicazione. Gli strumenti di gestione Self-Service offrono funzionalità di snapshot, clonazione e granularità dei servizi in modo efficiente, oltre a un unico riquadro di monitoraggio a cristalli. L'infrastruttura consente zero funzionalità RPO e RTO per la disponibilità dei dati e le esigenze di continuità aziendale. 

L'infrastruttura di archiviazione offre:
- Fino a 4 x 100-GB di uplink.
- Fino a 32 GB di uplink del canale fiber.
- Tutte le unità SSD Flash e NVMe.
- Latenza ultra-bassa e velocità effettiva elevata.
- Ridimensiona fino a 4 PB di archiviazione non elaborata. 
- Fino a 11 milioni IOPS.

Sono supportati i protocolli di accesso ai dati seguenti: 
- iSCSI 
- NFS (v3 o v4) 
- Fibre Channel 
- NVMe su FC  

## <a name="networking"></a>Rete
L'architettura dei servizi di rete di Azure è un componente chiave per una corretta distribuzione di carichi di lavoro specializzati nelle istanze di BareMetal. È probabile che non tutti i sistemi IT si trovino già in Azure. Azure ti offre la tecnologia di rete per rendere Azure simile a una data center virtuale alle distribuzioni software locali. La funzionalità di rete di Azure necessaria per le istanze di BareMetal include:

- Reti virtuali di Azure connesse al circuito ExpressRoute di Azure che si connette alle risorse di rete locali.
- Il circuito ExpressRoute che si connette in locale ad Azure deve avere una larghezza di banda minima di 1 Gbps o superiore.
- Active Directory e DNS estesi in Azure o completamente in esecuzione in Azure.

ExpressRoute consente di estendere la rete locale nel cloud Microsoft tramite una connessione privata con la guida del provider di connettività. È possibile usare **ExpressRoute local** per il trasferimento dei dati conveniente tra la posizione locale e l'area di Azure desiderata. Per estendere la connettività tra i confini geopolitici, è possibile abilitare **ExpressRoute Premium**. 

Viene eseguito il provisioning delle istanze di BareMetal all'interno dell'intervallo di indirizzi IP del server Azure VNet.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagramma dell'architettura del diagramma dell'infrastruttura BareMetal di Azure." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

L'architettura illustrata è suddivisa in tre sezioni.
- A **sinistra:** Mostra l'infrastruttura locale del cliente che esegue diverse applicazioni, che si connettono tramite il partner o il router perimetrale locale come Equinix. Per altre informazioni, vedere [provider e località di connettività: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- Al **centro:** Mostra il provisioning di [ExpressRoute](../expressroute/expressroute-introduction.md) con la sottoscrizione di Azure che offre connettività alla rete perimetrale di Azure.
- A **destra:** Mostra Azure IaaS e, in questo caso, l'uso di macchine virtuali per ospitare le applicazioni, di cui viene eseguito il provisioning nella rete virtuale di Azure.
- In **basso:** Mostra l'uso del gateway ExpressRoute abilitato con [ExpressRoute FastPath](../expressroute/about-fastpath.md) per la connettività Baremetal che offre bassa latenza.   
   >[!TIP]
   >Per supportare questa operazione, il gateway ExpressRoute deve essere UltraPerformance. Per ulteriori informazioni, vedere [informazioni sui gateway di rete virtuale ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nell'apprendere come identificare e interagire con le istanze di BareMetal tramite il portale di Azure.

> [!div class="nextstepaction"]
> [Gestire le istanze di BareMetal tramite il portale di Azure](connect-baremetal-infrastructure.md)
