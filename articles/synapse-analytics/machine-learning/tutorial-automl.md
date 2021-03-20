---
title: 'Esercitazione: Eseguire il training di un modello con Machine Learning automatizzato'
description: Esercitazione su come eseguire il training di un modello di Machine Learning senza codice in Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943526"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Esercitazione: Eseguire il training di un modello di Machine Learning senza codice

È possibile arricchire i dati delle tabelle Spark con nuovi modelli di Machine Learning di cui si esegue il training con [Machine Learning automatizzato](../../machine-learning/concept-automated-ml.md). Nell'area di lavoro di Azure Synapse Analytics è possibile selezionare una tabella Spark da usare come set di dati di training per lo sviluppo di modelli di Machine Learning, in un'esperienza senza codice.

Questa esercitazione illustra come eseguire il training dei modelli di Machine Learning usando un'esperienza senza codice in sinapsi Studio. Synapse Studio è una funzionalità di Azure Synapse Analytics. 

Si userà Machine Learning automatizzato in Azure Machine Learning, anziché codificare manualmente l'esperienza. Il tipo di modello di cui si esegue il training dipende dal problema che si sta tentando di risolvere.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Un'[area di lavoro di Azure Synapse Analytics](../get-started-create-workspace.md). Assicurarsi che disponga di un account di archiviazione Azure Data Lake Storage Gen2 configurato come risorsa di archiviazione predefinita. Assicurarsi di avere il ruolo di *Collaboratore ai dati del BLOB di archiviazione* per il file system di Data Lake Storage Gen2 usato.
- Un pool di Apache Spark nell'area di lavoro di Azure Synapse Analytics. Per informazioni dettagliate, vedere [Guida introduttiva: creare un pool SQL dedicato usando sinapsi Studio](../quickstart-create-sql-pool-studio.md).
- Un servizio collegato di Azure Machine Learning nell'area di lavoro di Azure Synapse Analytics. Per informazioni dettagliate, vedere [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-spark-table-for-the-training-dataset"></a>Creare una tabella Spark per il set di dati di training

Per eseguire questa esercitazione è necessaria una tabella Spark. Il notebook seguente ne crea uno:

