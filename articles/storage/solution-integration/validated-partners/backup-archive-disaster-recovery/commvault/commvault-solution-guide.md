---
title: Eseguire il backup dei dati in Azure con CommVault
titleSuffix: Azure Storage
description: Fornisce una panoramica dei fattori da prendere in considerazione e i passaggi da seguire per usare Azure come destinazione di archiviazione e percorso di ripristino per il backup e il ripristino di CommVault completi
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589889"
---
# <a name="backup-to-azure-with-commvault"></a>Eseguire il backup in Azure con CommVault

Questo articolo consente di integrare un'infrastruttura CommVault con archiviazione BLOB di Azure. Sono inclusi i prerequisiti, le considerazioni, l'implementazione e le linee guida operative. Questo articolo illustra come usare Azure come destinazione di backup fuori sede e un sito di ripristino in caso di emergenza, che impedisce il funzionamento normale nel sito primario.

> [!NOTE]
> CommVault offre una soluzione RTO (Lower Recovery Time Objective), CommVault Live Sync. Questa soluzione consente di avere una VM di standby che consente di eseguire il ripristino più rapidamente in caso di emergenza in un ambiente di produzione di Azure. Queste funzionalità non rientrano nell'ambito di questo documento.

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma seguente fornisce un'architettura di riferimento per le distribuzioni da sito locale ad Azure e in Azure.

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

Una piccola pianificazione iniziale consentirà di usare Azure come destinazione di backup e sito di ripristino fuori sede.

### <a name="get-started-with-azure"></a>Introduzione ad Azure

