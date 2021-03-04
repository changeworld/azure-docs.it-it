---
title: Valutazioni di Azure SQL in Azure Migrate strumento di individuazione e valutazione
description: Informazioni sulle valutazioni SQL di Azure in Azure Migrate strumento di individuazione e valutazione
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: d1ea328575cf07a22ce39549c34d5cd21e916427
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054765"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Panoramica della valutazione (migrazione a SQL di Azure)

Questo articolo fornisce una panoramica delle valutazioni per la migrazione di istanze di SQL Server locali da un ambiente VMware a database SQL di Azure o a istanze gestite usando lo [strumento Azure migrate: Discovery and Assessment](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-assessment-tool).

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale** . Se si dispone già di un progetto in Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

## <a name="whats-an-assessment"></a>Che cos'è una valutazione?
Una valutazione con lo strumento di individuazione e valutazione è uno snapshot temporizzato dei dati e misura la conformità e stima l'effetto della migrazione dei server locali in Azure.

## <a name="types-of-assessments"></a>Tipi di valutazioni

Sono disponibili tre tipi di valutazione che è possibile creare utilizzando lo strumento Azure Migrate: Discovery and Assessment.

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. È possibile valutare i server locali nell'ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione alle macchine virtuali di Azure con questo tipo di valutazione.
**SQL di Azure** | Valutazioni per la migrazione dei server SQL locali dall'ambiente VMware al database SQL di Azure o al Istanza gestita SQL di Azure.
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

Una valutazione di Azure SQL fornisce un criterio di ridimensionamento:

**Criteri di dimensionamento** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni in base ai dati sulle prestazioni raccolti | La configurazione di SQL di Azure si basa sui dati sulle prestazioni delle istanze e dei database SQL, che includono l'utilizzo della CPU, l'utilizzo della memoria, IOPS (file di dati e di log), la velocità effettiva e la latenza delle operazioni di i/o.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Ricerca per categorie valutare i server SQL locali?