1. Scaricare il notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importare il notebook in sinapsi Studio.
![Screenshot di Azure sinapsi Analytics con l'opzione di importazione evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selezionare il pool Spark che si vuole usare e quindi selezionare **Run all (Esegui tutto**). Questo passaggio ottiene i dati dei taxi di New York dal set di dati aperto e li salva nel database Spark predefinito.
![Screenshot di Azure Synapse Analytics con l'opzione Esegui tutti e un database Spark evidenziati.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Una volta completata l'esecuzione del notebook, si vedrà una nuova tabella Spark nel database Spark predefinito. In **Dati** trovare la tabella denominata **nyc_taxi**.
![Screenshot della scheda dati di analisi delle sinapsi di Azure, con la nuova tabella evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Aprire la procedura guidata di Machine Learning automatica

Per aprire la procedura guidata:

1. Fare clic con il pulsante destro del mouse sulla tabella Spark creata nel passaggio precedente. Selezionare quindi **Machine Learning**  >  **arricchisci con il nuovo modello**.
![Screenshot della tabella Spark con le opzioni Machine Learning e Arricchisci con un nuovo modello evidenziate.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Fornire i dettagli di configurazione per la creazione di un esperimento di Machine Learning automatizzato eseguito in Azure Machine Learning. Questa operazione esegue il training di più modelli. Il modello migliore da una corretta esecuzione viene registrato nel registro di sistema del modello Azure Machine Learning.

   ![Screenshot delle specifiche di configurazione per il training di un modello di machine learning.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Area di lavoro di Azure Machine Learning**: per creare un'esecuzione dell'esperimento di Machine Learning automatizzato, è necessaria un'area di lavoro di Azure Machine Learning. Occorre anche collegare l'area di lavoro di Azure Synapse Analytics all'area di lavoro di Azure Machine Learning usando un [servizio collegato](quickstart-integrate-azure-machine-learning.md). Una volta soddisfatti tutti i prerequisiti, è possibile specificare l'area di lavoro Azure Machine Learning che si desidera utilizzare per l'esecuzione automatica.

    - **Nome dell'esperimento**: specificare il nome dell'esperimento. Quando si invia un'esecuzione di Machine Learning automatizzato, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento nell'area di lavoro di Azure Machine Learning. Questa esperienza Crea un nuovo esperimento per impostazione predefinita e genera un nome proposto, ma è anche possibile specificare il nome di un esperimento esistente.

    - **Nome modello migliore**: specificare il nome del modello migliore dall'esecuzione automatizzata. Questo nome viene assegnato al modello migliore e viene salvato automaticamente nel registro di modelli di Azure Machine Learning dopo l'esecuzione. Un'esecuzione di Machine Learning automatizzato crea diversi modelli di Machine Learning. In base alla metrica primaria che verrà selezionata in un passaggio successivo, è possibile confrontare questi modelli e selezionare quello migliore.

    - **Colonna di destinazione**: la colonna da prevedere in base al training del modello eseguito. Scegliere la colonna da prevedere. In questa esercitazione viene selezionata la colonna numerica `fareAmount` come destinazione.

    - **Pool Spark**: specificare il pool Spark che si vuole usare per l'esecuzione dell'esperimento automatizzato. I calcoli vengono eseguiti nel pool specificato.

    - **Dettagli di configurazione di Spark**: oltre al pool Spark, è possibile specificare i dettagli della configurazione di sessione.

1. Selezionare **Continua**.

## <a name="choose-a-task-type"></a>Scegliere un tipo di attività

Selezionare il tipo di modello di Machine Learning per l'esperimento in base alla domanda a cui si sta cercando di rispondere. Poiché `fareAmount` è la colonna di destinazione ed è un valore numerico, è consigliabile selezionare **regressione** qui. Selezionare quindi **Continua**.

![Screenshot dell'opzione Arricchisci con un nuovo modello con la voce Regressione evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurazioni aggiuntive

Se è stata selezionata la **regressione** o la **classificazione** come tipo di modello nella sezione precedente, sono disponibili le configurazioni seguenti:

- **Metrica primaria**: immettere la metrica che misura le prestazioni del modello. Usare questa metrica per confrontare modelli diversi creati nell'esecuzione automatizzata e determinare quale modello è stato eseguito in modo ottimale.

- **Tempo del processo di training (ore)**: specificare la quantità massima di tempo, in ore, per l'esecuzione di un esperimento e il training dei modelli. Si noti che è anche possibile specificare valori minori di 1 (ad esempio, **0,5**).

- Numero massimo di **iterazioni simultanee**: scegliere il numero massimo di iterazioni eseguite in parallelo.

- **ONNX model compatibility** (Compatibilità con il modello ONNX): se si abilita questa opzione, i modelli sottoposti a training mediante Machine Learning automatizzato vengono convertiti nel formato ONNX. Questa opzione è particolarmente utile se si vuole usare il modello per assegnare punteggi nei pool SQL di Azure Synapse Analytics.

Tutte queste impostazioni prevedono un valore predefinito che è possibile personalizzare.
![Screenshot delle configurazioni aggiuntive per la configurazione di un modello di regressione.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Una volta completate tutte le configurazioni necessarie, è possibile avviare l'esecuzione automatizzata. È possibile scegliere **Crea esecuzione** in modo da avviare l'esecuzione direttamente, senza codice. In alternativa, se si preferisce usare il codice, è possibile selezionare **Apri nel notebook**. Questa opzione consente di visualizzare il codice che crea l'esecuzione e quindi l'esecuzione del notebook.

>[!NOTE]
>Se è stata selezionata l'opzione **previsione di serie temporali** come tipo di modello nella sezione precedente, è necessario eseguire configurazioni aggiuntive. La previsione, inoltre, non supporta la compatibilità con il modello ONNX.

### <a name="create-a-run-directly"></a>Creare un'esecuzione diretta

Per avviare l'esecuzione di Machine Learning automatizzato direttamente, selezionare **Avvia esecuzione**. Viene visualizzata una notifica che indica che è in corso l'avvio dell'esecuzione. Viene quindi visualizzata un'altra notifica che indica l'esito positivo. Si può anche controllare lo stato in Azure Machine Learning selezionando il collegamento nella notifica.
![Screenshot della notifica di operazione riuscita.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Creare un'esecuzione con un notebook

Selezionare **Apri nel notebook** per generare un notebook. Selezionare quindi **Esegui tutti**. Questo consente anche di aggiungere impostazioni all'esecuzione automatica di machine learning.

![Screenshot di un notebook, con l'esecuzione di tutti evidenziati.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Dopo aver inviato correttamente l'esecuzione, viene visualizzato un collegamento all'esecuzione dell'esperimento nell'area di lavoro Azure Machine Learning nell'output del notebook. Selezionare il collegamento per monitorare l'esecuzione automatizzata in Azure Machine Learning.
![Screenshot di Azure sinapsi Analytics con un collegamento evidenziato ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) .

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning (anteprima) per i pool SQL dedicati](tutorial-sql-pool-model-scoring-wizard.md)
- [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)