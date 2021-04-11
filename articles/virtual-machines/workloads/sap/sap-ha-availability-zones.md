---
title: Configurazioni del carico di lavoro SAP con le zone di disponibilità di Azure | Microsoft Docs
description: Architettura e scenari di disponibilità elevata per SAP NetWeaver con le zone di disponibilità di Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671637"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurazioni del carico di lavoro SAP con le zone di disponibilità di Azure
Inoltre, per la distribuzione dei diversi livelli di architettura SAP nei set di disponibilità di Azure, la [zone di disponibilità di Azure](../../../availability-zones/az-overview.md) introdotta più di recente può essere usata anche per le distribuzioni del carico di lavoro SAP. Una zona di disponibilità di Azure è definita come: "percorsi fisici univoci all'interno di un'area. Ogni zona è costituita da uno o più data center dotati di alimentazione, raffreddamento e rete indipendenti. Zone di disponibilità di Azure non sono disponibili in tutte le aree geografiche. Per le aree di Azure che forniscono zone di disponibilità, controllare la [mappa dell'area di Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Questa mappa mostrerà le aree che forniscono o sono annunciate per fornire zone di disponibilità. 

A partire dalla tipica architettura SAP NetWeaver o S/4HANA, è necessario proteggere tre diversi livelli:

- Livello dell'applicazione SAP, che può essere da una a poche dozzine di macchine virtuali. Si vuole ridurre al minimo le probabilità che le macchine virtuali vengano distribuite nello stesso server host. Si vuole anche che tali macchine virtuali si trovino in una prossimità accettabile al livello DBMS per la latenza di rete in una finestra accettabile
- Livello di SAP ASC/SCS che rappresenta un singolo punto di errore nell'architettura SAP NetWeaver e S/4HANA. In genere si esaminano due macchine virtuali che si desidera includere in un Framework di failover. Pertanto, queste macchine virtuali devono essere allocate in domini di errore e di aggiornamento dell'infrastruttura diversi
- Livello DBMS di SAP, che rappresenta anche un singolo punto di errore. Nei casi usuali, è costituito da due macchine virtuali coperte da un Framework di failover. Pertanto, queste macchine virtuali devono essere allocate in domini di errore e di aggiornamento dell'infrastruttura diversi. Le eccezioni sono SAP HANA distribuzioni con scalabilità orizzontale in cui è possibile usare più di due macchine virtuali

Le principali differenze tra la distribuzione delle macchine virtuali critiche tramite i set di disponibilità o zone di disponibilità sono:

- La distribuzione con un set di disponibilità allinea le VM all'interno del set in una singola zona o Data Center (ciò che si applica per l'area specifica). Di conseguenza, la distribuzione tramite il set di disponibilità non è protetta da problemi di alimentazione, raffreddamento o rete che interessano i dataceter della zona nel suo complesso. Sul lato più, le macchine virtuali sono allineate con domini di aggiornamento e di errore all'interno di tale area o Data Center. In particolare per il livello di SAP ASC o DBMS in cui si proteggono due VM per set di disponibilità, l'allineamento con domini di errore e di aggiornamento impedisce che entrambe le macchine virtuali stiano per finire sullo stesso hardware host 
- Distribuendo le macchine virtuali tramite un zone di disponibilità di Azure e scegliendo zone diverse (al massimo tre possibili finora), le macchine virtuali verranno distribuite nelle diverse posizioni fisiche e con l'aggiunta della protezione aggiuntiva da problemi di alimentazione, raffreddamento o rete che interessano i dataceter della zona nel suo complesso. Tuttavia, quando si distribuiscono più macchine virtuali della stessa famiglia di VM nella stessa zona di disponibilità, non esiste alcuna protezione da tali VM che terminano nello stesso host. Di conseguenza, la distribuzione tramite zone di disponibilità è ideale per il livello di SAP ASC e DBMS, in cui in genere si esaminano due VM ciascuna. Per il livello dell'applicazione SAP, che può essere notevolmente più di due macchine virtuali, potrebbe essere necessario eseguire il fallback a un modello di distribuzione diverso (vedere più avanti).

La motivazione di una distribuzione tra zone di disponibilità di Azure dovrebbe essere che l'utente, oltre a coprire gli errori di una singola VM critica, o la possibilità di ridurre i tempi di inattività per l'applicazione di patch al software all'interno di un critico, vuole proteggersi da problemi di infrastruttura più ampi che potrebbero influire sulla disponibilità di uno o più data center di Azure. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerazioni sulla distribuzione in più zone di disponibilità


