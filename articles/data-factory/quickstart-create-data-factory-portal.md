---
title: Creare una data factory di Azure tramite l'interfaccia utente di Azure Data Factory
description: Creare una data factory con una pipeline che copia i dati da una posizione nell'archivio BLOB di Azure a un'altra posizione.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655101"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Avvio rapido: Creare una data factory usando l'interfaccia utente di Azure Data Factory 

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La guida introduttiva descrive come usare l'interfaccia utente di Azure Data Factory per creare e monitorare una data factory. La pipeline creata in questa data factory *copia* dati da una cartella a un'altra nell'archivio BLOB di Azure. Per *trasformare* i dati usando Azure Data Factory, vedere [mapping del flusso di dati](concepts-data-flow-overview.md).

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md) prima di seguire la guida introduttiva. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Questo video contiene informazioni utili sull'interfaccia utente di Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Nel menu del portale di Azure selezionare **Crea una risorsa**.
1. Selezionare **Integrazione** e quindi **Data factory**. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Data Factory selezione nel nuovo riquadro.":::

1. Nella scheda **Informazioni di base** della pagina **Crea data factory** selezionare la **sottoscrizione** di Azure in cui si vuole creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. selezionare un gruppo di risorse esistente nell'elenco a discesa.

    b. Selezionare **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse.
    
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 

1. In **Area** selezionare la località per la data factory.

   L'elenco mostra solo le località supportate da Data Factory e in cui verranno archiviati i metadati di Azure Data Factory. Gli archivi dati associati (come Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (come Azure HDInsight) usati da Data Factory possono essere eseguiti in altre aree.
 
1. In **Nome** immettere **ADFTutorialDataFactory**.
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, **&lt;nomeutente&gt;ADFTutorialDataFactory**, e provare di nuovo a crearla. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nuovo data factory messaggio di errore per il nome duplicato.":::

1. Per **Versione** selezionare **V2**.

1. Selezionare **Avanti: Configurazione GIT** e quindi selezionare la casella di controllo **Configure Git later** (Configura GIT in un secondo momento).

1. Selezionare **Rivedi e crea** e quindi, una volta superata la convalida, selezionare **Crea**. Al termine della creazione, selezionare **Vai alla risorsa** per passare alla pagina **Data Factory**. 

1. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Azure Data Factory in una scheda del browser distinta.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Home page per la Azure Data Factory, con il riquadro Author & monitor.":::
   
   > [!NOTE]
   > Se il Web browser risulta bloccato su "Concessione autorizzazioni in corso", deselezionare la casella di controllo **Block third-party cookies and site data** (Blocca cookie e dati del sito di terze parti). In alternativa, mantenere selezionata la casella di controllo, creare un'eccezione per **login.microsoftonline.com** e quindi provare di nuovo ad aprire l'app.
   

## <a name="create-a-linked-service"></a>Creare un servizio collegato
In questa procedura viene creato un servizio collegato per collegare l'account di archiviazione di Azure alla data factory. Il servizio collegato ha le informazioni di connessione usate dal servizio Data Factory in fase di esecuzione per la connessione.

1. Nella pagina dell'interfaccia utente di Azure Data Factory aprire la scheda [**Gestisci**](./author-management-hub.md) dal riquadro sinistro.

1. Nella pagina Servizi collegati selezionare **+Nuovo** per creare un nuovo servizio collegato.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Nuovo servizio collegato.":::
   
1. Nella pagina **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e quindi **Continua**. 

1. Nella pagina New Linked Service (Azure Blob Storage) (Nuovo servizio collegato - Archiviazione BLOB di Azure) completare la procedura seguente: 

   a. Per **Nome** immettere **AzureStorageLinkedService**.

   b. Per **Nome account di archiviazione** selezionare il nome dell'account di archiviazione di Azure.

   c. Selezionare **Connessione di test** per confermare che il servizio Data Factory possa connettersi all'account di archiviazione. 

   d. Selezionare **Crea** per salvare il servizio collegato. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Servizio collegato.":::


## <a name="create-datasets"></a>Creare set di dati
In questa procedura verranno creati due set di dati: **InputDataset** e **OutputDataset**. I set di dati sono di tipo **AzureBlob**. Fanno riferimento al servizio collegato Archiviazione di Azure creato nella sezione precedente. 

Il set di dati di input rappresenta i dati di origini nella cartella di input. Nella definizione del set di dati di input specificare il contenitore BLOB (**adftutorial**), la cartella (**input**) e il file (**emp.txt**) che includono i dati di origine. 

