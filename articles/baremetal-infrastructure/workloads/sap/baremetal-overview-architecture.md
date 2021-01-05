---
title: Panoramica dell'anteprima dell'infrastruttura BareMetal in Azure
description: Panoramica su come distribuire l'infrastruttura BareMetal in Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: db974d9260344d1f6050235bb2a9fbaa0420659b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829201"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Che cos'è l'anteprima dell'infrastruttura BareMetal in Azure?

L'infrastruttura BareMetal di Azure offre una soluzione sicura per la migrazione di carichi di lavoro personalizzati dell'organizzazione. Le istanze di BareMetal sono hardware host/server non condiviso assegnato all'utente. Sblocca il porting della soluzione locale con carichi di lavoro specializzati che richiedono hardware, licenze e contratti di supporto certificati. Azure gestisce automaticamente il monitoraggio e la manutenzione dell'infrastruttura, mentre il monitoraggio del sistema operativo guest e il monitoraggio delle applicazioni rientrano nella propria proprietà.

L'infrastruttura BareMetal fornisce un percorso per modernizzare il paesaggio dell'infrastruttura, mantenendo al tempo stesso gli investimenti e l'architettura esistenti. Con l'infrastruttura BareMetal è possibile importare carichi di lavoro specializzati in Azure, consentendo l'accesso e l'integrazione con i servizi di Azure con bassa latenza.

## <a name="sku-availability-in-azure-regions"></a>Disponibilità degli SKU nelle aree di Azure
È disponibile l'infrastruttura BareMetal per carichi di lavoro specializzati e per utilizzo generico, a partire da quattro aree basate su timbri di revisione 4,2 (Rev 4,2):
- Europa occidentale
- Europa settentrionale
- Stati Uniti orientali 2
- Stati Uniti centro-meridionali

>[!NOTE]
>**Rev 4,2** è la versione più recente dell'infrastruttura Baremetal che usa l'architettura Rev 4 esistente.  Rev 4 fornisce una prossimità più vicina agli host della macchina virtuale (VM) di Azure e riduce la latenza tra le macchine virtuali di Azure e le unità di istanza di BareMetal. È possibile accedere alle istanze di BareMetal e gestirle tramite il portale di Azure. 

## <a name="support"></a>Supporto
L'infrastruttura BareMetal è conforme a ISO 27001, ISO 27017, SOC 1 e SOC 2.  USA anche un modello BYOL (Bring your own License): sistema operativo, carico di lavoro specializzato e applicazioni di terze parti.  

Non appena si riceve l'accesso alla radice e il controllo completo, si presuppone la responsabilità di:
- Progettazione e implementazione di soluzioni di backup e ripristino, disponibilità elevata e ripristino di emergenza
- Licenze, sicurezza e supporto per il software del sistema operativo e di terze parti

Microsoft è responsabile di:
- Fornire hardware certificato per carichi di lavoro specializzati 
- Provisioning del sistema operativo

:::image type="content" source="media/baremetal-support-model.png" alt-text="Modello di supporto dell'infrastruttura BareMetal" border="false":::

## <a name="compute"></a>Calcolo
L'infrastruttura BareMetal offre più SKU certificati per carichi di lavoro specializzati. Gli SKU disponibili variano dal sistema a due socket inferiore al sistema a 24 socket. Usare gli SKU certificati specifici del carico di lavoro per il carico di lavoro specializzato.

Il timbro dell'istanza di BareMetal stesso combina i componenti seguenti:

- **Calcolo:** Server basati su una diversa generazione di processori Intel Xeon che forniscono la funzionalità di elaborazione necessaria e sono certificati per il carico di lavoro specializzato.

- **Rete:** Un'infrastruttura di rete ad alta velocità unificata interconnette i componenti di calcolo, archiviazione e LAN.

- **Archiviazione:** Infrastruttura a cui si accede tramite un'infrastruttura di rete unificata.

All'interno dell'infrastruttura multi-tenant del timbro BareMetal, i clienti vengono distribuiti in tenant isolati. Quando si distribuisce un tenant, è necessario assegnare un nome a una sottoscrizione di Azure nella registrazione di Azure. Questa sottoscrizione di Azure è quella a cui vengono fatturate le istanze di BareMetal.

