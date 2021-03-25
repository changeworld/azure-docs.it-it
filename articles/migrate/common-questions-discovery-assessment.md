---
title: Domande sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate
description: Risposte alle domande più comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 0701ff53202d53131ceac8ceabb148fb5ff4f2b2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025153"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Individuazione, valutazione e analisi delle dipendenze-domande comuni

Questo articolo risponde alle domande più comuni sull'individuazione, la valutazione e l'analisi delle dipendenze in Azure Migrate. Per altre domande, vedere le risorse seguenti:

- [Domande generali](resources-faq.md) su Azure migrate
- Domande sull' [appliance Azure migrate](common-questions-appliance.md)
- Domande sulla [migrazione del server](common-questions-server-migration.md)
- Risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Quali sono le aree geografiche supportate per l'individuazione e la valutazione con Azure Migrate?

Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Quanti server è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 server dall'ambiente VMware, fino a 5.000 server dall'ambiente Hyper-V e fino a 1000 server fisici utilizzando una singola appliance. Se sono presenti più server, vedere la pagina relativa alla [scalabilità di una valutazione di Hyper-V](scale-hyper-v-assessment.md), alla [scalabilità di una valutazione VMware](scale-vmware-assessment.md)o alla [scalabilità di una valutazione del server fisico](scale-physical-assessment.md)

## <a name="how-do-i-choose-the-assessment-type"></a>Come è possibile scegliere il tipo di valutazione?

- Usare le **valutazioni delle VM di Azure** per valutare i server dell'ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) locale e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione alle macchine virtuali di Azure. [Altre informazioni](concepts-assessment-calculation.md)

- Usare il tipo di valutazione **Azure SQL** quando si vuole valutare la SQL Server locale dall'ambiente VMware per la migrazione al database SQL di Azure o al istanza gestita SQL di Azure. [Altre informazioni](concepts-assessment-calculation.md)

- Usare le valutazioni della **soluzione VMware di Azure (AVS)** quando si vuole valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione ad [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) con questo tipo di valutazione. [Scopri di più](concepts-azure-vmware-solution-assessment-calculation.md)

- È possibile usare un gruppo comune con computer VMware solo per eseguire entrambi i tipi di valutazione. Si noti che se si eseguono per la prima volta le valutazioni delle soluzioni Azure VMware in Azure Migrate, è consigliabile creare un nuovo gruppo di computer VMware.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Perché mancano i dati sulle prestazioni per alcuni/tutti i server nella macchina virtuale di Azure e/o nel report di valutazione AVS?

Per la valutazione "basata sulle prestazioni", l'esportazione del report di valutazione dice ' PercentageOfCoresUtilizedMissing ' o ' PercentageOfMemoryUtilizedMissing ' quando l'appliance Azure Migrate non è in grado di raccogliere i dati sulle prestazioni per i server locali. Verificare:

- Se i server sono accesi per la durata per la quale si sta creando la valutazione
- Se mancano solo i contatori di memoria e si sta tentando di valutare i server nell'ambiente Hyper-V. In questo scenario, abilitare la memoria dinamica nei server è Ricalcola ' la valutazione in modo da riflettere le modifiche più recenti. L'appliance può raccogliere i valori di utilizzo della memoria per l'ambiente Hyper-V solo quando nel server è abilitata la memoria dinamica.

- Se tutti i contatori delle prestazioni risultano mancanti, assicurarsi che le connessioni in uscita sulle porte 443 (HTTPS) siano consentite.

    > [!Note]
    > Se non è presente alcun contatore delle prestazioni, Azure Migrate: server Assessment esegue il fallback alla memoria o ai core allocati in locale e consiglia di conseguenza le dimensioni della macchina virtuale.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Perché mancano i dati sulle prestazioni per alcune/tutte le istanze/database SQL nella valutazione SQL di Azure?

Per assicurarsi che i dati sulle prestazioni vengano raccolti, controllare:

- Se i server SQL sono accesi per la durata per la quale si sta creando la valutazione
- Se lo stato della connessione dell'agente SQL in Azure Migrate è' Connected ' e controllare l'ultimo heartbeat 
- Se Azure Migrate stato della connessione per tutte le istanze SQL è' Connected ' nel pannello istanza SQL individuata
- Se mancano tutti i contatori delle prestazioni, assicurarsi che le connessioni in uscita sulle porte 443 (HTTPS) siano consentite

