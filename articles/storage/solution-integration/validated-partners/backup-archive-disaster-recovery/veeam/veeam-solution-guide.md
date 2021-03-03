---
title: Eseguire il backup dei dati in Azure con Veeam
titleSuffix: Azure Blob Storage Docs
description: La pagina Web fornisce una panoramica dei fattori da prendere in considerazione e i passaggi da seguire per sfruttare Azure come destinazione di archiviazione e il percorso di ripristino per il backup e il ripristino di Veeam
keywords: Veeam,, backup su cloud, backup, backup in Azure, ripristino di emergenza, continuità aziendale
author: Karl Rautenstrauch
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: 9f07703d23c3be6e842a54ba4bb0d46467ccb71d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745950"
---
# <a name="backup-to-azure-with-veeam"></a>Eseguire il backup in Azure con Veeam

Questo articolo fornisce una guida all'integrazione di un'infrastruttura Veeam con archiviazione BLOB di Azure. Sono inclusi i prerequisiti, i principi di archiviazione di Azure, l'implementazione e le linee guida operative. Questo articolo descrive solo l'uso di Azure come destinazione di backup fuori sede e un sito di ripristino in caso di emergenza, che impedisce il funzionamento normale nel sito primario. Veeam offre anche una soluzione RTO più bassa, la replica Veeam, che consente di disporre di una VM di standby pronta per l'avvio e il ripristino più rapidamente in caso di emergenza e protezione delle risorse in un ambiente di produzione di Azure. Veeam dedica inoltre strumenti per il backup delle risorse di Azure e Office 365. Queste funzionalità non rientrano nell'ambito di questo documento. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Architettura di riferimento per le distribuzioni da sito locale ad Azure e In-Azure

![Architettura di riferimento da Veeam ad Azure](../media/veeam-architecture.png)

La distribuzione di Veeam esistente può essere facilmente integrata con Azure aggiungendo un account di archiviazione di Azure o più account come archivio di backup cloud. Veeam consente inoltre di ripristinare i backup dall'ambiente locale all'interno di Azure, offrendo un sito di ripristino su richiesta in Azure.

## <a name="veeam-interoperability-matrix"></a>Matrice di interoperabilità Veeam
| Carico di lavoro | Archiviazione BLOB e GPv2 | Supporto del livello di accesso sporadico | Supporto del livello di archiviazione | Supporto per la famiglia Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VM/dati locali | V10A | V10A | N/D | 10a |
| Macchine virtuali di Azure | V10A | V10A | N/D | 10a |
| BLOB Azure | V10A | V10A | N/D | 10a |
| File di Azure | V10A | V10A | N/D | 10a | 

> [!Note]
Veeam Backup and Replication supporta l'API REST solo per Azure Data Box e pertanto Azure Data Box Disk non è supportata. In Veeam V11 è previsto il supporto per il livello archivio dell'archiviazione BLOB di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Una piccola pianificazione iniziale assicurerà di unire in join le classificazioni dei molti clienti felici che usano Azure come destinazione di backup e sito di ripristino fuori sede.

### <a name="are-you-new-to-azure"></a>Sei un nuovo utente di Azure?

