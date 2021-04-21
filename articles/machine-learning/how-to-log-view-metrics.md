---
title: Log & visualizzare le metriche e i file di log
titleSuffix: Azure Machine Learning
description: Abilitare la registrazione nelle esecuzioni di training di Machine Learning per monitorare le metriche di esecuzione in tempo reale e per diagnosticare errori e avvisi.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820554"
---
# <a name="log--view-metrics-and-log-files"></a>Log & visualizzare le metriche e i file di log

Registrare informazioni in tempo reale usando sia il pacchetto di registrazione Python predefinito che Azure Machine Learning funzionalità specifiche di Python SDK. È possibile registrare in locale e inviare i log all'area di lavoro nel portale.

I log consentono di diagnosticare gli errori e gli avvisi o di tenete traccia di metriche delle prestazioni come parametri e prestazioni modello. Questo articolo illustra come abilitare la registrazione negli scenari seguenti:

> [!div class="checklist"]
> * Metriche di esecuzione del log
> * Sessioni di training interattive
> * Invio dei processi di training tramite ScriptRunConfig
> * Impostazioni di `logging` native di Python
> * Registrazione da origini aggiuntive


> [!TIP]
> Questo articolo illustra come monitorare il processo di training dei modelli. Per informazioni sul monitoraggio dell'utilizzo delle risorse e di eventi di Azure Machine Learning, come quote, esecuzioni di training o distribuzioni di modelli completate, vedere [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipi di dati

