---
title: 'Guida introduttiva: Trasformare i dati usando un flusso di dati di mapping'
description: Questa esercitazione fornisce istruzioni dettagliate per l'uso di Azure Synapse Analytics trasformare i dati con il flusso di dati di mapping.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 37696d2f4054e46125b39f3d5efa794ce54f94b5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567724"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Guida introduttiva: Trasformare i dati usando i flussi di dati di mapping

In questa guida introduttiva si userà Azure Synapse Analytics per creare una pipeline che trasforma i dati da un'origine Azure Data Lake Storage Gen2 (ADLS Gen2) a un sink ADLS Gen2 usando il flusso di dati di mapping. Il modello di configurazione in questa guida introduttiva può essere espanso quando si trasformano i dati usando il flusso di dati di mapping

In questa guida introduttiva si eserviti i passaggi seguenti:

> [!div class="checklist"]
> * Creare una pipeline con un'Flusso di dati in Azure Synapse Analytics.
> * Creare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'Flusso di dati attività

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Azure Synapse lavoro:** creare un'area di lavoro Synapse usando il portale di Azure seguendo le istruzioni in Avvio [rapido: Creare un'area di lavoro Synapse.](quickstart-create-workspace.md)
* **Account di archiviazione di Azure:** si usa l'archiviazione ADLS come *archivi* dati *di origine e sink.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) per informazioni su come crearne uno.

    Il file che si sta trasformando in questa esercitazione è MoviesDB.csv, disponibile [qui](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo in locale come file CSV. Per caricare il file nell'account di archiviazione, vedere [Caricare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Gli esempi fanno riferimento a un contenitore denominato "sample-data".

### <a name="navigate-to-the-synapse-studio"></a>Passare a Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro di Synapse nel [portale di Azure](https://ms.portal.azure.com/#home). Selezionare **Apri** nella scheda Apri Synapse Studio sotto Attività iniziali.
* Aprire [Azure Synapse Analytics](https://web.azuresynapse.net/) e accedere all'area di lavoro.

In questo argomento di avvio rapido viene usata come esempio l'area di lavoro denominata "adftest2020". Si verrà indirizzati automaticamente alla home page di Synapse Studio.

![Home page di Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'Flusso di dati attività

Una pipeline contiene il flusso logico per un'esecuzione di un set di attività. In questa sezione si creerà una pipeline che contiene un'Flusso di dati attività.

1. Passare alla scheda **Integrazione**. Selezionare l'icona con il segno più accanto all'intestazione della pipeline e quindi selezionare Pipeline.

   ![Creare una nuova pipeline](media/doc-common-process/new-pipeline.png)

1. Nella pagina **delle** impostazioni Proprietà della pipeline immettere **TransformMovies per** **Nome**.

1. In *Sposta e trasforma nel* riquadro *Attività* trascinare Flusso **di dati** nell'area di disegno della pipeline.

1. Nella finestra **popup Della pagina Aggiunta flusso** di dati selezionare Crea nuovo flusso di **dati** Flusso di  ->  **dati**. Fare clic su **OK** al termine dell'operazione.

   ![Creare un flusso di dati](media/quickstart-data-flow/new-data-flow.png)

1. Assegnare al flusso di dati **il nome TransformMovies** nella **pagina** Proprietà.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Compilare la logica di trasformazione nell'area di disegno del flusso di dati

Dopo aver creato il Flusso di dati, si verrà inviati automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà compilato un flusso di dati che accetta il MoviesDB.csv nell'archiviazione ADLS e aggrega la classificazione media delle comete dal 1910 al 2000. Questo file verrà quindi scritto nuovamente nell'archiviazione ADLS.

1. Sopra l'area di disegno del flusso di dati, scorrere il **dispositivo di scorrimento Debug flusso** di dati. La modalità di debug consente di testare in modo interattivo la logica di trasformazione in un cluster Spark attivo. Flusso di dati i cluster possono richiedere da 5 a 7 minuti per il riscaldamento e agli utenti è consigliabile attivare prima il debug se si prevede di eseguire Flusso di dati sviluppo. Per altre informazioni, vedere [Modalità di debug](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![Far scorrere il debug su](media/quickstart-data-flow/debug-on.png)

1. Nell'area di disegno del flusso di dati aggiungere un'origine facendo clic sulla **casella Aggiungi** origine.

1. Assegnare all'origine **il nome MoviesDB**. Fare clic su **Nuovo per** creare un nuovo set di dati di origine.

    ![Creare un nuovo set di dati di origine](media/quickstart-data-flow/new-source-dataset.png)

1. Scegliere **Azure Data Lake Storage Gen2**. Fare clic su Continue.

    ![Scegliere Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Scegliere **DelimitedText.** Fare clic su Continue.

1. Assegnare al set di **dati il nome MoviesDB**. Nell'elenco a discesa del servizio collegato scegliere **Nuovo**.

1. Nella schermata di creazione del servizio collegato assegnare ADLS Gen2 servizio collegato **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa guida introduttiva si usa la chiave dell'account per connettersi all'account di archiviazione. È possibile fare clic **su Test connessione** per verificare che le credenziali siano state immesse correttamente. Al termine, fare clic su **Crea**.

    ![Creare un servizio collegato di origine](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Quando si torna alla schermata di creazione del set di dati, nel **campo Percorso** file immettere dove si trova il file. In questa guida introduttiva il file "MoviesDB.csv" si trova nel contenitore "sample-data". Poiché il file ha intestazioni, selezionare **Prima riga come intestazione**. Selezionare **From connection/store (Da connessione/archivio)** per importare lo schema di intestazione direttamente dal file nell'archivio. Fare clic su **OK** al termine dell'operazione.

    ![Impostazioni del set di dati di origine](media/quickstart-data-flow/source-dataset-properties.png)

1. Se il cluster di debug è stato avviato, passare alla scheda **Anteprima** dati della trasformazione di origine e fare clic **su** Aggiorna per ottenere uno snapshot dei dati. È possibile usare l'anteprima dei dati per verificare che la trasformazione sia configurata correttamente.

    ![Anteprima dati](media/quickstart-data-flow/data-preview.png)

1. Accanto al nodo di origine nell'area di disegno del flusso di dati fare clic sull'icona del segno più per aggiungere una nuova trasformazione. La prima trasformazione che si sta aggiungendo è un **filtro**.

    ![Aggiungere un filtro](media/quickstart-data-flow/add-filter.png)

1. Assegnare alla trasformazione filtro il **nome FilterYears.** Fare clic sulla casella dell'espressione accanto **a Filtra in base** a per aprire il generatore di espressioni. Qui si specifica la condizione di filtro.

1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in varie trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dello schema di input e parametri definiti dall'utente. Per altre informazioni su come compilare le espressioni, vedere Flusso di dati [generatore di espressioni.](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

    In questa guida introduttiva si vogliono filtrare i film di genere comedy che sono stati emersi tra gli anni 1910 e 2000. Poiché year è attualmente una stringa, è necessario convertirla in un intero usando la ```toInteger()``` funzione . Usare gli operatori maggiore o uguale a (>=) e minore o uguale a (<=) per eseguire il confronto con i valori letterali dell'anno 1910 e 200-. Unione di queste espressioni con l'operatore e (&&). L'espressione viene fornita come:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Per trovare i film che sono comemedie, è possibile usare la funzione per trovare il ```rlike()``` modello "Comedy" nei generi delle colonne. Unione dell'espressione rlike con il confronto dell'anno per ottenere:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Specificare la condizione di filtro](media/quickstart-data-flow/visual-expression-builder.png)

    Se è attivo un cluster di debug, è possibile verificare la logica facendo clic su **Aggiorna** per visualizzare l'output dell'espressione rispetto agli input usati. Esiste più di una risposta corretta su come è possibile eseguire questa logica usando il linguaggio delle espressioni del flusso di dati.

    Al **termine dell'operazione,** fare clic su Salva e fine.

1. Recuperare **un'anteprima dei** dati per verificare che il filtro funzioni correttamente.

1. La trasformazione successiva che verrà aggiunta è una trasformazione **Aggregazione** in **Modifica schema**.

    ![Aggiungere un'aggregazione](media/quickstart-data-flow/add-aggregate.png)

1. Assegnare alla trasformazione aggregazione il **nome AggregateComedyRatings**. Nella scheda **Raggruppa per** selezionare l'anno dall'elenco a discesa per raggruppare le aggregazioni in base all'anno di uscita del film. 

    ![Impostazioni di aggregazione 1](media/quickstart-data-flow/aggregate-settings.png)

1. Passare alla **scheda Aggregazioni.** Nella casella di testo a sinistra assegnare alla colonna di aggregazione il nome **AverageComedyRating**. Fare clic sulla casella di espressione destra per immettere l'espressione di aggregazione tramite il generatore di espressioni.

    ![Impostazioni di aggregazione 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Per ottenere la media della colonna **Rating,** usare la ```avg()``` funzione di aggregazione . Poiché **Rating** è una stringa e accetta un input numerico, è necessario convertire il valore ```avg()``` in un numero tramite la funzione ```toInteger()``` . Questa espressione è simile alla seguente:

    ```avg(toInteger(Rating))```

    Al **termine, fare clic su** Salva e fine.

    ![Average comedy rating](media/quickstart-data-flow/average-comedy-rating.png)

1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** e **AverageComedyRating.**

    ![Anteprima dei dati aggregati](media/quickstart-data-flow/transformation-output.png)

1. Successivamente, si vuole aggiungere una trasformazione **Sink** in **Destinazione**.

    ![Aggiungere un sink](media/quickstart-data-flow/add-sink.png)

1. Assegnare al sink il **nome**. Fare clic **su Nuovo** per creare il set di dati sink.

1. Scegliere **Azure Data Lake Storage Gen2**. Fare clic su Continue.

1. Scegliere **DelimitedText.** Fare clic su Continue.

1. Assegnare al set di dati sink **il nome MoviesSink**. Per il servizio collegato, scegliere ADLS Gen2 servizio collegato creato nel passaggio 7. Immettere una cartella di output in cui scrivere i dati. In questa guida introduttiva si scrive nella cartella "output" nel contenitore "sample-data". La cartella non deve esistere in anticipo e può essere creata dinamicamente. Impostare **Prima riga come intestazione** su true e selezionare Nessuno **per** **Importa schema.** Fare clic su **OK** al termine dell'operazione.

    ![Proprietà del set di dati sink](media/quickstart-data-flow/sink-dataset-properties.png)

A questo punto è stata completata la creazione del flusso di dati. È possibile eseguirlo nella pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Esecuzione e monitoraggio del Flusso di dati

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio si attiverà un'esecuzione di debug della pipeline del flusso di dati. Anche se l'anteprima dei dati non scrive dati, un'esecuzione di debug scriverà i dati nella destinazione sink.

1. Passare all'area di disegno della pipeline. Fare clic **su Debug** per attivare un'esecuzione del debug.

    ![Pipeline di debug](media/quickstart-data-flow/debug-pipeline.png)

1. Il debug della pipeline Flusso di dati attività di debug usa il cluster di debug attivo, ma l'inizializzazione deve richiedere almeno un minuto. È possibile tenere traccia dello stato di avanzamento tramite la **scheda** Output. Al termine dell'esecuzione, fare clic sull'icona degli occhiali per aprire il riquadro di monitoraggio.

    ![Debug dell'output](media/quickstart-data-flow/debugging-output.png)

1. Nel riquadro di monitoraggio è possibile visualizzare il numero di righe e il tempo impiegato in ogni passaggio della trasformazione.

    ![Monitoraggio delle trasformazioni](media/quickstart-data-flow/4-transformations.png)

1. Fare clic su una trasformazione per ottenere informazioni dettagliate sulle colonne e sul partizionamento dei dati.

    ![Dettagli della trasformazione](media/quickstart-data-flow/transformation-details.png)

Se questa guida introduttiva è stata seguita correttamente, nella cartella sink dovrebbero essere state scritte 83 righe e 2 colonne. È possibile verificare i dati controllando l'archiviazione BLOB.


## <a name="next-steps"></a>Passaggi successivi

Passare agli articoli seguenti per informazioni sul supporto Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Pipeline e attività](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Panoramica del flusso di dati di mapping](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Linguaggio delle espressioni del flusso di dati](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)