Microsoft offre un Framework da seguire per iniziare a usare Azure. Il [Framework di adozione del cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( \) è un approccio dettagliato alla trasformazione digitale aziendale e una guida completa alla pianificazione di un'adozione del cloud di livello produzione. Il CAF include una guida dettagliata per l' [installazione di Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) per i nuovi utenti in Azure, che consente di iniziare a usarla in modo rapido e sicuro ed è possibile trovare una versione interattiva nel [portale di Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Sono disponibili architetture di esempio e procedure consigliate specifiche per la distribuzione di applicazioni e risorse di formazione gratuite che consentono di accedere al percorso delle competenze di Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Prendere in considerazione la rete tra la località e Azure

Che si tratti di sfruttare le risorse cloud per eseguire produzione, test e sviluppo o come destinazione di backup e sito di ripristino, è importante comprendere le esigenze di larghezza di banda per il seeding del backup iniziale e per i trasferimenti giornalieri in corso.

Azure Data Box fornisce un mezzo per trasferire la linea di base di backup iniziale in Azure senza richiedere una larghezza di banda aggiuntiva se il trasferimento della linea di base è stimato per un tempo superiore a quello consentito. È possibile sfruttare il Trasferimento dati estimatore quando si crea un account di archiviazione per stimare il tempo necessario per trasferire il backup iniziale.

![Strumento di stima Trasferimento dati di archiviazione di Azure](../media/az-storage-transfer.png)

Tenere presente che sarà necessaria una capacità di rete sufficiente per supportare i trasferimenti di dati giornalieri nella finestra di trasferimento richiesta (finestra di backup) senza alcun effetto sulle applicazioni di produzione. In questa sezione vengono illustrati gli strumenti e le tecniche disponibili per valutare le esigenze di rete.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Come è possibile determinare la quantità di larghezza di banda necessaria?

Sono disponibili più opzioni di valutazione per determinare la frequenza di modifica e le dimensioni totali del set di backup per il trasferimento iniziale della linea di base in Azure. Di seguito sono riportati alcuni esempi di strumenti di valutazione e creazione di report, ad esempio:
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
|2 TB di nuovi dati scritti al giorno x 30 giorni     |$72 nelle transazioni          |
|Totale stimato mensile     |$1628,48         |
|---------|---------|
|Ripristino di una volta da 5 TB a locale su Internet pubblico   | $527,26         |

> [!Note]
Questa stima è stata generata nel calcolatore dei prezzi di Azure usando i prezzi con pagamento in base al consumo degli Stati Uniti orientali e si basa sul valore predefinito Veeam delle dimensioni del blocco 256 KB per i trasferimenti WAN. Questo esempio potrebbe non essere applicabile ai propri requisiti.

## <a name="implementation-and-operational-guidance"></a>Guida operativa e di implementazione

Questa sezione fornisce una breve guida all'aggiunta di archiviazione di Azure a una distribuzione Veeam locale. Per indicazioni dettagliate e considerazioni sulla pianificazione, è consigliabile esaminare la [Guida al backup di Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Aprire il portale di Azure e cercare "account di archiviazione" oppure fare clic sull'icona servizi predefiniti.

      ![Portale di Azure](../media/azure-portal.png)

      ![Account di archiviazione nel portale di Azure](../media/locate-storage-account.png)

2. Scegliere di aggiungere un account e selezionare o creare un gruppo di risorse, specificare un nome univoco, scegliere l'area, selezionare le prestazioni "standard", lasciare sempre il tipo di account "Storage V2", scegliere il livello di replica, che soddisfa i contratti di sicurezza e il livello predefinito usato dal software di backup. Un account di archiviazione di Azure rende disponibili livelli ad accesso frequente, sporadico e archivio in un singolo account e i criteri Veeam consentono di sfruttare più livelli per gestire in modo efficiente il ciclo di vita dei dati. Procedere con il passaggio successivo. 
    
      ![Creazione di un account di archiviazione](../media/account-create-1.png)

3. Usare le opzioni di rete predefinite per ora e passare alla "protezione dei dati". Qui è possibile scegliere di abilitare l'eliminazione temporanea, che consente di ripristinare un file di backup eliminato accidentalmente entro il periodo di conservazione definito e offre protezione da eliminazioni accidentali o dannose. 
    ![Creazione di un account di archiviazione-parte 2](../media/account-create-2.png)

4. Si consiglia quindi di usare le impostazioni predefinite della schermata "avanzate" per eseguire il backup nei casi d'uso di Azure.

    ![Creazione di un account di archiviazione-parte 3](../media/account-create-3.png) 

5. Aggiungere i tag per l'organizzazione se si sfruttano i tag e si crea l'account. A questo punto si ha a disposizione petabyte di archiviazione su richiesta.

6. Per aggiungere l'account all'ambiente Veeam è necessario eseguire due passaggi rapidi. Passare all'account creato nel portale di Azure e selezionare "contenitori" nel menu "servizio BLOB" nel pannello del portale. Aggiungere un nuovo contenitore e scegliere un nome significativo. Passare quindi all'elemento "chiavi di accesso" in "Impostazioni" e copiare il nome dell'account di archiviazione e una delle due chiavi di accesso. Nei passaggi successivi sono necessari il nome del contenitore, il nome dell'account e la chiave di accesso.

    ![Creazione di un contenitore](../media/container-b.png)
    
    ![Acquisisci informazioni sull'account](../media/access-key.png)
    
    > [!Note]
Veeam Backup and Replication offre opzioni aggiuntive per la connessione ad Azure. Per il caso d'uso di questo articolo, l'uso di Microsoft Azure archiviazione BLOB come destinazione di backup, usando il metodo precedente è la procedura consigliata.

7. ***(Facoltativo)*** È possibile aggiungere ulteriori livelli di sicurezza alla distribuzione.

     1. Configurare l'accesso in base al ruolo per limitare gli utenti che possono apportare modifiche all'account di archiviazione. [Per altre informazioni, vedi qui](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Limitare l'accesso all'account a segmenti di rete specifici con [firewall di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) per evitare tentativi di accesso dall'esterno della rete aziendale.

    ![Firewall di archiviazione](../media/storage-firewall.png) 

    1. Impostare un [blocco di eliminazione](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) sull'account per evitare l'eliminazione accidentale dell'account di archiviazione.

    ![Blocco risorsa ](../media/resource-lock.png) 1.) Configurare [procedure di sicurezza consigliate](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)aggiuntive. 
8. Nella console di gestione di backup e replica di Veeam passare a "infrastruttura di backup"--> fare clic con il pulsante destro del mouse nel riquadro Panoramica e selezionare "Aggiungi repository di backup" per aprire la configurazione guidata. Nella finestra di dialogo selezionare archiviazione oggetti, > Microsoft Azure archiviazione BLOB, > archiviazione BLOB di Azure.
    
    ![Schermata della procedura guidata del repository Veeam](../media/veeam-repo-a.png)

    ![Schermata b della procedura guidata del repository Veeam](../media/veeam-repo-b.png)

    ![Schermata c della procedura guidata del repository Veeam](../media/veeam-repo-c.png)

9. Specificare quindi un nome e una descrizione del nuovo repository BLOB Microsoft Azure.
    
    ![Schermata d della procedura guidata del repository Veeam](../media/veeam-repo-d.png)

10. Nel passaggio successivo aggiungere le credenziali per accedere all'account di archiviazione di Azure. Selezionare "Archiviazione di Microsoft Azure account" in cloud Credential Manager, immettere il nome dell'account di archiviazione e la chiave di accesso. Selezionare Azure Global nel selettore di area e qualsiasi server gateway, se applicabile.
    
    ![Schermata della procedura guidata del repository Veeam e](../media/veeam-repo-e.png)

> [!Note]
Se si sceglie di non usare un server gateway Veeam, assicurarsi che tutti gli extent del repository con scalabilità orizzontale abbiano accesso diretto a Internet.

11. Nel registro contenitori selezionare il contenitore di archiviazione di Azure e selezionare o creare una cartella in cui archiviare i backup. È anche possibile definire un limite flessibile per la capacità complessiva di archiviazione che verrà usata da Veeam (scelta consigliata). Esaminare le informazioni visualizzate nella sezione Riepilogo e completare lo strumento di configurazione. Il nuovo repository può quindi essere selezionato nella configurazione del processo di backup.

    ![Schermata f della procedura guidata del repository Veeam](../media/veeam-repo-f.png)
    
    ![Schermata della procedura guidata del repository Veeam g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Avvisi e monitoraggio delle prestazioni di Azure

È consigliabile monitorare le risorse di Azure e la capacità di Veeam di utilizzarle come si farebbe con qualsiasi destinazione di archiviazione su cui si basa per archiviare i backup. Una combinazione di monitoraggio di Azure e delle funzionalità di monitoraggio di Veeam, ad esempio la scheda statistiche nel nodo processi della console di gestione di Veeam o opzioni più avanzate come Veeam ONE reporter, consentirà di garantire l'integrità dell'ambiente.

#### <a name="microsoft-azure-portal"></a>Portale di Microsoft Azure

Microsoft Azure offre una soluzione di monitoraggio affidabile sotto forma di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). È possibile [configurare monitoraggio di Azure](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) per tenere traccia di capacità, transazioni, disponibilità, autenticazione e altro ancora di archiviazione di Azure. Il riferimento completo delle metriche registrate è disponibile [qui](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Alcune metriche utili per tenere traccia sono BlobCapacity: per assicurarsi di rimanere al di sotto del limite massimo di [capacità dell'account di archiviazione](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), in ingresso e in uscita, per tenere traccia della quantità di dati scritti e letti dall'account di archiviazione di Azure e SuccessE2ELatency per tenere traccia del tempo di round trip per le richieste da e verso archiviazione di Azure e MediaAgent. 

È anche possibile [creare avvisi di log](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) per tenere traccia dell'integrità del servizio di archiviazione di Azure e visualizzare il [Dashboard dello stato di Azure](https://status.azure.com/status) in qualsiasi momento.

#### <a name="veeam-reporting"></a>Reporting Veeam

[Configurazione di Veeam ONE Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Avvisi di backup e replica di Veeam](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Come aprire i casi di supporto

Quando è necessaria assistenza per la soluzione di backup in Azure, è consigliabile aprire un caso con Veeam e Azure, in modo che le organizzazioni di supporto possano interagire in modo collaborativo, se necessario.

#### <a name="how-to-open-a-case-with-veeam"></a>Come aprire un caso con Veeam

Passare al [portale di supporto clienti di Veeam](https://www.veeam.com/support.html), accedere e aprire un caso.

Se è necessario comprendere le opzioni di supporto disponibili in Veeam, vedere Criteri di [supporto clienti Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf) .

È anche possibile chiamare in per aprire un caso:

[Numeri di supporto in tutto il mondo](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Come aprire un caso con il team di supporto di Azure

All'interno del [portale di Azure](https://portal.azure.com) cercare "supporto" nella barra di ricerca nella parte superiore del portale e scegliere "+ nuova richiesta di supporto". 
> [!Note]
Quando si apre un caso, è necessario richiedere assistenza per "archiviazione di Azure" o "rete di Azure" e **non** "backup di Azure". Backup di Azure è un servizio nativo Microsoft Azure e il caso verrà instradato in modo errato.

### <a name="links-to-relevant-veeam-documentation"></a>Collegamenti alla documentazione Veeam pertinente

Documentazione di Veeam che fornisce ulteriori dettagli:

[Guida dell'utente di Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Guida all'architettura di Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Collegamento all'offerta del Marketplace

Puoi anche continuare a usare la soluzione Veeam che conosci e ritenere attendibile per proteggere i tuoi carichi di lavoro in esecuzione in Azure. Veeam ha semplificato la distribuzione della soluzione in Azure e la protezione di macchine virtuali di Azure e molti altri servizi di Azure.

[Distribuire Veeam B&R tramite Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Foglio dati di Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Passaggi successivi

Esplorare le risorse aggiuntive in questi siti Web esterni per ottenere informazioni sugli scenari di utilizzo specializzati:

[Video sulle procedure di Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Documenti tecnici Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Knowledge base e domande frequenti su Veeam](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
