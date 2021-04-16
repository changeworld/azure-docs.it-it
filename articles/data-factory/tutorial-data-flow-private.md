---
title: Trasformare i dati con un flusso Azure Data Factory dati di mapping della rete virtuale gestita
description: Questa esercitazione fornisce istruzioni dettagliate per l'uso di Azure Data Factory per trasformare i dati con i flussi di dati di mapping.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518279"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Trasformare i dati in modo sicuro usando il flusso di dati di mapping

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](./introduction.md).

In questa esercitazione si userà l'interfaccia utente di Data Factory per creare una pipeline che copia e trasforma i dati da un'origine *Azure Data Lake Storage Gen2 a un sink Data Lake Storage Gen2 (entrambi* consentendo l'accesso solo alle reti selezionate) usando il flusso di dati di mapping in una rete virtuale gestita di [Data Factory](managed-virtual-network-private-endpoint.md). È possibile espandere il modello di configurazione in questa esercitazione quando si trasformano i dati usando il flusso di dati di mapping.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
>
> * Creare una data factory.
> * Creare una pipeline con un'attività flusso di dati.
> * Creare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'attività flusso di dati.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. È possibile usare Data Lake Storage come *archivi dati* di origine *e sink.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal) per informazioni su come crearne uno. *Assicurarsi che l'account di archiviazione consenta l'accesso solo da reti selezionate.* 

Il file che verrà trasformato in questa esercitazione è moviesDB.csv disponibile in questo sito di contenuto [GitHub.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo in locale come file CSV. Per caricare il file nell'account di archiviazione, vedere [Caricare BLOB con](../storage/blobs/storage-quickstart-blobs-portal.md)portale di Azure . Gli esempi fanno riferimento a un contenitore denominato **sample-data.**

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea un data factory e si apre l'interfaccia Data Factory per creare una pipeline nel data factory.

1. Aprire Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**.
1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory (ad esempio, nomeADFTutorialDataFactory). Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    * Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.
    * Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono visualizzate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e database SQL di Azure) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono essere in altre aree.

1. Selezionare **Crea**.
1. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina **Data Factory**.
1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Creare un Azure IR in Data Factory virtuale gestita

In questo passaggio si crea un Azure IR e si abilita Data Factory rete virtuale gestita.

1. Nel portale Data Factory, passare a **Gestisci** e selezionare **Nuovo** per creare una nuova Azure IR.

   ![Screenshot che mostra la creazione di un nuovo Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Nella pagina **Configurazione del runtime di integrazione** scegliere il runtime di integrazione da creare in base alle funzionalità necessarie. In questa esercitazione selezionare **Azure, Self-Hosted e** quindi fare clic su **Continua.** 
1. Selezionare **Azure** e quindi fare clic **su Continua** per creare un runtime di integrazione di Azure.

   ![Screenshot che mostra una nuova Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. In **Configurazione della rete virtuale (anteprima)** selezionare **Abilita**.

   ![Screenshot che mostra l'abilitazione di una nuova Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Selezionare **Crea**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività flusso di dati

In questo passaggio si creerà una pipeline che contiene un'attività flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Screenshot che mostra la creazione di una pipeline.](./media/doc-common-process/get-started-page.png)

1. Nel riquadro delle proprietà per la pipeline immettere **TransformMovies come** nome della pipeline.
1. Nel riquadro **Attività** espandere **Sposta e trasforma**. Trascinare  Flusso di dati'attività dal riquadro all'area di disegno della pipeline.

1. Nel popup **Aggiunta flusso di dati** selezionare Crea nuovo flusso di **dati** e quindi selezionare **Mapping Flusso di dati**. Al termine, selezionare **OK.**

    ![Screenshot che mostra Mapping Flusso di dati.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Assegnare al flusso di dati **il nome TransformMovies** nel riquadro delle proprietà.
1. Nella barra superiore dell'area di disegno della pipeline far scorrere Flusso di dati **dispositivo di scorrimento debug.** La modalità di debug consente di testare in modo interattivo la logica di trasformazione in un cluster Spark attivo. Flusso di dati i cluster possono richiedere da 5 a 7 minuti per il riscaldamento e agli utenti è consigliabile attivare prima il debug se si prevede di eseguire Flusso di dati sviluppo. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

    ![Screenshot che mostra il dispositivo di scorrimento Debug flusso di dati.](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Compilare la logica di trasformazione nell'area di disegno del flusso di dati

Dopo aver creato il flusso di dati, si verrà inviati automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà compilato un flusso di dati che accetta il file moviesDB.csv in Data Lake Storage e aggrega la classificazione media delle comete dal 1910 al 2000. Si scriverà quindi questo file in Data Lake Storage.

### <a name="add-the-source-transformation"></a>Aggiungere la trasformazione di origine

In questo passaggio si configura l'Data Lake Storage Gen2 come origine.

1. Nell'area di disegno del flusso di dati aggiungere un'origine selezionando la **casella Aggiungi** origine.

1. Assegnare all'origine **il nome MoviesDB**. Selezionare **Nuovo per** creare un nuovo set di dati di origine.

1. Selezionare **Azure Data Lake Storage Gen2** e quindi **Continua.**

1. Selezionare **DelimitedText** e quindi **Continua.**

1. Assegnare al set di **dati il nome MoviesDB**. Nell'elenco a discesa del servizio collegato selezionare **Nuovo**.

1. Nella schermata di creazione del servizio collegato assegnare Data Lake Storage Gen2 servizio collegato **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa esercitazione si usa la chiave **dell'account** per connettersi all'account di archiviazione. 

1. Assicurarsi di abilitare **Creazione interattiva**. L'a attivazione potrebbe richiedere un minuto.

    ![Screenshot che mostra la creazione interattiva.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selezionare **Test connessione**. L'errore dovrebbe verificarsi perché l'account di archiviazione non consente l'accesso senza la creazione e l'approvazione di un endpoint privato. Nel messaggio di errore verrà visualizzato un collegamento che è possibile seguire per creare un endpoint privato gestito. In alternativa, passare direttamente alla scheda **Gestisci** e seguire le istruzioni in [questa sezione](#create-a-managed-private-endpoint) per creare un endpoint privato gestito.

1. Tenere aperta la finestra di dialogo e quindi passare all'account di archiviazione.

1. Seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-a-storage-account) per approvare il collegamento privato.

1. Tornare nella finestra di dialogo. Selezionare di nuovo **Test connessione** e selezionare **Crea** per distribuire il servizio collegato.

1. Nella schermata di creazione del set di dati immettere la posizione del file nel **campo Percorso** file. In questa esercitazione il file moviesDB.csv si trova nel **contenitore sample-data**. Poiché il file ha intestazioni, selezionare la **casella di controllo First row as header** (Prima riga come intestazione). Selezionare **Da connessione/archivio** per importare lo schema di intestazione direttamente dal file nell'archiviazione. Al termine, selezionare **OK.**

    ![Screenshot che mostra il percorso di origine.](media/tutorial-data-flow-private/source-file-path.png)

1. Se il cluster di debug è stato avviato, passare  alla scheda **Anteprima** dati della trasformazione di origine e selezionare Aggiorna per ottenere uno snapshot dei dati. È possibile usare l'anteprima dei dati per verificare che la trasformazione sia configurata correttamente.

    ![Screenshot che mostra la scheda Anteprima dati.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato utilizzato il collegamento ipertestuale durante il test della connessione precedente, seguire il percorso. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato.

1. Passare alla scheda **Gestisci**.

   > [!NOTE]
   > La scheda **Gestisci** potrebbe non essere disponibile per tutte le istanze di Data Factory. Se non viene visualizzata, è possibile accedere agli endpoint privati selezionando **Autore** > **Connessioni**  > **Endpoint privato**.

1. Passare alla sezione **Managed private endpoints** (Endpoint privati gestiti).
1. Selezionare **+ Nuovo** in **Managed private endpoints** (Endpoint privati gestiti).

    ![Screenshot che mostra il pulsante Nuovo in Managed private endpoints (Endpoint privati gestiti).](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selezionare il **Azure Data Lake Storage Gen2** nell'elenco e selezionare **Continua.**
1. Immettere il nome dell'account di archiviazione creato.
1. Selezionare **Crea**.
1. Dopo alcuni secondi si noterà che il collegamento privato creato necessita dell'approvazione.
1. Selezionare l'endpoint privato creato. Verrà visualizzato un collegamento ipertestuale, seguendo il quale sarà possibile approvare l'endpoint privato a livello di account di archiviazione.

    ![Screenshot che mostra il riquadro Gestisci endpoint privato.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Approvazione di un collegamento privato in un account di archiviazione

1. Nell'account di archiviazione passare a **Connessioni a endpoint privato** nella sezione **Impostazioni**.

1. Selezionare la casella di controllo accanto all'endpoint privato creato e selezionare **Approva.**

    ![Screenshot che mostra il pulsante Approva dell'endpoint privato.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Aggiungere una descrizione e selezionare **sì**.
1. Tornare nella sezione **Managed private endpoints** (Gestisci endpoint privati) della scheda **Gestisci** in Data Factory.
1. Dopo circa un minuto, verrà visualizzata l'approvazione per l'endpoint privato.

### <a name="add-the-filter-transformation"></a>Aggiungere la trasformazione filtro

1. Accanto al nodo di origine nell'area di disegno del flusso di dati selezionare l'icona con il segno più per aggiungere una nuova trasformazione. La prima trasformazione che verrà aggiunta è un **filtro**.

    ![Screenshot che mostra l'aggiunta di un filtro.](media/tutorial-data-flow-private/add-filter.png)
1. Assegnare alla trasformazione filtro il **nome FilterYears.** Selezionare la casella dell'espressione accanto **a Filtra in base** a per aprire il generatore di espressioni. Qui si specifica la condizione di filtro.

    ![Screenshot che mostra FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in varie trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dello schema di input e parametri definiti dall'utente. Per altre informazioni su come compilare espressioni, vedere Generatore [di espressioni del flusso di dati](./concepts-data-flow-expression-builder.md).

    * In questa esercitazione si vuole filtrare i film nel genere di comedy che è uscito tra gli anni 1910 e 2000. Poiché l'anno è attualmente una stringa, è necessario convertirlo in un numero intero usando la ```toInteger()``` funzione . Usare gli operatori maggiore o uguale a (>=) e minore o uguale a (<=) per eseguire il confronto con i valori letterali dell'anno 1910 e 2000. Unione di queste espressioni con l'operatore e (&&). L'espressione viene fornita come:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Per trovare i film che sono comemedie, è possibile usare la funzione per trovare il ```rlike()``` modello "Comedy" nei generi delle colonne. Unione dell'espressione rlike con il confronto dell'anno per ottenere:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Se è attivo un cluster di debug, è possibile verificare la logica selezionando **Aggiorna** per visualizzare l'output dell'espressione rispetto agli input usati. Esiste più di una risposta corretta su come è possibile eseguire questa logica usando il linguaggio delle espressioni del flusso di dati.

        ![Screenshot che mostra l'espressione di filtro.](media/tutorial-data-flow-private/filter-expression.png)

    * Selezionare **Salva e termina** dopo aver completato l'espressione.

1. Recuperare **un'anteprima dei** dati per verificare che il filtro funzioni correttamente.

    ![Screenshot che mostra l'anteprima dei dati filtrata.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Aggiungere la trasformazione aggregazione

1. La trasformazione successiva che verrà aggiunta è una trasformazione **Aggregazione** in **Modifica schema**.

    ![Screenshot che mostra l'aggiunta dell'aggregazione.](media/tutorial-data-flow-private/add-aggregate.png)
1. Assegnare alla trasformazione aggregazione il **nome AggregateComedyRating**. Nella scheda **Raggruppa per** selezionare **anno** dalla casella di riepilogo a discesa per raggruppare le aggregazioni in base all'anno di uscita del film.

    ![Screenshot che mostra il gruppo di aggregazione.](media/tutorial-data-flow-private/group-by-year.png)
1. Passare alla **scheda Aggregazioni.** Nella casella di testo a sinistra assegnare alla colonna di aggregazione il nome **AverageComedyRating**. Selezionare la casella dell'espressione a destra per immettere l'espressione di aggregazione tramite il generatore di espressioni.

    ![Screenshot che mostra il nome della colonna di aggregazione.](media/tutorial-data-flow-private/name-column.png)
1. Per ottenere la media della colonna **Rating**, usare la funzione ```avg()``` di aggregazione . Poiché **Rating** è una stringa e accetta un input numerico, è necessario convertire il ```avg()``` valore in un numero tramite la funzione ```toInteger()``` . Questa espressione è simile alla seguente:

    ```avg(toInteger(Rating))```

1. Al **termine, selezionare** Salva e termina.

    ![Screenshot che mostra il salvataggio dell'aggregazione.](media/tutorial-data-flow-private/save-aggregate.png)
1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** e **AverageComedyRating.**

### <a name="add-the-sink-transformation"></a>Aggiungere la trasformazione sink

1. Successivamente, si vuole aggiungere una trasformazione **Sink** in **Destinazione**.

    ![Screenshot che mostra l'aggiunta di un sink.](media/tutorial-data-flow-private/add-sink.png)
1. Assegnare al sink il **nome**. Selezionare **Nuovo per** creare il set di dati sink.

    ![Screenshot che mostra la creazione di un sink.](media/tutorial-data-flow-private/create-sink.png)
1. Nella pagina **Nuovo set di** dati selezionare **Azure Data Lake Storage Gen2** e quindi **continua.**

1. Nella pagina **Seleziona formato** selezionare **DelimitedText** e quindi **continua.**

1. Assegnare al set di dati sink **il nome MoviesSink**. Per il servizio collegato, scegliere lo stesso servizio collegato **ADLSGen2** creato per la trasformazione di origine. Immettere una cartella di output in cui scrivere i dati. In questa esercitazione si scrive **nell'output** della cartella nel **contenitore sample-data**. La cartella non deve esistere in anticipo e può essere creata dinamicamente. Selezionare la **casella di controllo Prima** riga come intestazione e selezionare Nessuno **per** **Importa schema**. Selezionare **OK**.

    ![Screenshot che mostra il percorso del sink.](media/tutorial-data-flow-private/sink-file-path.png)

A questo punto è stata completata la compilazione del flusso di dati. È possibile eseguirlo nella pipeline.

## <a name="run-and-monitor-the-data-flow"></a>Eseguire e monitorare il flusso di dati

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio viene attivata un'esecuzione di debug della pipeline del flusso di dati. Mentre l'anteprima dei dati non scrive dati, un'esecuzione di debug scriverà i dati nella destinazione sink.

1. Passare all'area di disegno della pipeline. Selezionare **Debug per** attivare un'esecuzione del debug.

1. Il debug della pipeline delle attività del flusso di dati usa il cluster di debug attivo, ma l'inizializzazione richiede ancora almeno un minuto. È possibile tenere traccia dello stato di avanzamento tramite la **scheda** Output. Al termine dell'esecuzione, selezionare l'icona degli occhiali per i dettagli dell'esecuzione.

1. Nella pagina dei dettagli è possibile visualizzare il numero di righe e il tempo impiegato per ogni passaggio della trasformazione.

    ![Screenshot che mostra un'esecuzione di monitoraggio.](media/tutorial-data-flow-private/run-details.png)
1. Selezionare una trasformazione per ottenere informazioni dettagliate sulle colonne e sul partizionamento dei dati.

Se questa esercitazione è stata seguita correttamente, nella cartella sink dovrebbero essere state scritte 83 righe e 2 colonne. È possibile verificare che i dati siano corretti controllando l'archivio BLOB.

## <a name="summary"></a>Riepilogo

In questa esercitazione è stata usata l'interfaccia utente di Data Factory per creare una pipeline che copia e trasforma i dati da un'origine Data Lake Storage Gen2 a un sink Data Lake Storage Gen2 (entrambi consentono l'accesso solo alle reti selezionate) usando il flusso di dati di mapping in una rete virtuale gestita di [Data Factory.](managed-virtual-network-private-endpoint.md)