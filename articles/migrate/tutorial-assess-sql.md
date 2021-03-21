---
title: Esercitazione per valutare le istanze SQL per la migrazione a Istanza gestita SQL di Azure e al database SQL di Azure
description: Informazioni su come creare una valutazione per SQL di Azure in Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 9b33890d53f67eee870b42462a65b4a0b7ba9981
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055531"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Esercitazione: valutare le istanze di SQL per la migrazione ad Azure SQL

Il percorso di migrazione ad Azure prevede la valutazione dei carichi di lavoro locali per misurare l'idoneità del cloud, identificare i rischi e stimare costi e complessità.
Questo articolo illustra come valutare i database delle istanze di SQL Server individuati in preparazione per la migrazione ad Azure SQL, usando lo strumento Azure Migrate: Discovery and Assessment.

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Eseguire una valutazione in base alla configurazione del server e ai dati sulle prestazioni.
> * Esaminare una valutazione SQL di Azure 

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite. 


## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

- Prima di seguire questa esercitazione per valutare le istanze di SQL Server per la migrazione a SQL di Azure, assicurarsi di aver individuato le istanze di SQL che si vuole valutare usando il dispositivo Azure Migrate, [seguire questa esercitazione](tutorial-discover-vmware.md)

## <a name="run-an-assessment"></a>Eseguire una valutazione
Eseguire una valutazione nel modo seguente:
1. Nella pagina **panoramica** > **Windows, Linux e SQL Server** fare clic su **valuta ed Esegui la migrazione dei server**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Pagina di panoramica per Azure Migrate":::
2. In **Azure migrate: individuazione e valutazione** fare clic su **valuta** e scegliere il tipo di valutazione come **SQL di Azure**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Elenco a discesa per scegliere il tipo di valutazione come SQL di Azure":::
3. In **valuta server** > è possibile visualizzare il tipo di valutazione preselezionato come **Azure SQL** e l'origine di individuazione viene impostata automaticamente su **server individuati da Azure migrate Appliance**.

4. Fare clic su **modifica** per esaminare le proprietà della valutazione.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Pulsante modifica da cui è possibile personalizzare le proprietà di valutazione":::
5. In proprietà di valutazione > **proprietà di destinazione**:
    - In **Località di destinazione** specificare l'area di Azure in cui eseguire la migrazione. 
        - Le raccomandazioni per la configurazione e i costi di Azure SQL si basano sul percorso specificato. 
    - Nel **tipo di distribuzione di destinazione**,
        - Selezionare **consigliato**. Se si vuole Azure migrate valutare la preparazione delle istanze di SQL per la migrazione al database SQL di Azure mi e Azure, e consigliare l'opzione di distribuzione di destinazione più adatta, il livello di destinazione, la configurazione di SQL Azure e le stime mensili. [Altre informazioni](concepts-azure-sql-assessment-calculation.md)
        - Selezionare il database **SQL di Azure**, se si vuole valutare la conformità delle istanze di SQL per la migrazione solo ai database SQL di Azure ed esaminare il livello di destinazione, la configurazione SQL di Azure e le stime mensili.
        - Selezionare **Azure SQL mi**, se si vuole valutare la conformità delle istanze di SQL per la migrazione solo a SQL di Azure istanza gestita ed esaminare il livello di destinazione, la configurazione SQL di Azure e le stime mensili.
    - In **capacità riservata** specificare se si desidera utilizzare la capacità riservata per SQL Server dopo la migrazione.
        - Se si seleziona un'opzione di capacità riservata, non è possibile specificare "discount (%)".

