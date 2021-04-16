---
title: Eseguire il backup dei dati in Azure con Commvault
titleSuffix: Azure Storage
description: Offre una panoramica dei fattori da considerare e dei passaggi da seguire per usare Azure come destinazione di archiviazione e percorso di ripristino per il backup e il ripristino completi di Commvault
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484779"
---
# <a name="backup-to-azure-with-commvault"></a>Backup in Azure con Commvault

Questo articolo consente di integrare un'infrastruttura Commvault con l'archiviazione BLOB di Azure. Include prerequisiti, considerazioni, implementazione e linee guida operative. Questo articolo descrive l'uso di Azure come destinazione di backup fuori sede e di un sito di ripristino in caso di emergenza, che impedisce il normale funzionamento all'interno del sito primario.

> [!NOTE]
> Commvault offre una soluzione RTO (Recovery Time Objective) più bassa, Commvault Live Sync. Questa soluzione consente di avere una macchina virtuale di standby che consente di eseguire il ripristino più rapidamente in caso di emergenza in un ambiente di produzione di Azure. Queste funzionalità non sono all'interno dell'ambito di questo documento.

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma seguente offre un'architettura di riferimento per le distribuzioni locali in Azure e in Azure.

![Architettura di riferimento di Commvault in Azure](../media/commvault-diagram.png)

La distribuzione commvault esistente può essere facilmente integrata con Azure aggiungendo un account di archiviazione di Azure o più account come destinazione di archiviazione cloud. Commvault consente anche di ripristinare i backup dall'ambiente locale all'interno di Azure offrendo un sito di ripristino su richiesta in Azure.

## <a name="commvault-interoperability-matrix"></a>Matrice di interoperabilità commvault

| Carico di lavoro | GPv2 e archiviazione BLOB | Supporto del livello ad accesso cool | Supporto del livello di archiviazione | Data Box family |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Macchine virtuali/dati locali | v11.5 | v11.5 | v11.10 | v11.10 |
| Macchine virtuali di Azure | v11.5 | v11.5 | v11.5 | N/D |
| BLOB Azure | v11.6 | v11.6 | v11.6 | N/D |
| File di Azure | v11.6 | v11.6 | v11.6 | N/D |

## <a name="before-you-begin"></a>Prima di iniziare

Una piccola pianificazione iniziale consente di usare Azure come destinazione di backup e sito di ripristino fuori sede.

### <a name="get-started-with-azure"></a>Introduzione ad Azure

Microsoft offre un framework da seguire per iniziare a usare Azure. Il [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) è un approccio dettagliato alla trasformazione digitale aziendale e una guida completa alla pianificazione di un'adozione del cloud di livello di produzione. CAF include una guida dettagliata alla configurazione di [Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) per iniziare a essere operativo in modo rapido e sicuro. È possibile trovare una versione interattiva nel [portale di Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Sono disponibili architetture di esempio, procedure consigliate specifiche per la distribuzione di applicazioni e risorse di formazione gratuite per trovare il percorso verso l'esperienza di Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Considerare la rete tra la località e Azure

Sia che si utilizzino risorse cloud per eseguire attività di produzione, test e sviluppo o come destinazione di backup e sito di ripristino, è importante comprendere le esigenze di larghezza di banda per il seeding iniziale del backup e per i trasferimenti quotidiani in corso.

Azure Data Box un modo per trasferire la baseline di backup iniziale in Azure senza richiedere più larghezza di banda. Ciò è utile se si stima che il trasferimento di base richiede più tempo di quanto si possa tollerare. È possibile usare lo strumento di stima del trasferimento dati quando si crea un account di archiviazione per stimare il tempo necessario per trasferire il backup iniziale.

![Mostra la stima Archiviazione di Azure trasferimento dei dati nel portale.](../media/az-storage-transfer.png)

