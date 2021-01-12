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
ms.openlocfilehash: e219531a88787f19197a2e8c2a80040497c6dc1e
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901420"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Esercitazione: Eseguire il training di un modello di Machine Learning senza codice

È possibile arricchire i dati delle tabelle Spark con nuovi modelli di Machine Learning di cui si esegue il training con [Machine Learning automatizzato](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml). Nell'area di lavoro di Azure Synapse Analytics è possibile selezionare una tabella Spark da usare come set di dati di training per lo sviluppo di modelli di Machine Learning, in un'esperienza senza codice.

In questa esercitazione si apprenderà come eseguire il training il modelli di Machine Learning usando un'esperienza senza codice in Azure Synapse Analytics Studio. Si userà Machine Learning automatizzato in Azure Machine Learning, invece di codificare l'esperienza manualmente. Il tipo di modello di cui si esegue il training dipende dal problema che si vuole provare a risolvere.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Un'[area di lavoro di Azure Synapse Analytics](../get-started-create-workspace.md). Assicurarsi che abbia l'account di archiviazione seguente, configurato come risorsa di archiviazione predefinita: Azure Data Lake Storage Gen2. Assicurarsi di avere il ruolo di **Collaboratore ai dati del BLOB di archiviazione** per il file system di Data Lake Storage Gen2 usato.
- Un pool di Apache Spark nell'area di lavoro di Azure Synapse Analytics. Per informazioni dettagliate, vedere [Avvio rapido: Creare un pool SQL dedicato con Azure Synapse Analytics Studio](../quickstart-create-sql-pool-studio.md).
- Un servizio collegato di Azure Machine Learning nell'area di lavoro di Azure Synapse Analytics. Per informazioni dettagliate, vedere [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Creare una tabella Spark per il set di dati di training

Per eseguire questa esercitazione è necessaria una tabella Spark. Il notebook seguente consente di crearne una.