Il set di dati di output rappresenta i dati copiati nella destinazione. Nella definizione del set di dati di output specificare il contenitore BLOB (**adftutorial**), la cartella (**output**) e il file in cui vengono copiati i dati. A ogni esecuzione di una pipeline è associato un ID univoco. È possibile accedere a questo ID usando la variabile di sistema **RunId**. Il nome del file di output viene valutato dinamicamente in base all'ID dell'esecuzione della pipeline.   

Nelle impostazioni del servizio collegato è stato specificato l'account di archiviazione di Azure che contiene i dati di origine. Nelle impostazioni del set di dati di origine specificare la posizione esatta in cui si trovano i dati, ovvero contenitore BLOB, cartella e file. Nelle impostazioni del set di dati di sink specificare la posizione in cui vengono copiati i dati, ovvero contenitore BLOB, cartella e file. 

1. Selezionare la scheda **Crea** dal riquadro sinistro.

1. Selezionare il pulsante **+** (segno più) e quindi selezionare **Set di dati**.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Menu per la creazione di un set di dati.":::

1. Nella pagina **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi selezionare **Continua**. 

1. Nella pagina **Select Format** (Seleziona formato) scegliere il tipo di formato dei dati e quindi fare clic su **Continua**. In questo caso selezionare **Binario** quando si copiano i file senza analizzare il contenuto.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Selezionare formato.":::   
   
1. Nella pagina **Imposta proprietà** completare la procedura seguente:

    a. In **Nome** immettere **InputDataset**. 

    b. Per **Servizio collegato** selezionare **AzureStorageLinkedService**.

    c. Per **Percorso file** selezionare il pulsante **Sfoglia**.

    d. Nella finestra **Choose a file or folder** (Scegliere un file o una cartella) passare alla cartella **input** nel contenitore **adftutorial**, selezionare il file **emp.txt** e quindi selezionare **OK**.
    
    e. Selezionare **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Impostare le proprietà per InputDataset.":::

1. Ripetere la procedura per creare il set di dati di output:  

    a. Selezionare il pulsante **+** (segno più) e quindi selezionare **Set di dati**.

    b. Nella pagina **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi selezionare **Continua**.

    c. Nella pagina **Select Format** (Seleziona formato) scegliere il tipo di formato dei dati e quindi fare clic su **Continua**.

    d. Nella pagina **Imposta proprietà** specificare **OutputDataset** per il nome. Selezionare **AzureStorageLinkedService** come servizio collegato.

    e. In **Percorso file** immettere **adftutorial/output**. Se la cartella **output** non esiste, l'attività di copia la crea in fase di esecuzione.

    f. Selezionare **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="Impostare le proprietà per OutputDataset.":::    

## <a name="create-a-pipeline"></a>Creare una pipeline 
In questa procedura viene creata e convalidata una pipeline con un'attività di copia che usa i set di dati di input e di output. Con l'attività di copia i dati vengono copiati dal file specificato nelle impostazioni del set di dati di input al file specificato nelle impostazioni del set di dati di output. Se il set di dati di input specifica solo una cartella, non il nome file, con l'attività di copia tutti i file della cartella di origine vengono copiati nella destinazione. 

1. Selezionare il pulsante **+** (segno più) e quindi selezionare **Pipeline**. 

1. Nel pannello Generale in **Proprietà** specificare **CopyPipeline** per **Nome**. Comprimere quindi il pannello facendo clic sull'icona Proprietà nell'angolo superiore destro.

1. Nella casella degli strumenti **Attività** espandere **Move & Transform** (Sposta e trasforma). Trascinare l'attività **Copia dati** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. È anche possibile eseguire una ricerca di attività nella casella degli strumenti **Attività**. Specificare **CopyFromBlobToBlob** per **Nome**.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Creazione di un'attività copia dati.":::   

1. Passare alla scheda **Origine** nelle impostazioni dell'attività Copia e selezionare **InputDataset** per **Source Dataset** (Set di dati di origine).

1. Passare alla scheda **Sink** nelle impostazioni dell'attività Copia e selezionare **OutputDataset** per **Sink Dataset** (Set di dati sink).

1. Fare clic su **Convalida** sulla barra degli strumenti della pipeline sopra l'area di disegno per convalidare le impostazioni della pipeline. Assicurarsi che la pipeline sia stata convalidata correttamente. Per chiudere l'output di convalida, selezionare il pulsante Convalida nell'angolo in alto a destra. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Convalidare una pipeline.":::

