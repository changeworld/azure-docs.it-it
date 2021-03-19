---
title: Eseguire il backup dei dati in Azure con Veeam
titleSuffix: Azure Storage
description: Fornisce una panoramica dei fattori da considerare e i passaggi da seguire per usare Azure come destinazione di archiviazione e il percorso di ripristino per il backup e il ripristino di Veeam
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589906"
---
# <a name="backup-to-azure-with-veeam"></a>Eseguire il backup in Azure con Veeam

Questo articolo consente di integrare un'infrastruttura Veeam con archiviazione BLOB di Azure. Sono inclusi i prerequisiti, le considerazioni, l'implementazione e le linee guida operative. Questo articolo illustra come usare Azure come destinazione di backup fuori sede e un sito di ripristino in caso di emergenza, che impedisce il funzionamento normale nel sito primario.

> [!NOTE]
> Veeam offre anche una soluzione RTO (Recovery Time Objective) più bassa, la replica Veeam. Questa soluzione consente di avere una VM di standby che consente di eseguire il ripristino più rapidamente in caso di emergenza in un ambiente di produzione di Azure. Veeam dispone inoltre di strumenti dedicati per eseguire il backup delle risorse di Azure e Office 365. Queste funzionalità non rientrano nell'ambito di questo documento.

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma seguente fornisce un'architettura di riferimento per le distribuzioni da sito locale ad Azure e in Azure.

