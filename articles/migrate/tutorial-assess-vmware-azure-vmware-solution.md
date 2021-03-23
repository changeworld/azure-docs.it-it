---
title: Valutare i server VMware per la migrazione alla soluzione VMware di Azure (AVS) con Azure Migrate
description: Informazioni su come valutare i server nell'ambiente VMware per la migrazione ad AVS con Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782137"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Esercitazione: valutare i server VMware per la migrazione a AVS

Il percorso di migrazione ad Azure prevede la valutazione dei carichi di lavoro locali per misurare l'idoneità del cloud, identificare i rischi e stimare costi e complessità.

Questo articolo illustra come valutare le macchine virtuali VMware/server individuati per la migrazione ad Azure VMware Solution (AVS), usando il Azure Migrate. La soluzione Azure VMware è un servizio gestito che consente di eseguire la piattaforma VMware in Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
- Eseguire una valutazione basata sui metadati del server e le informazioni di configurazione.
- Eseguire una valutazione in base ai dati sulle prestazioni.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.



## <a name="prerequisites"></a>Prerequisiti

Prima di seguire questa esercitazione per valutare i server per la migrazione a AVS, verificare di aver individuato i server che si desidera valutare:

- Per individuare i server che usano il dispositivo Azure Migrate, [seguire questa esercitazione](tutorial-discover-vmware.md). 
- Per individuare i server che usano un file CSV importato, [seguire questa esercitazione](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Decidere la valutazione da eseguire


Decidere se si vuole eseguire una valutazione usando criteri di ridimensionamento in base ai dati di configurazione del server o ai metadati raccolti così come sono in locale o ai dati dinamici delle prestazioni.

**Valutazione** | **Dettagli** | **Consiglio**
--- | --- | ---
**Come in locale** | Valuta in base ai metadati o ai dati di configurazione del server.  | Le dimensioni consigliate del nodo in AVS sono basate sulle dimensioni della macchina virtuale o del server locale, oltre alle impostazioni specificate nella valutazione per il tipo di nodo, il tipo di archiviazione e l'impostazione di errore a tolleranza.
**Basata sulle prestazioni** | Eseguire la valutazione in base ai dati dinamici relativi alle prestazioni. | Le dimensioni consigliate dei nodi nella soluzione Azure VMware sono basate sui dati relativi all'utilizzo della CPU e della memoria, unitamente alle impostazioni specificate nella valutazione per il tipo di nodo, il tipo di archiviazione e l'impostazione di tolleranza errori.

> [!NOTE]
> La valutazione della soluzione VMware di Azure (AVS) può essere creata solo per macchine virtuali VMware/Server.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1.  Nella pagina **panoramica** > **Windows, Linux e SQL Server** fare clic su **valuta ed Esegui la migrazione dei server**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Pagina di panoramica per Azure Migrate":::

1. In **Azure migrate: individuazione e valutazione** fare clic su **valuta**.

   ![Posizione del pulsante valuta](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. In **valuta** il  >  **tipo di valutazione** server selezionare **soluzione VMware di Azure (AVS)**.

1. In **Origine individuazione**:

    - Se sono stati individuati server che usano il dispositivo, selezionare **server individuati dal dispositivo Azure migrate**.
    - Se sono stati individuati server che usano un file CSV importato, selezionare **Server importati**. 
    
1. Fare clic su **modifica** per esaminare le proprietà della valutazione.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Pagina per la selezione delle impostazioni di valutazione":::
 

1. In **Proprietà valutazione** > **Proprietà destinazione**:

    - In **Località di destinazione** specificare l'area di Azure in cui eseguire la migrazione.
       - Le raccomandazioni relative alle dimensioni e ai costi si basano sulla località specificata.
   - Per impostazione predefinita, il **tipo di archiviazione** è **rete VSAN**. Questo è il tipo di archiviazione predefinito per un cloud privato della soluzione Azure VMware.
   - Le **istanze riservate** non sono attualmente supportate per i nodi della soluzione Azure VMware.
1. In **Dimensioni macchina virtuale**:
    - Per impostazione predefinita, il **tipo di nodo** è **AV36**. Azure Migrate consiglia il nodo dei nodi necessari per eseguire la migrazione dei server in AVS.
    - In **impostazione di ITF, livello RAID**, selezionare la combinazione di errori da tollerare e RAID.  L'opzione di ITF selezionata, combinata con il requisito del disco del server locale, determina il totale di archiviazione rete VSAN richiesto in AVS.
    - In **Oversubscription della CPU** specificare il rapporto di core virtuali associati a un core fisico nel nodo della soluzione Azure VMware. Un'oversubscription maggiore di 4:1 potrebbe causare una riduzione del livello delle prestazioni, ma può essere usata per i carichi di lavoro di tipo server Web.
    - In **fattore di overcommit della memoria** specificare il rapporto di memoria rispetto al commit sul cluster. Il valore 1 rappresenta il 100% di utilizzo di memoria, 0,5 ad esempio è 50% e 2 utilizza il 200% della memoria disponibile. È possibile aggiungere valori compresi tra 0,5 e 10 fino a una posizione decimale.
    - In **deduplicato e fattore di compressione** specificare i deduplicati previsti e il fattore di compressione per i carichi di lavoro. Il valore effettivo può essere ottenuto dalla configurazione locale di rete VSAN o archiviazione e può variare in base al carico di lavoro. Il valore 3 significherebbe 3x, quindi per il disco 300GB verrebbero usati solo 100 GB di spazio di archiviazione. Il valore 1 non significa alcun deduplicato o compressione. È possibile aggiungere solo valori da 1 a 10 fino a una posizione decimale.
1. In **Dimensioni nodo**: 
    - In **Criterio di dimensionamento** scegliere se si vuole basare la valutazione sui metadati statici o sui dati relativi alle prestazioni. Se si usano i dati relativi alle prestazioni:
        - In **Cronologia delle prestazioni** indicare la durata dei dati in base alla quale basare la valutazione
        - In **Utilizzo percentile** specificare il valore percentile da usare per il campione delle prestazioni. 
    - In **Fattore di comfort** indicare il buffer da usare durante la valutazione. Questa opzione tiene conto di aspetti quali l'utilizzo stagionale, una cronologia ridotta delle prestazioni e il probabile aumento dell'utilizzo futuro. Se ad esempio si usa un fattore di comfort di due:
    
        **Componente** | **Utilizzo effettivo** | **Aggiungere fattore di comfort (2.0)**
        --- | --- | ---
        Core | 2  | 4
        Memoria | 8 GB | 16 GB  

1. In **Prezzi**:
    - In **offerta**, viene visualizzata l' [offerta Azure](https://azure.microsoft.com/support/legal/offer-details/) che è stata registrata in. La valutazione stima il costo per l'offerta.
    - In **Valuta** selezionare la valuta di fatturazione per l'account.
    - In **Sconto (%)** aggiungere eventuali sconti specifici della sottoscrizione ricevuti oltre all'offerta di Azure. L'impostazione predefinita è 0%.

1. Se sono state apportate modifiche, fare clic su **Salva**.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Proprietà valutazione":::

1. In **Valuta server** fare clic su **Avanti**.

1. In **selezionare i server per valutare**  >  il **nome della valutazione** > specificare un nome per la valutazione. 
 
1. In **selezionare o creare un gruppo** > selezionare **Crea nuovo** e specificare un nome di gruppo. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Aggiungere server a un gruppo":::
 
1. Selezionare l'appliance e selezionare i server che si desidera aggiungere al gruppo. Quindi fare clic su **Next**.

1. In **Rivedi e crea valutazione** esaminare i dettagli della valutazione e fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    > [!NOTE]
    > Per le valutazioni basate sulle prestazioni, prima di creare una valutazione è consigliabile attendere almeno un giorno dopo aver avviato l'individuazione. In questo modo i dati sulle prestazioni raccolti saranno maggiormente attendibili. Per una classificazione più attendibile, se possibile, dopo l'avvio dell'individuazione attendere un tempo pari alla durata delle prestazioni specificata (giorno/settimana/mese).

## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione della soluzione Azure VMware descrive:

- Idoneità AVS: indica se i server locali sono idonei per la migrazione alla soluzione VMware di Azure (AVS).
- Numero di nodi AVS: numero stimato di nodi AVS necessari per eseguire i server.
- Utilizzo tra nodi AVS: utilizzo previsto della CPU, della memoria e delle risorse di archiviazione in tutti i nodi.
    - L'utilizzo include il fattore iniziale nel sovraccarico di gestione cluster, ad esempio server vCenter, NSX Manager (Large), NSX Edge, se HCX è distribuito anche HCX Manager e IX Appliance consumo ~ 44vCPU (11 CPU), 75 GB di RAM e 722GB di archiviazione prima della compressione e della deduplicazione. 
- Stima dei costi mensili: i costi mensili stimati per tutti i nodi della soluzione VMware di Azure (AVS) che eseguono i server locali.

## <a name="view-an-assessment"></a>Visualizzare una valutazione

Per visualizzare una valutazione:

1. In **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, fare clic sul numero accanto a * * soluzione VMware di Azure * *.

1. In **Valutazioni** selezionare una valutazione per aprirla. A titolo di esempio (stime e costi sono solo esemplificativi): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Riepilogo di AVS Assessment":::

1. Esaminare il riepilogo della valutazione. È anche possibile modificare le proprietà della valutazione o ricalcolare la valutazione.
 

### <a name="review-readiness"></a>Esaminare l'idoneità

1. Fare clic su **Idoneità per Azure**.
2. In **Azure conformità** verificare lo stato di conformità.

    - **Pronto per AVS**: è possibile eseguire la migrazione del server così com'è ad Azure AVS, senza alcuna modifica. Il server si avvierà in AVS con supporto AVS completo.
    - **Pronto con condizioni**: il server potrebbe avere problemi di compatibilità con la versione corrente di vSphere. Potrebbe essere necessario installare strumenti VMware o configurare altre impostazioni prima che sia completamente funzionante nella soluzione Azure VMware.
    - **Non idoneo/a per AVS?** : la macchina virtuale non verrà avviata nella soluzione Azure VMware. Ad esempio, se un server VMware locale dispone di un dispositivo esterno (ad esempio un CD-ROM) collegato e si sta usando VMware VMotion, l'operazione VMotion non riesce.
 - **Conformità sconosciuta**: Azure migrate non è stato possibile determinare la preparazione del server, a causa di metadati insufficienti raccolti dall'ambiente locale.

3. Esaminare lo strumento suggerito.

    - VMware HCX o Enterprise: per i server VMware, la soluzione VMware Hybrid Cloud Extension (HCX) è lo strumento di migrazione suggerito per eseguire la migrazione del carico di lavoro locale al cloud privato della soluzione VMware di Azure (AVS). Per ulteriori informazioni, .
    - Sconosciuto: per i server importati tramite un file CSV, lo strumento di migrazione predefinito è sconosciuto. Per i server VMware, tuttavia, è consigliabile usare la soluzione VMware Hybrid Cloud Extension (HCX).
4. Fare clic su uno stato di idoneità per la soluzione Azure VMware. È possibile visualizzare i dettagli sull'idoneità dei server ed eseguire il drill-down per visualizzare i dettagli dei server, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-estimates"></a>Verificare le stime dei costi

Il riepilogo della valutazione Mostra il costo stimato di calcolo e di archiviazione per i server in esecuzione in Azure. 

1. Esaminare i costi totali mensili. I costi sono aggregati per tutti i server nel gruppo valutato.

    - Le stime dei costi si basano sul numero di nodi AVS necessari considerando i requisiti di risorse di tutti i server in totale.
    - Dal momento che i prezzi per la soluzione Azure VMware vengono calcolati in base ai nodi, il costo totale non prevede la distribuzione dei costi di calcolo e dei costi di archiviazione.
    - La stima dei costi riguarda l'esecuzione dei server locali in AVS. AVS Assessment non considera i costi PaaS o SaaS.

2. Esaminare le stime di archiviazione mensili. La visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione. 
3. È possibile eseguire il drill-down per visualizzare i dettagli sui costi per server specifici.

### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Valutazione server assegna una classificazione di attendibilità alle valutazioni basate sulle prestazioni. La classificazione è compresa tra una stella (più bassa) e cinque stelle (più alta).

![Classificazione di attendibilità](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

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
- Configurare il mapping delle dipendenze [senza agente](how-to-create-group-machine-dependencies-agentless.md) o [basato sull'agente](how-to-create-group-machine-dependencies.md).
