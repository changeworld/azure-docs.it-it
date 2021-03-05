---
title: Eseguire il backup dei dati in Azure con CommVault
titleSuffix: Azure Blob Storage Docs
description: La pagina Web fornisce una panoramica dei fattori da prendere in considerazione e i passaggi da seguire per sfruttare Azure come destinazione di archiviazione e il percorso di ripristino per il backup e il ripristino completi di CommVault
keywords: CommVault, backup su cloud, backup, backup in Azure, ripristino di emergenza, continuità aziendale
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: f340a06f3b6b7c8fc0d78051fb0496dcab11fb5d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122749"
---
# <a name="back-up-to-azure-with-commvault"></a>Esegui il backup in Azure con CommVault

Questo articolo fornisce una guida all'integrazione di un'infrastruttura CommVault con archiviazione BLOB di Azure. Sono inclusi i prerequisiti, i principi di archiviazione di Azure, l'implementazione e le linee guida operative. Questo articolo descrive solo l'uso di Azure come destinazione di backup fuori sede e un sito di ripristino in caso di emergenza, che impedisce il funzionamento normale nel sito primario. CommVault offre anche una soluzione RTO più bassa, CommVault Live Sync, come mezzo per avere una VM di standby pronta per l'avvio e il ripristino più rapidamente in caso di emergenza e protezione delle risorse in un ambiente di produzione di Azure. Queste funzionalità non rientrano nell'ambito di questo documento. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Architettura di riferimento per le distribuzioni da sito locale ad Azure e In-Azure

![Architettura di riferimento da CommVault ad Azure](../media/commvault-diagram.png)

La distribuzione di CommVault esistente può essere facilmente integrata con Azure aggiungendo un account di archiviazione di Azure o più account come destinazione di archiviazione cloud. CommVault consente anche di ripristinare i backup dall'ambiente locale all'interno di Azure, offrendo un sito di ripristino su richiesta in Azure.   

## <a name="commvault-interoperability-matrix"></a>Matrice di interoperabilità CommVault
| Carico di lavoro | Archiviazione BLOB e GPv2 | Supporto del livello di accesso sporadico | Supporto del livello di archiviazione | Supporto per la famiglia Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VM/dati locali | v 11.5 | v 11.5 | v 11.10 | v 11.10 |
| Macchine virtuali di Azure | v 11.5 | v 11.5 | v 11.5 | N/D |
| BLOB Azure | v 11.6 | v 11.6 | v 11.6 | N/D |
| File di Azure | v 11.6 | v 11.6 | v 11.6 | N/D | 

## <a name="before-you-begin"></a>Prima di iniziare

Una piccola pianificazione iniziale assicurerà di unire in join le classificazioni dei molti clienti felici che usano Azure come destinazione di backup e sito di ripristino fuori sede.

### <a name="are-you-new-to-azure"></a>Sei un nuovo utente di Azure?

