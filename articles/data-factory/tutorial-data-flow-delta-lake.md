---
title: ETL Delta Lake con flussi di dati
description: Questa esercitazione fornisce istruzioni dettagliate per l'uso dei flussi di dati per trasformare e analizzare i dati in Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417664"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Trasformare i dati in Delta Lake usando i flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si userà l'area di disegno del flusso di dati per creare flussi di dati che consentono di analizzare e trasformare i dati in Azure Data Lake Storage (ADLS) Gen2 e di archiviarli in Delta Lake.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Si usa l'archiviazione ADLS come archivi dati di *origine* e *sink* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) per informazioni su come crearne uno.

Il file che si sta trasformando in questa esercitazione è MoviesDB.csv, disponibile [qui](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo localmente come file con estensione CSV. Per caricare il file nell'account di archiviazione, vedere [caricare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Gli esempi faranno riferimento a un contenitore denominato "Sample-Data".

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea una data factory e si apre l'Data Factory UX per creare una pipeline nell'data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, Data Factory interfaccia utente è supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa**  >  **integrazione**  >  **Data Factory**
1. Nella pagina **nuovo data factory** , in **nome**, immettere **ADFTutorialDataFactory**
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono trovarsi in altre aree.
1. Selezionare **Crea**.
1. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività flusso di dati

In questo passaggio verrà creata una pipeline che contiene un'attività flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)

1. Nella scheda **generale** della pipeline immettere **DeltaLake** per **nome** della pipeline.
1. Nella barra superiore della Factory scorrere il dispositivo di scorrimento **debug del flusso di dati** . La modalità di debug consente il test interattivo della logica di trasformazione in un cluster Spark attivo. I cluster di flussi di dati impiegano 5-7 minuti per il riscaldamento e si consiglia agli utenti di attivare prima il debug se pianificano lo sviluppo del flusso di dati. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

    ![Attività flusso di dati](media/tutorial-data-flow/dataflow1.png)
1. Nel riquadro **attività** espandere l'accordo di **spostamento e trasformazione** . Trascinare e rilasciare l'attività **flusso di dati** dal riquadro nell'area di disegno della pipeline.

    ![Screenshot che mostra l'area di disegno della pipeline in cui è possibile eliminare l'attività flusso di dati.](media/tutorial-data-flow/activity1.png)
1. Nella finestra popup **aggiunta flusso di dati** selezionare **Crea nuovo flusso di dati** e quindi assegnare un nome al flusso di dati **DeltaLake**. Fare clic su Fine al termine.

    ![Screenshot che mostra dove assegnare un nome al flusso di dati quando si crea un nuovo flusso di dati.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logica di trasformazione compilazione nell'area di disegno del flusso di dati

In questa esercitazione si genereranno due flussi di dati. Il primo flusso di dati è un'origine semplice da affondare per generare una nuova versione Delta Lake dal file CSV dei film riportato sopra. Infine, la progettazione del flusso verrà creata di seguito per aggiornare i dati in Delta Lake.

![Flusso finale](media/data-flow/data-flow-tutorial-6.png "Flusso finale")

### <a name="tutorial-objectives"></a>Obiettivi dell'esercitazione

1. Per ottenere l'origine del set di dati MoviesCSV, creare un nuovo Delta Lake
1. Compila la logica per le classificazioni aggiornate per i filmati 1988 in ' 1'
1. Elimina tutti i film da 1950
1. Inserire nuovi filmati per 2021 duplicando i film da 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Inizia da un'area di disegno del flusso di dati vuota

1. Fare clic sulla trasformazione origine
1. Fare clic su nuovo accanto a set di dati nel pannello inferiore 1 creare un nuovo servizio collegato per ADLS Gen2
1. Scegliere il testo delimitato per il tipo di set di dati
1. Denominare il set di dati "MoviesCSV" 
1. Puntare al file MoviesCSV caricato nell'archivio precedente
1. Imposta come delimitato da virgole e Includi intestazione nella prima riga 
1. Passare alla scheda proiezione di origine e fare clic su "rileva tipi di dati"
1. Una volta creato il set di proiezione, è possibile continuare 
1. Aggiungere una trasformazione sink
1. Delta è un tipo di set di dati inline. Sarà necessario puntare al ADLS Gen2 account di archiviazione.
   
   ![Set di dati inline](media/data-flow/data-flow-tutorial-5.png "Set di dati inline")

1. Scegliere il nome di una cartella nel contenitore di archiviazione in cui si desidera creare il Delta Delta del dispositivo ADF
1. Tornare alla finestra di progettazione della pipeline e fare clic su debug per eseguire la pipeline in modalità di debug solo con questa attività flusso di dati nell'area di disegno. Questa operazione genererà il nuovo Delta Lake in ADLS Gen2.
1. Da risorse della Factory fare clic su nuovo > flusso di dati 
1. Usare di nuovo MoviesCSV come origine e fare di nuovo clic su "rileva tipi di dati"
1. Aggiungere una trasformazione filtro alla trasformazione di origine nel grafico
1. Consenti solo le righe di film che corrispondono ai tre anni da usare, che saranno 1950, 1988 e 1960
1. Aggiornare le classificazioni per ogni film 1988 a' 1' aggiungendo ora una trasformazione colonna derivata alla trasformazione filtro
1. Nella stessa colonna derivata creare i film per 2021 prendendo un anno esistente e modificare l'anno in 2021. È ora di selezionare 1960.
1. Questo è l'aspetto delle tre colonne derivate

   ![Colonna derivata](media/data-flow/data-flow-tutorial-2.png "Colonna derivata")
   
1. ```Update, insert, delete, and upsert``` i criteri vengono creati nella trasformazione alter Row. Aggiungere una trasformazione alter Row dopo la colonna derivata.
1. I criteri alter Row dovrebbero avere un aspetto simile al seguente.

   ![Alter Row](media/data-flow/data-flow-tutorial-3.png "Alter Row")
   
1. Ora che sono stati impostati i criteri appropriati per ogni tipo di riga ALTER, verificare che siano state impostate le regole di aggiornamento appropriate per la trasformazione sink

   ![Sink](media/data-flow/data-flow-tutorial-4.png "Sink")
   
1. Qui viene usato il sink Delta Lake per la ADLS Gen2 data Lake e vengono consentiti gli inserimenti, gli aggiornamenti e le eliminazioni. 
1. Si noti che le colonne chiave sono costituite da una chiave composta costituita dalla colonna della chiave primaria del film e dall'anno. Ciò è dovuto al fatto che sono stati creati Fake 2021 film duplicando le 1960 righe. In questo modo si evitano conflitti durante la ricerca delle righe esistenti fornendo l'univocità.

### <a name="download-completed-sample"></a>Scarica l'esempio completato
[Ecco una soluzione di esempio per la pipeline Delta con un flusso di dati per le righe di aggiornamento/eliminazione nel lago:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [linguaggio delle espressioni del flusso di dati](data-flow-expression-functions.md).