È possibile valutare le istanze di SQL Server locali usando i dati di configurazione e utilizzo raccolti da un'appliance di Azure Migrate Lightweight. L'appliance individua le istanze e i database di SQL Server locali e invia i dati di configurazione e delle prestazioni a Azure Migrate. [Altre informazioni](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Ricerca per categorie valutare con l'appliance?
Se si sta distribuendo un'appliance Azure Migrate per individuare i server locali, seguire questa procedura:
1.  Configurare Azure e l'ambiente locale per lavorare con Azure Migrate.
2.  Per la prima valutazione, creare un progetto Azure Migrate e aggiungervi lo strumento Azure Migrate: Discovery and Assessment.
3.  Distribuire un'appliance Azure Migrate leggera. L'appliance individua continuamente i server locali e invia i dati di configurazione e delle prestazioni a Azure Migrate. Distribuire l'appliance come macchina virtuale o server fisico. Non è necessario installare alcun elemento sui server che si desidera valutare.

Quando l'appliance inizia l'individuazione, è possibile raccogliere i server che si desidera valutare in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **Azure SQL**.

Seguire questa esercitazione per valutare le [istanze di SQL Server](tutorial-assess-sql.md) per provare questa procedura.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>In che modo l'appliance calcola i dati sulle prestazioni per le istanze e i database SQL?

L'appliance raccoglie i dati sulle prestazioni per le impostazioni di calcolo con i seguenti passaggi:
1. L'appliance raccoglie un punto di esempio in tempo reale. Per SQL Server, un punto di esempio viene raccolto ogni 30 secondi.
2. Il dispositivo aggrega i punti dati di esempio raccolti ogni 30 secondi in 10 minuti. Per creare il punto dati, l'appliance seleziona i valori di picco di tutti gli esempi. Invia il numero massimo, la media e la varianza per ogni contatore in Azure.
3. Azure Migrate archivia tutti i punti dati di 10 minuti per l'ultimo mese.
4. Quando si crea una valutazione, Azure Migrate identifica il punto dati appropriato da usare per rightsizing. L'identificazione si basa sui valori percentile per la cronologia delle prestazioni e l'utilizzo percentile.
    - Se, ad esempio, la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è il 95 ° percentile, la valutazione Ordina i punti di campionamento di 10 minuti per l'ultima settimana. Li ordina in ordine crescente e sceglie il valore del 95 ° percentile per rightsizing.
    - Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se è stato selezionato il 99 ° percentile.
    - Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, selezionare il 99 ° percentile per l'utilizzo percentile.
5. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per le metriche raccolte dal dispositivo:
    - Utilizzo CPU (%)
    - Utilizzo memoria (%)
    - Lettura i/o e scrittura i/o (file di dati e di log)
    - Lettura di MB/s e scrittura MB/s (velocità effettiva)
    - Latenza delle operazioni di i/o

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Quali proprietà vengono usate per creare e personalizzare una valutazione SQL di Azure?

Ecco cosa è incluso nelle proprietà di valutazione di SQL Azure:

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione. Le raccomandazioni per la configurazione e i costi di Azure SQL si basano sul percorso specificato.
**Tipo di distribuzione di destinazione** | Il tipo di distribuzione di destinazione in cui si vuole eseguire la valutazione: selezionare **consigliato**. Se si vuole Azure migrate valutare la conformità dei server SQL per la migrazione al database SQL di Azure mi e Azure, e consigliare l'opzione di distribuzione di destinazione più adatta, il livello di destinazione, la configurazione di SQL Azure e le stime mensili. Selezionare il database **SQL di Azure**, se si vuole valutare i server SQL per la migrazione solo ai database SQL di Azure ed esaminare il livello di destinazione, la configurazione del database SQL di Azure e le stime mensili. Selezionare **Azure SQL mi**, se si vuole valutare i server SQL per la migrazione solo ai database SQL di Azure ed esaminare il livello di destinazione, la configurazione di Azure SQL mi e le stime mensili.
**Capacità riservata** | Specifica la capacità riservata in modo da tenere conto delle stime dei costi nella valutazione. Se si seleziona un'opzione di capacità riservata, non è possibile specificare "discount (%)".
**Criteri di dimensionamento** | Questa proprietà viene usata per dimensionare correttamente la configurazione di SQL Azure. Il valore predefinito è **basato sulle prestazioni** , ovvero la valutazione raccoglierà le metriche delle prestazioni SQL Server istanze e database per consigliare una istanza gestita SQL di Azure e/o una raccomandazione per il livello/configurazione del database SQL di Azure di dimensioni ottimali.
**Cronologia delle prestazioni** | Cronologia prestazioni specifica la durata utilizzata per la valutazione dei dati sulle prestazioni.
**Utilizzo percentile** | L'utilizzo percentile specifica il valore percentile dell'esempio di prestazioni utilizzato per rightsizing.
**Fattore di comfort** | Buffer utilizzato durante la valutazione. In questo modo vengono rilevati problemi come l'utilizzo stagionale, la cronologia delle prestazioni brevi e probabilmente aumenti nell'utilizzo futuro. Ad esempio, un'istanza di 10 core con utilizzo del 20% normalmente produce un'istanza a due core. Con un fattore di comfort di 2,0, il risultato è invece un'istanza a quattro core.
**Programma offerta/licenza** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Attualmente è possibile scegliere solo da sviluppo/test con pagamento in base al consumo e con pagamento in base al consumo. Si noti che è possibile usufruire di uno sconto aggiuntivo applicando la capacità riservata e Vantaggio Azure Hybrid sull'offerta con pagamento in base al consumo.
**Livello di servizio** | L'opzione del livello di servizio più appropriata per soddisfare le esigenze aziendali per la migrazione al database SQL di Azure e/o Istanza gestita SQL di Azure: selezionare **consigliata** se si desidera Azure migrate di consigliare il livello di servizio più adatto per i server. Può trattarsi di un utilizzo generico o di un business critical. Selezionare **per utilizzo generico** se si vuole una configurazione SQL di Azure progettata per carichi di lavoro orientati al budget. [Altre informazioni](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) Selezionare **business critical** se si vuole una configurazione SQL di Azure progettata per carichi di lavoro a bassa latenza con resilienza elevata a errori e failover rapidi. [Altre informazioni](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Valuta** | Valuta di fatturazione per l'account.
**Sconto (%)** | Tutti gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%.
**Vantaggio Azure Hybrid** | Specifica se si dispone già di una licenza di SQL Server. In tal caso, è possibile applicare la Vantaggio Azure Hybrid quando si impostano le licenze in Azure con il software SQL Server Assurance attivo.

[Esaminare le procedure consigliate per la](best-practices-assessment.md) creazione di una valutazione con Azure migrate.

## <a name="calculate-readiness"></a>Calcola conformità

> [!NOTE]
> La valutazione include solo i database in stato online. Nel caso in cui il database si trovi in un altro stato, la valutazione ignora la preparazione, il dimensionamento e il calcolo dei costi per tali database. Se si desidera valutare tali database, modificare lo stato del database e ricalcolare la valutazione in un determinato periodo di tempo.

### <a name="azure-sql-readiness"></a>Conformità a SQL di Azure

La conformità di SQL Azure per le istanze e i database SQL si basa su un controllo di compatibilità delle funzionalità con il database SQL di Azure e Istanza gestita SQL di Azure:
- La valutazione SQL di Azure considera le funzionalità dell'istanza SQL Server attualmente utilizzate dai carichi di lavoro SQL Server di origine (processi di SQL Agent, server collegati e così via) e gli schemi di database utente (tabelle, viste, trigger, stored procedure e così via) per identificare i problemi di compatibilità.
- Se non sono stati rilevati problemi di compatibilità, la conformità è contrassegnata come **pronta** per il tipo di distribuzione di destinazione (database SQL di azure o istanza gestita SQL di Azure)
- Se sono presenti problemi di compatibilità non critici, ad esempio funzionalità danneggiate o non supportate che non bloccano la migrazione a un tipo di distribuzione di destinazione specifico, l'idoneità viene contrassegnata come **pronta** (icona con collegamento ipertestuale e informazioni blu) con i dettagli dell' **avviso** e le linee guida consigliate per la correzione.
- Se si verificano problemi di compatibilità che potrebbero bloccare la migrazione a un tipo di distribuzione di destinazione specifico, la conformità viene contrassegnata come **non pronta** con i dettagli del **problema** e le linee guida consigliate per la correzione.
    - Se in un'istanza SQL è presente anche un database non pronto per un tipo di distribuzione di destinazione specifico, l'istanza viene contrassegnata come **non pronta** per quel tipo di distribuzione.
- Se l'individuazione è ancora in corso o si verificano problemi di individuazione per un'istanza o un database SQL, la conformità è contrassegnata come **sconosciuta** perché la valutazione non è stata in grado di calcolare la conformità per l'istanza di SQL.

### <a name="recommended-deployment-type"></a>Tipo di distribuzione consigliato

Se si seleziona il tipo di distribuzione di destinazione come **consigliato** nelle proprietà di valutazione di SQL azure, Azure migrate consiglia un tipo di distribuzione SQL di Azure compatibile con l'istanza di SQL. La migrazione a una destinazione consigliata da Microsoft riduce il lavoro di migrazione globale. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Tipo di distribuzione consigliato basato su conformità SQL di Azure

 **Conformità del database SQL di Azure** | **Disponibilità di SQL in Azure** | **Tipo di distribuzione consigliato** | **Sono state calcolate le stime dei costi e la configurazione di SQL Azure?**
 --- | --- | --- | --- |
 Ready | Ready | DATABASE SQL di Azure o Azure SQL MI | Sì
 Ready | Non pronto o sconosciuto | Database SQL di Azure | Sì
 Non pronto o sconosciuto | Ready | Azure SQL MI | Sì
 Non pronto | Non pronto | Potenzialmente pronto per la macchina virtuale di Azure | No
 Non pronto o sconosciuto | Non pronto o sconosciuto | Sconosciuto | No

> [!NOTE]
> Se il tipo di distribuzione consigliato è selezionato **in proprietà** di valutazione e se il SQL Server di origine è adatto sia per il database SQL di Azure database singolo che per il istanza gestita SQL di Azure, la valutazione suggerisce un'opzione specifica che ottimizza i costi e si adatta ai limiti delle dimensioni e delle prestazioni.

#### <a name="potentially-ready-for-azure-vm"></a>Potenzialmente pronto per la macchina virtuale di Azure

Se l'istanza di SQL non è pronta per il database SQL di Azure e Istanza gestita SQL di Azure, il tipo di distribuzione consigliato è contrassegnato come *potenzialmente pronto per la VM di Azure*.
- Per determinare se il server in cui è in esecuzione l'istanza è pronto per la migrazione a una macchina virtuale di Azure, è consigliabile creare una valutazione in Azure Migrate con tipo di valutazione "VM di Azure". Tenere presente quanto segue:
    - Le valutazioni delle VM di Azure in Azure Migrate sono attualmente in fase di Lift-and-Shift e non prendono in considerazione le metriche delle prestazioni specifiche per l'esecuzione di istanze e database SQL nella macchina virtuale di Azure. 
    - Quando si esegue una valutazione delle VM di Azure in un server, le stime delle dimensioni e dei costi consigliate saranno per tutte le istanze in esecuzione nel server e di cui è possibile eseguire la migrazione a una macchina virtuale di Azure tramite lo strumento di migrazione server. Prima di eseguire la migrazione, [esaminare le linee guida sulle prestazioni](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) per SQL Server in macchine virtuali di Azure.


## <a name="calculate-sizing"></a>Calcolare il dimensionamento

### <a name="azure-sql-configuration"></a>Configurazione di SQL Azure

Dopo che la valutazione ha determinato la conformità e il tipo di distribuzione SQL di Azure consigliato, viene calcolato un livello di servizio specifico e una configurazione SQL di Azure (dimensioni SKU) che possono soddisfare o superare le prestazioni dell'istanza di SQL locale:
1. Durante il processo di individuazione, Azure Migrate raccoglie le prestazioni e la configurazione dell'istanza SQL che includono:
    - VCore (allocato) e utilizzo CPU (%)
        - L'utilizzo della CPU per un'istanza di SQL è la percentuale di CPU allocata utilizzata dall'istanza in SQL Server
        - L'utilizzo della CPU per un database è la percentuale di CPU allocata utilizzata dal database nell'istanza di SQL
    - Memoria (allocata) e utilizzo memoria (%)
    - Lettura i/o e scrittura i/o (file di dati e di log)
        - Le operazioni di i/o di lettura e di i/o di scrittura a livello di istanza SQL vengono calcolate aggiungendo le operazioni di i/o di lettura e scrittura i/o di tutti i database individuati in tale istanza.
    - Lettura di MB/s e scrittura MB/s (velocità effettiva)
    - Latenza delle operazioni di i/o
    - Dimensioni totali del database e organizzazioni di file di database
        - Le dimensioni del database vengono calcolate aggiungendo tutti i file di dati e di log.
1. La valutazione aggrega tutti i dati relativi alla configurazione e alle prestazioni e tenta di trovare la corrispondenza migliore tra diversi livelli di servizio e configurazioni di Azure SQL e sceglie una configurazione che può corrispondere o superare i requisiti di prestazioni dell'istanza di SQL, ottimizzando i costi.

### <a name="confidence-ratings"></a>Classificazioni di confidenza 
Ogni valutazione SQL di Azure è associata a una classificazione di attendibilità. La classificazione varia da uno (più basso) a cinque stelle (più alta). La classificazione di attendibilità consente di stimare l'affidabilità delle indicazioni relative alle dimensioni Azure Migrate fornisce.
- La classificazione di attendibilità viene assegnata a una valutazione. La valutazione è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.
- Per il dimensionamento basato sulle prestazioni, la valutazione raccoglie i dati sulle prestazioni di tutti i database e le istanze di SQL, tra cui:
    - Utilizzo CPU (%)
    - Utilizzo memoria (%)
    - Lettura i/o e scrittura i/o (file di dati e di log)
    - Lettura di MB/s e scrittura MB/s (velocità effettiva)
    - Latenza delle operazioni di i/o
    
Se uno di questi numeri di utilizzo non è disponibile, le indicazioni sulle dimensioni potrebbero non essere affidabili.
Questa tabella mostra le classificazioni di attendibilità della valutazione, che dipendono dalla percentuale di punti dati disponibili:

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle

#### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa
Ecco alcuni motivi per cui una valutazione potrebbe ottenere un livello di confidenza basso:
- L'ambiente non è stato profilato per la durata per la quale si sta creando la valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- La valutazione non è in grado di raccogliere i dati sulle prestazioni per alcuni o tutti i server nel periodo di valutazione. Per una classificazione di attendibilità elevata, verificare che:
    - I server sono accesi per la durata della valutazione
    - Sono consentite le connessioni in uscita sulle porte 443
    - Se Azure Migrate stato della connessione dell'agente SQL in Azure Migrate è' Connected ' e controllare l'ultimo heartbeat 
    - Se Azure Migrate stato di connessione per tutte le istanze SQL è "connesso" nel pannello istanza SQL individuata

    Ricalcolare la valutazione in modo da riflettere le ultime modifiche apportate alla classificazione di attendibilità.
- Alcuni database o istanze sono stati creati durante la fase di calcolo della valutazione. Si supponga, ad esempio, di aver creato una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcuni database o istanze sono stati creati solo una settimana fa. In questo caso, i dati sulle prestazioni per i nuovi server non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà bassa.

> [!NOTE]
> Poiché le valutazioni SQL di Azure sono valutazioni basate sulle prestazioni, se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'applicazione di profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile.

## <a name="calculate-monthly-costs"></a>Calcola i costi mensili
Una volta completate le indicazioni sul dimensionamento, Azure SQL Assessment calcola i costi di calcolo e di archiviazione per le configurazioni di Azure SQL consigliate usando un'API di prezzi interna. Aggrega il costo di calcolo e di archiviazione in tutte le istanze per calcolare il costo totale di calcolo mensile. 
### <a name="compute-cost"></a>Costo calcolo
- Per il calcolo del costo di calcolo per una configurazione di SQL Azure, la valutazione considera le proprietà seguenti:
    - Vantaggio Azure Hybrid per licenze SQL
    - Capacità riservata
    - Percorso di destinazione di Azure
    - Valuta
    - Programma offerta/licenza
    - Sconto (%)

### <a name="storage-cost"></a>Costo di archiviazione
- Le stime dei costi di archiviazione includono solo i file di dati e non i file di log. 
- Per il calcolo dei costi di archiviazione per una configurazione di SQL Azure, la valutazione considera le proprietà seguenti:
    - Percorso di destinazione di Azure
    - Valuta
    - Programma offerta/licenza
    - Sconto (%)
- Il costo di archiviazione dei backup non è incluso nella valutazione.
- **Database SQL di Azure**
    - Nella stima dei costi è stato aggiunto un minimo di 5 GB di spazio di archiviazione e viene aggiunto un costo di archiviazione aggiuntivo per l'archiviazione con incrementi di 1 GB. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Istanza gestita di database SQL di Azure**
    - Non è stato aggiunto alcun costo di archiviazione per la prima archiviazione da 32 GB/istanza/mese e viene aggiunto un costo di archiviazione aggiuntivo per l'archiviazione con incrementi di 32 GB. [Altre informazioni](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Passaggi successivi
- [Rivedere](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni. 
- Informazioni su come eseguire una [valutazione SQL di Azure](how-to-create-azure-sql-assessment.md).