È possibile registrare più tipi di dati, tra cui valori scalari, elenchi, tabelle, immagini, directory e altro ancora. Per altre informazioni e per esempi di codice Python per tipi di dati diversi, vedere la [pagina di informazioni di riferimento sulla classe Run](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Metriche di esecuzione della registrazione 

Usare i metodi seguenti nelle API di registrazione per influenzare le visualizzazioni delle metriche. Prendere nota [dei limiti del servizio](./resource-limits-quotas-capacity.md#metrics) per queste metriche registrate. 

|Valore registrato|Codice di esempio| Formattare nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|
|Immagine del log|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Usare questo metodo per registrare un file di immagine o un tracciato matplotlib nell'esecuzione. Queste immagini saranno visibili e confrontabili nel record di esecuzione|

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

## <a name="view-run-metrics"></a>Visualizzare le metriche di esecuzione

## <a name="via-the-sdk"></a>Tramite l'SDK
È possibile visualizzare le metriche relative a un modello sottoposto a training usando ```run.get_metrics()```. Vedere l'esempio seguente. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Visualizzare le metriche di esecuzione nell'interfaccia utente di AML Studio

È possibile esplorare i record di esecuzione completati, incluse le metriche registrate, [nel studio di Azure Machine Learning](https://ml.azure.com).

Passare alla **scheda Esperimenti.** Per visualizzare tutte le esecuzioni nell'area di lavoro tra esperimenti, selezionare la **scheda Tutte le** esecuzioni. È possibile eseguire il drill-down delle esecuzioni per esperimenti specifici applicando il filtro Esperimento nella barra dei menu superiore.

Per la singola visualizzazione Esperimento selezionare la **scheda Tutti gli** esperimenti. Nel dashboard di esecuzione dell'esperimento è possibile visualizzare le metriche e i log monitorati per ogni esecuzione. 

È anche possibile modificare la tabella dell'elenco di esecuzioni per selezionare più esecuzioni e visualizzare l'ultimo valore registrato, minimo o massimo per le esecuzioni. Personalizzare i grafici per confrontare i valori delle metriche registrate e le aggregazioni tra più esecuzioni. È possibile tracciare più metriche sull'asse y del grafico e personalizzare l'asse x per tracciare le metriche registrate. 


### <a name="view-and-download-log-files-for-a-run"></a>Visualizzare e scaricare i file di log per un'esecuzione 

I file di log sono una risorsa essenziale per il debug dei carichi di lavoro di Azure Machine Learning. Dopo aver inviato un processo di training, eseguire il drill-down a un'esecuzione specifica per visualizzarne i log e gli output:  

1. Passare alla **scheda Experiments (Esperimenti).**
1. Selezionare l'ID di esecuzione per un'esecuzione specifica.
1. Selezionare **Output e log** nella parte superiore della pagina.
2. Selezionare **Scarica tutto** per scaricare tutti i log in una cartella ZIP.
3. È anche possibile scaricare singoli file di log scegliendo il file di log e selezionando **Scarica**

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Screenshot della sezione Output e log di un'esecuzione.":::

Le tabelle seguenti mostrano il contenuto dei file di log nelle cartelle che verranno mostrate in questa sezione.

> [!NOTE]
> Non verranno necessariamente visualizzati tutti i file per ogni esecuzione. Ad esempio, il file 20_image_build_log*.txt viene visualizzato solo quando viene compilata una nuova immagine( ad esempio quando si modifica l'ambiente).

#### <a name="azureml-logs-folder"></a>`azureml-logs`

|File  |Descrizione  |
|---------|---------|
|20_image_build_log.txt     | Log di compilazione delle immagini Docker per l'ambiente di training, facoltativo, uno per ogni esecuzione. Applicabile solo quando si aggiorna l'ambiente. In caso contrario, AML riutilizzerà l'immagine memorizzata nella cache. In caso di esito positivo, contiene i dettagli del registro immagini per l'immagine corrispondente.         |
|55_azureml-execution-<node_id>.txt     | log stdout/stderr dello strumento host, uno per nodo. Esegue il pull dell'immagine nella destinazione di calcolo. Si noti che questo log viene visualizzato solo dopo aver protetto le risorse di calcolo.         |
|65_job_prep-<node_id>.txt     |   log stdout/stderr dello script di preparazione del processo, uno per nodo. Scaricare il codice per calcolare la destinazione e gli archivi dati (se richiesto).       |
|70_driver_log(_x).txt      |  log stdout/stderr dallo script di controllo AML e dallo script di training del cliente, uno per processo. **Output standard dello script. In questo file vengono visualizzati i log del codice, ad esempio le istruzioni di stampa.** Nella maggior parte dei casi, i log verranno monitorati qui.       |
|70_mpi_log.txt     |   Log del framework MPI, facoltativo, uno per ogni esecuzione. Solo per l'esecuzione di MPI.   |
|75_job_post-<node_id>.txt     |  log stdout/stderr dello script di rilascio del processo, uno per nodo. Inviare i log e rilasciare le risorse di calcolo in Azure.        |
|process_info.jssu      |   mostra il processo in esecuzione in quale nodo.  |
|process_status.jssu      | mostra lo stato del processo, ad esempio se un processo non è avviato, in esecuzione o completato.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml`

|File  |Descrizione  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   registro di sistema per la preparazione del processo        |
|job_release_azureml.log     | log di sistema per la versione del processo        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id`

Quando l'opzione sidecar è abilitata, gli script di preparazione e rilascio del processo verranno eseguiti all'interno del contenitore sidecar.  Esiste una cartella per ogni nodo. 

|File  |Descrizione  |
|---------|---------|
|start_cms.txt     |  Log del processo che viene avviato all'avvio del contenitore Sidecar       |
|prep_cmd.txt      |   Log per ContextManagers immesso durante l'esecuzione (parte di questo contenuto `job_prep.py` verrà trasmesso a `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Log per ComtextManagers terminato durante `job_release.py` l'esecuzione        |

#### <a name="other-folders"></a>Altre cartelle

Per il training dei processi in cluster multi-calcolo, sono presenti log per ogni IP del nodo. La struttura per ogni nodo è la stessa dei processi a nodo singolo. È disponibile un'altra cartella logs per i log di esecuzione complessivi, stderr e stdout.

Azure Machine Learning le informazioni provenienti da varie origini durante il training, ad esempio AutoML o il contenitore Docker che esegue il processo di training. Molti di questi log non sono documentati. Se si verificano problemi e si contatta il supporto tecnico Microsoft, l'agente potrebbe essere in grado di utilizzare questi log durante la risoluzione dei problemi.


## <a name="interactive-logging-session"></a>Sessione di registrazione interattiva

Le sessioni di registrazione interattive vengono in genere usate negli ambienti notebook. Il metodo [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) avvia una sessione di registrazione interattiva. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento. Il metodo [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) termina le sessioni e contrassegna l'esecuzione come completata.

## <a name="scriptrun-logs"></a>Log ScriptRun

Questa sezione illustra come aggiungere codice di registrazione all'interno delle esecuzioni create se la configurazione è stata eseguita con ScriptRunConfig. È possibile usare la classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) per incapsulare gli script e gli ambienti per esecuzioni ripetibili. È anche possibile usare questa opzione per mostrare un widget visuale di Jupyter Notebook per il monitoraggio.

Questo esempio esegue uno spostamento di parametri su valori alfa e acquisisce i risultati con il metodo [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Creare uno script di training che include la logica di registrazione, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Inviare lo script ```train.py``` per l'esecuzione in un ambiente gestito dall'utente. L'intera cartella di script viene inviata per il training.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

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

## <a name="other-logging-sources"></a>Altre origini di registrazione

Azure Machine Learning può anche registrare le informazioni provenienti da altre origini durante il training, ad esempio le esecuzioni di Machine Learning automatizzato o i contenitori Docker che eseguono i processi. Questi log non sono documentati, ma se si verificano problemi e si contatta il supporto tecnico Microsoft, potrebbero risultare utili per la risoluzione.

Per informazioni sulla registrazione di metriche nella finestra di progettazione di Azure Machine Learning, vedere [Come registrare le metriche nella finestra di progettazione](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Notebook di esempio

I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare Azure Machine Learning, vedere questi articoli:

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).