Se uno dei contatori delle prestazioni non è presente, Azure SQL Assessment consiglia la configurazione di Azure SQL più piccola per tale istanza/database.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Perché la classificazione di attendibilità della valutazione è bassa?

La classificazione di attendibilità viene calcolata per le valutazioni basate sulle prestazioni in base alla percentuale di [punti dati disponibili](./concepts-assessment-calculation.md#ratings) necessaria per calcolare la valutazione. Di seguito sono riportati i motivi per cui una valutazione potrebbe ottenere una classificazione di attendibilità bassa:

- L'ambiente non è stato analizzato per il perioro di tempo per cui si sta creando la valutazione. Ad esempio, se si sta creando una valutazione con periodo di tempo delle prestazioni impostato su 1 settimana, è necessario attendere almeno una settimana dopo avere avviato l'individuazione perché siano raccolti tutti i punti dati. Se non è possibile attendere la durata, impostare la durata delle prestazioni su un periodo inferiore e **ricalcolare** la valutazione.
 
- La valutazione non è in grado di raccogliere i dati sulle prestazioni per alcuni o tutti i server nel periodo di valutazione. Per una classificazione di attendibilità elevata, verificare che: 
    - I server sono accesi per la durata della valutazione
    - Sono consentite le connessioni in uscita sulle porte 443
    - Per i server Hyper-V la memoria dinamica è abilitata 
    - Lo stato della connessione degli agenti nel Azure Migrate è' Connected ' e controlla l'ultimo heartbeat
    - Per le valutazioni SQL di Azure, Azure Migrate stato di connessione per tutte le istanze SQL è "connesso" nel pannello istanza SQL individuata

    **Ricalcolare** la valutazione in modo che rifletta le ultime modifiche apportate alla classificazione di attendibilità.

- Per le valutazioni di VM e AVS di Azure, sono stati creati pochi server dopo l'avvio dell'individuazione. Ad esempio, se si sta creando una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma sono stati creati pochi server nell'ambiente solo una settimana fa. In questo caso, i dati sulle prestazioni per i nuovi server non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà bassa. [Scopri di più](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Per le valutazioni di Azure SQL, alcuni database o istanze di SQL sono stati creati dopo l'avvio dell'individuazione. Se, ad esempio, si sta creando una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma nell'ambiente sono state create alcune istanze o database SQL solo una settimana fa. In questo caso, i dati sulle prestazioni per i nuovi server non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà bassa. [Scopri di più](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Si vuole provare la nuova valutazione SQL di Azure
L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Introduzione a [questa esercitazione](tutorial-discover-vmware.md). Per provare questa funzionalità in un progetto esistente, assicurarsi di aver completato i [prerequisiti](how-to-discover-sql-existing-project.md) descritti in questo articolo.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Durante la creazione di una valutazione di Azure SQL, non è possibile visualizzare alcuni server

- La valutazione di Azure SQL può essere eseguita solo in server in esecuzione in cui sono state individuate istanze di SQL. Se non vengono visualizzati i server e le istanze di SQL che si desidera valutare, attendere qualche minuto prima che l'individuazione venga completata e quindi creare la valutazione. 
- Se non è possibile visualizzare un gruppo creato in precedenza durante la creazione della valutazione, rimuovere tutti i server non VMware o i server senza un'istanza di SQL dal gruppo.
- Se si eseguono per la prima volta le valutazioni di Azure SQL in Azure Migrate, è consigliabile creare un nuovo gruppo di server.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Si desidera capire come è stata calcolata la conformità per l'istanza?
L'idoneità delle istanze di SQL è stata calcolata dopo che è stato eseguito un controllo della compatibilità delle funzionalità con il tipo di distribuzione di Azure SQL di destinazione (Database SQL di Azure o Istanza gestita di SQL di Azure). [Scopri di più](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Perché la conformità per tutte le istanze di SQL è contrassegnata come sconosciuta?
Se l'individuazione è stata avviata di recente ed è ancora in corso, è possibile visualizzare la conformità per alcune o tutte le istanze di SQL come sconosciute. È consigliabile attendere un po' di tempo prima che l'appliance profili l'ambiente e quindi ricalcoli la valutazione.
Il rilevamento SQL viene eseguito una volta ogni 24 ore e potrebbe essere necessario attendere fino a un giorno per le ultime modifiche di configurazione da riflettere. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Perché la conformità per alcune istanze di SQL è contrassegnata come sconosciuta?
Questo problema può verificarsi se: 
- L'individuazione è ancora in corso. È consigliabile attendere un po' di tempo prima che l'appliance profili l'ambiente e quindi ricalcoli la valutazione.
- Sono presenti alcuni problemi di individuazione da correggere nel pannello "Errors and notifications" (Errori e notifiche).

Il rilevamento SQL viene eseguito una volta ogni 24 ore e potrebbe essere necessario attendere fino a un giorno per le ultime modifiche di configurazione da riflettere.

## <a name="my-assessment-is-in-outdated-state"></a>Lo stato della valutazione è Obsoleto

### <a name="azure-vmavs-assessment"></a>Valutazione della macchina virtuale di Azure/AVS
Se sono presenti modifiche locali ai server che fanno parte di un gruppo che è stato valutato, la valutazione viene contrassegnata come obsoleta. Una valutazione può essere contrassegnata come "obsoleta" a causa di una o più modifiche nelle proprietà seguenti:
- Numero di core del processore
- Memoria allocata
- Tipo di avvio o firmware
- Nome, versione e architettura del sistema operativo
- Numero di dischi
- Numero di scheda di rete
- Modifica dimensioni disco (GB allocato)
- Aggiornamento delle proprietà nic. Esempio: modifiche all'indirizzo Mac, aggiunta dell'indirizzo IP e così via.

**Ricalcolare** la valutazione in modo da riflettere le ultime modifiche apportate alla valutazione.

### <a name="azure-sql-assessment"></a>Valutazione di Azure SQL
Se sono state apportate modifiche ai database e alle istanze di SQL locali presenti in un gruppo valutato, la valutazione viene contrassegnata come **Obsoleto**:
- L'istanza di SQL è stata aggiunta o rimossa da un server
- Il database SQL è stato aggiunto o rimosso da un'istanza di SQL
- Le dimensioni totali del database in un'istanza di SQL sono cambiate di una percentuale superiore al 20%
- Modifica del numero di core del processore e/o della memoria allocata

**Ricalcolare** la valutazione in modo da riflettere le ultime modifiche apportate alla valutazione.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Perché è stato consigliato un determinato tipo di distribuzione di destinazione?
Azure Migrate consiglia un determinato tipo di distribuzione di Azure SQL compatibile con l'istanza di SQL. La migrazione a una destinazione consigliata da Microsoft riduce il lavoro richiesto complessivo per la migrazione. Questa configurazione di Azure SQL (SKU) è stata consigliata dopo che sono state prese in considerazione le caratteristiche delle prestazioni dell'istanza di SQL e dei database che gestisce. Se varie configurazioni di Azure SQL sono idonee, è consigliabile scegliere quella più efficace. [Scopri di più](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Quale destinazione di distribuzione è consigliabile scegliere se l'istanza di SQL è pronta per il database e l'istanza gestita di SQL Azure? 
Se l'istanza è pronta sia per il database che per l'istanza gestita di Azure SQL, è consigliabile usare il tipo di distribuzione di destinazione per il quale il costo stimato della configurazione di Azure SQL è inferiore.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Perché l'istanza è contrassegnata come potenzialmente pronta per la macchina virtuale di Azure nella valutazione SQL di Azure?
Questo può verificarsi quando lo stato del tipo di distribuzione di destinazione scelto nelle proprietà della valutazione è **Consigliato** e l'istanza di SQL non è pronta per Database SQL di Azure e Istanza gestita di SQL di Azure. È consigliabile creare una valutazione in Azure Migrate con il tipo di valutazione **Macchina virtuale di Azure** per determinare se il server in cui è in esecuzione l'istanza è pronto per eseguire la migrazione a una macchina virtuale di Azure.
È consigliabile creare una valutazione in Azure Migrate con tipo di valutazione come **macchina virtuale di Azure** per determinare se il server in cui è in esecuzione l'istanza è pronto per la migrazione a una macchina virtuale di Azure:
- Le valutazioni delle VM di Azure in Azure Migrate sono attualmente incentrate su Lift-a-Shift e non prenderanno in considerazione le metriche delle prestazioni specifiche per l'esecuzione di istanze e database SQL nella macchina virtuale di Azure. 
- Quando si esegue una valutazione della macchina virtuale di Azure in un server, le dimensioni consigliate e le stime dei costi faranno riferimento a tutte le istanze in esecuzione nel server e sarà possibile eseguire la migrazione a una macchina virtuale di Azure tramite lo strumento di migrazione del server. Prima di eseguire la migrazione, [esaminare le linee guida sulle prestazioni](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) per SQL Server nelle macchine virtuali di Azure.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Non è possibile visualizzare alcuni database nella valutazione anche se l'istanza fa parte della valutazione

La valutazione di Azure SQL include solo database il cui stato è Online. Nel caso in cui lo stato del database sia un altro, la valutazione ignora l'idoneità, il dimensionamento e il calcolo dei costi per tali database. Se si desidera valutare tali database, modificarne lo stato e ricalcolare la valutazione in un secondo momento.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Vorrei confrontare i costi per l'esecuzione delle istanze di SQL in una VM di Azure rispetto al database SQL di Azure/Azure SQL Istanza gestita

È possibile creare una valutazione con il tipo **Macchina virtuale di Azure** nello stesso gruppo usato nella valutazione di **Azure SQL**. È quindi possibile confrontare i due report affiancati. Tuttavia, le valutazioni delle macchine virtuali di Azure in Azure Migrate sono attualmente in fase di trasferimento in modalità lift-and-shift e non prenderanno in considerazione le metriche delle prestazioni specifiche per l'esecuzione di database e istanze di SQL nella macchina virtuale di Azure. Quando si esegue una valutazione della macchina virtuale di Azure in un server, le dimensioni consigliate e le stime dei costi faranno riferimento a tutte le istanze in esecuzione nel server e sarà possibile eseguire la migrazione a una macchina virtuale di Azure tramite lo strumento di migrazione del server. Prima di eseguire la migrazione, [esaminare le linee guida sulle prestazioni](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) per SQL Server nelle macchine virtuali di Azure.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Il costo di archiviazione nella valutazione SQL di Azure è zero
Per il Istanza gestita SQL di Azure, non è stato aggiunto alcun costo di archiviazione per la prima archiviazione da 32 GB/istanza/mese e viene aggiunto un costo di archiviazione aggiuntivo per l'archiviazione con incrementi di 32 GB. [Altre informazioni](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Non è possibile visualizzare alcuni gruppi quando si crea una valutazione della soluzione VMware di Azure (AVS)

- La valutazione della soluzione Azure VMware può essere eseguita su gruppi che hanno solo computer VMware. Se si intende eseguire una valutazione della soluzione Azure VMware, rimuovere dal gruppo eventuali computer non VMware.
- Se si eseguono per la prima volta le valutazioni della soluzione Azure VMware in Azure Migrate, è consigliabile creare un nuovo gruppo di computer VMware.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Non è possibile visualizzare alcuni tipi di VM e dimensioni in Azure per enti pubblici

I tipi e le dimensioni delle macchine virtuali supportate per la valutazione e la migrazione dipendono dalla disponibilità in Azure Government location. È possibile [esaminare e confrontare](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) i tipi di VM in Azure per enti pubblici.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>La dimensione del server è cambiata. È possibile eseguire nuovamente una valutazione?

L'appliance Azure Migrate raccoglie continuamente informazioni sull'ambiente locale.  Una valutazione è uno snapshot temporizzato dei server locali. Se si modificano le impostazioni in un server che si desidera valutare, utilizzare l'opzione Ricalcola per aggiornare la valutazione con le modifiche più recenti.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Ricerca per categorie individuare i server in un ambiente multi-tenant?

- **VMware**: se un ambiente viene condiviso tra i tenant e non si vuole individuare i server di un tenant nella sottoscrizione di un altro tenant, creare server VMware vCenter credenziali che possono accedere solo ai server che si desidera individuare. Quindi, usare queste credenziali quando si avvia l'individuazione nell'appliance Azure Migrate.
- **Hyper-v**: l'individuazione usa le credenziali dell'host Hyper-v. Se i server condividono lo stesso host Hyper-V, attualmente non è possibile separare l'individuazione.  

## <a name="do-i-need-vcenter-server"></a>È necessario server vCenter?

Sì, Azure Migrate richiede server vCenter in un ambiente VMware per eseguire l'individuazione. Azure Migrate non supporta l'individuazione di host ESXi che non sono gestiti da server vCenter.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Quali sono le opzioni di ridimensionamento in una valutazione delle VM di Azure?

Con il ridimensionamento locale, Azure Migrate non considera i dati sulle prestazioni del server per la valutazione. Azure Migrate valuta le dimensioni delle macchine virtuali in base alla configurazione locale. Con il dimensionamento basato sulle prestazioni, il dimensionamento è basato sui dati di utilizzo.

Ad esempio, se un server locale ha quattro core e 8 GB di memoria al 50% di utilizzo della CPU e il 50% di memoria:
- Il dimensionamento locale consiglierà uno SKU di VM di Azure con quattro core e 8 GB di memoria.
- Il dimensionamento basato sulle prestazioni consiglierà uno SKU di VM con due core e 4 GB di memoria perché la percentuale di utilizzo è considerata.

Analogamente, il ridimensionamento del disco dipende dai criteri di ridimensionamento e dal tipo di archiviazione:
- Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è automatico, Azure Migrate prende in considerazione i valori di IOPS e velocità effettiva del disco quando identifica il tipo di disco di destinazione (standard o Premium).
- Se i criteri di ridimensionamento sono basati sulle prestazioni e il tipo di archiviazione è Premium, Azure Migrate consiglia uno SKU del disco Premium in base alle dimensioni del disco locale. La stessa logica viene applicata al dimensionamento del disco quando il dimensionamento è locale e il tipo di archiviazione è standard o Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>La cronologia delle prestazioni e l'utilizzo influiscono sul dimensionamento in una valutazione delle VM di Azure?

Sì, la cronologia delle prestazioni e l'utilizzo influiscono sul dimensionamento in una valutazione delle VM di Azure.

### <a name="performance-history"></a>Cronologia delle prestazioni

Solo per il dimensionamento basato sulle prestazioni, Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e quindi la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure:

1. L'appliance continua a profilare l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi.
2. Il dispositivo esegue il rollup degli esempi raccolti di 20 secondi e li usa per creare un singolo punto dati ogni 15 minuti.
3. Per creare il punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20 secondi.
4. Il dispositivo invia il punto dati ad Azure.

### <a name="utilization"></a>Utilizzo

Quando si crea una valutazione in Azure, a seconda della durata delle prestazioni e del valore percentile cronologia prestazioni impostato, Azure Migrate calcola il valore di utilizzo effettivo e quindi lo usa per il ridimensionamento.

Se ad esempio si imposta la durata delle prestazioni su un giorno e il valore percentile sul 95 ° percentile, Azure Migrate Ordina i punti di campionamento di 15 minuti inviati dall'agente di raccolta per il giorno precedente in ordine crescente. Il valore del 95 ° percentile viene scelto come utilizzo effettivo.

L'utilizzo del valore 95 ° percentile garantisce che gli outlier vengano ignorati. Gli outlier potrebbero essere inclusi se il Azure Migrate usa il 99 ° percentile. Per selezionare l'utilizzo di picco per il periodo senza gli outlier, impostare Azure Migrate per usare il 99 ° percentile.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>In che modo le valutazioni basate sull'importazione sono diverse dalle valutazioni con l'origine di individuazione come appliance?

Le valutazioni delle VM di Azure basate sull'importazione sono valutazioni create con computer importati in Azure Migrate usando un file CSV. Solo quattro campi sono obbligatori per l'importazione: nome del server, Core, memoria e sistema operativo. Ecco alcuni aspetti da considerare: 
 - I criteri di conformità sono meno rigorosi nelle valutazioni basate sull'importazione sul parametro di tipo di avvio. Se il tipo di avvio non viene specificato, si presuppone che il computer disponga di un tipo di avvio BIOS e che il computer non sia contrassegnato come pronto in modo **condizionale**. Nelle valutazioni con l'origine di individuazione come appliance, la conformità è contrassegnata come **predisposta in modo condizionale** se il tipo di avvio è mancante. Questa differenza nel calcolo della conformità è dovuta al fatto che gli utenti potrebbero non disporre di tutte le informazioni sui computer nelle prime fasi della pianificazione della migrazione quando vengono eseguite le valutazioni basate sull'importazione. 
 - Per le valutazioni di importazione basate sulle prestazioni viene utilizzato il valore di utilizzo fornito dall'utente per il dimensionamento corretto dei calcoli. Poiché il valore di utilizzo viene fornito dall'utente, le opzioni **Cronologia prestazioni** e **utilizzo percentile** sono disabilitate nelle proprietà di valutazione. Nelle valutazioni con l'origine di individuazione come appliance, il valore percentile scelto viene selezionato dai dati sulle prestazioni raccolti dal dispositivo.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Perché lo strumento di migrazione suggerito nella valutazione AVS basata sull'importazione è contrassegnato come sconosciuto?

Per i computer importati tramite un file CSV, lo strumento di migrazione predefinito in AVS assessment è sconosciuto. Tuttavia, per i computer VMware è consigliabile usare la soluzione VMware Hybrid Cloud Extension (HCX). [Altre informazioni](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>Informazioni sulla visualizzazione delle dipendenze

La visualizzazione delle dipendenze può essere utile per valutare i gruppi di server di cui eseguire la migrazione con maggiore sicurezza. La visualizzazione delle dipendenze controlla le dipendenze tra computer prima di eseguire una valutazione. Consente di evitare interruzioni impreviste quando si esegue la migrazione ad Azure. Azure Migrate usa la soluzione Mapping dei servizi in Monitoraggio di Azure per abilitare la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md)

> [!NOTE]
> L'analisi delle dipendenze basata su agente non è disponibile in Azure per enti pubblici. È possibile usare l'analisi delle dipendenze senza agenti

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual è la differenza tra l'agente e l'agente?

Le differenze tra la visualizzazione senza agente e la visualizzazione basata su agenti sono riepilogate nella tabella.

**Requisito** | **Senza agente** | **Basata su agenti**
--- | --- | ---
Supporto | Questa opzione è attualmente in anteprima ed è disponibile solo per i server nell'ambiente VMware. [Esaminare](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) i sistemi operativi supportati. | In disponibilità generale (GA).
Agente | Non è necessario installare gli agenti nei computer che si vuole controllare in modo incrociato. | Agenti da installare in ogni computer locale che si vuole analizzare: [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)e [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Prerequisiti | [Esaminare](concepts-dependency-visualization.md#agentless-analysis) i prerequisiti e i requisiti di distribuzione. | [Esaminare](concepts-dependency-visualization.md#agent-based-analysis) i prerequisiti e i requisiti di distribuzione.
Log Analytics | Non obbligatorio. | Azure Migrate usa la soluzione [Mapping dei servizi](../azure-monitor/vm/service-map.md) in [Log di Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md) per la visualizzazione delle dipendenze. [Altre informazioni](concepts-dependency-visualization.md#agent-based-analysis)
Funzionamento | Acquisisce i dati di connessione TCP nei computer abilitati per la visualizzazione delle dipendenze. Dopo l'individuazione, raccoglie i dati a intervalli di cinque minuti. | Mapping dei servizi agenti installati in un computer raccolgono i dati relativi ai processi TCP e alle connessioni in ingresso/in uscita per ogni processo.
Dati | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta. | Nome del server del computer di origine, processo, nome dell'applicazione.<br/><br/> Nome del server del computer di destinazione, processo, nome dell'applicazione e porta.<br/><br/> Il numero di connessioni, la latenza e le informazioni sul trasferimento dei dati sono raccolte e disponibili per Log Analytics query. 
Visualizzazione | La mappa delle dipendenze di un singolo server può essere visualizzata per una durata di un'ora a 30 giorni. | Mappa delle dipendenze di un singolo server.<br/><br/> La mappa può essere visualizzata solo in un'ora.<br/><br/> Mappa delle dipendenze di un gruppo di server.<br/><br/> Aggiungere e rimuovere i server in un gruppo dalla vista mappa.
Esportazione dati | Ultimi 30 giorni è possibile scaricare i dati in formato CSV. | È possibile eseguire query sui dati con Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>È necessario distribuire l'appliance per l'analisi delle dipendenze senza agenti?

Sì, l' [appliance di Azure migrate](migrate-appliance.md) deve essere distribuita.

## <a name="do-i-pay-for-dependency-visualization"></a>Si paga per la visualizzazione delle dipendenze?

No. Scopri di più sui [prezzi Azure migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Cosa si installa per la visualizzazione delle dipendenze basate su agenti?

Per usare la visualizzazione delle dipendenze basata su agente, scaricare e installare gli agenti in ogni computer locale che si vuole valutare:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Se sono presenti computer che non dispongono di connettività Internet, scaricare e installare il gateway Log Analytics.

Questi agenti sono necessari solo se si usa la visualizzazione di dipendenza basata su agenti.

## <a name="can-i-use-an-existing-workspace"></a>È possibile usare un'area di lavoro esistente?

Sì, per la visualizzazione delle dipendenze basate su agenti, è possibile allegare un'area di lavoro esistente al progetto di migrazione e usarla per la visualizzazione delle dipendenze. 

## <a name="can-i-export-the-dependency-visualization-report"></a>È possibile esportare il report di visualizzazione delle dipendenze?

No, il report di visualizzazione delle dipendenze nella visualizzazione basata su agente non può essere esportato. Tuttavia, Azure Migrate USA Mapping dei servizi ed è possibile usare l' [API REST mapping dei servizi](/rest/api/servicemap/machines/listconnections) per recuperare le dipendenze in formato JSON.

## <a name="can-i-automate-agent-installation"></a>È possibile automatizzare l'installazione dell'agente?

Per la visualizzazione delle dipendenze basate su agente:

- Usare uno [script per installare Dependency Agent](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Per MMA, [usare la riga di comando o l'automazione](../azure-monitor/agents/log-analytics-agent.md#installation-options)oppure usare uno [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Oltre agli script, è possibile utilizzare strumenti di distribuzione come Microsoft endpoint Configuration Manager e [Intigua](https://www.intigua.com/intigua-for-azure-migration) per distribuire gli agenti.

## <a name="what-operating-systems-does-mma-support"></a>Quali sistemi operativi sono supportati da MMA?

- Visualizzare l'elenco dei [sistemi operativi Windows supportati da MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- Visualizzare l'elenco dei [sistemi operativi Linux supportati da MMA](../azure-monitor/agents/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>È possibile visualizzare le dipendenze per più di un'ora?

Per la visualizzazione basata su agenti, è possibile visualizzare le dipendenze per un massimo di un'ora. È possibile tornare indietro fino a un mese fino a una data specifica nella cronologia, ma la durata massima per la visualizzazione è di un'ora. Ad esempio, è possibile usare la durata dell'ora nella mappa delle dipendenze per visualizzare le dipendenze per ieri, ma è possibile visualizzare le dipendenze solo per una finestra di un'ora. Tuttavia, è possibile usare i log di monitoraggio di Azure per [eseguire query sui dati di dipendenza](./how-to-create-group-machine-dependencies.md) per un periodo di tempo più lungo.

Per la visualizzazione senza agenti, è possibile visualizzare la mappa delle dipendenze di un singolo server da una durata compresa tra un'ora e 30 giorni.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>È possibile visualizzare le dipendenze per gruppi di più di 10 server?

È possibile [visualizzare le dipendenze](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) per i gruppi che dispongono di un massimo di 10 server. Se si dispone di un gruppo con più di 10 server, è consigliabile suddividere il gruppo in gruppi più piccoli, quindi visualizzare le dipendenze.

## <a name="next-steps"></a>Passaggi successivi

Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).