1. Scaricare il notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importare il notebook in Azure Synapse Analytics Studio.
![Screenshot di Azure Synapse Analytics con l'opzione Importa evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selezionare il pool di Spark da usare e quindi selezionare **Esegui tutti**. Si otterranno i dati dei taxi di New York dal set di dati aperto, che verranno salvati nel database Spark predefinito.
![Screenshot di Azure Synapse Analytics con l'opzione Esegui tutti e un database Spark evidenziati.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Una volta completata l'esecuzione del notebook, si vedrà una nuova tabella Spark nel database Spark predefinito. In **Dati** trovare la tabella denominata **nyc_taxi**.
![Screenshot della scheda Dati di Azure Synapse Analytics con la nuova tabella evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>Avviare la procedura guidata di Machine Learning automatizzato

Ecco come:

1. Fare clic con il pulsante destro del mouse sulla tabella Spark creata nel passaggio precedente. Per aprire la procedura guidata, selezionare **Machine Learning** > **Arricchisci con un nuovo modello**.
![Screenshot della tabella Spark con le opzioni Machine Learning e Arricchisci con un nuovo modello evidenziate.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. È quindi possibile specificare i dettagli di configurazione per creare un'esecuzione dell'esperimento di Machine Learning automatizzato in Azure Machine Learning. Viene eseguito il training di più modelli e quello migliore di un'esecuzione riuscita viene registrato nel registro modelli di Azure Machine Learning.

   ![Screenshot delle specifiche della configurazione Arricchisci con un nuovo modello.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Area di lavoro di Azure Machine Learning**: per creare un'esecuzione dell'esperimento di Machine Learning automatizzato, è necessaria un'area di lavoro di Azure Machine Learning. Occorre anche collegare l'area di lavoro di Azure Synapse Analytics all'area di lavoro di Azure Machine Learning usando un [servizio collegato](quickstart-integrate-azure-machine-learning.md). Dopo aver soddisfatto tutti i prerequisiti, è possibile specificare l'area di lavoro di Azure Machine Learning da usare per questa esecuzione automatizzata.

    - **Nome dell'esperimento**: specificare il nome dell'esperimento. Quando si invia un'esecuzione di Machine Learning automatizzato, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento nell'area di lavoro di Azure Machine Learning. Questa esperienza crea per impostazione predefinita un nuovo esperimento e genera un nome proposto, ma si può anche specificare il nome di un esperimento esistente.

    - **Modello migliore**: specificare il nome del modello migliore generato dall'esecuzione automatizzata. Questo nome viene assegnato al modello migliore e viene salvato automaticamente nel registro di modelli di Azure Machine Learning dopo l'esecuzione. Un'esecuzione di Machine Learning automatizzato crea diversi modelli di Machine Learning. In base alla metrica primaria che verrà selezionata in un passaggio successivo, è possibile confrontare questi modelli e selezionare quello migliore.

    - **Colonna di destinazione**: la colonna da prevedere in base al training del modello eseguito. Scegliere la colonna da prevedere. In questa esercitazione viene selezionata la colonna numerica `fareAmount` come destinazione.

    - **Pool di Spark**: il pool di Spark da usare per l'esecuzione dell'esperimento automatizzata. I calcoli vengono eseguiti nel pool specificato.

    - **Spark configuration details** (Dettagli della configurazione di Spark): oltre all'opzione per il pool di Spark, è anche disponibile un'opzione per specificare i dettagli della configurazione della sessione.

1. Selezionare **Continua**.

## <a name="choose-task-type"></a>Scegliere il tipo di attività

Selezionare il tipo di modello di Machine Learning per l'esperimento in base alla domanda a cui si sta cercando di rispondere. Poiché `fareAmount` è la colonna di destinazione ed è un valore numerico, selezionare **Regressione**. Selezionare quindi **Continua**.

![Screenshot dell'opzione Arricchisci con un nuovo modello con la voce Regressione evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurazioni aggiuntive

Se come tipo di modello si è selezionato **Regressione** o **Classificazione** nella sezione precedente, sono disponibili le configurazioni seguenti:

- **Metrica primaria**: la metrica usata per misurare le prestazioni del modello. Si tratta della metrica usata per confrontare i diversi modelli creati nell'esecuzione automatizzata e per determinare qual è il migliore.

- **Durata del processo di training (ore)** : la quantità massima di tempo, in ore, assegnata per l'esecuzione di un esperimento e il training dei modelli. Si noti che è anche possibile specificare valori minori di 1 (ad esempio `0.5`).

- **Numero massimo di iterazioni simultanee**: rappresenta il numero massimo di iterazioni eseguite in parallelo.

- **ONNX model compatibility** (Compatibilità con il modello ONNX): se si abilita questa opzione, i modelli sottoposti a training mediante Machine Learning automatizzato vengono convertiti nel formato ONNX. Questa opzione è particolarmente utile se si vuole usare il modello per assegnare punteggi nei pool SQL di Azure Synapse Analytics.

Tutte queste impostazioni prevedono un valore predefinito che è possibile personalizzare.
![Screenshot delle configurazioni aggiuntive di Arricchisci con un nuovo modello.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Una volta completate tutte le configurazioni necessarie, è possibile avviare l'esecuzione automatizzata. È possibile scegliere **Crea esecuzione** in modo da avviare l'esecuzione direttamente, senza codice. In alternativa, se si preferisce usare il codice, è possibile selezionare **Apri nel notebook**. Questa opzione consente di visualizzare il codice che crea l'esecuzione e di eseguire il notebook.

>[!NOTE]
>Se come tipo di modello si è selezionato **Previsione di serie temporali** nella sezione precedente, è necessario impostare altre configurazioni. La previsione, inoltre, non supporta la compatibilità con il modello ONNX.

### <a name="create-run-directly"></a>Creare l'esecuzione direttamente

Per avviare l'esecuzione di Machine Learning automatizzato direttamente, selezionare **Avvia esecuzione**. Viene visualizzata una notifica che indica che è in corso l'avvio dell'esecuzione. Alla fine viene visualizzata un'altra notifica che indica che l'esecuzione è riuscita. Si può anche controllare lo stato in Azure Machine Learning selezionando il collegamento nella notifica.
![Screenshot della notifica di operazione riuscita.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Creare un'esecuzione con il notebook

Selezionare **Apri nel notebook** per generare un notebook. Selezionare quindi **Esegui tutti**. In questo modo è anche possibile aggiungere altre impostazioni all'esecuzione di Machine Learning automatizzato.

![Screenshot del notebook con l'opzione Esegui tutti evidenziata.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Dopo avere inviato correttamente l'esecuzione, nell'output del notebook nell'area di lavoro di Azure Machine Learning sarà disponibile un collegamento all'esecuzione dell'esperimento. Selezionare il collegamento per monitorare l'esecuzione automatizzata in Azure Machine Learning.
![Screenshot di Azure Synapse Analytics con il collegamento evidenziato.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning (anteprima) per i pool SQL dedicati](tutorial-sql-pool-model-scoring-wizard.md)
- [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)