6. In proprietà valutazione > **criteri di valutazione**:
    - Per impostazione predefinita, i criteri di ridimensionamento sono **basati sulle prestazioni,** il che significa che Azure migrate raccoglie le metriche delle prestazioni relative alle istanze di SQL e ai database gestiti da esso per consigliare un database SQL di Azure di dimensioni ottimali e/o una configurazione di azure SQL istanza gestita. È possibile specificare:
        - **Cronologia delle prestazioni** per indicare la durata dei dati su cui si desidera basare la valutazione. (Il valore predefinito è un giorno)
        - **Utilizzo percentile**, per indicare il valore percentile che si desidera utilizzare per l'esempio delle prestazioni. (Il valore predefinito è 95 ° percentile)
    - In **Fattore di comfort** indicare il buffer da usare durante la valutazione. Questa opzione tiene conto di aspetti quali l'utilizzo stagionale, una cronologia ridotta delle prestazioni e il probabile aumento dell'utilizzo futuro. Se ad esempio si usa un fattore di comfort di due: 
        
        **Componente** | **Utilizzo effettivo** | **Aggiungere fattore di comfort (2.0)**
        --- | --- | ---
        Core | 2  | 4
        Memoria | 8 GB | 16 GB
   
7. In **Prezzi**:
    - Nel **programma offerta/licenza** specificare l'offerta Azure se si è iscritti. Attualmente è possibile scegliere solo da sviluppo/test con pagamento in base al consumo e con pagamento in base al consumo. 
        - Puoi usufruire di uno sconto aggiuntivo applicando la capacità riservata e Vantaggio Azure Hybrid in base all'offerta con pagamento in base al consumo. 
        - È possibile applicare Vantaggio Azure Hybrid in base a sviluppo/test con pagamento in base al consumo. Attualmente la valutazione non supporta l'applicazione di capacità riservata oltre all'offerta di sviluppo/test con pagamento in base al consumo.
    - In **livello di servizio** scegliere l'opzione del livello di servizio più appropriata per soddisfare le esigenze aziendali per la migrazione al database SQL di Azure e/o istanza gestita SQL di Azure: 
        - Selezionare **consigliato** se si desidera che Azure migrate consigli il livello di servizio più adatto per i server. Può trattarsi di un utilizzo generico o di un business critical. Altre informazioni
        - Selezionare **per utilizzo generico** se si vuole una configurazione SQL di Azure progettata per carichi di lavoro orientati al budget.
        - Selezionare **business critical** se si vuole una configurazione SQL di Azure progettata per carichi di lavoro a bassa latenza con resilienza elevata a errori e failover rapidi.
    - In **Sconto (%)** aggiungere eventuali sconti specifici della sottoscrizione ricevuti oltre all'offerta di Azure. L'impostazione predefinita è 0%.
    - In **Valuta** selezionare la valuta di fatturazione per l'account.
    - In **vantaggio Azure Hybrid** specificare se si dispone già di una licenza di SQL Server. In tal caso, è possibile applicare la Vantaggio Azure Hybrid quando si impostano le licenze in Azure con il software SQL Server Assurance attivo.
    - Se sono state apportate modifiche, fare clic su Salva.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Pulsante Salva in proprietà valutazione":::
8. In **valuta server** > fare clic su Avanti.
9.  In **selezionare i server per valutare**  >  il **nome della valutazione** > specificare un nome per la valutazione.
10. In **selezionare o creare un gruppo** > selezionare **Crea nuovo** e specificare un nome di gruppo.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Posizione del pulsante nuovo gruppo":::
11. Selezionare l'appliance e selezionare i server che si desidera aggiungere al gruppo. Quindi fare clic su Avanti.
12. In **Verifica + crea valutazione** esaminare i dettagli della valutazione e fare clic su Crea valutazione per creare il gruppo ed eseguire la valutazione.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Posizione del pulsante Verifica e crea valutazione.":::
13. Dopo aver creato la valutazione, passare a **Windows, Linux e SQL Server**  >  **Azure migrate: riquadro Discovery and Assessment** > fare clic sul numero accanto a SQL assessment di Azure.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Spostamento alla valutazione creata":::
15. Fare clic sul nome della valutazione che si desidera visualizzare.

