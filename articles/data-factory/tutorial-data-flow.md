---
title: Trasformare i dati usando un flusso di dati di mapping
description: Questa esercitazione fornisce istruzioni dettagliate per l'uso di Azure Data Factory per trasformare i dati con il flusso di dati di mapping
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: 0842dad0e0ea6f9987727e8abf3d0eaf8a59e821
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517519"
---
# <a name="transform-data-using-mapping-data-flows"></a>Trasformare i dati con i flussi di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si userà l'interfaccia utente di Azure Data Factory per creare una pipeline che copia e trasforma i dati da un'origine Azure Data Lake Storage (ADLS) Gen2 a un sink ADLS Gen2 usando il flusso di dati di mapping. Il modello di configurazione di questa esercitazione può essere espanso quando si trasformano i dati usando il flusso di dati di mapping

 >[!NOTE]
   >Questa esercitazione è destinata al mapping dei flussi di dati in generale. I flussi di dati sono disponibili sia in Azure Data Factory che in Synapse Pipelines. Se non si ha ancora a che fare con i flussi di dati in Azure Synapse pipeline, seguire questa Flusso di dati [usando Azure Synapse pipeline](../synapse-analytics/concepts-data-flow-overview.md) 
   
In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'Flusso di dati attività.
> * Creare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'Flusso di dati attività

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. L'archiviazione ADLS viene utilizzata come archivio *dati* di *origine e sink.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) per informazioni su come crearne uno.