Quando si usano le zone di disponibilità, tenere presente quanto segue:

- Non esistono garanzie relative alle distanze tra diverse zone di disponibilità all'interno di un'area di Azure.
- Le zone di disponibilità non sono una soluzione di ripristino di emergenza ideale. In caso di calamità naturali gravi che causano danni molto estesi in alcune aree del mondo, inclusi danni notevoli alla rete elettrica, la distanza tra diverse zone potrebbe non essere sufficientemente estesa da costituire una soluzione di ripristino di emergenza adeguata.
- La latenza di rete tra le zone di disponibilità non è la stessa in tutte le aree di Azure. In alcuni casi, è possibile distribuire ed eseguire il livello dell'applicazione SAP su zone diverse, perché la latenza di rete da una zona alla macchina virtuale DBMS attiva è accettabile. In alcune aree di Azure, tuttavia, la latenza tra la macchina virtuale DBMS attiva e l'istanza dell'applicazione SAP, distribuite in zone diverse, può non essere sufficiente per i processi aziendali SAP. In questi casi, l'architettura di distribuzione deve essere diversa, con un'architettura attiva/attiva per l'applicazione o un'architettura attiva/passiva in cui la latenza di rete tra zone è troppo elevata.
- Nel decidere dove usare le zone di disponibilità, tenere conto della latenza di rete tra le zone. La latenza di rete svolge un ruolo importante in due aree:
    - Latenza tra le due istanze di DBMS per le quali è necessario configurare la replica sincrona. Maggiore sarà la latenza di rete, maggiore sarà l'impatto sulla scalabilità del carico di lavoro.
    - La differenza nella latenza di rete tra una macchina virtuale che esegue un'istanza di dialogo SAP nella stessa zona dell'istanza del DBMS attiva e una macchina virtuale simile in un'altra zona. Maggiore sarà questa differenza, più alto sarà l'impatto sul tempo di esecuzione dei processi aziendali e dei processi batch, a seconda che siano eseguiti nella stessa zona del sistema DBMS o in una zona diversa.

Quando si distribuiscono macchine virtuali di Azure in più zone di disponibilità e si implementano soluzioni di failover nella stessa area di Azure, si applicano alcune restrizioni:

- È necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) quando si distribuisce nelle zone di disponibilità di Azure. 
- Il mapping delle enumerazioni di zona con le zone fisiche è fisso per ogni sottoscrizione di Azure. Se si usano sottoscrizioni diverse per distribuire i sistemi SAP, è necessario definire le zone ideali per ogni sottoscrizione.
- Non è possibile distribuire i set di disponibilità di Azure all'interno di una zona di disponibilità di Azure, a meno che non si usi il [gruppo di posizionamento](../../co-location.md) Il modo in cui è possibile distribuire il livello DBMS di SAP e i servizi centrali tra le zone e allo stesso tempo distribuire il livello dell'applicazione SAP usando i set di disponibilità e ottenere ancora prossimità delle VM è documentato nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md). Se non si usano i gruppi di posizionamento di prossimità di Azure, è necessario scegliere uno o l'altro come Framework di distribuzione per le macchine virtuali.
- Non è possibile usare un'istanza di [Azure Load Balancer Basic](../../../load-balancer/load-balancer-overview.md) per creare soluzioni cluster di failover basate su Windows Server Failover Clustering o Linux Pacemaker. È invece necessario usare lo SKU di [Azure Load Balancer standard](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Combinazione ideale delle zone di disponibilità
Se si vuole distribuire un sistema SAP NetWeaver o S/4HANA tra le zone, è possibile distribuire due architetture principali:

- Attivo/attivo: la coppia di macchine virtuali che eseguono ASC/SCS e la coppia di macchine virtuali che eseguono il livello DBMS vengono distribuite in due zone. Il numero di macchine virtuali che eseguono il livello applicazione SAP viene distribuito in un numero pari tra le stesse due zone. Se viene eseguito il failover di una macchina virtuale DBMS o ASC/SCS, è possibile che venga eseguito il rollback di alcune transazioni aperte e attive. Ma gli utenti rimangono connessi. Non importa in quale zona sono in esecuzione le istanze della macchina virtuale DBMS attiva e dell'applicazione. Questa architettura è l'architettura preferita per la distribuzione tra le zone.
- Attivo/passivo: la coppia di macchine virtuali che eseguono ASC/SCS e la coppia di macchine virtuali che eseguono il livello DBMS vengono distribuite in due zone. Il numero di macchine virtuali che eseguono il livello applicazione SAP viene distribuito in uno dei zone di disponibilità. Il livello dell'applicazione viene eseguito nella stessa zona dell'istanza di ASC/SCS e DBMS attiva. Usare questa architettura di distribuzione se la latenza di rete tra le diverse zone è troppo elevata per eseguire il livello dell'applicazione distribuito tra le zone. Al contrario, il livello dell'applicazione SAP deve essere eseguito nella stessa zona dell'istanza ASC/SCS e/o DBMS attiva. Se viene eseguito il failover di una macchina virtuale ASC/SCS o DBMS sulla zona secondaria, è possibile che si verifichi una latenza di rete superiore e con una riduzione della velocità effettiva. È necessario eseguire il failback della VM precedentemente sottoposta a failover non appena possibile per tornare ai livelli di velocità effettiva precedenti. Se si verifica un'interruzione di zona, il livello dell'applicazione deve essere sottoposta a failover nella zona secondaria. Un'attività che gli utenti sperimentano come chiusura completa del sistema. In alcune aree di Azure, questa architettura è l'unica architettura possibile quando si desidera utilizzare zone di disponibilità. Se non si è in grado di accettare il potenziale impatto di un failover di ASC/SCS o di macchine virtuali DBMS nella zona secondaria, è possibile che si stia migliorando la permanenza con le distribuzioni dei set di disponibilità


Quindi, prima di decidere come usare zone di disponibilità, è necessario determinare:

- La latenza di rete tra le tre zone di un'area di Azure, Conoscere la latenza di rete tra le zone di un'area consente di scegliere le zone con la minore latenza di rete nel traffico di rete tra zone.
- La differenza tra la latenza tra le macchine virtuali all'interno di una delle zone scelte e la latenza di rete tra le due zone scelte.
- Se i tipi di macchina virtuale che è necessario distribuire sono disponibili nelle due zone scelte. Con alcune macchine virtuali, in particolare le macchine virtuali serie M, possono verificarsi situazioni in cui alcuni SKU sono disponibili solo in due delle tre zone.

## <a name="network-latency-between-and-within-zones"></a>Latenza di rete tra e nelle zone
Per determinare qual è la latenza tra le varie zone, è necessario:

- Distribuire lo SKU di macchina virtuale che si vuole usare per l'istanza del DBMS in tutte e tre le zone. Assicurarsi che [Rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sia abilitata durante l'esecuzione di questa misurazione.
- Dopo aver individuato le due zone con la minore latenza di rete, distribuire altre tre macchine virtuali dello SKU che si vuole usare come macchina virtuale del livello dell'applicazione nelle tre zone di disponibilità. Misurare la latenza di rete per le due macchine virtuali di DBMS nelle due zone DBMS selezionate. 
- Utilizzare **`niping`** come strumento di misurazione. lo strumento di SAP descritto nelle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Prestare attenzione ai comandi documentati per le misurazioni della latenza. Poiché **ping** non funziona nei percorsi del codice di Rete accelerata di Azure, non è consigliabile usarlo.

Non è necessario eseguire manualmente questi test. È possibile trovare un test di [latenza della zona di disponibilità](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) della procedura PowerShell che consente di automatizzare i test di latenza descritti. 

In base alle misurazioni e alla disponibilità degli SKU di macchine virtuali nelle zone di disponibilità, è necessario prendere alcune decisioni:

- Definire le zone ideali per il livello DBMS.
- Stabilire se si vuole distribuire il livello dell'applicazione SAP attivo su una, due o tutte e tre le zone, in base alle differenze della latenza di rete all'interno di una zona o tra più zone.
- Stabilire se si vuole distribuire una configurazione attiva/passiva o una configurazione attiva/attiva dal punto di vista dell'applicazione. Queste configurazioni sono illustrate più avanti in questo articolo.

Nel prendere queste decisioni, tenere presenti anche le raccomandazioni per la latenza di rete di SAP documentate nella nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Le misurazioni e le decisioni sono valide per la sottoscrizione di Azure in uso quando si eseguono le misurazioni. Se si usa un'altra sottoscrizione di Azure, è necessario ripetere le misurazioni. Il mapping delle zone enumerate può essere diverso per un'altra sottoscrizione di Azure.