## <a name="debug-the-pipeline"></a>Eseguire il debug della pipeline
In questo passaggio viene eseguito il debug della pipeline prima della distribuzione in Data Factory. 

1. Nella barra degli strumenti della pipeline sopra l'area di disegno fare clic su **Debug** per attivare un'esecuzione dei test. 
    
1. Assicurarsi che sia visualizzato lo stato dell'esecuzione della pipeline nella scheda **Output** delle impostazioni della pipeline nella parte inferiore. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Output dell'esecuzione della pipeline":::

1. Assicurarsi che venga visualizzato un file di output nella cartella **output** del contenitore **adftutorial**. Se la cartella output non esiste, il servizio Data Factory la crea automaticamente. 

## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline
In questa procedura vengono distribuite entità (servizi collegati, set di dati, pipeline) in Azure Data Factory. Viene quindi attivata manualmente un'esecuzione della pipeline. 

1. Prima di attivare una pipeline, è necessario pubblicare entità in Data Factory. Per pubblicare, selezionare **Pubblica tutti** nella parte superiore. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Pubblica tutto.":::    

1. Per attivare manualmente la pipeline, selezionare **Aggiungi trigger** sulla barra degli strumenti della pipeline e quindi selezionare **Trigger Now** (Attiva adesso). Nella pagina **Esecuzione della pipeline** scegliere **OK**.

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Usare il pulsante **Aggiorna** per aggiornare l'elenco.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="Scheda per il monitoraggio delle esecuzioni di pipeline"::: 
1. Selezionare il collegamento **CopyPipeline**. In questa pagina verrà visualizzato lo stato dell'esecuzione dell'attività di copia. 

1. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali). Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività Copia](copy-activity-overview.md). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Dettagli dell'operazione di copia.":::
1. Assicurarsi che un nuovo file sia visualizzato nella cartella **output**. 
1. È possibile tornare alla visualizzazione **Esecuzioni della pipeline** dalla visualizzazione **Esecuzioni attività** selezionando il collegamento **Esecuzioni della pipeline**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Attivare la pipeline in base a una pianificazione
La procedura è facoltativa in questa esercitazione. È possibile creare un *trigger di pianificazione* per pianificare l'esecuzione periodica (ogni ora, ogni giorno e così via) della pipeline. In questa procedura viene creato un trigger per l'esecuzione ogni minuto fino alla data e ora di fine specificate. 

1. Passare alla scheda **Autore**. 

1. Passare alla pipeline, selezionare **Aggiungi trigger** sulla barra degli strumenti della pipeline e quindi selezionare **New/Edit** (Nuovo/Modifica). 

1. Nella pagina **Add Triggers** (Aggiungi trigger) selezionare **Choose trigger** (Scegli trigger) e quindi selezionare **Nuovo**. 

1. Nella pagina **Nuovo trigger**, in **Fine**, selezionare **In data**, specificare un'ora di fine successiva di qualche minuto all'ora corrente e quindi selezionare **OK**. 

   Per ogni esecuzione della pipeline sono previsti costi, quindi specificare un'ora di fine successiva di qualche minuto all'ora di inizio. Assicurarsi che si tratti dello stesso giorno. Assicurarsi tuttavia che il tempo specificato tra l'ora di pubblicazione e l'ora di fine sia sufficiente per l'esecuzione della pipeline. Il trigger viene applicato solo dopo la pubblicazione della soluzione in Data Factory, non quando si salva il trigger nell'interfaccia utente. 

1. Nella pagina **Nuovo trigger** selezionare la casella di controllo **Attivato** e quindi selezionare **OK**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Nuova impostazione del trigger.":::   
1. Rivedere il messaggio di avviso e selezionare **OK**.

1. Selezionare **Pubblica tutti** per pubblicare le modifiche in Data Factory. 

1. Passare alla scheda **Monitoraggio** a sinistra. Selezionare **Aggiorna** per aggiornare l'elenco. La pipeline viene eseguita una volta al minuto a partire dall'ora di pubblicazione fino all'ora di fine. 

   Notare i valori della colonna **ATTIVATO DA**. L'esecuzione manuale del trigger è stata generata dal passaggio precedente, ovvero **Trigger Now** (Attiva adesso). 

1. Passare alla visualizzazione **Trigger Runs** (Esecuzioni del trigger). 

1. Assicurarsi che venga creato un file di output per ogni esecuzione della pipeline fino alla data e ora di fine specificate nella cartella **output**. 

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra nell'archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, vedere le [esercitazioni](tutorial-copy-data-portal.md).
