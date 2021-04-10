---
title: Procedure consigliate di valutazione in Azure Migrate strumento di individuazione e valutazione
description: Suggerimenti per la creazione di valutazioni con Azure Migrate strumento di individuazione e valutazione.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 1bf844dafe450e90213db2e447bb5392064eb245
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786771"
---
# <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

[Azure Migrate](./migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.

In questo articolo vengono riepilogate le procedure consigliate per la creazione di valutazioni mediante lo strumento Azure Migrate Discovery and Assessment.

Valutazioni create con Azure Migrate: lo strumento di individuazione e valutazione è uno snapshot temporizzato dei dati. Esistono tre tipi di valutazione che è possibile creare usando Azure Migrate: individuazione e valutazione:

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare i server locali nell'ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione. [Scopri di più](concepts-assessment-calculation.md)
**SQL di Azure** | Valutazioni per la migrazione dei server SQL locali dall'ambiente VMware al database SQL di Azure o al Istanza gestita SQL di Azure. [Altre informazioni](concepts-azure-sql-assessment-calculation.md)
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Criteri di dimensionamento
Opzioni relative ai criteri di ridimensionamento nelle valutazioni Azure Migrate:

**Criteri di dimensionamento** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni in base ai dati sulle prestazioni raccolti | **Valutazione delle macchine virtuali di Azure**: la raccomandazione sulle dimensioni della VM è basata sui dati relativi all'utilizzo di CPU e memoria.<br/><br/> La raccomandazione sul tipo di disco (HDD/SSD standard o dischi gestiti Premium) è basata sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.<br/><br/>**Azure SQL Assessment**: la configurazione di SQL Azure è basata sui dati sulle prestazioni delle istanze e dei database SQL, che includono: utilizzo della CPU, utilizzo della memoria, IOPS (file di dati e di log), velocità effettiva e latenza delle operazioni di i/o<br/><br/>**Valutazione della soluzione Azure VMware**: la raccomandazione sui nodi della soluzione Azure VMware è basata sui dati relativi all'utilizzo di CPU e memoria.
**Come in locale** | Valutazioni che non usano i dati sulle prestazioni per fare raccomandazioni. | **Valutazione delle macchine virtuali di Azure**: la raccomandazione sulle dimensioni della VM è basata sulle dimensioni delle VM locali<br/><br> Il tipo di disco consigliato è basato sull'opzione selezionata nell'impostazione del tipo di archiviazione per la valutazione.<br/><br/> **Valutazione della soluzione Azure VMware**: la raccomandazione sui nodi della soluzione Azure VMware è basata sulle dimensioni delle VM locali.

#### <a name="example"></a>Esempio
Ad esempio, se si dispone di una macchina virtuale locale con quattro core con utilizzo del 20% e memoria di 8 GB con utilizzo del 10%, la valutazione della VM di Azure sarà la seguente:

- **Valutazione basata sulle prestazioni**:
    - Identifica i core e la memoria effettivi basati sull'utilizzo di core (4 x 0,20 = 0,8) e memoria (8 GB x 0,10 = 0,8).
    - Applica il fattore di comfort specificato nelle proprietà della valutazione (si tratta di 1,3 x) per ottenere i valori da utilizzare per il ridimensionamento. 
    - Suggerisce le dimensioni più vicine della macchina virtuale in Azure che possono supportare ~ 1,04 Core (0,8 x 1,3) e ~ 1,04 GB (0,8 x 1,3) di memoria.

- **Valutazione As-is (As on-premises)**:
    -  Consiglia una macchina virtuale con quattro core; 8 GB di memoria.


## <a name="best-practices-for-creating-assessments"></a>Procedure consigliate per la creazione di valutazioni

Il Azure Migrate appliance continua a profilare l'ambiente locale e invia i metadati e i dati sulle prestazioni in Azure. Seguire queste procedure consigliate per le valutazioni dei server individuati usando un'appliance:

- **Crea valutazioni così come sono**: è possibile creare valutazioni così come sono immediatamente dopo la visualizzazione dei server nel portale di Azure migrate. Non è possibile creare una valutazione SQL di Azure con criteri di ridimensionamento "come in locale".
- **Creare una valutazione basata sulle prestazioni**: dopo aver configurato l'individuazione, è consigliabile attendere almeno un giorno prima di eseguire una valutazione basata sulle prestazioni:
    - La raccolta dei dati sulle prestazioni richiede tempo. L'attesa di almeno un giorno garantisce che siano presenti sufficienti punti dati sulle prestazioni prima di eseguire la valutazione.
    - Quando si eseguono valutazioni basate sulle prestazioni, assicurarsi di profilare l'ambiente per la durata della valutazione. Se, ad esempio, si crea una valutazione con una durata delle prestazioni impostata su una settimana, è necessario attendere almeno una settimana dopo l'avvio dell'individuazione, per tutti i punti dati da raccogliere. In caso contrario, la valutazione non otterrà una classificazione a cinque stelle.
- **Ricalcola valutazioni**: poiché le valutazioni sono snapshot temporizzati, non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati più recenti, è necessario ricalcolarla.

Seguire queste procedure consigliate per le valutazioni dei server importati in Azure Migrate tramite. File CSV:

- **Crea valutazioni così come sono**: è possibile creare valutazioni così come sono immediatamente dopo la visualizzazione dei server nel portale di Azure migrate.
- **Creazione di una valutazione basata sulle prestazioni**: consente di ottenere una stima dei costi migliore, soprattutto se si ha un provisioning eccessivo della capacità del server in locale. Tuttavia, l'accuratezza della valutazione basata sulle prestazioni dipende dai dati sulle prestazioni specificati dall'utente per i server. 
- **Ricalcola valutazioni**: poiché le valutazioni sono snapshot temporizzati, non vengono aggiornate automaticamente con i dati più recenti. Per aggiornare una valutazione con i dati importati più recenti, è necessario ricalcolarli.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Parametri di ridimensionamento dell'ITF per le valutazioni AVS

Il motore di archiviazione usato in AVS è rete VSAN. I criteri di archiviazione di vSAN definiscono i requisiti di archiviazione per le macchine virtuali. Questi criteri garantiscono il livello di servizio richiesto per le macchine virtuali, perché determinano la modalità di allocazione dell'archiviazione alla macchina virtuale. Le combinazioni FTT-RAID disponibili sono le seguenti: 

**Tolleranza errori** | **Configurazione RAID** | **Numero minimo di host richiesti** | **Considerazioni sul dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (Mirroring) | 3 | Una macchina virtuale da 100 GB utilizza 200 GB.
1 | RAID-5 (Erasure Coding) | 4 | Una macchina virtuale da 100 GB utilizza 133,33 GB
2 | RAID-1 (Mirroring) | 5 | Una macchina virtuale da 100 GB utilizza 300 GB.
2 | RAID-6 (Erasure Coding) | 6 | Una macchina virtuale da 100 GB utilizza 150 GB.
3 | RAID-1 (Mirroring) | 7 | Una macchina virtuale da 100 GB utilizza 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Procedure consigliate per le classificazioni di confidenza

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità compresa tra 1 stella (più bassa) e 5 stelle. Per usare in modo efficace la classificazione delle confidenze:

- Le valutazioni delle macchine virtuali di Azure e AVS sono necessarie:
    - I dati di utilizzo della CPU e della memoria per ogni server
    - Dati di IOPS/velocità effettiva di lettura/scrittura per ogni disco collegato al server locale
    - I dati di rete in/out per ogni scheda di rete collegata al server.
     
- Per le valutazioni SQL di Azure sono necessari i dati sulle prestazioni delle istanze e dei database SQL da valutare, tra cui:
    - Dati relativi all'utilizzo della CPU e della memoria
    - Dati di IOPS/velocità effettiva di lettura/scrittura dei file di dati e di log
    - Latenza delle operazioni di i/o

A seconda della percentuale di punti dati disponibili per la durata selezionata, la classificazione di attendibilità per una valutazione viene fornita come riepilogata nella tabella seguente.

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle


## <a name="common-assessment-issues"></a>Problemi di valutazione comuni

Di seguito viene illustrato come risolvere alcuni problemi di ambiente comuni che influiscono sulle valutazioni.

###  <a name="out-of-sync-assessments"></a>Valutazioni out-of-Sync

Se si aggiungono o rimuovono server da un gruppo dopo aver creato una valutazione, la valutazione creata verrà contrassegnata come non **sincronizzata**. Eseguire nuovamente la valutazione (**Ricalcola**) per riflettere le modifiche apportate al gruppo.

### <a name="outdated-assessments"></a>Valutazioni obsolete

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Valutazione delle macchine virtuali di Azure e AVS Assessment
Se sono presenti modifiche nei server locali che si trovano in un gruppo valutato, la valutazione viene contrassegnata come **obsoleta**. Una valutazione può essere contrassegnata come "obsoleta" a causa di una o più modifiche nelle proprietà seguenti:
- Numero di core del processore
- Memoria allocata
- Tipo di avvio o firmware
- Nome, versione e architettura del sistema operativo
- Numero di dischi
- Numero di scheda di rete
- Modifica dimensioni disco (GB allocato)
- Aggiornamento delle proprietà nic. Esempio: modifiche all'indirizzo Mac, aggiunta dell'indirizzo IP e così via.
    
Eseguire di nuovo la valutazione (**Ricalcola**) per riflettere le modifiche.
    
#### <a name="azure-sql-assessment"></a>Valutazione di Azure SQL
Se vengono apportate modifiche alle istanze e ai database SQL locali inclusi in un gruppo valutato, la valutazione viene contrassegnata come **obsoleta**. Una valutazione può essere contrassegnata come "obsoleta" a causa di uno o più dei motivi seguenti:
- L'istanza di SQL è stata aggiunta o rimossa da un server
- Il database SQL è stato aggiunto o rimosso da un'istanza di SQL
- Le dimensioni totali del database in un'istanza di SQL sono cambiate di una percentuale superiore al 20%
- Modifica del numero di core del processore
- Modifica nella memoria allocata        
  
    Eseguire di nuovo la valutazione (**Ricalcola**) per riflettere le modifiche.

### <a name="low-confidence-rating"></a>Classificazione con attendibilità bassa

Una valutazione potrebbe non avere tutti i punti dati per diversi motivi:

- L'ambiente non è stato analizzato per il perioro di tempo per cui si sta creando la valutazione. Ad esempio, se si sta creando una valutazione con periodo di tempo delle prestazioni impostato su 1 settimana, è necessario attendere almeno una settimana dopo avere avviato l'individuazione perché siano raccolti tutti i punti dati. Se non è possibile attendere per tale periodo, modificare la durata delle prestazioni a un periodo più breve e "Ricalcolare" la valutazione.
 
- La valutazione non è in grado di raccogliere i dati sulle prestazioni per alcuni o tutti i server nel periodo di valutazione. Per una classificazione di attendibilità elevata, verificare che: 
    - I server sono accesi per la durata della valutazione
    - Sono consentite le connessioni in uscita sulle porte 443
    - Per i server Hyper-V la memoria dinamica è abilitata 
    - Lo stato della connessione degli agenti nel Azure Migrate è' Connected ' e controlla l'ultimo heartbeat
    - Per le valutazioni SQL di Azure, Azure Migrate stato di connessione per tutte le istanze SQL è "connesso" nel pannello istanza SQL individuata

    Ricalcolare la valutazione in modo da riflettere le ultime modifiche apportate alla classificazione di attendibilità.

- Per le valutazioni di VM e AVS di Azure, sono stati creati pochi server dopo l'avvio dell'individuazione. Ad esempio, se si sta creando una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma sono stati creati pochi server nell'ambiente solo una settimana fa. In questo caso, i dati sulle prestazioni per i nuovi server non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà bassa.

- Per le valutazioni di Azure SQL, alcuni database o istanze di SQL sono stati creati dopo l'avvio dell'individuazione. Se, ad esempio, si sta creando una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma nell'ambiente sono state create alcune istanze o database SQL solo una settimana fa. In questo caso, i dati sulle prestazioni per i nuovi server non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà bassa.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Guida dello strumento di migrazione per le valutazioni AVS

Nel report di idoneità per Azure per la valutazione della soluzione Azure VMware è possibile visualizzare gli strumenti consigliati seguenti: 
- **VMware HCX o Enterprise**: per i server VMware, la soluzione VMware Hybrid Cloud Extension (HCx) è lo strumento di migrazione suggerito per eseguire la migrazione del carico di lavoro locale al cloud privato della soluzione VMware di Azure (AVS). [Altre informazioni](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Sconosciuto**: per i server importati tramite un file CSV, lo strumento di migrazione predefinito è sconosciuto. Tuttavia, per i server in ambiente VMware, è consigliabile usare la soluzione VMware Hybrid Cloud Extension (HCX).


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](concepts-assessment-calculation.md) su come vengono calcolate le valutazioni.
- [Informazioni](how-to-modify-assessment.md) su come personalizzare una valutazione.
