---
title: Monitorare e visualizzare i log di esecuzione di ML & le metriche
titleSuffix: Azure Machine Learning
description: Monitora gli esperimenti di machine learning e visualizza le metriche di esecuzione con i widget Jupyter e il Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ea96e1056e6157cfddbdc2f0b6451ed55a74d1de
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756059"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorare e visualizzare i log e le metriche per l'esecuzione di ML

Informazioni su come monitorare le esecuzioni di Azure Machine Learning e visualizzare i log. 

Quando si esegue un esperimento, i log e le metriche vengono trasmessi per l'utente.  Inoltre, è possibile aggiungere un proprio.  Per informazioni, vedere [abilitare la registrazione nelle esecuzioni di training di Azure ml](how-to-track-experiments.md).

I log consentono di diagnosticare gli errori e gli avvisi per l'esecuzione. Le metriche delle prestazioni, come parametri e accuratezza del modello, consentono di tenere traccia e monitorare le esecuzioni.

In questo articolo si apprenderà come visualizzare i log usando i metodi seguenti:

> [!div class="checklist"]
> * Monitoraggio eseguito in studio
> * Monitoraggio eseguito con il widget Jupyter Notebook
> * Monitorare le esecuzioni automatiche di Machine Learning
> * Visualizza log di output al termine
> * Visualizzare i log di output in studio