> [!NOTE]
> Poiché le valutazioni SQL di Azure sono valutazioni basate sulle prestazioni, si consiglia di attendere almeno un giorno dopo l'avvio dell'individuazione prima di creare una valutazione. In questo modo i dati sulle prestazioni raccolti saranno maggiormente attendibili. Se l'individuazione è ancora in corso, la preparazione delle istanze di SQL verrà contrassegnata come **sconosciuta**. Idealmente, dopo l'avvio dell'individuazione, **attendere la durata delle prestazioni specificata (giorno/settimana/mese)** per creare o ricalcolare la valutazione per una classificazione con attendibilità elevata. 

## <a name="review-an-assessment"></a>Esaminare una valutazione

**Per visualizzare una valutazione**:

1. **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione** > fare clic sul numero accanto a valutazione SQL di Azure.
2. Fare clic sul nome della valutazione che si desidera visualizzare. Come esempio (stime e costi solo per esempio): Panoramica di :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="SQL Assessment":::
3. Esaminare il riepilogo della valutazione. È anche possibile modificare le proprietà di valutazione o ricalcolare la valutazione.

#### <a name="discovered-items"></a>Elementi individuati

Indica il numero di istanze e database di SQL Server che sono stati valutati in questa valutazione.
    
#### <a name="azure-readiness"></a>Idoneità per Azure

Indica la distribuzione delle istanze di SQL valutate: 
    