Il file che si sta trasformando in questa esercitazione è MoviesDB.csv, disponibile [qui.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo in locale come file CSV. Per caricare il file nell'account di archiviazione, vedere [Caricare BLOB con](../storage/blobs/storage-quickstart-blobs-portal.md)portale di Azure . Gli esempi fanno riferimento a un contenitore denominato "sample-data".

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea un data factory e si apre l'esperienza Data Factory UX per creare una pipeline nel data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, Data Factory'interfaccia utente è supportata solo nei web browser Microsoft Edge e Google Chrome.
2. Nel menu sinistro selezionare **Crea una risorsa** > **Integrazione** > **Data factory**:

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nuovo data factory messaggio di errore per il nome duplicato.":::
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
5. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
6. In **Versione** selezionare **V2**.
7. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono essere in altre aree.
8. Selezionare **Crea**.
9. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
10. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'Flusso di dati attività

In questo passaggio si creerà una pipeline che contiene un'Flusso di dati attività.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)

1. Nella scheda **Generale** della pipeline immettere **TransformMovies** per **Nome** della pipeline.
1. Nel riquadro **Attività** espandere la casella di controllo **Sposta e** trasforma. Trascinare l'attività **Flusso di dati** dal riquadro all'area di disegno della pipeline.

    ![Screenshot che mostra il canvas della pipeline in cui è possibile eliminare l'Flusso di dati attività.](media/tutorial-data-flow/activity1.png)
1. Nella finestra **popup Adding Flusso di dati** (Aggiunta di Flusso di dati) selezionare Create new Flusso di dati **(Crea** nuovo Flusso di dati quindi assegnare al flusso di dati il **nome TransformMovies).** Fare clic su Fine al termine.

    ![Screenshot che mostra dove assegnare un nome al flusso di dati quando si crea un nuovo flusso di dati.](media/tutorial-data-flow/activity2.png)
1. Nella barra superiore dell'area di disegno della pipeline far scorrere il **Flusso di dati dispositivo di scorrimento debug.** La modalità di debug consente di testare in modo interattivo la logica di trasformazione in un cluster Spark attivo. Flusso di dati i cluster possono richiedere da 5 a 7 minuti per il riscaldamento e agli utenti è consigliabile attivare prima il debug se si prevede di Flusso di dati sviluppo. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

    ![Flusso di dati attività](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Compilare la logica di trasformazione nell'area di disegno del flusso di dati

Dopo aver creato il Flusso di dati, si verrà inviati automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà compilato un flusso di dati che accetta il moviesDB.csv nell'archiviazione ADLS e aggrega la classificazione media delle comete dal 1910 al 2000. Questo file verrà quindi scritto nuovamente nell'archiviazione ADLS.

1. Nell'area di disegno del flusso di dati aggiungere un'origine facendo clic sulla **casella Aggiungi** origine.

    ![Screenshot che mostra la casella Aggiungi origine.](media/tutorial-data-flow/dataflow2.png)
1. Assegnare all'origine **il nome MoviesDB**. Fare clic su **Nuovo per** creare un nuovo set di dati di origine.

    ![Screenshot che mostra dove selezionare Nuovo dopo aver specificato l'origine.](media/tutorial-data-flow/dataflow3.png)
1. Scegliere **Azure Data Lake Storage Gen2**. Fare clic su Continue.

    ![Screenshot che mostra il riquadro Azure Data Lake Storage Gen2 personalizzato.](media/tutorial-data-flow/dataset1.png)
1. Scegliere **DelimitedText.** Fare clic su Continue.

    ![Screenshot che mostra il riquadro DelimitedText.](media/tutorial-data-flow/dataset2.png)
1. Assegnare al set di **dati il nome MoviesDB**. Nell'elenco a discesa del servizio collegato scegliere **Nuovo.**

    ![Screenshot che mostra l'elenco a discesa Servizio collegato.](media/tutorial-data-flow/dataset3.png)
1. Nella schermata di creazione del servizio collegato assegnare al servizio collegato ADLS Gen2 il nome **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa esercitazione si usa la chiave dell'account per connettersi all'account di archiviazione. È possibile fare clic **su Test connessione** per verificare che le credenziali siano state immesse correttamente. Al termine, fare clic su Crea.

    ![Servizio collegato](media/tutorial-data-flow/ls1.png)
1. Quando si torna alla schermata di creazione del set di dati, immettere dove si trova il file nel **campo Percorso** file. In questa esercitazione il file moviesDB.csv si trova nel contenitore sample-data. Poiché il file ha intestazioni, selezionare **Prima riga come intestazione**. Selezionare **From connection/store (Da connessione/archivio)** per importare lo schema di intestazione direttamente dal file nell'archivio. Fare clic su OK al termine dell'operazione.

    ![Set di dati](media/tutorial-data-flow/dataset4.png)
1. Se il cluster di debug è stato avviato, passare  alla scheda **Anteprima** dati della trasformazione di origine e fare clic su Aggiorna per ottenere uno snapshot dei dati. È possibile usare l'anteprima dei dati per verificare che la trasformazione sia configurata correttamente.

    ![Screenshot che mostra dove è possibile visualizzare in anteprima i dati per verificare che la trasformazione sia configurata correttamente.](media/tutorial-data-flow/dataflow4.png)
1. Accanto al nodo di origine nell'area di disegno del flusso di dati fare clic sull'icona del segno più per aggiungere una nuova trasformazione. La prima trasformazione che si sta aggiungendo è un **filtro**.

    ![Flusso di dati Canvas](media/tutorial-data-flow/dataflow5.png)
1. Assegnare alla trasformazione filtro il **nome FilterYears**. Fare clic sulla casella dell'espressione accanto **a Filtra per** aprire il generatore di espressioni. Qui si specifica la condizione di filtro.

    ![Screenshot che mostra la casella Filtra in base all'espressione.](media/tutorial-data-flow/filter1.png)
1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in varie trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dello schema di input e parametri definiti dall'utente. Per altre informazioni su come compilare espressioni, vedere Flusso di dati [generatore di espressioni](concepts-data-flow-expression-builder.md).

    In questa esercitazione si vuole filtrare i film di genere comedy che sono usciti tra gli anni 1910 e 2000. Poiché year è attualmente una stringa, è necessario convertirla in un numero intero usando la ```toInteger()``` funzione . Usare gli operatori maggiore o uguale a (>=) e minore o uguale a (<=) per eseguire il confronto con i valori letterali dell'anno 1910 e 200-. Unione di queste espressioni con l'operatore e (&&). L'espressione viene specificata come:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Per trovare i film che sono comemedie, è possibile usare la funzione per trovare il ```rlike()``` modello "Comedy" nei generi di colonna. Unione dell'espressione rlike con il confronto dell'anno per ottenere:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Se è attivo un cluster di debug, è possibile verificare la logica facendo clic su **Aggiorna** per visualizzare l'output dell'espressione rispetto agli input usati. Esiste più di una risposta corretta su come è possibile eseguire questa logica usando il linguaggio delle espressioni del flusso di dati.

    ![Filtra](media/tutorial-data-flow/filter2.png)

    Al **termine dell'operazione,** fare clic su Salva e fine.

1. Recuperare **un'anteprima dei** dati per verificare che il filtro funzioni correttamente.

    ![Screenshot che mostra l'anteprima dei dati recuperata.](media/tutorial-data-flow/filter3.png)
1. La trasformazione successiva che verrà aggiunta è una trasformazione **Aggregazione** in **Modifica schema**.

    ![Screenshot che mostra il modificatore dello schema Aggregate.](media/tutorial-data-flow/agg1.png)
1. Assegnare alla trasformazione aggregazione **il nome AggregateComedyRatings**. Nella scheda **Raggruppa per** selezionare **l'anno** dall'elenco a discesa per raggruppare le aggregazioni in base all'anno di uscita del film.

    ![Screenshot che mostra l'opzione year nella scheda Raggruppa per in Impostazioni di aggregazione.](media/tutorial-data-flow/agg2.png)
1. Passare alla **scheda Aggregazioni.** Nella casella di testo a sinistra assegnare alla colonna di aggregazione il nome **AverageComedyRating**. Fare clic sulla casella di espressione destra per immettere l'espressione di aggregazione tramite il generatore di espressioni.

    ![Screenshot che mostra l'opzione year nella scheda Aggregazioni in Impostazioni di aggregazione.](media/tutorial-data-flow/agg3.png)
1. Per ottenere la media della colonna **Rating**, usare la ```avg()``` funzione di aggregazione . Poiché **Rating** è una stringa e accetta un input numerico, è necessario convertire il valore ```avg()``` in un numero tramite la funzione ```toInteger()``` . L'espressione è simile alla seguente:

    ```avg(toInteger(Rating))```

    Al **termine, fare clic su** Salva e fine.

    ![Screenshot che mostra l'espressione salvata.](media/tutorial-data-flow/agg4.png)
1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** **e AverageComedyRating.**

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. Successivamente, si vuole aggiungere una trasformazione **Sink** in **Destinazione**.

    ![Screenshot che mostra dove aggiungere una trasformazione sink in Destinazione.](media/tutorial-data-flow/sink1.png)
1. Assegnare al sink il **nome Sink**. Fare **clic su Nuovo** per creare il set di dati del sink.

    ![Screenshot che mostra dove è possibile assegnare un nome al sink e creare un nuovo set di dati del sink.](media/tutorial-data-flow/sink2.png)
1. Scegliere **Azure Data Lake Storage Gen2**. Fare clic su Continue.

    ![Screenshot che mostra il riquadro Azure Data Lake Storage Gen2 che è possibile scegliere.](media/tutorial-data-flow/dataset1.png)
1. Scegliere **DelimitedText.** Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset2.png)
1. Assegnare al set di dati sink **il nome MoviesSink**. Per il servizio collegato, scegliere il servizio collegato ADLS gen2 creato nel passaggio 6. Immettere una cartella di output in cui scrivere i dati. In questa esercitazione si scrive nella cartella 'output' nel contenitore 'sample-data'. La cartella non deve esistere in anticipo e può essere creata dinamicamente. Impostare **Prima riga come intestazione** su true e selezionare Nessuno **per** Importa **schema**. Fare clic su Fine.

    ![Sink](media/tutorial-data-flow/sink3.png)

A questo punto è stata completata la compilazione del flusso di dati. È possibile eseguirlo nella pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Esecuzione e monitoraggio del Flusso di dati

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio si attiverà un'esecuzione di debug della pipeline del flusso di dati. Anche se l'anteprima dei dati non scrive dati, un'esecuzione di debug scriverà i dati nella destinazione sink.

1. Passare all'area di disegno della pipeline. Fare clic **su Debug** per attivare un'esecuzione del debug.

    ![Screenshot che mostra l'area di disegno della pipeline con Debug evidenziato.](media/tutorial-data-flow/pipeline1.png)
1. Il debug della pipeline Flusso di dati attività di debug usa il cluster di debug attivo, ma l'inizializzazione deve richiedere almeno un minuto. È possibile tenere traccia dello stato di avanzamento tramite la **scheda** Output. Al termine dell'esecuzione, fare clic sull'icona degli occhiali per aprire il riquadro di monitoraggio.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. Nel riquadro di monitoraggio è possibile visualizzare il numero di righe e il tempo impiegato in ogni passaggio della trasformazione.

    ![Screenshot che mostra il riquadro di monitoraggio in cui è possibile visualizzare il numero di righe e il tempo impiegato in ogni passaggio di trasformazione.](media/tutorial-data-flow/pipeline3.png)
1. Fare clic su una trasformazione per ottenere informazioni dettagliate sulle colonne e sul partizionamento dei dati.

    ![Monitoraggio](media/tutorial-data-flow/pipeline4.png)

Se questa esercitazione è stata eseguita correttamente, nella cartella sink dovrebbero essere state scritte 83 righe e 2 colonne. È possibile verificare che i dati siano corretti controllando l'archivio BLOB.

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questa esercitazione esegue un flusso di dati che aggrega la classificazione media delle comete dal 1910 al 2000 e scrive i dati in ADLS. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'Flusso di dati attività.
> * Creare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'Flusso di dati attività

Altre informazioni sul [linguaggio delle espressioni del flusso di dati.](data-flow-expression-functions.md)