Per informazioni generali su come gestire gli esperimenti, vedere [avviare, monitorare e annullare le esecuzioni di training](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitoraggio eseguito con il widget Jupyter notebook

Quando si usa il metodo **ScriptRunConfig** per inviare le esecuzioni, è possibile controllare lo stato di avanzamento dell'esecuzione usando il [widget Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Come per l'invio dell'esecuzione, il widget è asincrono e fornisce aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo.

Visualizzare il widget di Jupyter durante l'attesa del completamento dell'esecuzione.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Screenshot del widget di Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

È anche possibile ottenere un collegamento alla stessa visualizzazione nell'area di lavoro.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitorare le esecuzioni automatiche di Machine Learning

Per le esecuzioni automatiche di Machine Learning, per accedere ai grafici da un'esecuzione precedente, sostituire `<<experiment_name>>` con il nome dell'esperimento appropriato:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget di notebook di Jupyter per Machine Learning automatizzato](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Mostra output al completamento

Quando si usa **ScriptRunConfig**, è possibile usare ```run.wait_for_completion(show_output = True)``` per visualizzare quando viene completato il training del modello. Il flag ```show_output``` fornisce output dettagliato. Per ulteriori informazioni, vedere la sezione ScriptRunConfig di [come abilitare la registrazione](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

## <a name="query-run-metrics"></a>Eseguire query sulle metriche di esecuzione

È possibile visualizzare le metriche relative a un modello sottoposto a training usando ```run.get_metrics()```. Ad esempio, è possibile usarlo con l'esempio precedente per determinare il modello migliore cercando il modello con il valore medio di errore quadratico (MSE) più basso.

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Visualizzare i record di esecuzione in studio

È possibile esplorare i record di esecuzione completati, incluse le metriche registrate, in [Azure Machine Learning Studio](https://ml.azure.com).

Passare alla scheda **esperimenti** . Per visualizzare tutte le esecuzioni nell'area di lavoro tra gli esperimenti, selezionare la scheda **tutte le esecuzioni** . È possibile eseguire il drill-down delle esecuzioni per esperimenti specifici applicando il filtro dell'esperimento sulla barra dei menu superiore.

Per la singola visualizzazione esperimento, selezionare la scheda **tutti gli esperimenti** . Nel dashboard dell'esperimento è possibile visualizzare le metriche e i log rilevati per ogni esecuzione. 

È anche possibile modificare la tabella elenco di esecuzione per selezionare più esecuzioni e visualizzare il valore massimo, minimo o massimo registrato per le esecuzioni. Personalizzare i grafici per confrontare i valori delle metriche registrate e le aggregazioni in più esecuzioni. 

![Dettagli dell'esecuzione in Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts"></a>Formattare i grafici 

Usare i metodi seguenti nelle API di registrazione per influenzare le visualizzazioni della metrica.

|Valore registrato|Codice di esempio| Formattare nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|


### <a name="view-log-files-for-a-run"></a>Visualizzare i file di log per un'esecuzione 

I file di log sono una risorsa essenziale per il debug dei carichi di lavoro di Azure ML. Eseguire il drill-down fino a un'esecuzione specifica per visualizzare i relativi log e output:  

1. Passare alla scheda **esperimenti** .
1. Selezionare il runID per un'esecuzione specifica.
1. Selezionare **output e log** nella parte superiore della pagina.

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="Screenshot della sezione dell'output e dei log di un'esecuzione":::

Le tabelle seguenti illustrano il contenuto dei file di log nelle cartelle che verranno visualizzate in questa sezione.

> [!NOTE]
> Ogni file non sarà necessariamente visibile per ogni esecuzione. Ad esempio, il 20_image_build_log *. txt viene visualizzato solo quando viene compilata una nuova immagine, ad esempio quando si modifica l'ambiente.

#### <a name="azureml-logs-folder"></a>`azureml-logs`

|File  |Descrizione  |
|---------|---------|
|20_image_build_log.txt     | Log di compilazione dell'immagine Docker per l'ambiente di training, facoltativo, uno per esecuzione. Applicabile solo quando si aggiorna l'ambiente. Altrimenti AML riutilizzerà l'immagine memorizzata nella cache. Se ha esito positivo, contiene i dettagli del registro immagini per l'immagine corrispondente.         |
|55_azureml-Execution-<node_id # C1.txt     | log stdout/stderr dello strumento host, uno per nodo. Esegue il pull dell'immagine nella destinazione di calcolo. Si noti che questo log viene visualizzato solo dopo aver protetto le risorse di calcolo.         |
|65_job_prep <node_id # C1.txt     |   log stdout/stderr dello script di preparazione del processo, uno per nodo. Scaricare il codice per calcolare la destinazione e gli archivi dati (se richiesto).       |
|70_driver_log (_x). txt      |  log stdout/stderr dallo script di controllo AML e dallo script di training del cliente, uno per processo. **Si tratta dell'output standard dello script. Questo è il punto in cui vengono visualizzati i log del codice, ad esempio le istruzioni Print.** Nella maggior parte dei casi i log vengono monitorati qui.       |
|70_mpi_log.txt     |   Log del Framework MPI, facoltativo, uno per esecuzione. Solo per l'esecuzione MPI.   |
|75_job_post <node_id # C1.txt     |  log stdout/stderr dello script di rilascio del processo, uno per nodo. Inviare i log, rilasciare le risorse di calcolo in Azure.        |
|process_info.js      |   Mostra il processo in esecuzione sul nodo.  |
|process_status.js      | Mostra lo stato del processo, ad esempio se un processo non è avviato, in esecuzione o completato.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml`

|File  |Descrizione  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   Registro di sistema per la preparazione dei processi        |
|job_release_azureml. log     | Registro di sistema per la versione del processo        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id`

Quando sidecar è abilitato, gli script di preparazione e rilascio del processo verranno eseguiti nel contenitore sidecar.  È presente una cartella per ogni nodo. 

|File  |Descrizione  |
|---------|---------|
|start_cms.txt     |  Log del processo che inizia all'avvio del contenitore sidecar       |
|prep_cmd.txt      |   Log per ContextManagers immesso quando `job_prep.py` viene eseguito (alcuni di questi verranno trasmessi a `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Log per ComtextManagers terminato quando `job_release.py` viene eseguito        |

#### <a name="other-folders"></a>Altre cartelle

Per il training dei processi nei cluster multicalcolo, sono presenti log per ogni IP del nodo. La struttura per ogni nodo corrisponde ai processi a nodo singolo. È disponibile un'altra cartella logs per i log di esecuzione complessiva, stderr e stdout.

Azure Machine Learning registra le informazioni da diverse origini durante il training, ad esempio AutoML o il contenitore Docker che esegue il processo di training. Molti di questi log non sono documentati. Se si verificano problemi e si contatta il supporto tecnico Microsoft, l'agente potrebbe essere in grado di utilizzare questi log durante la risoluzione dei problemi.

## <a name="monitor-a-compute-cluster"></a>Monitorare un cluster di calcolo

Per monitorare le esecuzioni per una destinazione di calcolo specifica dal browser, seguire questa procedura:

1. In [Azure Machine Learning Studio](https://ml.azure.com/)selezionare l'area di lavoro e quindi selezionare __calcolo__ dal lato sinistro della pagina.

1. Selezionare __Cluster di training__ per visualizzare un elenco di destinazioni di calcolo usate per il training. Selezionare quindi il cluster.

    ![Selezionare il cluster di training](./media/how-to-track-experiments/select-training-compute.png)

1. Selezionare __Esecuzioni__. Viene visualizzato l'elenco delle esecuzioni che usano questo cluster. Per visualizzare i dettagli per un'esecuzione specifica, usare il collegamento nella colonna __Esecuzione__. Per visualizzare i dettagli per un'esecuzione specifica, usare il collegamento nella colonna __Esperimento__.

    ![Selezionare le esecuzioni per il cluster di training](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Poiché le destinazioni di calcolo del training sono una risorsa condivisa, possono avere più esecuzioni in coda o attive in un determinato momento.
    > 
    > Un'esecuzione può contenere esecuzioni figlie, quindi un processo di training può generare più voci.

Una volta completata l'esecuzione, questa non viene più visualizzata nella pagina. Per visualizzare le informazioni sulle esecuzioni completate, visitare la sezione __Esperimenti__ in Studio e selezionare l'esperimento e l'esecuzione. Per altre informazioni, vedere la sezione [visualizzare le metriche per le esecuzioni completate](#view-the-experiment-in-the-web-portal).


## <a name="next-steps"></a>Passaggi successivi

Provare a eseguire questi passaggi successivi per informazioni su come usare Azure Machine Learning:

* Informazioni su come [tenere traccia degli esperimenti e abilitare i log nella finestra di progettazione Azure Machine Learning](how-to-track-designer-experiments.md).

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).