>[!NOTE]
>Un cliente distribuito nell'istanza di BareMetal viene isolato in un tenant. Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e di calcolo assegnate ai diversi tenant non possono vedersi reciprocamente o comunicare tra loro nelle istanze BareMetal.

## <a name="os"></a>OS
Durante il provisioning dell'istanza di BareMetal, è possibile selezionare il sistema operativo che si vuole installare nei computer. 

>[!NOTE]
>Tenere presente che l'infrastruttura BareMetal è un modello BYOL.

Le versioni del sistema operativo Linux disponibili sono:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Archiviazione
Le istanze di BareMetal basate su un tipo di SKU specifico sono disponibili con archiviazione NFS predefinita basata su un tipo di carico di lavoro specifico. Quando si esegue il provisioning di BareMetal, è possibile effettuare il provisioning di archiviazione aggiuntiva in base alla crescita stimata inviando una richiesta Tutto lo spazio di archiviazione è dotato di un disco tutto Flash nella revisione 4,2 con supporto per NFSv3 e NFSv4. Sarà disponibile la versione più recente di NVMe SSD 4,5. Per ulteriori informazioni sul dimensionamento dello spazio di archiviazione, vedere la sezione [tipo di carico di lavoro Baremetal](../../../virtual-machines/workloads/sap/get-started.md) .

>[!NOTE]
>Per impostazione predefinita, lo spazio di archiviazione usato per BareMetal soddisfa i requisiti di sicurezza FIPS 140-2 che offrono la crittografia inattiva. I dati vengono archiviati in modo sicuro sui dischi.

## <a name="networking"></a>Rete
L'architettura dei servizi di rete di Azure è un componente chiave per una corretta distribuzione di carichi di lavoro specializzati nelle istanze di BareMetal. È probabile che non tutti i sistemi IT si trovino già in Azure. Azure ti offre la tecnologia di rete per rendere Azure simile a una data center virtuale alle distribuzioni software locali. La funzionalità di rete di Azure necessaria per le istanze di BareMetal è:

- Le reti virtuali di Azure sono connesse al circuito ExpressRoute che si connette alle risorse di rete locali.
- Un circuito ExpressRoute che si connette da locale ad Azure deve avere una larghezza di banda minima di 1 Gbps o superiore.
- Active Directory e DNS estesi in Azure o completamente in esecuzione in Azure.

L'uso di ExpressRoute consente di estendere la rete locale nel cloud Microsoft tramite una connessione privata con la guida del provider di connettività. È possibile abilitare **ExpressRoute Premium** per estendere la connettività tra i confini geopolitici o usare **ExpressRoute locale** per un trasferimento dei dati conveniente tra la località vicina all'area di Azure desiderata.

Viene eseguito il provisioning delle istanze di BareMetal all'interno dell'intervallo di indirizzi IP del server Azure VNET.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Diagramma dell'infrastruttura di Azure BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

L'architettura illustrata è suddivisa in tre sezioni.
- A **sinistra:** Mostra l'infrastruttura locale del cliente che esegue diverse applicazioni, che si connettono tramite il partner o il router perimetrale locale come Equinix. Per altre informazioni, vedere [provider e località di connettività: Azure ExpressRoute](../../../expressroute/expressroute-locations.md).
- Al **centro:** Mostra il provisioning di [ExpressRoute](../../../expressroute/expressroute-introduction.md) con la sottoscrizione di Azure che offre connettività alla rete perimetrale di Azure.
- A **destra:** Mostra Azure IaaS e, in questo caso, l'uso di macchine virtuali per ospitare le applicazioni, di cui viene eseguito il provisioning nella rete virtuale di Azure.
- In **basso:** Mostra l'uso del gateway ExpressRoute abilitato con [ExpressRoute FastPath](../../../expressroute/about-fastpath.md) per la connettività Baremetal che offre bassa latenza.   
   >[!TIP]
   >Per supportare questa operazione, il gateway ExpressRoute deve essere UltraPerformance.  Per ulteriori informazioni, vedere [informazioni sui gateway di rete virtuale ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nell'apprendere come identificare e interagire con le unità di istanza di BareMetal tramite il portale di Azure.

> [!div class="nextstepaction"]
> [Gestire istanze bare metal tramite il portale di Azure](baremetal-infrastructure-portal.md)