![Diagramma dell'architettura di riferimento da Veeam a Azure.](../media/veeam-architecture.png)

La distribuzione di Veeam esistente può essere facilmente integrata con Azure aggiungendo un account di archiviazione di Azure o più account come archivio di backup cloud. Veeam consente inoltre di ripristinare i backup dall'ambiente locale all'interno di Azure, offrendo un sito di ripristino su richiesta in Azure.

## <a name="veeam-interoperability-matrix"></a>Matrice di interoperabilità Veeam

| Carico di lavoro | Archiviazione BLOB e GPv2 | Supporto del livello di accesso sporadico | Supporto del livello di archiviazione | Supporto per la famiglia Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VM/dati locali | V10A | V10A | N/D | 10a<sup>*</sup> |
| Macchine virtuali di Azure | V10A | V10A | N/D | 10a<sup>*</sup> |
| BLOB Azure | V10A | V10A | N/D | 10a<sup>*</sup> |
| File di Azure | V10A | V10A | N/D | 10a<sup>*</sup> |

<sup>*</sup>Il backup e la replica Veeam supportano l'API REST solo per Azure Data Box. Pertanto, Azure Data Box Disk non è supportata.

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

Sono disponibili più opzioni di valutazione per determinare la frequenza di modifica e le dimensioni totali del set di backup per il trasferimento iniziale della linea di base in Azure. Di seguito sono riportati alcuni esempi di strumenti di valutazione e creazione di report:

- [MiTrend](https://mitrend.com/)
- [APT sono](https://www.veritas.com/insights/aptare-it-analytics)
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
|**n. area** s     | 1         | 1         | 2         | 2 |
|**Failover manuale nell'area secondaria**     | N/D         | N/D         | Sì         | Sì |

**Livelli di archiviazione BLOB:**

|  | Livello critico   |Livello ad accesso sporadico   | Livello archivio |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilità** | 99,9%         | 99%         | Offline      |
| **Addebiti per l'utilizzo** | Costi di archiviazione più elevati, minore accesso e costi delle transazioni | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Conservazione minima dei dati obbligatoria** | N/D | 30 giorni | 180 giorni |
| **Latenza (tempo per il primo byte)** | Millisecondi | Millisecondi | Ore |

#### <a name="sample-backup-to-azure-cost-model"></a>Backup di esempio nel modello di costo di Azure

Il pagamento in base al consumo può essere scoraggiante per i clienti che non hanno familiarità con il cloud. Quando si paga solo la capacità usata, si paga anche per le transazioni (lettura e scrittura) ed è in [uscita per i dati](https://azure.microsoft.com/pricing/details/bandwidth/) letti nell'ambiente locale quando si usa il [piano dati locale di Azure Express Route Direct o Express Route illimitata](https://azure.microsoft.com/pricing/details/expressroute/) per i dati in uscita da Azure. È possibile usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per eseguire analisi di simulazione. È possibile basare l'analisi sui prezzi di listino o sui [prezzi di capacità riservata di archiviazione di Azure](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), che possono offrire un risparmio fino al 38%. Ecco un esempio di esercitazione sui prezzi per modellare il costo mensile del backup in Azure. Questo è solo un esempio. *I prezzi possono variare a causa di attività non acquisite qui.*

|Fattore di costo  |Costo mensile  |
|---------|---------|
|100 TB di dati di backup nell'archiviazione ad accesso sporadico     |$1556,48         |
|2 TB di nuovi dati scritti al giorno x 30 giorni     |$72 nelle transazioni          |
|Totale stimato mensile     |$1628,48         |
|---------|---------|
|Ripristino di una volta da 5 TB a locale su Internet pubblico   | $527,26         |

> [!Note]
> Questa stima è stata generata nel calcolatore dei prezzi di Azure usando i prezzi con pagamento in base al consumo degli Stati Uniti orientali e si basa sul valore predefinito Veeam di dimensioni del blocco di 256 KB per i trasferimenti WAN. Questo esempio potrebbe non essere applicabile ai propri requisiti.

## <a name="implementation-guidance"></a>Indicazioni relative all'implementazione

Questa sezione fornisce una breve guida per aggiungere archiviazione di Azure a una distribuzione locale di Veeam. Per istruzioni dettagliate e considerazioni sulla pianificazione, vedere la [Guida al backup di Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Aprire il portale di Azure e cercare gli **account di archiviazione**. È anche possibile fare clic sull'icona di servizio predefinita.

      ![Mostra come aggiungere un account di archiviazione nell'portale di Azure.](../media/azure-portal.png)

      ![Mostra la posizione in cui è stata digitata l'archiviazione nella casella di ricerca del portale di Azure.](../media/locate-storage-account.png)

2. Selezionare **Crea** per aggiungere un account. Selezionare o creare un gruppo di risorse, specificare un nome univoco, scegliere l'area, selezionare prestazioni **standard** , lasciare sempre il tipo di account di **archiviazione V2**, scegliere il livello di replica che soddisfa i contratti di sicurezza e il livello predefinito a cui si applica il software di backup. Un account di archiviazione di Azure rende disponibili livelli ad accesso frequente, sporadico e archivio in un singolo account e i criteri Veeam consentono di usare più livelli per gestire efficacemente il ciclo di vita dei dati.

    ![Mostra le impostazioni dell'account di archiviazione nel portale](../media/account-create-1.png)

3. Mantieni le opzioni di rete predefinite per il momento e passa alla **protezione dei dati**. Qui è possibile scegliere di abilitare l'eliminazione temporanea, che consente di ripristinare un file di backup eliminato accidentalmente entro il periodo di conservazione definito e offre protezione da eliminazioni accidentali o dannose.

    ![Mostra le impostazioni di protezione dei dati nel portale.](../media/account-create-2.png)

4. Si consiglia quindi di usare le impostazioni predefinite della schermata **Avanzate** per il backup nei casi d'uso di Azure.

    ![Mostra la scheda Impostazioni avanzate nel portale.](../media/account-create-3.png)

5. Aggiungere i tag per l'organizzazione se si usa l'assegnazione di tag e si crea l'account.

6. Per aggiungere l'account all'ambiente Veeam è necessario eseguire due passaggi rapidi. Passare all'account creato nel portale di Azure e selezionare **contenitori** dal menu **servizio BLOB** . Aggiungere un contenitore e scegliere un nome significativo. Passare quindi all'elemento **chiavi di accesso** in **Impostazioni** e copiare il nome dell' **account di archiviazione** e una delle due chiavi di accesso. Nei passaggi successivi sarà necessario il nome del contenitore, il nome dell'account e la chiave di accesso.

    ![Mostra la creazione del contenitore nel portale.](../media/container-b.png)

    ![Mostra le impostazioni della chiave di accesso nel portale.](../media/access-key.png)

    > [!Note]
    > Veeam Backup and Replication offre opzioni aggiuntive per la connessione ad Azure. Per il caso d'uso di questo articolo, l'uso di Microsoft Azure archiviazione BLOB come destinazione di backup, usando il metodo precedente è la procedura consigliata.

7. *(Facoltativo)* È possibile aggiungere più livelli di sicurezza alla distribuzione.

     1. Configurare l'accesso in base al ruolo per limitare gli utenti che possono apportare modifiche all'account di archiviazione. Per ulteriori informazioni, vedere [ruoli predefiniti per le operazioni di gestione](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Limitare l'accesso all'account a segmenti di rete specifici con [le impostazioni del firewall di archiviazione](../../../../common/storage-network-security.md) per impedire i tentativi di accesso dall'esterno della rete aziendale.

        ![Mostra le impostazioni del firewall di archiviazione nel portale.](../media/storage-firewall.png)

     1. Impostare un [blocco di eliminazione](../../../../../azure-resource-manager/management/lock-resources.md) sull'account per evitare l'eliminazione accidentale dell'account di archiviazione.

        ![Blocco delle risorse](../media/resource-lock.png)

     1. Configurare [procedure di sicurezza consigliate](../../../../../storage/blobs/security-recommendations.md)aggiuntive.

8. Nella console di gestione di backup e replica di Veeam passare a **infrastruttura di backup** -> fare clic con il pulsante destro del mouse nel riquadro Panoramica e selezionare **Aggiungi archivio di backup** per aprire la configurazione guidata. Nella finestra di dialogo selezionare **archiviazione oggetti** Microsoft Azure archiviazione BLOB di  ->    ->  **Azure archiviazione BLOB di Azure**.

    ![Mostra la selezione dell'archiviazione di oggetti nella procedura guidata del repository Veeam.](../media/veeam-repo-a.png)

    ![Mostra la selezione di Microsoft Azure archiviazione BLOB nella procedura guidata del repository Veeam.](../media/veeam-repo-b.png)

    ![Mostra la selezione dell'archiviazione BLOB di Azure nella procedura guidata del repository Veeam.](../media/veeam-repo-c.png)

9. Specificare quindi un nome e una descrizione del nuovo repository di archiviazione BLOB.

    ![Mostra come digitare un nome per il repository nella procedura guidata del repository Veeam.](../media/veeam-repo-d.png)

10. Nel passaggio successivo aggiungere le credenziali per accedere all'account di archiviazione di Azure. Selezionare **archiviazione di Microsoft Azure account** in Gestione credenziali cloud e immettere il nome e la chiave di accesso dell'account di archiviazione. Selezionare **Azure Global** nel selettore di area e qualsiasi server gateway, se applicabile.

    ![Mostra come specificare un account nella procedura guidata del repository Veeam.](../media/veeam-repo-e.png)

    > [!Note]
    > Se si sceglie di non usare un server gateway Veeam, assicurarsi che tutti gli extent del repository con scalabilità orizzontale abbiano accesso diretto a Internet.

11. Nel registro contenitori selezionare il contenitore di archiviazione di Azure e selezionare o creare una cartella in cui archiviare i backup. È anche possibile definire un limite flessibile per la capacità complessiva di archiviazione che verrà usata da Veeam, operazione consigliata. Esaminare le informazioni visualizzate nella sezione Riepilogo e completare lo strumento di configurazione. È ora possibile selezionare il nuovo repository nella configurazione del processo di backup.

    ![Mostra come specificare un contenitore nella procedura guidata del repository Veeam.](../media/veeam-repo-f.png)

    ![Mostra la creazione di una cartella nella procedura guidata del repository Veeam.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Informazioni operative

### <a name="azure-alerts-and-performance-monitoring"></a>Avvisi e monitoraggio delle prestazioni di Azure

È consigliabile monitorare le risorse di Azure e la capacità di Veeam di utilizzarle come si farebbe con qualsiasi destinazione di archiviazione su cui si basa per archiviare i backup. Una combinazione di monitoraggio di Azure e delle funzionalità di monitoraggio di Veeam (la scheda **statistiche** nel nodo **processi** della console di gestione di Veeam o opzioni più avanzate come Veeam ONE reporter) consentirà di garantire l'integrità dell'ambiente.

#### <a name="azure-portal"></a>Portale di Azure

Azure offre una soluzione di monitoraggio affidabile sotto forma di [monitoraggio di Azure](../../../../../azure-monitor/essentials/monitor-azure-resource.md). È possibile [configurare monitoraggio di Azure](../../../../blobs/monitor-blob-storage.md) per tenere traccia di capacità, transazioni, disponibilità, autenticazione e altro ancora di archiviazione di Azure. Il riferimento completo delle metriche registrate è disponibile [qui](../../../../blobs/monitor-blob-storage-reference.md). Alcune metriche utili per tenere traccia sono BlobCapacity: per assicurarsi di rimanere al di sotto del limite massimo di [capacità dell'account di archiviazione](../../../../common/scalability-targets-standard-account.md), in ingresso e in uscita, per tenere traccia della quantità di dati scritti e letti dall'account di archiviazione di Azure e SuccessE2ELatency per tenere traccia del tempo di round trip per le richieste da e verso archiviazione di Azure e MediaAgent.

È anche possibile [creare avvisi di log](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) per tenere traccia dell'integrità del servizio di archiviazione di Azure e visualizzare il [Dashboard dello stato di Azure](https://status.azure.com/status) in qualsiasi momento.

#### <a name="veeam-reporting"></a>Reporting Veeam

- [Configurare Veeam ONE Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Avvisi di backup e replica di Veeam](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Come aprire i casi di supporto

Quando è necessario assistenza per la soluzione di backup in Azure, è necessario aprire un caso con Veeam e Azure. Ciò consente alle organizzazioni di supporto di collaborare, se necessario.

#### <a name="to-open-a-case-with-veeam"></a>Per aprire un caso con Veeam

Nel [sito del supporto clienti di Veeam](https://www.veeam.com/support.html), accedere e aprire un caso.

Per informazioni sulle opzioni di supporto disponibili in Veeam, vedere i [criteri di supporto clienti di Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

È anche possibile chiamare per aprire un caso: [numeri di supporto in tutto il mondo](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Per aprire un caso con Azure

Nella [portale di Azure](https://portal.azure.com) cercare **supporto** nella barra di ricerca nella parte superiore. Selezionare **Guida e supporto**  ->  **nuova richiesta di supporto**.

> [!NOTE]
> Quando si apre un caso specifico, è necessario avere assistenza per archiviazione di Azure o rete di Azure. Non specificare backup di Azure. Backup di Azure è il nome di un servizio di Azure e il tuo caso verrà instradato in modo errato.

### <a name="links-to-relevant-veeam-documentation"></a>Collegamenti alla documentazione Veeam pertinente

Per altri dettagli, vedere la documentazione di Veeam seguente:

- [Guida dell'utente di Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Guida all'architettura di Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Offerte del Marketplace

Puoi continuare a usare la soluzione Veeam che conosci e considera attendibile per proteggere i tuoi carichi di lavoro in esecuzione in Azure. Veeam ha semplificato la distribuzione della soluzione in Azure e la protezione di macchine virtuali di Azure e molti altri servizi di Azure.

- [Distribuire il backup Veeam & la replica tramite Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Sito Web di backup e ripristino di Veeam per Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli scenari di utilizzo specializzati, vedere le risorse seguenti nel sito web Veeam:

- [Video sulle procedure di Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Documenti tecnici Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Knowledge base e domande frequenti su Veeam](https://www.veeam.com/knowledge-base.html?ad=menu-resources)