Tenere presente che sarà necessaria una capacità di rete sufficiente per supportare i trasferimenti di dati giornalieri all'interno della finestra di trasferimento richiesta (finestra di backup) senza influire sulle applicazioni di produzione. In questa sezione vengono descritti gli strumenti e le tecniche disponibili per valutare le esigenze di rete.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Determinare la larghezza di banda necessaria

Per determinare la larghezza di banda necessaria, usare le risorse seguenti:

- Report dal software di backup.
- Commvault fornisce report standard per determinare la [frequenza delle](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) modifiche e le dimensioni totali del set di [backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) per il trasferimento iniziale della baseline in Azure.
- Strumenti di valutazione e creazione di report indipendenti dal software di backup, ad esempio:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Determinare la larghezza di banda Internet non utilizzata

È importante sapere quanta larghezza di banda in genere non utilizzata (o *headroom)* è disponibile quotidianamente. Ciò consente di valutare se è possibile soddisfare gli obiettivi per:

- tempo iniziale per il caricamento quando non si usa Azure Data Box per il seeding offline
- completamento dei backup giornalieri in base alla frequenza delle modifiche identificata in precedenza e alla finestra di backup

Usare i metodi seguenti per identificare la larghezza di banda disponibile per i backup in Azure.

- Se si è un cliente Azure ExpressRoute, visualizzare l'utilizzo [del circuito](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) nel portale di Azure.
- Contattare l'ISP. Devono essere in grado di condividere report che mostrano l'utilizzo giornaliero e mensile esistente.
- Esistono diversi strumenti che consentono di misurare l'utilizzo monitorando il traffico di rete a livello di router/commutatore. Tra queste sono incluse:
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Scegliere le opzioni di archiviazione giuste