Microsoft offre un Framework da seguire per iniziare a usare Azure. Il [Framework di adozione cloud](/azure/architecture/cloud-adoption/) è un approccio dettagliato alla trasformazione digitale aziendale e una guida completa alla pianificazione di un'adozione del cloud di livello produzione. Il CAF include una guida dettagliata per l' [installazione di Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) che consente di diventare operativi in modo rapido e sicuro. È possibile trovare una versione interattiva nel [portale di Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Sono disponibili architetture di esempio, procedure consigliate specifiche per la distribuzione di applicazioni e risorse di formazione gratuite che consentono di accedere al percorso delle competenze di Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Prendere in considerazione la rete tra la località e Azure

Sia che si tratti di usare risorse cloud per eseguire attività di produzione, test e sviluppo o come destinazione di backup e sito di ripristino, è importante comprendere le esigenze di larghezza di banda per il seeding del backup iniziale e per i trasferimenti giornalieri in corso.

Azure Data Box fornisce un modo per trasferire la linea di base di backup iniziale in Azure senza richiedere una maggiore larghezza di banda. Questa operazione è utile se il trasferimento di base è stimato per un tempo superiore a quello consentito. È possibile usare lo strumento di stima Trasferimento dati quando si crea un account di archiviazione per stimare il tempo necessario per trasferire il backup iniziale.

![Mostra lo strumento di stima per il trasferimento dei dati di archiviazione di Azure nel portale.](../media/az-storage-transfer.png)

Tenere presente che sarà necessaria una capacità di rete sufficiente per supportare i trasferimenti di dati giornalieri nella finestra di trasferimento richiesta (finestra di backup) senza alcun effetto sulle applicazioni di produzione. In questa sezione vengono illustrati gli strumenti e le tecniche disponibili per valutare le esigenze di rete.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Determinare la quantità di larghezza di banda necessaria

Per determinare la quantità di larghezza di banda necessaria, usare le risorse seguenti:

- Report dal software di backup.
- CommVault fornisce report standard per determinare la [frequenza di modifica](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) e le [dimensioni totali del set di backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) per il trasferimento iniziale della linea di base in Azure.
- Strumenti di valutazione e reporting indipendenti dal software di backup, ad esempio:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Determinare la larghezza di banda Internet non utilizzata

È importante conoscere la quantità di larghezza di banda non utilizzata *, o la capacità, disponibile* quotidianamente. Questo consente di valutare se è possibile soddisfare gli obiettivi di:

- tempo iniziale da caricare quando non si usa Azure Data Box per il seeding offline
- completamento dei backup giornalieri in base al tasso di modifica identificato in precedenza e alla finestra di backup

Usare i metodi seguenti per identificare lo spazio disponibile per la larghezza di banda che i backup in Azure possono utilizzare.

- Se sei un cliente di Azure ExpressRoute esistente, Visualizza l' [utilizzo del circuito](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) nel portale di Azure.
- Contattare il provider di servizi Internet. Devono essere in grado di condividere report che mostrano l'utilizzo giornaliero e mensile esistente.
- Sono disponibili diversi strumenti che consentono di misurare l'utilizzo monitorando il traffico di rete a livello di router/Commuter. Tra queste sono incluse:
  - [Pack di Bandwidth Bandwidth Analyzer](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [PRTG Paessler](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Scegliere le opzioni di archiviazione corrette

Quando si usa Azure come destinazione di backup, si userà l' [archiviazione BLOB di Azure](../../../../blobs/storage-blobs-introduction.md). L'archiviazione BLOB è la soluzione di archiviazione oggetti Microsoft. L'archiviazione BLOB è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati, ovvero dati non conformi a un modello di dati o a una definizione. Archiviazione di Azure è inoltre durevole, a disponibilità elevata, sicura e scalabile. È possibile selezionare la risorsa di archiviazione appropriata per il carico di lavoro per fornire il [livello di resilienza](../../../../common/storage-redundancy.md) per soddisfare i contratti di contratto interni. L'archiviazione BLOB è un servizio con pagamento in base al consumo. Viene [addebitato mensilmente](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) la quantità di dati archiviati, l'accesso a tali dati e, nel caso dei livelli di archiviazione e di accesso sporadico, un periodo di conservazione minimo necessario. Nelle tabelle seguenti vengono riepilogate le opzioni di resilienza e di suddivisione in livelli applicabili ai dati di backup.

**Opzioni di resilienza dell'archiviazione BLOB:**

|  |Con ridondanza locale  |Zone-redundant  |Con ridondanza geografica  |Con ridondanza geografica  |
|---------|---------|---------|---------|---------|
|**Numero di copie valide**     | 3         | 3         | 6         | 6 |
|**n. di zone di disponibilità**     | 1         | 3         | 2         | 4 |
|**numero di aree**     | 1         | 1         | 2         | 2 |
|**Failover manuale nell'area secondaria**     | N/D         | N/D         | Sì         | Sì |

**Livelli di archiviazione BLOB:**

|  | Livello critico   |Livello ad accesso sporadico   | Livello archivio |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilità** | 99,9%         | 99%         | Offline      |
| **Addebiti per l'utilizzo** | Costi di archiviazione più elevati, minore accesso e costi delle transazioni | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Conservazione minima dei dati obbligatoria**| N/D | 30 giorni | 180 giorni |
| **Latenza (tempo per il primo byte)** | Millisecondi | Millisecondi | Ore |

#### <a name="sample-backup-to-azure-cost-model"></a>Backup di esempio nel modello di costo di Azure

Il pagamento in base al consumo può essere scoraggiante per i clienti che non hanno familiarità con il cloud. Quando si paga solo la capacità usata, si paga anche per le transazioni (lettura e scrittura) ed è in [uscita per i dati](https://azure.microsoft.com/pricing/details/bandwidth/) letti nell'ambiente locale quando si usa il [piano dati locale di Azure Express Route Direct o Express Route illimitata](https://azure.microsoft.com/pricing/details/expressroute/) per i dati in uscita da Azure. È possibile usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per eseguire analisi di simulazione. È possibile basare l'analisi sui prezzi di listino o sui [prezzi di capacità riservata di archiviazione di Azure](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), che possono offrire un risparmio fino al 38%. Ecco un esempio di esercitazione sui prezzi per modellare il costo mensile del backup in Azure. Questo è solo un esempio. *I prezzi possono variare a causa di attività non acquisite qui.*

|Fattore di costo  |Costo mensile  |
|---------|---------|
|100 TB di dati di backup nell'archiviazione ad accesso sporadico     |$1556,48         |
|2 TB di nuovi dati scritti al giorno x 30 giorni     |$39 nelle transazioni          |
|Totale stimato mensile     |$1595,48         |
|---------|---------|
|Ripristino di una volta da 5 TB a locale su Internet pubblico   | $491,26         |

> [!NOTE]
> Questa stima è stata generata nel calcolatore dei prezzi di Azure usando i prezzi con pagamento in base al consumo degli Stati Uniti orientali e si basa sul valore predefinito CommVault di 32 MB di dimensioni del sottoblocco, che genera 65.536 richieste PUT (transazioni di scrittura) al giorno. Questo esempio potrebbe non essere applicabile ai propri requisiti.

## <a name="implementation-guidance"></a>Indicazioni relative all'implementazione

Questa sezione fornisce una breve guida per aggiungere archiviazione di Azure a una distribuzione CommVault locale. Per istruzioni dettagliate e considerazioni sulla pianificazione, vedere la [Guida all'architettura del cloud pubblico di CommVault per Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Aprire il portale di Azure e cercare gli **account di archiviazione**. È anche possibile fare clic sull'icona **account di archiviazione** predefiniti.

    ![Mostra come aggiungere un account di archiviazione nell'portale di Azure.](../media/azure-portal.png)
  
    ![Mostra la posizione in cui è stata digitata l'archiviazione nella casella di ricerca del portale di Azure.](../media/locate-storage-account.png)

2. Selezionare **Crea** per aggiungere un account. Selezionare o creare un gruppo di risorse, specificare un nome univoco, scegliere l'area, selezionare prestazioni **standard** , lasciare sempre il tipo di account di **archiviazione V2**, scegliere il livello di replica che soddisfa i contratti di sicurezza e il livello predefinito a cui si applica il software di backup. Un account di archiviazione di Azure rende disponibili livelli ad accesso frequente, sporadico e archivio in un singolo account e i criteri di CommVault consentono di usare più livelli per gestire in modo efficiente il ciclo di vita dei dati.

    ![Mostra le impostazioni dell'account di archiviazione nel portale](../media/account-create-1.png)

3. Mantieni le opzioni di rete predefinite per il momento e passa alla **protezione dei dati**. Qui è possibile scegliere di abilitare l'eliminazione temporanea, che consente di ripristinare un file di backup eliminato accidentalmente entro il periodo di conservazione definito e offre protezione da eliminazioni accidentali o dannose.

    ![Mostra le impostazioni di protezione dei dati nel portale.](../media/account-create-2.png)

4. Si consiglia quindi di usare le impostazioni predefinite della schermata **Avanzate** per il backup nei casi d'uso di Azure.

    ![Mostra la scheda Impostazioni avanzate nel portale.](../media/account-create-3.png)

5. Aggiungere i tag per l'organizzazione se si usa l'assegnazione di tag e si crea l'account.

6. Per aggiungere l'account all'ambiente CommVault, è necessario eseguire due passaggi rapidi. Passare all'account creato nel portale di Azure e selezionare **contenitori** dal menu **servizio BLOB** . Aggiungere un contenitore e scegliere un nome significativo. Passare quindi all'elemento **chiavi di accesso** in **Impostazioni** e copiare il nome dell' **account di archiviazione** e una delle due chiavi di accesso. Nei passaggi successivi saranno necessari il nome del contenitore, il nome dell'account e la chiave di accesso.

    ![Mostra la creazione del contenitore nel portale.](../media/container.png)

    ![Mostra le impostazioni della chiave di accesso nel portale.](../media/access-key.png)

7. (*Facoltativo*) È possibile aggiungere ulteriori livelli di sicurezza alla distribuzione.

    1. Configurare l'accesso in base al ruolo per limitare gli utenti che possono apportare modifiche all'account di archiviazione. Per ulteriori informazioni, vedere [ruoli predefiniti per le operazioni di gestione](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Limitare l'accesso all'account a segmenti di rete specifici con [le impostazioni del firewall di archiviazione](../../../../common/storage-network-security.md) per impedire i tentativi di accesso dall'esterno della rete aziendale.

        ![Mostra le impostazioni del firewall di archiviazione nel portale.](../media/storage-firewall.png)

    1. Impostare un [blocco di eliminazione](../../../../../azure-resource-manager/management/lock-resources.md) sull'account per evitare l'eliminazione accidentale dell'account di archiviazione.

        ![Mostra l'impostazione di un blocco di eliminazione nel portale.](../media/resource-lock.png)

    1. Configurare [procedure di sicurezza consigliate](../../../../../storage/blobs/security-recommendations.md)aggiuntive.

1. Nel centro comandi di CommVault passare a **Gestisci**  ->    ->  **Gestione credenziali** di sicurezza. Scegliere un **account cloud**, **tipo** di fornitore **archiviazione di Microsoft Azure**, selezionare il **MediaAgent**, che trasferirà i dati da e verso Azure, aggiungere il nome dell'account di archiviazione e la chiave di accesso.

    ![Mostra l'aggiunta di credenziali nel centro comandi di CommVault.](../media/commvault-credential.png)

9. Passare quindi a cloud di **archiviazione**  ->   nel centro comandi di CommVault. Scegliere **Aggiungi**. Immettere un nome descrittivo per l'account di archiviazione e quindi selezionare **archiviazione di Microsoft Azure** dall'elenco **tipo** . Selezionare un server agente multimediale da usare per trasferire i backup in archiviazione di Azure. Aggiungere il contenitore creato, scegliere il livello di archiviazione da usare nell'account di archiviazione di Azure e selezionare le credenziali create nel passaggio #8. Infine, scegliere se trasferire o meno i backup deduplicati e un percorso per il database di deduplicazione.

     ![Screenshot dell'interfaccia utente di aggiunta cloud di CommVault. Nel menu a discesa archivio è selezionato * * Archive * *.](../media/commvault-add-storage.png)

10. Infine, aggiungere la nuova risorsa di archiviazione di Azure a un piano esistente o nuovo nel centro comandi di CommVault tramite **Gestisci**  ->  **piani** come destinazione di backup.

    ![Screenshot dell'interfaccia utente del centro comandi di CommVault. Nella finestra di spostamento a sinistra, sotto * * Gestisci * *, * * piani * * è selezionato.](../media/commvault-plan.png)

11. *(Facoltativo)* Se si prevede di usare Azure come sito di ripristino o CommVault per eseguire la migrazione di server e applicazioni in Azure, è consigliabile distribuire un proxy VSA in Azure. [Qui](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)è possibile trovare istruzioni dettagliate.

## <a name="operational-guidance"></a>Informazioni operative

### <a name="azure-alerts-and-performance-monitoring"></a>Avvisi e monitoraggio delle prestazioni di Azure

È consigliabile monitorare le risorse di Azure e la capacità di CommVault di utilizzarle come si farebbe con qualsiasi destinazione di archiviazione su cui si basa per archiviare i backup. Una combinazione di monitoraggio di Azure e il monitoraggio del centro comandi CommVault consentiranno di garantire l'integrità dell'ambiente.

#### <a name="azure-portal"></a>Portale di Azure

Azure offre una soluzione di monitoraggio affidabile sotto forma di [monitoraggio di Azure](../../../../../azure-monitor/essentials/monitor-azure-resource.md). È possibile [configurare monitoraggio di Azure](../../../../blobs/monitor-blob-storage.md) per tenere traccia di capacità, transazioni, disponibilità, autenticazione e altro ancora di archiviazione di Azure. È possibile trovare il riferimento completo delle metriche raccolte [qui](../../../../blobs/monitor-blob-storage-reference.md). Alcune metriche utili per tenere traccia sono BlobCapacity: per assicurarsi di rimanere al di sotto del limite massimo di [capacità dell'account di archiviazione](../../../../common/scalability-targets-standard-account.md), in ingresso e in uscita, per tenere traccia della quantità di dati scritti e letti dall'account di archiviazione di Azure e SuccessE2ELatency per tenere traccia del tempo di round trip per le richieste da e verso archiviazione di Azure e MediaAgent.

È anche possibile [creare avvisi di log](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) per tenere traccia dell'integrità del servizio di archiviazione di Azure e visualizzare il [Dashboard dello stato di Azure](https://status.azure.com/status) in qualsiasi momento.

#### <a name="commvault-command-center"></a>Centro comandi CommVault

- [Creare un avviso per i pool di archiviazione cloud](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Per informazioni su dove è possibile visualizzare i report di prestazioni, il completamento del processo e avviare la risoluzione dei problemi di base, vedere [Dashboard](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Come aprire i casi di supporto

Quando è necessario assistenza per la soluzione di backup in Azure, è necessario aprire un caso sia con CommVault che con Azure. Ciò consente alle organizzazioni di supporto di collaborare, se necessario.

#### <a name="to-open-a-case-with-commvault"></a>Per aprire un case con CommVault

Nel [sito di supporto di CommVault](https://www.commvault.com/support), accedere e aprire un caso.

Per informazioni sulle opzioni del contratto di supporto disponibili, vedere [Opzioni di supporto per CommVault](https://ma.commvault.com/support)

È anche possibile chiamare per aprire un caso o raggiungere il supporto CommVault tramite posta elettronica:

- Numero verde: + 1 877-780-3077
- [Numeri di supporto in tutto il mondo](https://ma.commvault.com/Support/TelephoneSupport)
- [Supporto CommVault di posta elettronica](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Per aprire un caso con Azure

Nella [portale di Azure](https://portal.azure.com) cercare **supporto** nella barra di ricerca nella parte superiore. Selezionare **Guida e supporto**  ->  **nuova richiesta di supporto**.

> [!NOTE]
> Quando si apre un caso specifico, è necessario avere assistenza per archiviazione di Azure o rete di Azure. Non specificare backup di Azure. Backup di Azure è il nome di un servizio di Azure e il tuo caso verrà instradato in modo errato.

### <a name="links-to-relevant-commvault-documentation"></a>Collegamenti alla documentazione di CommVault pertinente

Per ulteriori informazioni, vedere la documentazione di CommVault seguente:

- [Guida dell'utente di CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Guida all'architettura di Azure CommVault](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Offerte del Marketplace

CommVault ha semplificato la distribuzione della soluzione in Azure per proteggere le macchine virtuali di Azure e molti altri servizi di Azure. Per altre informazioni, vedere i riferimenti seguenti:

- [Distribuire CommVault tramite Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [CommVault per il foglio dati di Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Elenco di servizi e funzionalità di Azure supportati in CommVault](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Come usare CommVault per proteggere SAP HANA in Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli scenari di utilizzo specializzati, vedere le risorse aggiuntive di CommVault.

- [Usare CommVault per eseguire la migrazione di server e applicazioni in Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Proteggi SAP in Azure con CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Proteggere Office365 con CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)