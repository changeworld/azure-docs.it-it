---
title: Creare una valutazione delle VM di Azure con Azure Migrate server Assessment | Microsoft Docs
description: Viene descritto come creare una valutazione delle VM di Azure con lo strumento di valutazione di Azure Migrate server
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 178bdca78c6f011c607de8e1f5d5eabcdbaab7d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567689"
---
# <a name="create-an-azure-vm-assessment"></a>Creare una valutazione per le macchine virtuali di Azure

Questo articolo descrive come creare una valutazione delle VM di Azure per macchine virtuali VMware locali o macchine virtuali Hyper-V con Azure Migrate: server assessment.

[Azure migrate](migrate-services-overview.md) consente di eseguire la migrazione ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato](./create-manage-projects.md) un progetto Azure migrate.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server assessment.
- Per creare una valutazione, è necessario configurare un'appliance Azure Migrate per [VMware](how-to-set-up-appliance-vmware.md) o [Hyper-V](how-to-set-up-appliance-hyper-v.md). L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment. [Altre informazioni](migrate-appliance.md)


## <a name="azure-vm-assessment-overview"></a>Panoramica della valutazione delle VM di Azure
Esistono due tipi di criteri di ridimensionamento che è possibile usare per creare una valutazione delle VM di Azure usando Azure Migrate: server assessment.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni VM consigliate**: in base ai dati sull'utilizzo di CPU e memoria.<br/><br/> **Tipo di disco consigliato (disco gestito Standard o Premium)** : in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni VM consigliate**: in base alle dimensioni delle VM locali<br/><br> **Tipo di disco consigliato**: in base all'impostazione del tipo di archiviazione selezionata per la valutazione.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Nella pagina **Server** selezionare **Server Windows e Linux** e fare clic su **Valutare ed eseguire la migrazione dei server**.

   ![Posizione del pulsante Valutare ed eseguire la migrazione dei server](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. In **Azure Migrate: Valutazione server** fare clic su **Valuta**.

    ![Posizione del pulsante Valuta](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. In **Valuta server** > **Tipo di valutazione** selezionare **Macchina virtuale di Azure**.
4. In **Origine individuazione**:

    - Se le macchine virtuali sono state individuate usando l'appliance, selezionare **Macchine virtuali individuate dall'appliance di Azure Migrate**.
    - Se le macchine virtuali sono state individuate usando un file CSV importato, selezionare **Macchine virtuali importate**. 
    
1. Fare clic su **modifica** per esaminare le proprietà della valutazione.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Posizione del pulsante modifica per esaminare le proprietà di valutazione":::

1. In **Proprietà valutazione** > **Proprietà destinazione**:
    - In **Località di destinazione** specificare l'area di Azure in cui eseguire la migrazione.
        - Le raccomandazioni relative alle dimensioni e ai costi si basano sulla località specificata. Una volta modificato il percorso di destinazione per impostazione predefinita, verrà richiesto di specificare le **istanze riservate** e la **serie di macchine virtuali**.
        - In Azure per enti pubblici è possibile specificare [queste aree](migrate-support-matrix.md#supported-geographies-azure-government) come destinazione delle valutazioni
    - In **Tipo di archiviazione**
        - Se nella valutazione si vogliono usare i dati basati sulle prestazioni, selezionare **Automatico** in modo che sia Azure Migrate a consigliare un tipo di archiviazione, sulla base dei dati relativi a base alle operazioni di I/O al secondo e alla velocità effettiva del disco.
        - In alternativa, selezionare il tipo di archiviazione da usare per la macchina virtuale quando si esegue la migrazione.
    - In **Istanze riservate** specificare se si vogliono usare istanze riservate per la macchina virtuale quando si esegue la migrazione.
        - Se si sceglie di usare un'istanza riservata, non è possibile specificare **Sconto (%)** o **Tempo di attività macchina virtuale**. 
        - [Altre informazioni](https://aka.ms/azurereservedinstances)
 1. In **Dimensioni macchina virtuale**:
     - In **Criterio di dimensionamento** scegliere se si vuole basare la valutazione sui dati/metadati di configurazione della macchina virtuale o sui dati relativi alle prestazioni. Se si usano i dati relativi alle prestazioni:
        - In **Cronologia delle prestazioni** indicare la durata dei dati in base alla quale basare la valutazione
        - In **Utilizzo percentile** specificare il valore percentile da usare per il campione delle prestazioni. 
    - In **Serie macchina virtuale** specificare la serie di macchine virtuali di Azure che si vuole prendere in considerazione.
        - Se si usa la valutazione basata sulle prestazioni, il valore viene suggerito da Azure Migrate.
        - Perfezionare le impostazioni in base alle esigenze. Se, ad esempio, non si ha un ambiente di produzione in cui sono richieste le macchine virtuali della serie A in Azure, si può escludere la serie A dall'elenco delle serie.
    - In **Fattore di comfort** indicare il buffer da usare durante la valutazione. Questa opzione tiene conto di aspetti quali l'utilizzo stagionale, una cronologia ridotta delle prestazioni e il probabile aumento dell'utilizzo futuro. Se ad esempio si usa un fattore di comfort di due:
    
        **Componente** | **Utilizzo effettivo** | **Aggiungere fattore di comfort (2.0)**
        --- | --- | ---
        Core | 2  | 4
        Memoria | 8 GB | 16 GB
   
1. In **Prezzi**:
    - In **Offerta** specificare l'[offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Valutazione server stima il costo di tale offerta.
    - In **Valuta** selezionare la valuta di fatturazione per l'account.
    - In **Sconto (%)** aggiungere eventuali sconti specifici della sottoscrizione ricevuti oltre all'offerta di Azure. L'impostazione predefinita è 0%.
    - In **Tempo di attività macchina virtuale** specificare la durata (giorni al mese/ora al giorno) in cui le macchine virtuali sono in esecuzione.
        - Questa opzione è utile per le macchine virtuali di Azure che non vengono eseguite in modo continuativo.
        - Le stime dei costi sono basate sulla durata specificata.
        - Il valore predefinito è 31 giorni al mese e 24 ore al giorno.
    - In **Sottoscrizione con contratto Enterprise** specificare se prendere in considerazione uno sconto per la sottoscrizione con contratto Enterprise per la stima dei costi. 
    - In **Vantaggio Azure Hybrid** specificare se si ha già una licenza di Windows Server. Se si ha già una licenza coperta da un contratto Software Assurance per le sottoscrizioni di Windows Server, è possibile richiedere il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/) quando si importano licenze in Azure.

1. Se sono state apportate modifiche, fare clic su **Salva**.

    ![Proprietà valutazione](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. In **valuta server** > fare clic su **Avanti**.

1. In **Seleziona computer per valutare**  >  il **nome della valutazione** > specificare un nome per la valutazione. 

1. In **selezionare o creare un gruppo** > selezionare **Crea nuovo** e specificare un nome di gruppo. 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assess-group.png" alt-text="Aggiungere macchine virtuali a un gruppo":::

1. Selezionare l'appliance e quindi le macchine virtuali da aggiungere al gruppo. Quindi fare clic su **Next**.


1. In **Rivedi e crea valutazione** esaminare i dettagli della valutazione e fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

1. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server** > **Valutazioni**.

1. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.
    > [!NOTE]
    > Per le valutazioni basate sulle prestazioni, prima di creare una valutazione è consigliabile attendere almeno un giorno dopo aver avviato l'individuazione. In questo modo i dati sulle prestazioni raccolti saranno maggiormente attendibili. Per una classificazione più attendibile, se possibile, dopo l'avvio dell'individuazione attendere un tempo pari alla durata delle prestazioni specificata (giorno/settimana/mese).


## <a name="review-an-azure-vm-assessment"></a>Rivedere una valutazione di macchine virtuali di Azure

Una valutazione di VM di Azure descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

### <a name="view-an-azure-vm-assessment"></a>Visualizzare una valutazione della VM di Azure

1. In **Obiettivi della migrazione** >  **Server** fare clic su **Valutazioni** in **Azure Migrate: Valutazione server**.
2. In **Valutazioni** fare clic su una valutazione per aprirla.

    ![Riepilogo della valutazione](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se le VM sono pronte per la migrazione ad Azure.
2. Verificare lo stato delle VM:
    - **Idonea per Azure**: Azure Migrate consiglia le dimensioni e le stime dei costi per le VM nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

3. Fare clic su uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle VM ed eseguire il drill-down per visualizzare i dettagli delle VM, incluse le impostazioni di calcolo, archiviazione e rete.



### <a name="review-cost-details"></a>Esaminare i dettagli dei costi

Questa visualizzazione mostra il costo stimato di calcolo e archiviazione associato all'esecuzione delle macchine virtuali in Azure.

1. Esaminare i costi mensili di calcolo e archiviazione. I costi sono aggregati per tutte le VM nel gruppo valutato.

    - Le stime dei costi sono basate sulle dimensioni consigliate per una VM e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione delle VM locali come VM IaaS. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

2. È possibile esaminare le stime dei costi di archiviazione mensile. Questa visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione.
3. È possibile eseguire il drill-down per visualizzare i dettagli relativi a VM specifiche.


### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità.

![Classificazione di attendibilità](./media/how-to-create-assessment/confidence-rating.png)

- La classificazione è compresa tra 1 stella (minima) e 5 stelle (massima).
- La classificazione di attendibilità aiuta a stimare l'affidabilità delle indicazioni relative alle dimensioni fornite dalla valutazione.
- La classificazione di attendibilità è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.

Di seguito sono elencate le classificazioni di attendibilità per una valutazione.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle




## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping delle dipendenze](how-to-create-group-machine-dependencies.md) per creare gruppi con attendibilità elevata.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.