Quando si usa Azure come destinazione di backup, si userà [l'archiviazione BLOB di Azure.](../../../../blobs/storage-blobs-introduction.md) L'archiviazione BLOB è la soluzione di archiviazione oggetti di Microsoft. L'archiviazione BLOB è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati, ovvero dati che non rispettano alcun modello o definizione di dati. Inoltre, Archiviazione di Azure è durevole, a disponibilità elevata, sicura e scalabile. È possibile selezionare l'archiviazione giusta per il carico di lavoro per fornire il [livello di resilienza](../../../../common/storage-redundancy.md) per soddisfare i contratti di servizio interni. L'archiviazione BLOB è un servizio con pagamento in base all'utilizzo. Viene addebitato [mensilmente](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) la quantità di dati archiviati, l'accesso ai dati e, nel caso dei livelli di archiviazione e ad accesso fisso, un periodo di conservazione minimo richiesto. Le opzioni di resilienza e di livelli applicabili ai dati di backup sono riepilogate nelle tabelle seguenti.

**Opzioni di resilienza dell'archiviazione BLOB:**

|  |Ridondanza locale  |Zone-redundant  |Ridondanza geografica  |Ridondanza della zona geografica  |
|---------|---------|---------|---------|---------|
|**Numero effettivo di copie**     | 3         | 3         | 6         | 6 |
|**N. di zone di disponibilità**     | 1         | 3         | 2         | 4 |
|**N. di aree**     | 1         | 1         | 2         | 2 |
|**Failover manuale nell'area secondaria**     | N/D         | N/D         | Sì         | Sì |

**Livelli di archiviazione BLOB:**

|  | Livello ad accesso più caldo   |Livello ad accesso cool   | Livello archivio |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilità** | 99,9%         | 99%         | Offline      |
| **Addebiti per l'utilizzo** | Costi di archiviazione più elevati, costi di accesso più bassi e costi di transazione | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Conservazione minima dei dati richiesta**| N/D | 30 giorni | 180 giorni |
| **Latenza (tempo fino al primo byte)** | Millisecondi | Millisecondi | Ore |

#### <a name="sample-backup-to-azure-cost-model"></a>Esempio di backup nel modello dei costi di Azure

Con il pagamento in base all'utilizzo può essere ossessione per i clienti che non hanno di nuovo il cloud. Mentre si paga solo per la capacità usata, si pagano anche [](https://azure.microsoft.com/pricing/details/bandwidth/) le transazioni (lettura e/o scrittura) e i dati in uscita per la lettura dei dati nell'ambiente locale quando è in uso un piano dati senza limiti di [Azure Express Route](https://azure.microsoft.com/pricing/details/expressroute/) o locale diretto di Express Route in cui sono inclusi i dati in uscita da Azure. È possibile usare il [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per eseguire l'analisi di simulazione. È possibile basare l'analisi sui prezzi di listino o sui prezzi Archiviazione di Azure [capacità](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)riservata, che possono offrire un risparmio fino al 38%. Ecco un esempio di esercizio sui prezzi per modellare il costo mensile del backup in Azure. Questo è solo un esempio. *I prezzi possono variare a causa delle attività non acquisite qui.*

|Fattore di costo  |Costo mensile  |
|---------|---------|
|100 TB di dati di backup nell'archiviazione ad accesso freddo     |$1556,48         |
|2 TB di nuovi dati scritti al giorno x 30 giorni     |$ 39 nelle transazioni          |
|Totale stimato mensile     |$1595,48         |
|---------|---------|
|Ripristino una sola volta di 5 TB in locale su Internet pubblico   | $491,26         |

> [!NOTE]
> Questa stima è stata generata nel calcolatore prezzi di Azure usando i prezzi con pagamento in base al consumo negli Stati Uniti orientali ed è basata sul valore predefinito commvault di dimensioni del blocco secondario di 32 MB che genera 65.536 richieste PUT (transazioni di scrittura) al giorno. Questo esempio potrebbe non essere applicabile ai requisiti.

## <a name="implementation-guidance"></a>Indicazioni relative all'implementazione

Questa sezione fornisce una breve guida per l'aggiunta di Archiviazione di Azure a una distribuzione Commvault locale. Per indicazioni dettagliate e considerazioni sulla pianificazione, vedere la guida [commvault Public Cloud Architecture per Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Aprire il portale di Azure e cercare gli account **di archiviazione**. È anche possibile fare clic sull'icona **account di archiviazione** predefinita.

    ![Mostra l'aggiunta di un account di archiviazione nel portale di Azure.](../media/azure-portal.png)
  
    ![Mostra dove è stata digitata l'archiviazione nella casella di ricerca del portale di Azure.](../media/locate-storage-account.png)

2. Selezionare **Crea** per aggiungere un account. Selezionare o creare un gruppo di risorse, specificare un nome univoco, scegliere l'area, selezionare **Prestazioni standard,** lasciare sempre il tipo di account **storage V2,** scegliere il livello di replica che soddisfa i contratti di servizio e il livello predefinito che verrà applicato dal software di backup. Un account Archiviazione di Azure rende disponibili i livelli di accesso ad accesso più caldo, ad accesso più freddo e archivio all'interno di un singolo account e i criteri di Commvault consentono di usare più livelli per gestire in modo efficace il ciclo di vita dei dati.

    ![Mostra le impostazioni dell'account di archiviazione nel portale](../media/account-create-1.png)

3. Mantenere le opzioni di rete predefinite per il momento e passare a **Protezione dati.** In questo caso, è possibile scegliere di abilitare l'eliminazione temporaneamente, che consente di ripristinare un file di backup eliminato accidentalmente entro il periodo di conservazione definito e offre protezione da eliminazioni accidentali o dannose.

    ![Mostra le impostazioni di protezione dei dati nel portale.](../media/account-create-2.png)

4. È quindi consigliabile usare le impostazioni predefinite della schermata **Avanzate** per il backup nei casi d'uso di Azure.

    ![Mostra la scheda Impostazioni avanzate nel portale.](../media/account-create-3.png)

5. Aggiungere tag per l'organizzazione se si usa l'assegnazione di tag e creare l'account.

6. Per poter aggiungere l'account all'ambiente Commvault sono necessari due passaggi rapidi. Passare all'account creato nel portale di Azure e selezionare **Contenitori** nel menu **Servizio BLOB.** Aggiungere un contenitore e scegliere un nome significativo. Passare quindi all'elemento **Chiavi di accesso** in Impostazioni **e** copiare il nome dell'account **di archiviazione** e una delle due chiavi di accesso. Il nome del contenitore, il nome dell'account e la chiave di accesso saranno necessari nei passaggi successivi.

    ![Mostra la creazione del contenitore nel portale.](../media/container.png)

    ![Mostra le impostazioni della chiave di accesso nel portale.](../media/access-key.png)

7. (*Facoltativo*) È possibile aggiungere altri livelli di sicurezza alla distribuzione.

    1. Configurare l'accesso in base al ruolo per limitare gli utenti che possono apportare modifiche all'account di archiviazione. Per altre informazioni, vedere [Ruoli predefiniti per le operazioni di gestione.](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)
    1. Limitare l'accesso all'account a segmenti di rete specifici con impostazioni [del firewall](../../../../common/storage-network-security.md) di archiviazione per impedire tentativi di accesso dall'esterno della rete aziendale.

        ![Mostra le impostazioni del firewall di archiviazione nel portale.](../media/storage-firewall.png)

    1. Impostare un [blocco di eliminazione per](../../../../../azure-resource-manager/management/lock-resources.md) l'account per impedire l'eliminazione accidentale dell'account di archiviazione.

        ![Mostra l'impostazione di un blocco di eliminazione nel portale.](../media/resource-lock.png)

    1. Configurare procedure [consigliate per la sicurezza aggiuntive.](../../../../../storage/blobs/security-recommendations.md)

1. In Commvault Command Center passare a **Manage**  ->  **Security**  ->  **Gestione credenziali**. Scegliere un **account cloud,** **tipo di** fornitore **di Archiviazione di Microsoft Azure,** selezionare **MediaAgent**, che trasferirà i dati da e verso Azure, aggiungere il nome dell'account di archiviazione e la chiave di accesso.

    ![Mostra l'aggiunta di credenziali in Commvault Command Center.](../media/commvault-credential.png)

9. Passare quindi a **Cloud**  ->  **di archiviazione** in Commvault Command Center. Scegliere **aggiungi**. Immettere un nome descrittivo per l'account di archiviazione e quindi **selezionare** Archiviazione di Microsoft Azure dall'elenco **Tipo.** Selezionare un server dell'agente multimediale da usare per trasferire i backup Archiviazione di Azure. Aggiungere il contenitore creato, scegliere il livello di archiviazione da usare nell'account di archiviazione di Azure e selezionare le credenziali create nel passaggio #8. Infine, scegliere se trasferire o meno i backup deduplicati e un percorso per il database di deduplicazione.

     ![Screenshot dell'interfaccia utente Aggiungi cloud di Commvault. Nel menu a discesa Archivio è selezionato **Archivio**.](../media/commvault-add-storage.png)

10. Infine, aggiungere la nuova risorsa Archiviazione di Azure a un piano esistente o nuovo in Commvault Command Center tramite **Gestisci** piani  ->   come destinazione di backup.

    ![Screenshot dell'interfaccia utente di Commvault Command Center. Nel riquadro di spostamento a sinistra, in **Gestisci**, è selezionato **Piani**.](../media/commvault-plan.png)

11. *(Facoltativo)* Se si prevede di usare Azure come sito di ripristino o Commvault per eseguire la migrazione di server e applicazioni ad Azure, è consigliabile distribuire un proxy VSA in Azure. Le istruzioni dettagliate sono disponibili [qui.](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)

## <a name="operational-guidance"></a>Informazioni operative

### <a name="azure-alerts-and-performance-monitoring"></a>Avvisi di Azure e monitoraggio delle prestazioni

È consigliabile monitorare sia le risorse di Azure che la capacità di Commvault di sfruttarle come si farebbe con qualsiasi destinazione di archiviazione su cui ci si basa per archiviare i backup. Una combinazione di Monitoraggio di Azure e il monitoraggio di Commvault Command Center consentono di mantenere integro l'ambiente.

#### <a name="azure-portal"></a>Portale di Azure

Azure offre una soluzione di monitoraggio affidabile sotto forma di [Monitoraggio di Azure](../../../../../azure-monitor/essentials/monitor-azure-resource.md). È possibile [configurare Monitoraggio di Azure](../../../../blobs/monitor-blob-storage.md) per tenere traccia Archiviazione di Azure capacità, transazioni, disponibilità, autenticazione e altro ancora. È possibile trovare il riferimento completo alle metriche raccolte [qui.](../../../../blobs/monitor-blob-storage-reference.md) Alcune metriche utili da tenere traccia sono BlobCapacity, per assicurarsi di rimanere al di sotto del limite massimo di capacità [dell'account](../../../../common/scalability-targets-standard-account.md)di archiviazione, in ingresso e in uscita, per tenere traccia della quantità di dati scritti e letti dall'account Archiviazione di Azure e SuccessE2ELatency, per tenere traccia del tempo di roundtrip per le richieste da e verso Archiviazione di Azure e MediaAgent.

È anche possibile creare [avvisi di log per](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) tenere traccia Archiviazione di Azure'integrità del servizio e visualizzare il dashboard dello stato di [Azure](https://status.azure.com/status) in qualsiasi momento.

#### <a name="commvault-command-center"></a>Commvault Command Center

- [Creare un avviso per i pool di archiviazione cloud](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Per informazioni su dove è possibile visualizzare i report sulle prestazioni, il completamento del processo e iniziare la risoluzione dei problemi di base, vedere [Dashboard.](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)

### <a name="how-to-open-support-cases"></a>Come aprire i casi di supporto

Quando è necessaria assistenza per la soluzione di backup in Azure, è consigliabile aprire un caso con Commvault e Azure. Ciò consente alle organizzazioni di supporto di collaborare, se necessario.

#### <a name="to-open-a-case-with-commvault"></a>Per aprire un caso con Commvault

Nel sito [di supporto di Commvault](https://www.commvault.com/support)accedere e aprire un caso.

Per comprendere le opzioni del contratto di supporto disponibili, vedere Opzioni di supporto [di Commvault](https://ma.commvault.com/support)

È anche possibile chiamare per aprire un caso o contattare il supporto commvault tramite posta elettronica:

- Numero verde: +1 877-780-3077
- [Numeri di supporto a livello mondiale](https://ma.commvault.com/Support/TelephoneSupport)
- [Inviare un messaggio di posta elettronica al supporto di Commvault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Per aprire un caso con Azure

Nel riquadro [portale di Azure](https://portal.azure.com) supporto **nella** barra di ricerca nella parte superiore. Selezionare **Guida e supporto per** Nuova richiesta di  ->  **supporto**.

> [!NOTE]
> Quando si apre un caso, è necessario richiedere assistenza per Archiviazione di Azure o Rete di Azure. Non specificare Backup di Azure. Backup di Azure è il nome di un servizio di Azure e il caso verrà instradato in modo non corretto.

### <a name="links-to-relevant-commvault-documentation"></a>Collegamenti alla documentazione di Commvault pertinente

Per altri dettagli, vedere la documentazione di Commvault seguente:

- [Guida dell'utente di Commvault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Guida all'architettura di Commvault Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Offerte del Marketplace

Commvault ha reso più semplice distribuire la propria soluzione in Azure per proteggere le macchine virtuali di Azure e molti altri servizi di Azure. Per altre informazioni, vedere i riferimenti seguenti:

- [Distribuire Commvault tramite Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Foglio dati di Commvault per Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Elenco di commvault di funzionalità e servizi di Azure supportati](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Come usare Commvault per proteggere SAP HANA in Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli scenari di utilizzo specializzati, vedere queste risorse aggiuntive di Commvault.

- [Usare Commvault per eseguire la migrazione di server e applicazioni ad Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Proteggere SAP in Azure con Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Proteggere Office365 con Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)