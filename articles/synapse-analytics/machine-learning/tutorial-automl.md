---
title: 'Esercitazione: Training di modelli di Machine Learning con AutoML'
description: Esercitazione su come eseguire il training di un modello di Machine Learning in Azure Synapse con AutoML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464488"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Esercitazione: Training di modelli di Machine Learning senza codice in Azure Synapse con AutoML (anteprima)

Informazioni su come arricchire facilmente i dati delle tabelle Spark con nuovi modelli di Machine Learning di cui si esegue il training con [AutoML in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Un utente di Synapse può semplicemente selezionare una tabella Spark nell'area di lavoro di Azure Synapse da usare come set di dati di training per lo sviluppo di modelli di Machine Learning in un'esperienza senza codice.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Eseguire il training di modelli di Machine Learning con un'esperienza senza codice in Azure Synapse che usa ML automatizzato in Azure ML. Il tipo di modello di cui si esegue il training dipende dal problema che si vuole provare a risolvere.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Synapse Analytics](../get-started-create-workspace.md) con un account di archiviazione di ADLS Gen2 configurato come archiviazione predefinita. È necessario essere il **collaboratore dei dati del BLOB di archiviazione** del file system di ADLS Gen2 che si vuole usare.
- Pool di Spark nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un pool di Spark in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Servizio collegato di Azure Machine Learning nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un servizio collegato di Azure Machine Learning in Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Creare una tabella Spark per il set di dati di training

Per questa esercitazione è necessaria una tabella Spark. Il notebook seguente creerà una tabella Spark.

1. Scaricare il notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Importare il notebook in Azure Synapse Studio.
![Importare il notebook](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selezionare il pool di Spark da usare e fare clic su `Run all`. Eseguire questo notebook per ottenere i dati dei taxi di New York dal set di dati aperto e salvarli nel database Spark predefinito.
![Esegui tutto](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Una volta completata l'esecuzione del notebook, verrà creata una tabella Spark nel database Spark predefinito. Passare all'hub dati e individuare la tabella denominata `nyc_taxi`.
![Tabella Spark](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>Avviare la procedura guidata AutoML per eseguire il training di un modello

Fare clic con il pulsante destro del mouse sulla tabella Spark creata nel passaggio precedente. Scegliere "Machine Learning-> Enrich with new model" (Arricchisci con il nuovo modello) per aprire la procedura guidata.
![Avviare la procedura guidata AutoML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Verrà visualizzato un pannello di configurazione e verrà chiesto di specificare i dettagli per creare un'esecuzione di esperimento di AutoML in Azure Machine Learning. Verrà quindi eseguito il training di più modelli e quello migliore di un'esecuzione riuscita verrà registrato nel registro di modelli di Azure ML:

![Configurare l'esecuzione - Passaggio 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Area di lavoro di Azure ML**: per la creazione dell'esecuzione dell'esperimento di AutoML, è necessaria un'area di lavoro di Azure ML. È anche necessario collegare l'area di lavoro di Azure Synapse all'area di lavoro di Azure ML usando un [servizio collegato](quickstart-integrate-azure-machine-learning.md). Dopo aver soddisfatto tutti i prerequisiti, è possibile specificare l'area di lavoro di Azure ML da usare per questa esecuzione di AutoML.

- **Nome dell'esperimento**: specificare il nome dell'esperimento. Quando si invia un'esecuzione di AutoML, si fornisce un nome dell'esperimento. Le informazioni per l'esecuzione vengono archiviate in tale esperimento nell'area di lavoro di Azure ML. Questa esperienza creerà per impostazione predefinita un nuovo esperimento e genererà un nome proposto, ma è anche possibile specificare il nome di un esperimento esistente.

- **Modello migliore**: specificare il nome del modello migliore generato dall'esecuzione di AutoML. Questo nome verrà assegnato al modello migliore e verrà salvato automaticamente nel registro di modelli di Azure ML dopo l'esecuzione. Un'esecuzione di AutoML creerà molti modelli di Machine Learning. In base alla metrica primaria che si selezionerà in un passaggio successivo, è possibile confrontare questi modelli e selezionare quello migliore.

- **Colonna di destinazione**: la colonna da prevedere in base al training del modello eseguito. Scegliere la colonna da prevedere.

- **Pool di Spark**: il pool di Spark da usare per l'esecuzione dell'esperimento di AutoML. I calcoli verranno eseguiti nel pool specificato.

- **Spark configuration details** (Dettagli della configurazione di Spark): oltre all'opzione per il pool di Spark, è anche disponibile un'opzione per specificare i dettagli della configurazione della sessione.

In questa esercitazione si seleziona la colonna numerica `fareAmount` come destinazione.

Fare clic su "Continua".

## <a name="choose-task-type"></a>Scegliere il tipo di attività

Selezionare il tipo di modello di Machine Learning per l'esperimento in base alla domanda a cui si prova a dare una risposta. Poiché è stata selezionata la colonna di destinazione `fareAmount`, che è un valore numerico, verrà selezionata l'opzione *Regressione*.

Fare clic su "Continua" per configurare altre impostazioni.

![Selezione del tipo di attività](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurazioni aggiuntive

Se si seleziona il tipo *Classificazione* o *Regressione*, le configurazioni aggiuntive sono:

- **Metrica primaria**: la metrica usata per misurare le prestazioni del modello. Questa metrica verrà usata per confrontare i modelli diversi creati nell'esecuzione di AutoML e per determinare qual è il migliore.

- **Durata del processo di training (ore)** : la quantità massima di tempo, in ore, assegnata per l'esecuzione di un esperimento e il training dei modelli. Si noti che è anche possibile specificare valori minori di 1. Ad esempio `0.5`.

- **Numero massimo di iterazioni simultanee**: rappresenta il numero massimo di iterazioni che dovranno essere eseguite in parallelo.

- **ONNX model compatibility** (Compatibilità con il modello ONNX): se questa opzione è abilitata, i modelli di cui viene eseguito il training con AutoML verranno convertiti in formato ONNX. Questa opzione è particolarmente utile se il modello verrà usato per assegnare punteggi nei pool SQL di Azure Synapse.

Tutte queste impostazioni prevedono un valore predefinito che è possibile personalizzare.
![Configurazioni aggiuntive](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Tenere presente che se si seleziona "Previsione serie temporale", è necessario specificare altre configurazioni. Le previsioni, inoltre, non supportano la compatibilità con il modello ONNX.

Una volta completate tutte le configurazioni necessarie, è possibile avviare l'esecuzione di AutoML.

È possibile avviare un'esecuzione di AutoML in due modi in Azure Synapse Analytics. Per un'esperienza senza codice, è possibile scegliere direttamente **Create run** (Crea esecuzione). Se si preferisce usare codice, selezionare **Open in notebook** (Apri nel notebook) per visualizzare il codice che crea l'esecuzione ed eseguire il notebook.

### <a name="create-run-directly"></a>Creare direttamente l'esecuzione

Fare clic su "Start Run" (Avvia esecuzione) per avviare direttamente l'esecuzione di AutoML. Verrà visualizzata una notifica che indica che è in corso l'avvio dell'esecuzione di AutoML.

Una volta avviata correttamente l'esecuzione di AutoML, verrà visualizzata un'altra notifica di operazione riuscita. È anche possibile fare clic sul pulsante della notifica per controllare lo stato dell'esecuzione.
Ecco il messaggio visualizzato in Azure ML dopo aver fatto clic sul collegamento nella notifica di operazione riuscita.
![Notifica di operazione riuscita](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Creare un'esecuzione con il notebook

Selezionare *Open In Notebook* (Apri nel notebook) per generare un notebook. Fare clic su *Esegui tutto* per eseguire il notebook.
È anche possibile aggiungere altre impostazioni all'esecuzione di AutoML.

![Notebook aperto](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Al termine dell'esecuzione del notebook, nel relativo output nell'area di lavoro di Azure ML sarà disponibile un collegamento all'esecuzione dell'esperimento. È possibile fare clic sul collegamento per monitorare l'esecuzione di AutoML in Azure ML.
![Esegui tutto nel notebook](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Assegnazione di punteggi al modello di Machine Learning nei pool SQL dedicati di Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Synapse](quickstart-integrate-azure-machine-learning.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)