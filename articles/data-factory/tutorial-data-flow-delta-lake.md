---
title: Delta lake ETL con flussi di dati
description: Questa esercitazione fornisce istruzioni dettagliate sull'uso dei flussi di dati per trasformare e analizzare i dati in Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/16/2021
ms.openlocfilehash: 4a88ed2df74d3eebb96c42e2cdc87b14153419cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565373"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Trasformare i dati in delta lake usando i flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si userà l'area di disegno del flusso di dati per creare flussi di dati che consentono di analizzare e trasformare i dati in Azure Data Lake Storage (ADLS) Gen2 e archiviarli in Delta Lake.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. L'archiviazione ADLS viene utilizzata come archivio *dati* di *origine e sink.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) per informazioni su come crearne uno.

Il file che si sta trasformando in questa esercitazione è MoviesDB.csv, disponibile [qui.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv) Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo in locale come file CSV. Per caricare il file nell'account di archiviazione, vedere [Caricare BLOB con](../storage/blobs/storage-quickstart-blobs-portal.md)portale di Azure . Gli esempi fanno riferimento a un contenitore denominato "sample-data".

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea un data factory e si apre l'esperienza utente Data Factory per creare una pipeline nel data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, Data Factory'interfaccia utente è supportata solo nei web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa integrazione**  >    >  **Data Factory**
1. Nella pagina **Nuovo data factory,** in **Nome** immettere **ADFTutorialDataFactory**
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono essere in altre aree.
1. Selezionare **Crea**.
1. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività flusso di dati

In questo passaggio si creerà una pipeline che contiene un'attività flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)

1. Nella scheda **Generale** per la pipeline immettere **DeltaLake** come **Nome** della pipeline.
1. Nel riquadro **Attività** espandere la **accordina Sposta e** trasforma. Trascinare **l'attività Flusso di dati** dal riquadro all'area di disegno della pipeline.

    ![Screenshot che mostra l'area di disegno della pipeline in cui è possibile eliminare l'Flusso di dati attività.](media/tutorial-data-flow/activity1.png)
1. Nella finestra **popup Adding Flusso di dati** (Aggiunta di Flusso di dati) selezionare Create new Flusso di dati (Crea nuovo **Flusso di dati** quindi assegnare al flusso di dati il nome **DeltaLake).** Fare clic su Fine al termine.

    ![Screenshot che mostra dove assegnare un nome al flusso di dati quando si crea un nuovo flusso di dati.](media/tutorial-data-flow/activity2.png)
1. Nella barra superiore dell'area di disegno della pipeline scorrere il dispositivo Flusso di dati dispositivo di **scorrimento debug.** La modalità di debug consente di testare in modo interattivo la logica di trasformazione in un cluster Spark attivo. Flusso di dati i cluster possono richiedere da 5 a 7 minuti per il riscaldamento e agli utenti è consigliabile attivare prima il debug se si prevede di Flusso di dati sviluppo. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

    ![Screenshot che mostra dove si trova il dispositivo di scorrimento Debug flusso di dati.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Compilare la logica di trasformazione nell'area di disegno del flusso di dati

In questa esercitazione verranno generati due flussi di dati. Il flusso di dati originale è un'origine semplice per eseguire il sink per generare un nuovo Delta Lake dal file CSV dei film precedente. Infine, si creerà la struttura del flusso seguente per aggiornare i dati in Delta Lake.

![Flusso finale](media/data-flow/data-flow-tutorial-6.png &quot;Flusso finale")

### <a name="tutorial-objectives"></a>Obiettivi dell'esercitazione

1. Prendere l'origine del set di dati MoviesCSV dall'esempio precedente, creare un nuovo Delta Lake da essa
1. Compilare la logica per aggiornare le classificazioni per i film del 1988 a '1'
1. Eliminare tutti i film dal 1950
1. Inserire nuovi film per il 2021 duplicando i film del 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Iniziare da un'area di disegno del flusso di dati vuota

1. Fare clic sulla trasformazione di origine
1. Fare clic su nuovo accanto al set di dati nel pannello inferiore 1 Creare un nuovo servizio collegato per ADLS Gen2
1. Scegliere Testo delimitato per il tipo di set di dati
1. Assegnare al set di dati il nome "MoviesCSV" 
1. Puntare al file MoviesCSV caricato nell'archiviazione precedente
1. Impostarlo come delimitato da virgole e includere l'intestazione nella prima riga 
1. Passare alla scheda proiezione di origine e fare clic su "Rileva tipi di dati"
1. Dopo aver impostato la proiezione, è possibile continuare 
1. Aggiungere una trasformazione sink
1. Delta è un tipo di set di dati inline. È necessario puntare all'account di ADLS Gen2 di archiviazione.
   
   ![Set di dati inline](media/data-flow/data-flow-tutorial-5.png "Set di dati inline")

1. Scegliere un nome di cartella nel contenitore di archiviazione in cui si vuole creare Il delta lake
1. Tornare alla finestra di progettazione della pipeline e fare clic su Debug per eseguire la pipeline in modalità di debug solo con questa attività flusso di dati nell'area di disegno. Verrà generato il nuovo Delta Lake in ADLS Gen2.
1. In Risorse di fabbrica fare clic su nuovo > flusso di dati 
1. Usare di nuovo MoviesCSV come origine e fare di nuovo clic su "Rileva tipi di dati"
1. Aggiungere una trasformazione filtro alla trasformazione di origine nel grafico
1. Consentire solo le righe di film corrispondenti ai tre anni con cui si lavora, che saranno 1950, 1988 e 1960
1. Aggiornare le classificazioni per ogni film del 1988 a "1" aggiungendo ora una trasformazione colonna derivata alla trasformazione filtro
1. Nella stessa colonna derivata creare film per il 2021 prendendo un anno esistente e impostando l'anno su 2021. Si sceglie 1960.
1. Ecco l'aspetto delle tre colonne derivate

   ![Colonna derivata](media/data-flow/data-flow-tutorial-2.png "Colonna derivata")
   
1. ```Update, insert, delete, and upsert``` I criteri vengono creati nella trasformazione Modifica riga. Aggiungere una trasformazione Modifica riga dopo la colonna derivata.
1. I criteri di modifica delle righe dovrebbero avere un aspetto simile al seguente.

   ![Alter Row](media/data-flow/data-flow-tutorial-3.png "Alter Row")
   
1. Ora che sono stati impostati i criteri adeguati per ogni modifica del tipo di riga, verificare che siano state impostate le regole di aggiornamento appropriate nella trasformazione sink

   ![Sink](media/data-flow/data-flow-tutorial-4.png "Sink")
   
1. In questo caso viene utilizzato il sink Delta Lake per l'ADLS Gen2 data lake e sono consentiti inserimenti, aggiornamenti ed eliminazioni. 
1. Si noti che le colonne chiave sono una chiave composta composta dalla colonna chiave primaria Movie e dalla colonna year. Questo perché sono stati creati film fittizi del 2021 duplicando le righe 1960. In questo modo si evitano conflitti durante la ricerca delle righe esistenti fornendo univocità.

### <a name="download-completed-sample"></a>Scaricare l'esempio completato
[Ecco una soluzione di esempio per la pipeline Delta con un flusso di dati per le righe di aggiornamento/eliminazione nel lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [linguaggio delle espressioni del flusso di dati.](data-flow-expression-functions.md)