Microsoft offre un Framework da seguire per iniziare a usare Azure. Il [Framework di adozione del cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( \) è un approccio dettagliato alla trasformazione digitale aziendale e una guida completa alla pianificazione di un'adozione del cloud di livello produzione. Il CAF include una guida dettagliata per l' [installazione di Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) per i nuovi utenti in Azure, che consente di iniziare a usarla in modo rapido e sicuro ed è possibile trovare una versione interattiva nel portale di [Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Sono disponibili architetture di esempio e procedure consigliate specifiche per la distribuzione di applicazioni e risorse di formazione gratuite che consentono di accedere al percorso delle competenze di Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Prendere in considerazione la rete tra la località e Azure

Che si tratti di sfruttare le risorse cloud per eseguire produzione, test e sviluppo o come destinazione di backup e sito di ripristino, è importante comprendere le esigenze di larghezza di banda per il seeding del backup iniziale e per i trasferimenti giornalieri in corso. 

Azure Data Box fornisce un mezzo per trasferire la linea di base di backup iniziale in Azure senza richiedere una larghezza di banda aggiuntiva se il trasferimento della linea di base è stimato per un tempo superiore a quello consentito. È possibile sfruttare il Trasferimento dati estimatore quando si crea un account di archiviazione per stimare il tempo necessario per trasferire il backup iniziale.

![Strumento di stima Trasferimento dati di archiviazione di Azure](../media/az-storage-transfer.png)

Tenere presente che sarà necessaria una capacità di rete sufficiente per supportare i trasferimenti di dati giornalieri nella finestra di trasferimento richiesta (finestra di backup) senza alcun effetto sulle applicazioni di produzione. In questa sezione vengono illustrati gli strumenti e le tecniche disponibili per valutare le esigenze di rete.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Come è possibile determinare la quantità di larghezza di banda necessaria?

-  Report dal software di backup. 
  CommVault fornisce report standard per determinare la [frequenza di modifica](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) e le [dimensioni totali del set di backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) per il trasferimento iniziale della linea di base in Azure.
- Strumenti di valutazione e reporting indipendenti dal software di backup, ad esempio:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>In che modo è possibile conoscere la quantità di spazio su cui si dispone con la connessione Internet corrente?

È importante conoscere la quantità di spazio disponibile, o in genere non utilizzata, della larghezza di banda disponibile quotidianamente. Questo consentirà di valutare correttamente se è possibile soddisfare gli obiettivi per il caricamento iniziale, quando non si usano Azure Data Box per il seeding offline e per il completamento dei backup giornalieri in base alla frequenza di modifica identificata sopra e alla finestra di backup. Di seguito sono riportati i metodi che è possibile usare per identificare lo spazio disponibile per la larghezza di banda che i backup in Azure possono utilizzare.

- Sei un cliente di Azure ExpressRoute esistente? Visualizzare l' [utilizzo del circuito](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) nel portale di Azure.
- È possibile contattare il provider di servizi Internet. Devono avere report da condividere con l'utente che illustra l'utilizzo giornaliero e mensile esistente.
- Sono disponibili diversi strumenti che consentono di misurare l'utilizzo monitorando il traffico di rete a livello di router/switch, tra cui:
  - [Pack di Bandwidth Bandwidth Analyzer](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [PRTG Paessler](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Scelta delle opzioni di archiviazione corrette

Quando si usa Azure come destinazione di backup, i clienti usano l' [Archivio BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Archiviazione BLOB di Azure è la soluzione Microsoft per l'archiviazione di oggetti. L'archiviazione BLOB è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati, ovvero dati non conformi a un modello di dati o a una definizione. Archiviazione di Azure è inoltre durevole, a disponibilità elevata, sicura e scalabile. La piattaforma Microsoft offre la flessibilità necessaria per selezionare la risorsa di archiviazione appropriata per il carico di lavoro appropriato, in modo da fornire il [livello di resilienza](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) per soddisfare i contratti di contratto interni. L'archiviazione BLOB è un servizio con pagamento in base al consumo. Viene [addebitato mensilmente](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) la quantità di dati archiviati, l'accesso a tali dati e, nel caso dei livelli di accesso sporadico e archivio, un periodo di conservazione minimo necessario. Nelle tabelle seguenti vengono riepilogate le opzioni di resilienza e di suddivisione in livelli applicabili ai dati di backup.

**Opzioni di resilienza dell'archiviazione BLOB di Azure:**

|  |Ridondanza locale  |Con ridondanza della zona  |Con ridondanza geografica  |Ridondanza della zona geografica  |
|---------|---------|---------|---------|---------|
|Numero di copie valide     | 3         | 3         | 6         | 6 |
|numero di zone di disponibilità     | 1         | 3         | 2         | 4 |
|numero di aree     | 1         | 1         | 2         | 2 |
|Failover manuale nell'area secondaria     | N/D         | ND         | Sì         | Sì |

**Livelli di archiviazione BLOB di Azure:**

|  | Livello critico   |Livello ad accesso sporadico   | Livello archivio |
| ----------- | ----------- | -----------  | -----------  |
| Disponibilità | 99,9%         | 99%         | Offline      |
| Addebiti per utilizzo | Costi di archiviazione più elevati, minore accesso e costi delle transazioni | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| Conservazione minima dei dati obbligatoria | N/D | 30 giorni | 180 giorni |
| Latenza (tempo per il primo byte) | Millisecondi | Millisecondi | Ore |

#### <a name="sample-backup-to-azure-cost-model"></a>Backup di esempio nel modello di costo di Azure

Il concetto di pagamento in base al consumo può essere molto difficile per i clienti che non hanno familiarità con il cloud pubblico. Quando si paga solo la capacità usata, si paga anche per le transazioni (lettura e scrittura) ed è in [uscita per i dati](https://azure.microsoft.com/pricing/details/bandwidth/) letti nell'ambiente locale quando si usa il [piano dati locale di Azure Express Route Direct o Express Route illimitata](https://azure.microsoft.com/pricing/details/expressroute/) per i dati in uscita da Azure. È possibile eseguire analisi di simulazione in base al prezzo di listino o con i [prezzi di capacità riservata di archiviazione di Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations), che possono offrire fino al 38% di risparmi nel calcolatore dei prezzi di [Azure](https://azure.microsoft.com/pricing/calculator/). Di seguito è riportato un esempio di esercitazione sui prezzi per modellare il costo mensile del backup in Azure. questo è solo un esempio e ***i prezzi possono variare a causa delle attività non acquisite*** in questo articolo:


|Fattore di costo  |Costo mensile  |
|---------|---------|
|100 TB di dati di backup nell'archiviazione ad accesso sporadico     |$1556,48         |
|2 TB di nuovi dati scritti al giorno x 30 giorni     |$39 nelle transazioni          |
|Totale stimato mensile     |$1595,48         |
|---------|---------|
|Ripristino di una volta da 5 TB a locale su Internet pubblico   | $491,26         |


> [!Note] 
Questa stima è stata generata nel calcolatore dei prezzi di Azure usando il piano tariffario degli Stati Uniti orientali con pagamento in base al consumo ed è basata sul valore predefinito di CommVault di 32 MB di dimensioni secondarie che genera 65.536 richieste PUT, note come transazioni di scrittura, al giorno. Questo esempio potrebbe non essere applicabile ai propri requisiti.

## <a name="implementation-and-operational-guidance"></a>Guida operativa e di implementazione

Questa sezione fornisce una breve guida all'aggiunta di archiviazione di Azure a una distribuzione CommVault locale. Per indicazioni dettagliate e considerazioni sulla pianificazione, è consigliabile esaminare la [Guida all'architettura di Azure CommVault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16).

1. Aprire il portale di Azure e cercare "account di archiviazione" oppure fare clic sull'icona servizi predefiniti.
    
    1. ![Portale di Azure](../media/azure-portal.png)
  
    1. ![Account di archiviazione nel portale di Azure](../media/locate-storage-account.png)

2. Scegliere di aggiungere un account e selezionare o creare un gruppo di risorse, specificare un nome univoco, scegliere l'area, selezionare le prestazioni "standard", lasciare sempre il tipo di account "Storage V2", scegliere il livello di replica, che soddisfa i contratti di sicurezza e il livello predefinito usato dal software di backup. Un account di archiviazione di Azure rende disponibili livelli ad accesso frequente, sporadico e archivio in un singolo account e i criteri di CommVault consentono di sfruttare più livelli per gestire in modo efficiente il ciclo di vita dei dati. Procedere con il passaggio successivo. 

    ![Creazione di un account di archiviazione](../media/account-create-1.png)

3. Usare le opzioni di rete predefinite per ora e passare alla "protezione dei dati". Qui è possibile scegliere di abilitare l'eliminazione temporanea, che consente di ripristinare un file di backup eliminato accidentalmente entro il periodo di conservazione definito e offre protezione da eliminazioni accidentali o dannose. 
    
    ![Creazione di un account di archiviazione-parte 2](../media/account-create-2.png)

4. Si consiglia quindi di usare le impostazioni predefinite della schermata "avanzate" per eseguire il backup nei casi d'uso di Azure.

    ![Creazione di un account di archiviazione-parte 3](../media/account-create-3.png) 

5. Aggiungere i tag per l'organizzazione se si sfruttano i tag e si crea l'account. A questo punto si ha a disposizione petabyte di archiviazione su richiesta.

6. Per aggiungere l'account all'ambiente CommVault, è necessario eseguire due passaggi rapidi. Passare all'account creato nella portale di Azure e selezionare "contenitori" nel menu "servizio BLOB" nel pannello del portale. Aggiungere un nuovo contenitore e scegliere un nome significativo. Passare quindi all'elemento "chiavi di accesso" in "Impostazioni" e copiare il nome dell'account di archiviazione e una delle due chiavi di accesso. Nei passaggi successivi sono necessari il nome del contenitore, il nome dell'account e la chiave di accesso.
    
    ![Creazione di un contenitore](../media/container.png)
    
    ![Acquisisci informazioni sull'account](../media/access-key.png)

7. ***(Facoltativo)*** È possibile aggiungere ulteriori livelli di sicurezza alla distribuzione.
    
    1. Configurare l'accesso in base al ruolo per limitare gli utenti che possono apportare modifiche all'account di archiviazione. [Per altre informazioni, vedi qui](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. Limitare l'accesso all'account a segmenti di rete specifici con [firewall di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) per evitare tentativi di accesso dall'esterno della rete aziendale.

    ![Firewall di archiviazione](../media/storage-firewall.png) 

    1. Impostare un [blocco di eliminazione](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) sull'account per evitare l'eliminazione accidentale dell'account di archiviazione.

    ![Blocco delle risorse](../media/resource-lock.png)
    
    1. Configurare [procedure di sicurezza consigliate](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)aggiuntive.
    
1. Nel centro comandi di CommVault passare a "Gestisci"--> "sicurezza"--> "Gestione credenziali". Scegliere un "account cloud", "tipo di fornitore" di Archiviazione di Microsoft Azure, selezionare il "MediaAgent", che trasferirà i dati da e verso Azure, aggiungere il nome e la chiave di accesso dell'account di archiviazione.
    
    ![Credenziali CommVault](../media/commvault-credential.png)

9. Passare quindi a "Storage" (archiviazione)--> "cloud" nel centro comandi di CommVault. Scegliere "Aggiungi". Immettere un nome descrittivo per l'account di archiviazione e quindi selezionare "Archiviazione di Microsoft Azure" dall'elenco "tipo". Selezionare un server agente multimediale da usare per trasferire i backup in archiviazione di Azure. Aggiungere il contenitore creato, scegliere il livello di archiviazione da utilizzare nell'account di archiviazione di Azure e selezionare le credenziali create nel passaggio #8. Infine, scegliere se trasferire o meno i backup deduplicati e un percorso per il database di deduplicazione.
    
     ![CommVault aggiungere spazio di archiviazione](../media/commvault-add-storage.png)

10. Infine, aggiungere la nuova risorsa di archiviazione di Azure a un piano esistente o nuovo nel centro comandi di CommVault tramite "Gestisci"--> "piani" come "destinazione di backup".

    ![CommVault aggiungere spazio di archiviazione](../media/commvault-plan.png)

11. ***(Facoltativo)*** Se si prevede di sfruttare Azure come sito di ripristino o CommVault per eseguire la migrazione di server e applicazioni in Azure, è consigliabile distribuire un proxy VSA in Azure. [Qui](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)è possibile trovare istruzioni dettagliate.  

### <a name="azure-alerting-and-performance-monitoring"></a>Avvisi e monitoraggio delle prestazioni di Azure

È consigliabile monitorare le risorse di Azure e la capacità di CommVault di utilizzarle come si farebbe con qualsiasi destinazione di archiviazione su cui si basa per archiviare i backup. Una combinazione di monitoraggio di Azure e il monitoraggio del centro comandi CommVault consentiranno di garantire l'integrità dell'ambiente.

#### <a name="microsoft-azure-portal"></a>Portale di Microsoft Azure

Microsoft Azure offre una soluzione di monitoraggio affidabile sotto forma di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). È possibile [configurare monitoraggio di Azure](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) per tenere traccia di capacità, transazioni, disponibilità, autenticazione e altro ancora di archiviazione di Azure. Il riferimento completo delle metriche registrate è disponibile [qui](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Alcune metriche utili per tenere traccia sono BlobCapacity: per assicurarsi di rimanere al di sotto del limite massimo di [capacità dell'account di archiviazione](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), in ingresso e in uscita, per tenere traccia della quantità di dati scritti e letti dall'account di archiviazione di Azure e SuccessE2ELatency per tenere traccia del tempo di round trip per le richieste da e verso archiviazione di Azure e MediaAgent. 

È anche possibile [creare avvisi di log](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) per tenere traccia dell'integrità del servizio di archiviazione di Azure e visualizzare il [Dashboard dello stato di Azure](https://status.azure.com/status) in qualsiasi momento.

#### <a name="commvault-command-center"></a>Centro comandi CommVault

[Creazione di avvisi per i pool di archiviazione cloud](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Dove i clienti possono passare per visualizzare i report di prestazioni, il completamento del processo e avviare la risoluzione dei problemi di base](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Come aprire i casi di supporto

Quando è necessaria assistenza per la soluzione di backup in Azure, è consigliabile aprire un caso sia con CommVault che con Azure, in modo che le organizzazioni di supporto possano interagire in modo collaborativo, se necessario.

#### <a name="how-to-open-a-case-with-commvault"></a>Come aprire un case con CommVault

Accedere al [sito di supporto di CommVault](https://www.commvault.com/support), accedere e aprire un caso.

Se è necessario comprendere le opzioni del contratto di supporto disponibili, vedere Opzioni di [supporto per CommVault](https://ma.commvault.com/support)

È anche possibile chiamare per aprire un caso o raggiungere il supporto CommVault tramite posta elettronica:

Numero verde: + 1 877-780-3077

[Numeri di supporto in tutto il mondo](https://ma.commvault.com/Support/TelephoneSupport)

[Supporto CommVault di posta elettronica](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Come aprire un caso con il team di supporto di Azure

All'interno del [portale di Azure](https://portal.azure.com) cercare "supporto" nella barra di ricerca nella parte superiore del portale e scegliere "+ nuova richiesta di supporto". 
> [!Note]
Quando si apre un caso, è necessario richiedere assistenza per "archiviazione di Azure" o "rete di Azure" e **non** "backup di Azure". Backup di Azure è un servizio nativo Microsoft Azure e il caso verrà instradato in modo errato.

### <a name="links-to-relevant-commvault-documentation"></a>Collegamenti alla documentazione di CommVault pertinente

Documentazione di CommVault che fornisce ulteriori dettagli:

[Guida dell'utente di CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[Guida all'architettura di Azure CommVault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>Collegamento all'offerta del Marketplace

È anche possibile continuare a usare la soluzione CommVault che si conosce e si considera attendibile per proteggere i carichi di lavoro in esecuzione in Azure. CommVault ha semplificato la distribuzione della soluzione in Azure e la protezione di macchine virtuali di Azure e molti altri servizi di Azure.

[Distribuire CommVault tramite Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Foglio dati di Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[Elenco completo delle funzionalità e dei servizi di Azure supportati](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[Come usare CommVault per proteggere SAP HANA in Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Passaggi successivi

Esplorare le risorse aggiuntive in questi siti Web esterni per ottenere informazioni sugli scenari di utilizzo specializzati:

[Usare CommVault per eseguire la migrazione di server e applicazioni in Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[Proteggi SAP in Azure con CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[Proteggere Office365 con CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)