**Tipo di distribuzione di destinazione (in proprietà valutazione)** | **Idoneità**   
--- | --- |
**Consigliato** |  Pronto per il database SQL di Azure, pronto per Istanza gestita SQL di Azure, potenzialmente pronto per la macchina virtuale di Azure, la conformità è sconosciuta (nel caso in cui l'individuazione sia in corso o ci siano problemi di individuazione da correggere)
**Database SQL di Azure** o **Azure SQL mi** | Pronto per il database SQL di Azure o per il Istanza gestita SQL di Azure, non pronto per il database SQL di Azure o Azure SQL Istanza gestita, la conformità è sconosciuta (nel caso in cui l'individuazione sia in corso o ci siano problemi di individuazione da correggere)
     
È possibile eseguire il drill-down per comprendere i dettagli relativi ai problemi di migrazione/avvisi che è possibile correggere prima della migrazione ad Azure SQL. [Altre informazioni](concepts-azure-sql-assessment-calculation.md) È anche possibile esaminare le configurazioni di Azure SQL consigliate per la migrazione a database SQL di Azure e/o istanze gestite.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Dettagli sui costi del database SQL di Azure e Istanza gestita

La stima dei costi mensili include i costi di calcolo e archiviazione per le configurazioni SQL di Azure corrispondenti al database SQL di Azure consigliato e/o al tipo di distribuzione Istanza gestita SQL di Azure. [Altre informazioni](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Esaminare l'idoneità

1. Fare clic su **conformità a SQL di Azure**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Dettagli conformità SQL di Azure":::
1. In conformità a SQL di Azure esaminare la conformità del **database SQL di Azure** e la **disponibilità di Azure SQL** per le istanze di SQL valutate:
    - **Pronto**: l'istanza è pronta per la migrazione al database SQL di Azure/mi senza problemi di migrazione o avvisi. 
        - Ready (icona informazioni blu e collegamento ipertestuale): l'istanza è pronta per la migrazione al database SQL di Azure/MI senza problemi di migrazione, ma presenta alcuni avvisi di migrazione che è necessario esaminare. È possibile fare clic sul collegamento ipertestuale per esaminare gli avvisi di migrazione e le linee guida consigliate per la correzione: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="valutazione con stato pronto":::       
    - **Non pronto**: l'istanza presenta uno o più problemi di migrazione per la migrazione al database SQL di Azure/mi. È possibile fare clic sul collegamento ipertestuale ed esaminare i problemi di migrazione e le linee guida per la correzione consigliate.
    - **Sconosciuto**: Azure migrate non è in grado di valutare la conformità perché è in corso l'individuazione o si sono verificati problemi durante l'individuazione che devono essere corretti dal pannello notifiche. Se il problema persiste, contattare il supporto tecnico Microsoft. 
1. Esaminare il tipo di distribuzione consigliato per l'istanza SQL che viene determinata in base alla matrice seguente:

    - **Tipo di distribuzione di destinazione** (come selezionato in proprietà valutazione): **consigliato**

        **Conformità del database SQL di Azure** | **Disponibilità di SQL in Azure** | **Tipo di distribuzione consigliato** | **Sono state calcolate le stime dei costi e la configurazione di SQL Azure?**
         --- | --- | --- | --- |
        Ready | Ready | [Altre informazioni](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) su database SQL di Azure o Azure SQL mi | Sì
        Ready | Non pronto o sconosciuto | Database SQL di Azure | Sì
        Non pronto o sconosciuto | Ready | Azure SQL MI | Sì
        Non pronto | Non pronto | Potenzialmente pronto per le macchine virtuali di Azure [altre informazioni](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        Non pronto o sconosciuto | Non pronto o sconosciuto | Sconosciuto | No
    
    - **Tipo di distribuzione di destinazione** (come selezionato in proprietà valutazione): **database SQL di Azure**
    
        **Conformità del database SQL di Azure** | **Sono state calcolate le stime dei costi e la configurazione di SQL Azure?**
        --- | --- |
        Ready | Sì
        Non pronto | No
        Sconosciuto | No
    
    - **Tipo di distribuzione di destinazione** (come selezionato in proprietà valutazione): **Azure SQL mi**
    
        **Disponibilità di SQL in Azure** | **Sono state calcolate le stime dei costi e la configurazione di SQL Azure?**
         --- | --- |
        Ready | Sì
        Non pronto | No
        Sconosciuto | No

4. Fare clic sul nome dell'istanza drill-down per visualizzare il numero di database utente, i dettagli dell'istanza, incluse le proprietà dell'istanza, le risorse di calcolo (con ambito di istanza) e i dettagli di archiviazione del database di origine.
5. Fare clic sul numero di database utente per esaminare l'elenco di database e i relativi dettagli. Come esempio (stime e costi solo per esempio): dettagli dell' :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="istanza di SQL":::
5. Fare clic su Verifica dettagli nella colonna problemi di migrazione per esaminare i problemi e gli avvisi relativi alla migrazione per un tipo di distribuzione di destinazione specifico. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problemi e avvisi relativi alla migrazione del database":::

### <a name="review-cost-estimates"></a>Verificare le stime dei costi
Il riepilogo della valutazione Mostra i costi di calcolo e archiviazione mensili stimati per le configurazioni SQL di Azure corrispondenti ai database SQL di Azure consigliati e/o al tipo di distribuzione delle istanze gestite.

1. Esaminare i costi totali mensili. I costi vengono aggregati per tutte le istanze di SQL nel gruppo valutato.
    - Le stime dei costi si basano sulla configurazione di SQL Azure consigliata per un'istanza. 
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione. Come esempio (stime e costi solo per esempio):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Dettagli sui costi":::

1. È possibile eseguire il drill-down a livello di istanza per visualizzare la configurazione SQL di Azure e le stime dei costi a livello di istanza.  
1. È anche possibile eseguire il drill-down nell'elenco database per esaminare la configurazione SQL di Azure e le stime dei costi per ogni database quando si consiglia la configurazione di un database SQL di Azure.

### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità
Azure Migrate assegna una classificazione di attendibilità a tutte le valutazioni SQL di Azure in base alla disponibilità dei punti dati relativi alle prestazioni e all'utilizzo necessari per calcolare la valutazione per tutte le istanze e i database di SQL valutati. La classificazione è compresa tra una stella (più bassa) e cinque stelle (più alta).
La classificazione di attendibilità è utile per stimare l'affidabilità delle raccomandazioni sulle dimensioni nella valutazione. Le classificazioni di attendibilità sono le seguenti:

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle

[Altre informazioni](concepts-azure-sql-assessment-calculation.md#confidence-ratings) sulle classificazioni di attendibilità.


## <a name="next-steps"></a>Passaggi successivi

- [Altre](concepts-azure-sql-assessment-calculation.md) informazioni su come vengono calcolate le valutazioni SQL di Azure.
- Avviare la migrazione di istanze e database SQL usando il [servizio migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
