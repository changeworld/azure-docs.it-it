---
title: Distribuire Horizon in una soluzione VMware di Azure
description: Informazioni su come distribuire VMware Horizon in una soluzione VMware di Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: c34d0ac7806f8284e893cf3ad4f3c82dd404ff41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181398"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Distribuire Horizon in una soluzione VMware di Azure 

>[!NOTE]
>Questo documento è incentrato sul prodotto VMware Horizon, noto in precedenza come Horizon 7. Horizon è una soluzione diversa rispetto a Horizon cloud in Azure, sebbene esistano alcuni componenti condivisi. I vantaggi principali della soluzione VMware di Azure includono un metodo di ridimensionamento più semplice e l'integrazione della gestione di VMware Cloud Foundation nel portale di Azure.

[VMware Horizon](https://www.vmware.com/products/horizon.html)®, una piattaforma per desktop e applicazioni virtuali, viene eseguita nel Data Center e fornisce una gestione semplice e centralizzata. Offre desktop virtuali e applicazioni su qualsiasi dispositivo, ovunque. Horizon consente di creare ed eseguire il broker di connessioni a desktop virtuali Windows e Linux, applicazioni ospitate da Desktop remoto server (RDS), desktop e computer fisici.

In questo articolo viene illustrata la distribuzione di Horizon in una soluzione VMware di Azure. Per informazioni generali su VMware Horizon, vedere la documentazione di Horizon Production:

* [Che cos'è VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Altre informazioni su VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Architettura di riferimento per Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Con l'introduzione di Horizon sulla soluzione VMware di Azure, sono ora disponibili due soluzioni VDI (Virtual Desktop Infrastructure) sulla piattaforma Azure. Il diagramma seguente riepiloga le differenze principali a livello generale.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Orizzonte sulla soluzione VMware di Azure e sul cloud Horizon in Azure" border="false":::

Horizon 2006 e versioni successive nella riga di rilascio di Horizon 8 supporta la distribuzione locale e la distribuzione della soluzione VMware di Azure. Sono disponibili alcune funzionalità di Horizon supportate in locale, ma non nella soluzione VMware di Azure. Sono supportati anche altri prodotti nell'ecosistema Horizon. Per informazioni, vedere [parità di funzionalità e interoperabilità](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Distribuisci Horizon in un cloud ibrido

È possibile distribuire Horizon in un ambiente cloud ibrido quando si usa Horizon cloud Pod Architecture (CPA) per connettere i data center locali e di Azure. CPA consente di ridimensionare la distribuzione, compilare un cloud ibrido e garantire ridondanza per la continuità aziendale e il ripristino di emergenza.  Per altre informazioni, vedere [espansione degli ambienti Horizon 7 esistenti](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA non è una distribuzione estesa; ogni pod Horizon è distinto e tutti i server di connessione che appartengono a ognuno dei singoli Pod devono trovarsi in un'unica posizione ed essere eseguiti nello stesso dominio broadcast da una prospettiva di rete.

Analogamente a data center locali o privati, Horizon può essere distribuito in un cloud privato della soluzione VMware di Azure. Nelle sezioni seguenti verranno illustrate le differenze principali nella distribuzione di Horizon in locale e nella soluzione VMware di Azure.

Il cloud privato di Azure è concettualmente identico a quello di VMware SDDC, un termine usato in genere nella documentazione di Horizon. Il resto di questo documento usa i termini interscambiabili con il cloud privato di Azure e VMware SDDC.

Per la gestione delle licenze di sottoscrizione, è necessario il connettore Horizon cloud per la soluzione Azure Horizon in Azure. Il connettore cloud può essere distribuito in rete virtuale di Azure insieme ai server di connessione Horizon.

>[!IMPORTANT]
>Il supporto del piano di controllo Horizon per la soluzione VMware Horizon in Azure non è ancora disponibile. Assicurarsi di scaricare la versione VHD di Horizon Cloud Connector.

## <a name="vcenter-cloud-admin-role"></a>ruolo di amministratore del cloud vCenter

Poiché la soluzione VMware di Azure è un servizio SDDC e Azure gestisce il ciclo di vita della soluzione VMware SDDC in Azure, il modello di autorizzazione vCenter nella soluzione VMware di Azure è limitato dalla progettazione.

I clienti devono usare il ruolo di amministratore del cloud, che dispone di un set limitato di autorizzazioni di vCenter. Il prodotto Horizon è stato modificato per collaborare con il ruolo di amministratore del cloud nella soluzione VMware di Azure, in particolare:

* Il provisioning del clone istantaneo è stato modificato per l'esecuzione in una soluzione VMware di Azure.

* Un criterio rete VSAN specifico (VMware_Horizon) è stato creato nella soluzione VMware di Azure per funzionare con Horizon, che deve essere disponibile e usato in SDDCs distribuito per Horizon.

* la cache di lettura basata sul contenuto vSphere (CBRC), nota anche come View Storage Accelerator, è disabilitata durante l'esecuzione nella soluzione VMware di Azure.

>[!IMPORTANT]
>CBRC non deve essere nuovamente attivato.

>[!NOTE]
>La soluzione VMware di Azure configura automaticamente impostazioni orizzonte specifiche purché venga distribuito Horizon 2006 (noto anche come Horizon 8) e versioni successive nel ramo Horizon 8 e selezionare l'opzione **Azure** nel programma di installazione del server di connessione Horizon.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Architettura di distribuzione della soluzione VMware di Azure Horizon

Un tipico progetto di architettura Horizon usa una strategia pod e Block. Un blocco è un singolo vCenter, mentre più blocchi combinati rendono un pod. Un pod Horizon è un'unità di organizzazione determinata dai limiti di scalabilità di Horizon. Ogni pod Horizon dispone di un portale di gestione separato, quindi una procedura di progettazione standard consiste nel ridurre al minimo il numero di Pod.

Ogni Cloud ha il proprio schema di connettività di rete. In combinazione con VMware SDDC networking/NSX Edge, la connettività di rete della soluzione VMware di Azure presenta requisiti univoci per la distribuzione di orizzonti diversi dall'ambiente locale.

Ogni cloud privato di Azure e SDDC è in grado di gestire 4.000 sessioni desktop o dell'applicazione, supponendo che:

* Il traffico del carico di lavoro viene allineato al profilo di lavoro delle attività LoginVSI.

* Viene considerato solo il traffico del protocollo, nessun dato utente.

* NSX Edge è configurato in modo da essere di grandi dimensioni.

>[!NOTE]
>Il profilo e le esigenze del carico di lavoro possono essere diversi e quindi i risultati possono variare in base al caso d'uso. I volumi di dati utente possono ridurre i limiti di scalabilità nel contesto del carico di lavoro. Ridimensionare e pianificare la distribuzione di conseguenza. Per altre informazioni, vedere le linee guida per il ridimensionamento nella sezione [dimensioni degli host della soluzione VMware di Azure per le distribuzioni Horizon](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Dato il cloud privato di Azure e il limite massimo di SDDC, è consigliabile un'architettura di distribuzione in cui i server di connessione Horizon e VMware Unified Access Gateway (UAGs) siano in esecuzione all'interno della rete virtuale di Azure. Converte in modo efficace ogni cloud privato di Azure e SDDC in un blocco. A sua volta, massimizzare la scalabilità di Horizon in esecuzione nella soluzione VMware di Azure.

La connessione dalla rete virtuale di Azure ai cloud privati/SDDCs di Azure deve essere configurata con ExpressRoute FastPath. Il diagramma seguente illustra una distribuzione di base di un pod.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Distribuzione tipica di un pod di Horizon con percorso veloce ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Connettività di rete per la scalabilità di Horizon nella soluzione VMware di Azure

Questa sezione illustra l'architettura di rete a un livello elevato con alcuni esempi di distribuzione comuni per semplificare la scalabilità di Horizon nella soluzione VMware di Azure. Lo stato attivo è specifico per gli elementi di rete cruciali. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Soluzione VMware Single Horizon pod in Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Soluzione VMware Single Horizon pod in Azure" border="false":::

Un singolo POD Horizon è lo scenario di distribuzione più semplice, perché si distribuisce un solo Horizon pod nell'area Stati Uniti orientali.  Poiché ogni cloud privato e SDDC è stimato per la gestione delle sessioni Desktop 4.000, si distribuiscono le dimensioni massime del pod di Horizon.  È possibile pianificare la distribuzione di un massimo di tre cloud privati/SDDCs.

Con le macchine virtuali (VM) dell'infrastruttura Horizon distribuite nella rete virtuale di Azure, è possibile raggiungere le sessioni 12.000 per ogni pod Horizon. La connessione tra ogni cloud privato e SDDC alla rete virtuale di Azure è ExpressRoute percorso rapido.  Non è necessario alcun traffico East-West tra cloud privati. 

I presupposti chiave per questo esempio di distribuzione di base includono quanto segue:

* Non è presente un pod Horizon locale che si vuole connettere a questo nuovo pod usando cloud Pod Architecture (CPA).

* Gli utenti finali si connettono ai desktop virtuali tramite Internet (rispetto alla connessione tramite un data center locale).

Il controller di dominio di Active Directory viene connesso in rete virtuale di Azure con l'istanza locale di AD tramite VPN o circuito ExpressRoute.

Una variante dell'esempio di base potrebbe essere il supporto della connettività per le risorse locali. Ad esempio, gli utenti accedono ai desktop e generano il traffico delle applicazioni desktop virtuali o si connettono a un pod di Horizon locale usando CPA.

Il diagramma mostra come supportare la connettività per le risorse locali. Per connettersi alla rete aziendale alla rete virtuale di Azure, è necessario un circuito ExpressRoute.  Sarà anche necessario connettere la rete aziendale a ogni cloud privato e SDDCs usando ExpressRoute Copertura globale.  Consente la connettività dal SDDC al circuito ExpressRoute e alle risorse locali. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Connettere la rete aziendale a una rete virtuale di Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Più baccelli Horizon nella soluzione VMware di Azure in più aree

Un altro scenario consiste nel ridimensionare l'orizzonte tra più baccelli.  In questo scenario si distribuiscono due pod di Horizon in due aree diverse e la si federa usando CPA. È simile alla configurazione di rete nell'esempio precedente, ma con alcuni collegamenti tra aree aggiuntive. 

Si connetterà la Rete in ingresso virtuale di Azure ogni area ai cloud privati/SDDCs nell'altra area. Consente ai server di connessione Horizon parte della Federazione CPA di connettersi a tutti i desktop sotto la gestione. L'aggiunta di altri cloud privati/SDDCs a questa configurazione consente di eseguire il ridimensionamento a 24.000 sessioni complessive. 

Gli stessi principi si applicano se si distribuiscono due pod Horizon nella stessa area.  Assicurarsi di distribuire il secondo pod Horizon in una *rete virtuale di Azure separata*. Analogamente all'esempio di Pod singolo, è possibile connettere la rete aziendale e il Pod locale a questo esempio di più POD/aree usando ExpressRoute e Copertura globale. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Più baccelli Horizon nella soluzione VMware di Azure in più aree" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Ridimensionare gli host della soluzione VMware di Azure per le distribuzioni Horizon 

La metodologia di dimensionamento di Horizon in un host in esecuzione nella soluzione VMware di Azure è più semplice rispetto a Horizon in locale.  Questo perché l'host della soluzione VMware di Azure è standardizzato.  Il dimensionamento host esatto consente di determinare il numero di host necessari per supportare i requisiti VDI.  È fondamentale determinare il costo per desktop.

### <a name="sizing-tables"></a>Ridimensionamento di tabelle

I requisiti specifici di vCPU/vRAM per i desktop virtuali Horizon dipendono dal profilo del carico di lavoro specifico del cliente.   Collaborare con il team di vendita MSFT e VMware per determinare i requisiti di vCPU/vRAM per i desktop virtuali. 

| vCPU per macchina virtuale | vRAM per macchina virtuale (GB) | Istanza | VM 100 | VM 200 | VM 300 | VM 400 | VM 500 | VM 600 | VM 700 | VM 800 | VM 900 | VM 1000 | VM 2000 | VM 3000 | VM 4000 | VM 5000 | VM 6000 | VM 6400 |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    Soluzione Azure VMware   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    Soluzione Azure VMware   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    Soluzione Azure VMware   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    Soluzione Azure VMware   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    Soluzione Azure VMware   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    Soluzione Azure VMware   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    Soluzione Azure VMware   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    Soluzione Azure VMware   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    Soluzione Azure VMware   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    Soluzione Azure VMware   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    Soluzione Azure VMware   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    Soluzione Azure VMware   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    Soluzione Azure VMware   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    Soluzione Azure VMware   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    Soluzione Azure VMware   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    Soluzione Azure VMware   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    Soluzione Azure VMware   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    Soluzione Azure VMware   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    Soluzione Azure VMware   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    Soluzione Azure VMware   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    Soluzione Azure VMware   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    Soluzione Azure VMware   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    Soluzione Azure VMware   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    Soluzione Azure VMware   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Input di ridimensionamento dell'orizzonte

Ecco cosa è necessario raccogliere per il carico di lavoro pianificato:

* Numero di desktop simultanei

* VCPU necessari per desktop

* VRAM necessaria per desktop

* Spazio di archiviazione necessario per desktop

In generale, le distribuzioni VDI sono vincolate alla CPU o alla RAM, che determinano le dimensioni dell'host. Si prenda l'esempio seguente per un tipo di carico di lavoro LoginVSI Knowledge Worker, convalidato con il test delle prestazioni:

* 2.000 distribuzione desktop simultanea

* 2vCPU per desktop.

* vRAM da 4 GB per desktop.

* 50 GB di spazio di archiviazione per desktop

Per questo esempio, il numero totale di fattori host è pari a 18, ottenendo una densità della macchina virtuale per host di 111.

>[!IMPORTANT]
>I carichi di lavoro dei clienti variano a seconda di questo esempio di LoginVSI Knowledge Worker. Come parte della pianificazione della distribuzione, usare le SEs EUC VMware per esigenze specifiche di dimensionamento e prestazioni. Assicurarsi di eseguire il test delle prestazioni utilizzando il carico di lavoro effettivo e pianificato prima di finalizzare il dimensionamento dell'host e modificarlo di conseguenza.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Gestione delle licenze della soluzione VMware di Azure per Horizon 

Sono disponibili quattro componenti per i costi complessivi per l'esecuzione di Horizon in una soluzione VMware di Azure. 

### <a name="azure-vmware-solution-capacity-cost"></a>Costo della capacità della soluzione VMware di Azure

Per informazioni sui prezzi, vedere la pagina dei [prezzi della soluzione VMware di Azure](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Costo della licenza Horizon

Esistono due licenze disponibili per l'uso con la soluzione VMware di Azure, che può essere utente simultaneo (CCU) o utente denominato (NU):

* Licenza Horizon Subscription

* Licenza Horizon Universal Subscription

Se si distribuisce solo Horizon per la soluzione VMware di Azure per il futuro prevedibile, usare la licenza Horizon Subscription poiché si tratta di un costo inferiore.

Se distribuito in una soluzione VMware di Azure e in locale, come nel caso di utilizzo del ripristino di emergenza, scegliere la licenza Horizon Universal Subscription. Include una licenza vSphere per la distribuzione locale, quindi ha un costo superiore.

Collaborare con il team di vendita VMware EUC per determinare il costo della licenza Horizon in base alle esigenze.

### <a name="azure-instance-types"></a>Tipi di istanza di Azure

Per informazioni sulle dimensioni delle macchine virtuali di Azure che saranno necessarie per l'infrastruttura Horizon, vedere le linee guida di VMware disponibili [qui](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="references"></a>Riferimenti
[Requisiti di sistema per l'agente Horizon per Linux](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla soluzione VMware Horizon in Azure, vedere le [domande frequenti](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)su VMware Horizon.
