---
title: Valutare le istanze di macchine virtuali GCP per la migrazione ad Azure con Azure Migrate
description: Viene descritto come valutare le istanze di macchine virtuali GCP per la migrazione ad Azure usando Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 6a59400ca0d8f2e4ced899166fe6e67b5ac1d2d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780685"
---
# <a name="tutorial-assess-google-cloud-platform-gcp-vm-instances-for-migration-to-azure"></a>Esercitazione: Valutare le istanze di VM GCP (Google Cloud Platform) per la migrazione ad Azure

Il percorso di migrazione ad Azure prevede la valutazione dei carichi di lavoro locali per determinare l'idoneità per il cloud, identificare i rischi e stimare costi e complessità.

Questo articolo illustra come valutare Google Cloud Platform istanze di macchine virtuali (GCP) per la migrazione ad Azure usando lo strumento Azure Migrate: Discovery and Assessment.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
- Eseguire una valutazione basata sui metadati del server e le informazioni di configurazione.
- Eseguire una valutazione in base ai dati sulle prestazioni.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

- Prima di seguire la procedura descritta in questa esercitazione, completare la prima esercitazione di questa serie per [individuare l'inventario locale](tutorial-discover-gcp.md). 


## <a name="decide-which-assessment-to-run"></a>Decidere la valutazione da eseguire

Decidere se si vuole eseguire una valutazione usando criteri di ridimensionamento in base ai dati di configurazione del server o ai metadati raccolti così come sono in locale o in base ai dati sulle prestazioni.

**Valutazione** | **Dettagli** | **Consiglio**
--- | --- | ---
**Come in locale** | Valuta in base ai metadati o ai dati di configurazione del server.  | Le dimensioni consigliate per le macchine virtuali di Azure sono basate sulle dimensioni delle macchine virtuali locali.<br/><br> Il tipo di disco di Azure consigliato è basato sull'opzione selezionata nella valutazione per l'impostazione del tipo di archiviazione.
**Basata sulle prestazioni** | Valutazione basata sui dati sulle prestazioni raccolti. | Le dimensioni consigliate per le macchine virtuali di Azure sono basate sui dati di utilizzo della CPU e della memoria.<br/><br/> Il tipo di disco consigliato è basato sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Nella pagina **panoramica** > **Windows, Linux e SQL Server** fare clic su **valuta ed Esegui la migrazione dei server**.

   ![Posizione del pulsante Valutare ed eseguire la migrazione dei server](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. In **Azure migrate: individuazione e valutazione** fare clic su **valuta**.

    ![Posizione del pulsante Valuta](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. In **Valuta server** > **Tipo di valutazione** selezionare **Macchina virtuale di Azure**.
4. In **Origine individuazione**:

    - Se sono stati individuati server che usano il dispositivo, selezionare **server individuati dal dispositivo Azure migrate**.
    - Se sono stati individuati server che usano un file CSV importato, selezionare **Server importati**. 
    
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
     - In **criterio di ridimensionamento** selezionare se si desidera basare la valutazione sui metadati o sui dati di configurazione del server o sui dati basati sulle prestazioni. Se si usano i dati relativi alle prestazioni:
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
    - In **Offerta** specificare l'[offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. La valutazione stima il costo per l'offerta.
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

1. In **selezionare i server per valutare**  >  il **nome della valutazione** > specificare un nome per la valutazione. 

1. In **selezionare o creare un gruppo** > selezionare **Crea nuovo** e specificare un nome di gruppo. 
    

1. Selezionare l'appliance e quindi le macchine virtuali da aggiungere al gruppo. Quindi fare clic su **Next**.


1. In **Rivedi e crea valutazione** esaminare i dettagli della valutazione e fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

1. Una volta creata la valutazione, visualizzarla nei **Server**  >  **Azure migrate: valutazioni di individuazione e valutazione**  >  .

1. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.
    > [!NOTE]
    > Per le valutazioni basate sulle prestazioni, prima di creare una valutazione è consigliabile attendere almeno un giorno dopo aver avviato l'individuazione. In questo modo i dati sulle prestazioni raccolti saranno maggiormente attendibili. Per una classificazione più attendibile, se possibile, dopo l'avvio dell'individuazione attendere un tempo pari alla durata delle prestazioni specificata (giorno/settimana/mese).

## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

Per visualizzare una valutazione:

1. In **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, fare clic sul numero accanto a **valutazioni**.
2. In **Valutazioni** selezionare una valutazione per aprirla. A titolo di esempio (stime e costi sono solo esemplificativi): 

    ![Riepilogo della valutazione](./media/tutorial-assess-gcp/assessment-summary.png)

3. Esaminare il riepilogo della valutazione. È anche possibile modificare le proprietà della valutazione o ricalcolare la valutazione.
 
 
### <a name="review-readiness"></a>Esaminare l'idoneità

1. Fare clic su **Idoneità per Azure**.
2. In **Idoneità per Azure** esaminare lo stato delle macchine virtuali:
    - **Idonea per Azure**: questo stato viene usato quando Azure Migrate consiglia le dimensioni e le stime dei costi per le macchine virtuali nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: questo stato viene usato quando Azure Migrate non può valutare l'idoneità a causa di problemi di disponibilità dei dati.

3. Selezionare uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle macchine virtuali ed eseguire il drill-down per visualizzare i dettagli delle macchine virtuali, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-estimates"></a>Verificare le stime dei costi

Il riepilogo della valutazione mostra i costi di calcolo e archiviazione stimati per l'esecuzione delle macchine virtuali in Azure. 

1. Esaminare i costi totali mensili. I costi sono aggregati per tutte le VM nel gruppo valutato.

    - Le stime dei costi si basano sulle raccomandazioni relative alle dimensioni per un server, i relativi dischi e le relative proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione delle macchine virtuali locali in macchine virtuali di Azure. La stima non considera i costi per PaaS o SaaS.

2. Esaminare i costi di archiviazione mensili. La visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione. 
3. È possibile eseguire il drill-down per visualizzare i dettagli dei costi per macchine virtuali specifiche.

### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Azure Migrate assegna una classificazione di attendibilità alle valutazioni basate sulle prestazioni. La classificazione è compresa tra una stella (più bassa) e cinque stelle (più alta).

![Classificazione di attendibilità](./media/tutorial-assess-gcp/confidence-rating.png)

La classificazione di attendibilità è utile per stimare l'affidabilità delle raccomandazioni sulle dimensioni nella valutazione. La classificazione è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate se si crea una valutazione basata su un file CSV.


Le classificazioni di attendibilità sono elencate di seguito.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle

[Altre informazioni](concepts-assessment-calculation.md#confidence-ratings-performance-based) sulle classificazioni di attendibilità.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le dipendenze del server usando il [mapping delle dipendenze](concepts-dependency-visualization.md)
- Configurare il mapping delle dipendenze [basato sull'agente](how-to-create-group-machine-dependencies.md).