> [!IMPORTANT]
> È previsto che le misurazioni indicate in precedenza restituiscano risultati diversi in ogni area di Azure che supporta le [zone di disponibilità](https://azure.microsoft.com/global-infrastructure/geographies/). Anche se i requisiti di latenza di rete non cambiano, può essere necessario adottare strategie di distribuzione diverse in diverse aree di Azure, perché la latenza di rete tra le zone può essere diversa. In alcune aree di Azure la latenza di rete fra le tre zone può essere notevolmente diversa. In altre aree la latenza di rete fra le tre zone può essere più uniforme. Non è corretto affermare che è sempre presente una latenza di rete compresa tra 1 e 2 millisecondi. La latenza di rete tra le zone di disponibilità nelle aree di Azure non può essere generalizzata.

## <a name="activeactive-deployment"></a>Distribuzione attiva/attiva
Questa architettura di distribuzione è denominata Active/Active perché i server applicazioni SAP attivi vengono distribuiti in due o tre zone. L'istanza di SAP Central Services che usa la replica di accodamento verrà distribuita tra due zone. Lo stesso vale per il livello DBMS, che verrà distribuito tra le stesse zone di SAP Central Services. Quando si considera questa configurazione, è necessario trovare le due zone di disponibilità nell'area di appartenenza che offrono una latenza di rete tra zone accettabile per il carico di lavoro e per la replica DBMS sincrona. È anche opportuno assicurarsi che la differenza tra la latenza di rete all'interno delle zone selezionate e tra le zone non sia eccessiva. 

La natura dell'architettura SAP è che, a meno che non venga configurata in modo diverso, gli utenti e i processi batch possono essere eseguiti nelle diverse istanze dell'applicazione. L'effetto collaterale di questo fatto con la distribuzione attiva/attiva è che i processi batch possono essere eseguiti da qualsiasi istanza dell'applicazione SAP indipendentemente dal fatto che vengano eseguiti nella stessa zona con il sistema DBMS attivo o meno. Se la differenza di latenza di rete tra le zone di differenza è ridotta rispetto alla latenza di rete all'interno di una zona, la differenza nei tempi di esecuzione dei processi batch potrebbe non essere significativa. Tuttavia, più grande è la differenza di latenza di rete all'interno di una zona rispetto al traffico di rete in più zone, il tempo di esecuzione dei processi batch può essere più interessato se il processo è stato eseguito in una zona in cui l'istanza DBMS non è attiva. Il cliente deve decidere quali sono le differenze accettabili in fase di esecuzione. E con tale latenza di rete accettabile per il traffico tra le zone.

Aree di Azure in cui una distribuzione attiva/attiva dovrebbe essere possibile senza grandi differenze in fase di esecuzione e velocità effettiva all'interno del livello dell'applicazione distribuito in zone di disponibilità diversi, ad esempio:

- Uniti occidentale (tutte e tre le zone)
- East Uniti (tutte e tre le zone)
- Stati Uniti centrali (tutte e tre le zone)
- Europa settentrionale (tutte e tre le zone)
- Europa occidentale (due delle tre zone)
- Stati Uniti orientali (due delle tre zone)
- Stati Uniti centro-meridionali (due delle tre zone)
- Regno Unito meridionale (due delle tre zone)

Le aree di Azure in cui non è consigliabile l'architettura di distribuzione SAP tra le zone sono:

- Francia centrale 
- Sudafrica settentrionale
- Canada centrale
- Giappone orientale

A seconda di ciò che si è disposti a tollerare sulle differenze in fase di esecuzione, anche altre aree non elencate potrebbero essere qualificate.


Uno schema semplificato di una distribuzione attiva/attiva su due zone può essere simile al seguente:

![Distribuzione attiva/attiva in una zona](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A questa configurazione si applicano le considerazioni seguenti:

- Non usando il [gruppo di posizionamento di prossimità di Azure](../../co-location.md), si considera il zone di disponibilità di Azure come domini di errore e di aggiornamento per tutte le VM perché i set di disponibilità non possono essere distribuiti in zone di disponibilità di Azure.
- Se si vuole combinare le distribuzioni di zona per il livello DBMS e i servizi centrali, ma si vogliono usare i set di disponibilità di Azure per il livello dell'applicazione, è necessario usare i gruppi di prossimità di Azure come descritto nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
- Per i servizi di bilanciamento del carico per i cluster di failover di SAP Central Services e per il livello DBMS, è necessario usare lo [SKU di Azure Load Balancer Standard](../../../load-balancer/load-balancer-standard-availability-zones.md). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure distribuita per ospitare il sistema SAP, unita alle relative subnet, è estesa tra le zone. Non è necessario separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Managed Disks di Azure](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- Archiviazione Premium di Azure, [archiviazione Ultra SSD](../../disks-types.md#ultra-disk)o e non supporta alcun tipo di replica di archiviazione tra le zone. L'applicazione (DBMS o SAP Central Services) deve replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tali dischi condivisi o condivisioni tra le zone. Sono supportate le tecnologie seguenti:
  - Per Windows, una soluzione cluster che usa SIOS DataKeeper, come documentato in [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Per SUSE Linux, una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Attualmente la soluzione che usa File server di scalabilità orizzontale Microsoft, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), non è supportata tra le zone.
- La terza zona viene usata per ospitare il dispositivo SBD se si compila un [cluster SUSE Linux pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e si usano dispositivi SBD anziché l'agente di schermatura di Azure. O per altre istanze dell'applicazione.
- Per ottenere la coerenza del tempo di esecuzione per i processi aziendali critici, è possibile provare a indirizzare determinati utenti e processi batch alle istanze dell'applicazione che si trovano in zona con l'istanza di DBMS attiva usando i gruppi di server di batch SAP, i gruppi di accesso SAP o i gruppi RFC. In caso di failover di zona, è tuttavia necessario spostare manualmente questi gruppi in istanze in esecuzione su macchine virtuali nella stessa zona della macchina virtuale del database attivo.  
- Può essere necessario distribuire le istanze di dialogo inattive in ognuna delle zone. 

> [!IMPORTANT]
> In questo scenario attivo/attivo gli addebiti aggiuntivi per la larghezza di banda vengono annunciati da Microsoft da 04/01/2020 il. Controllare i [Dettagli prezzi](https://azure.microsoft.com/pricing/details/bandwidth/)per la larghezza di banda del documento. Il trasferimento dei dati tra il livello applicazione SAP e il livello DBMS di SAP è piuttosto intenso. Pertanto lo scenario attivo/attivo può contribuire ai costi. Continua a controllare questo articolo per ottenere i costi esatti 


## <a name="activepassive-deployment"></a>Distribuzione attiva/passiva
Se non si riesce a trovare una differenza accettabile tra la latenza di rete all'interno del traffico di rete di una zona e tra zone, è possibile distribuire un'architettura attiva/passiva dal punto di vista del livello dell'applicazione SAP. Occorre definire una zona *attiva*, che è la zona in cui si distribuisce il livello dell'applicazione completo e in cui si tenta di eseguire sia l'istanza DBMS attiva che l'istanza di SAP Central Services. Con questa configurazione, è necessario assicurarsi di non avere variazioni estreme a livello di tempi di esecuzione, a seconda che un processo venga eseguito o meno nella stessa zona dell'istanza DBMS attiva, per le transazioni aziendali e i processi batch.

Le aree di Azure in cui può essere preferibile questo tipo di architettura di distribuzione in zone diverse sono:

- Asia sud-orientale
- Australia orientale
- Brasile meridionale
- Germania centro-occidentale
- Sudafrica settentrionale
- Francia centrale 
- Canada centrale
- Giappone orientale


Il layout di base di questa architettura è simile al seguente:

![Distribuzione attiva/passiva in una zona](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

A questa configurazione si applicano le considerazioni seguenti:

- I set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure. Per compensare tale situazione, è possibile usare i gruppi di posizionamento di prossimità di Azure, come documentato nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
- Quando si usa questa architettura, è necessario monitorare attentamente lo stato e provare a mantenere le istanze di DBMS e di SAP Central Services attive nella stessa zona del livello dell'applicazione distribuita. In caso di failover dell'istanza di SAP Central Services o DBMS, è opportuno assicurarsi che sia possibile eseguire manualmente il failback nella zona con il livello dell'applicazione SAP distribuita non appena possibile.
- Per i servizi di bilanciamento del carico per i cluster di failover di SAP Central Services e per il livello DBMS, è necessario usare lo [SKU di Azure Load Balancer Standard](../../../load-balancer/load-balancer-standard-availability-zones.md). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure distribuita per ospitare il sistema SAP, unita alle relative subnet, è estesa tra le zone. Non è necessario separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Managed Disks di Azure](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- Archiviazione Premium di Azure, [archiviazione Ultra SSD](../../disks-types.md#ultra-disk)o e non supporta alcun tipo di replica di archiviazione tra le zone. L'applicazione (DBMS o SAP Central Services) deve replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tali dischi condivisi o condivisioni tra le zone. Sono supportate le tecnologie seguenti:
    - Per Windows, una soluzione cluster che usa SIOS DataKeeper, come documentato in [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Per SUSE Linux, una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Attualmente la soluzione che usa File server di scalabilità orizzontale Microsoft, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), non è supportata tra le zone.
- La terza zona viene usata per ospitare il dispositivo SBD se si compila un [cluster SUSE Linux pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e si usano dispositivi SBD anziché l'agente di schermatura di Azure. O per altre istanze dell'applicazione.
- È consigliabile distribuire le macchine virtuali inattive nella zona passiva (dal punto di vista del DBMS), in modo da poter avviare le risorse dell'applicazione in caso di errore della zona.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) non è attualmente in grado di replicare le macchine virtuali attive nelle macchine virtuali inattive tra zone. 
- È consigliabile investire in automazione che consente di avviare automaticamente il livello dell'applicazione SAP nella seconda zona se si verifica un'interruzione di zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configurazione combinata per disponibilità elevata e ripristino di emergenza
Microsoft non condivide alcuna informazione sulla distanza geografica fra le strutture che ospitano diverse zone di disponibilità in un'area di Azure. Nonostante ciò, alcuni clienti usano le zone per una configurazione di disponibilità elevata e ripristino di emergenza combinata che promette un obiettivo del punto di ripristino (RPO, Recovery Point Objective) pari a zero. Un RPO di zero significa che non è necessario perdere le transazioni del database di cui è stato eseguito il commit anche in caso di ripristino di emergenza. 

> [!NOTE]
> È consigliabile usare una configurazione simile alla seguente solo in determinate circostanze. È ad esempio possibile usarla quando i dati non possono essere spostati fuori dall'area di Azure per motivi di sicurezza o conformità. 

Ecco un esempio di una configurazione di questo tipo:

![Disponibilità elevata e ripristino di emergenza combinati nelle zone](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

A questa configurazione si applicano le considerazioni seguenti:

- Si presuppone che esista una distanza significativa tra le strutture che ospitano una zona di disponibilità, in caso contrario è obbligatorio rimanere all'interno di una determinata area di Azure. I set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure. Per compensare tale situazione, è possibile usare i gruppi di posizionamento di prossimità di Azure, come documentato nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
- Quando si usa questa architettura, è necessario monitorare attentamente lo stato e provare a mantenere le istanze di DBMS e di SAP Central Services attive nella stessa zona del livello dell'applicazione distribuita. In caso di failover dell'istanza di SAP Central Services o DBMS, è opportuno assicurarsi che sia possibile eseguire manualmente il failback nella zona con il livello dell'applicazione SAP distribuita non appena possibile.
- Esistono istanze dell'applicazione di produzione pre-installate nelle macchine virtuali che eseguono istanze dell'applicazione di controllo qualità attive.
- In un caso di errore di zona, arrestare le istanze dell'applicazione QA e avviare invece le istanze di produzione. Per eseguire questa operazione, è necessario usare nomi virtuali per le istanze dell'applicazione.
- Per i servizi di bilanciamento del carico per i cluster di failover di SAP Central Services e per il livello DBMS, è necessario usare lo [SKU di Azure Load Balancer Standard](../../../load-balancer/load-balancer-standard-availability-zones.md). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure distribuita per ospitare il sistema SAP, unita alle relative subnet, è estesa tra le zone. Non è necessario separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Managed Disks di Azure](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure e l'[archiviazione Ultra SSD](../../disks-types.md#ultra-disk) non supportano alcun tipo di replica dell'archiviazione tra le zone. L'applicazione (DBMS o SAP Central Services) deve replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tali dischi condivisi o condivisioni tra le zone. Sono supportate le tecnologie seguenti:
    - Per Windows, una soluzione cluster che usa SIOS DataKeeper, come documentato in [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Per SUSE Linux, una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Attualmente la soluzione che usa File server di scalabilità orizzontale Microsoft, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), non è supportata tra le zone.
- La terza zona viene usata per ospitare il dispositivo SBD se si compila un [cluster SUSE Linux pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e si usano dispositivi SBD anziché l'agente di schermatura di Azure. 





## <a name="next-steps"></a>Passaggi successivi
Ecco alcuni passaggi da eseguire successivamente per la distribuzione in più zone di disponibilità di Azure:

- [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)