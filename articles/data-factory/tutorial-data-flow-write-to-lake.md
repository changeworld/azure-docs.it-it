---
title: Procedure consigliate per la scrittura di file in data Lake con flussi di dati
description: Questa esercitazione illustra le procedure consigliate per la scrittura di file in data Lake con flussi di dati
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582247"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Procedure consigliate per la scrittura di file in data Lake con flussi di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

Questa esercitazione illustra le procedure consigliate che è possibile applicare durante la scrittura di file in ADLS Gen2 o nell'archiviazione BLOB di Azure usando flussi di dati. È necessario accedere a un account di archiviazione BLOB di Azure o Azure Data Lake Store account Gen2 per la lettura di un file parquet e quindi archiviare i risultati in cartelle.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Si usa l'archiviazione ADLS come archivi dati di *origine* e *sink* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) per informazioni su come crearne uno.

Nei passaggi di questa esercitazione si presuppone che sia presente 

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea una data factory e si apre l'Data Factory UX per creare una pipeline nell'data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, Data Factory interfaccia utente è supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa**  >  **integrazione**  >  **Data Factory**
1. Nella pagina **nuovo data factory** , in **nome**, immettere **ADFTutorialDataFactory**
1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.
    
    b. Selezionare **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere [usare i gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md).
    
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

Si utilizzeranno i dati di origine (in questa esercitazione si userà un'origine file parquet) e si userà una trasformazione sink per trasferire i dati in formato parquet usando i meccanismi più efficaci per data Lake ETL.

![Flusso finale](media/data-flow/parts-final.png "Flusso finale")

### <a name="tutorial-objectives"></a>Obiettivi dell'esercitazione

1. Scegliere uno dei set di dati di origine in un nuovo flusso di dati
1. Usare i flussi di dati per partizionare in modo efficace il set di dati sink
1. Atterrare i dati partizionati in ADLS Gen2 Lake Folders

### <a name="start-from-a-blank-data-flow-canvas"></a>Inizia da un'area di disegno del flusso di dati vuota

Per prima cosa, è necessario configurare l'ambiente del flusso di dati per ognuno dei meccanismi descritti di seguito per l'atterraggio dei dati in ADLS Gen2

1. Fare clic sulla trasformazione origine.
1. Fare clic sul pulsante nuovo accanto a DataSet nel pannello inferiore.
1. Scegliere un set di dati o crearne uno nuovo. Per questa demo verrà usato un set di dati parquet denominato dati utente.
1. Aggiungere una trasformazione colonna derivata. Questa operazione verrà usata per impostare in modo dinamico i nomi delle cartelle desiderate.
1. Aggiungere una trasformazione sink.
   
### <a name="hierarchical-folder-output"></a>Output cartella gerarchica

È molto comune usare valori univoci nei dati per creare gerarchie di cartelle per partizionare i dati nel Lake. Si tratta di un modo ottimale per organizzare ed elaborare i dati nel lago e in Spark (il motore di calcolo dietro i flussi di dati). Tuttavia, vi sarà un piccolo costo in termini di prestazioni per organizzare l'output in questo modo. Si prevede una lieve riduzione delle prestazioni complessive della pipeline mediante questo meccanismo nel sink.

1. Tornare alla finestra di progettazione del flusso di dati e modificare il flusso di dati creato sopra. Fare clic sulla trasformazione sink.
1. Fare clic su Ottimizza > imposta partizionamento > chiave
1. Selezionare la colonna o le colonne che si desidera utilizzare per impostare la struttura di cartelle gerarchica.
1. Si noti che l'esempio seguente usa anno e mese come colonne per la denominazione delle cartelle. I risultati saranno cartelle del modulo ```releaseyear=1990/month=8``` .
1. Quando si accede alle partizioni di dati in un'origine del flusso di dati, si punterà solo alla cartella di primo livello precedente ```releaseyear``` e si userà un modello di carattere jolly per ogni cartella successiva, ad esempio: ```**/**/*.parquet```
1. Per modificare i valori dei dati o anche se è necessario generare valori sintetici per i nomi delle cartelle, utilizzare la trasformazione colonna derivata per creare i valori che si desidera utilizzare nei nomi delle cartelle.

![Partizionamento delle chiavi](media/data-flow/key-parts.png "Partizionamento delle chiavi")
   
### <a name="name-folder-as-data-values"></a>Nome cartella come valori di dati

Una tecnica di sink leggermente migliore per i dati Lake usando ADLS Gen2 che non offre lo stesso vantaggio del partizionamento chiave/valore è ```Name folder as column data``` . Mentre lo stile di partizionamento chiave della struttura gerarchica consentirà di elaborare più facilmente le sezioni di dati, questa tecnica è una struttura di cartelle bidimensionale in grado di scrivere i dati più rapidamente.

1. Tornare alla finestra di progettazione del flusso di dati e modificare il flusso di dati creato sopra. Fare clic sulla trasformazione sink.
1. Fare clic su Ottimizza > imposta partizionamento > usa il partizionamento corrente.
1. Fare clic su impostazioni > cartella nome come dati della colonna.
1. Selezionare la colonna da utilizzare per la generazione dei nomi di cartella.
1. Per modificare i valori dei dati o anche se è necessario generare valori sintetici per i nomi delle cartelle, utilizzare la trasformazione colonna derivata per creare i valori che si desidera utilizzare nei nomi delle cartelle.

![Opzione cartella](media/data-flow/folders.png "Cartelle")

### <a name="name-file-as-data-values"></a>Nome file come valori di dati

Le tecniche elencate nelle esercitazioni precedenti sono casi d'uso validi per la creazione di categorie di cartelle in data Lake. Lo schema di denominazione dei file predefinito utilizzato da tali tecniche consiste nell'utilizzare l'ID del processo executor di Spark. In alcuni casi può essere utile impostare il nome del file di output in un sink di testo del flusso di dati. Questa tecnica è consigliata solo per l'uso con file di piccole dimensioni. Il processo di Unione dei file di partizione in un unico file di output è un processo a esecuzione prolungata.

1. Tornare alla finestra di progettazione del flusso di dati e modificare il flusso di dati creato sopra. Fare clic sulla trasformazione sink.
1. Fare clic su Ottimizza > imposta partizionamento > partizione singola. Si tratta di un requisito a partizione singola che crea un collo di bottiglia nel processo di esecuzione quando i file vengono uniti. Questa opzione è consigliata solo per i file di piccole dimensioni.
1. Fare clic su impostazioni > nome file come dati della colonna.
1. Selezionare la colonna da utilizzare per la generazione di nomi di file.
1. Per modificare i valori dei dati o anche se è necessario generare valori sintetici per i nomi di file, utilizzare la trasformazione colonna derivata per creare i valori che si desidera utilizzare nei nomi di file.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [sink del flusso di dati](data-flow-sink.md).
