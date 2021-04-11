---
title: Registrare gli esperimenti e le metriche di ML
titleSuffix: Azure Machine Learning
description: Abilitare la registrazione sulle esecuzioni di training ML per monitorare le metriche di esecuzione in tempo reale e per facilitare la diagnosi di errori e avvisi.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d91c88da1416071b5eee2a8eb10e3029086839e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561781"
---
# <a name="enable-logging-in-ml-training-runs"></a>Abilitare la registrazione in esecuzioni di training ML


Azure Machine Learning Python SDK consente di registrare le informazioni in tempo reale usando sia il pacchetto di registrazione Python predefinito che la funzionalità specifica dell'SDK. È possibile registrare in locale e inviare i log all'area di lavoro nel portale.

I log consentono di diagnosticare gli errori e gli avvisi o di tenete traccia di metriche delle prestazioni come parametri e prestazioni modello. Questo articolo illustra come abilitare la registrazione negli scenari seguenti:

> [!div class="checklist"]
> * Sessioni di training interattive
> * Invio dei processi di training tramite ScriptRunConfig
> * Impostazioni di `logging` native di Python
> * Registrazione da origini aggiuntive


> [!TIP]
> Questo articolo illustra come monitorare il processo di training dei modelli. Per informazioni sul monitoraggio dell'utilizzo delle risorse e di eventi di Azure Machine Learning, come quote, esecuzioni di training o distribuzioni di modelli completate, vedere [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipi di dati

È possibile registrare più tipi di dati, tra cui valori scalari, elenchi, tabelle, immagini, directory e altro ancora. Per altre informazioni e per esempi di codice Python per tipi di dati diversi, vedere la [pagina di informazioni di riferimento sulla classe Run](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Metrica di esecuzione della registrazione 

Usare i metodi seguenti nelle API di registrazione per influenzare le visualizzazioni della metrica. Si notino i [limiti del servizio](./resource-limits-quotas-capacity.md#metrics) per queste metriche registrate. 

|Valore registrato|Codice di esempio| Formattare nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|
|Immagine del log|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Utilizzare questo metodo per registrare un file di immagine o un tracciato matplotlib nell'esecuzione. Queste immagini saranno visibili e confrontabili nel record di esecuzione|

### <a name="logging-with-mlflow"></a>Registrazione con MLflow
Usare MLFlowLogger per registrare le metriche.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Sessione di registrazione interattiva

Le sessioni di registrazione interattive vengono in genere usate negli ambienti notebook. Il metodo [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) avvia una sessione di registrazione interattiva. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento. Il metodo [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) termina le sessioni e contrassegna l'esecuzione come completata.

## <a name="scriptrun-logs"></a>Log ScriptRun

Questa sezione illustra come aggiungere codice di registrazione all'interno delle esecuzioni create se la configurazione è stata eseguita con ScriptRunConfig. È possibile usare la classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) per incapsulare gli script e gli ambienti per esecuzioni ripetibili. È anche possibile usare questa opzione per mostrare un widget visuale di Jupyter Notebook per il monitoraggio.

Questo esempio esegue uno spostamento di parametri su valori alfa e acquisisce i risultati con il metodo [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Creare uno script di training che include la logica di registrazione, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Inviare lo script ```train.py``` per l'esecuzione in un ambiente gestito dall'utente. L'intera cartella di script viene inviata per il training.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = src)]


   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? nome = esecuzione)]

    Il parametro `show_output` attiva la registrazione dettagliata, che consente di visualizzare i dettagli dal processo di training oltre a informazioni su tutte le risorse remote o le destinazioni di calcolo. Usare il codice seguente per attivare la registrazione dettagliata quando si invia l'esperimento.

```python
run = exp.submit(src, show_output=True)
```

È anche possibile usare lo stesso parametro nella funzione `wait_for_completion` all'esecuzione risultante.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Registrazione nativa di Python

Alcuni log nell'SDK possono contenere un errore che indica di impostare il livello di registrazione su DEBUG. Per impostare il livello di registrazione, aggiungere il codice seguente allo script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Origini di registrazione aggiuntive

Azure Machine Learning può anche registrare le informazioni provenienti da altre origini durante il training, ad esempio le esecuzioni di Machine Learning automatizzato o i contenitori Docker che eseguono i processi. Questi log non sono documentati, ma se si verificano problemi e si contatta il supporto tecnico Microsoft, potrebbero risultare utili per la risoluzione.

Per informazioni sulla registrazione di metriche nella finestra di progettazione di Azure Machine Learning, vedere [Come registrare le metriche nella finestra di progettazione](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Notebook di esempio

I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare Azure Machine Learning, vedere questi articoli:

* Informazioni su come [registrare le metriche nella finestra di progettazione di Azure Machine Learning](how-to-track-designer-experiments.md).

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).