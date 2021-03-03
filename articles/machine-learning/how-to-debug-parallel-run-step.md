---
title: Risoluzione dei problemi relativi a ParallelRunStep
titleSuffix: Azure Machine Learning
description: Suggerimenti per la risoluzione dei problemi quando si verificano errori usando ParallelRunStep nelle pipeline di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: ee41ae2a705ceaa0e9742c91552d6bdae26820ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690278"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Risoluzione dei problemi relativi a ParallelRunStep

In questo articolo si apprenderà come risolvere i problemi quando si ottengono errori usando la classe [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) di [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Per suggerimenti generali sulla risoluzione dei problemi di una pipeline, vedere [risoluzione dei problemi relativi alle pipeline di Machine Learning](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Test degli script in locale

 Il ParallelRunStep viene eseguito come passaggio nelle pipeline di ML. È consigliabile [testare gli script in locale](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) come primo passaggio.

##  <a name="script-requirements"></a>Requisiti dello script

Lo script per un `ParallelRunStep` *deve contenere* due funzioni:
- `init()`: usare questa funzione per qualsiasi preparazione dispendiosa o comune per un'inferenza successiva. Usarla ad esempio per caricare il modello in un oggetto globale. Questa funzione verrà chiamata solo una volta all'inizio del processo.
-  `run(mini_batch)`: la funzione viene eseguita per ogni istanza di `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` richiama il metodo run e passa un elenco o un `DataFrame` Pandas come argomento al metodo. Ogni voce in mini_batch sarà un percorso file se l'input è un oggetto `FileDataset` oppure un `DataFrame` Pandas se l'input è un oggetto `TabularDataset`.
    -  `response`: il metodo run() deve restituire un `DataFrame` Pandas o una matrice. Per append_row output_action, questi elementi restituiti vengono accodati nel file di output comune. Per summary_only, il contenuto degli elementi viene ignorato. Per tutte le azioni di output, ogni elemento di output restituito indica un'esecuzione riuscita dell'elemento di input nel mini-batch di input. Verificare che nel risultato dell'esecuzione siano inclusi dati sufficienti per eseguire il mapping dell'input al risultato dell'output dell'esecuzione. L'output dell'esecuzione verrà scritto nel file di output ma non sarà necessariamente in ordine, quindi sarà necessario usare una chiave nell'output per eseguirne il mapping all'input.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se è presente un altro file o un'altra cartella nella stessa directory dello script di inferenza, è possibile farvi riferimento individuando la directory di lavoro corrente.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametri per ParallelRunConfig

`ParallelRunConfig` è la configurazione principale per l'istanza di `ParallelRunStep` all'interno della pipeline di Azure Machine Learning. Si usa per eseguire il wrapping dello script e configurare i parametri necessari, incluse tutte le voci seguenti:
- `entry_script`: script utente come percorso di file locale che verrà eseguito in parallelo su più nodi. Se `source_directory` è presente, usare un percorso relativo. In caso contrario, usare qualsiasi percorso accessibile nel computer.
- `mini_batch_size`: dimensioni del mini-batch passato a una singola chiamata di `run()` (Facoltativo; il valore predefinito è `10` file per `FileDataset` e `1MB` per `TabularDataset`.)
    - Per `FileDataset`, è il numero di file con un valore minimo di `1`. È possibile combinare più file in un solo mini-batch.
    - Per `TabularDataset`, sono le dimensioni dei dati. Valori di esempio sono `1024`, `1024KB`, `10MB` e `1GB`. Il valore consigliato è `1MB`. Il mini-batch di `TabularDataset` non supererà mai i limiti dei file. Supponiamo ad esempio di avere una serie di file CSV di varie dimensioni, comprese tra 100 KB e 10 MB. Se si imposta `mini_batch_size = 1MB`, i file di dimensioni inferiori a 1 MB verranno considerati come un mini-batch. I file di dimensioni maggiori di 1 MB verranno suddivisi in più mini-batch.
- `error_threshold`: numero di errori di record per `TabularDataset` e di errori di file per `FileDataset` che devono essere ignorati durante l'elaborazione. Se il numero di errori per l'intero input supera questo valore, il processo viene interrotto. La soglia di errore è per l'intero input e non per il singolo mini-batch inviato al metodo `run()`. L'intervallo è `[-1, int.max]`. La parte `-1` indica di ignorare tutti gli errori durante l'elaborazione.
- `output_action`: uno dei valori seguenti indica come verrà organizzato l'output:
    - `summary_only`: l'output viene archiviato dallo script utente. `ParallelRunStep` userà l'output solo per il calcolo della soglia di errore.
    - `append_row`: per tutti gli input, nella cartella di output verrà creato un solo file in cui accodare tutti gli output separati per riga.
- `append_row_file_name`: per personalizzare il nome del file di output per append_row output_action (facoltativo; il valore predefinito è `parallel_run_step.txt`).
- `source_directory`: percorsi delle cartelle che contengono tutti i file da eseguire nella destinazione di calcolo (facoltativo).
- `compute_target`: È supportato solo `AmlCompute`.
- `node_count`: numero di nodi di calcolo da usare per l'esecuzione dello script utente.
- `process_count_per_node`: numero di processi per nodo. È consigliabile impostare questo valore sul numero di GPU o CPU di un nodo (facoltativo; il valore predefinito è `1`).
- `environment`: definizione dell'ambiente Python. È possibile configurarlo per usare un ambiente Python esistente o per configurare un ambiente temporaneo. La definizione è anche responsabile dell'impostazione delle dipendenze dell'applicazione necessarie (facoltativo).
- `logging_level`: livello di dettaglio del log. I valori in ordine crescente di livello di dettaglio sono: `WARNING`, `INFO` e `DEBUG`. (Facoltativo. Il valore predefinito è `INFO`)
- `run_invocation_timeout`: timeout della chiamata del metodo `run()` in secondi. (Facoltativo. Il valore predefinito è`60`)
- `run_max_try`: Numero massimo di tentativi di `run()` per un mini-batch. Una `run()` viene considerata non riuscita se viene generata un'eccezione o se non viene restituito nulla quando viene raggiunto `run_invocation_timeout` (facoltativo; il valore predefinito è `3`). 

È possibile specificare `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` e `run_max_try` come `PipelineParameter` in modo che, quando si ripete l'esecuzione della pipeline, sia possibile ottimizzare i valori dei parametri. In questo esempio si usa `PipelineParameter` per `mini_batch_size` e `Process_count_per_node` e questi valori verranno cambiati quando si ripete l'esecuzione in un secondo momento. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametri per la creazione di ParallelRunStep

Creare ParallelRunStep usando lo script, la configurazione dell'ambiente e i parametri. Specificare la destinazione di calcolo già collegata all'area di lavoro come destinazione di esecuzione dello script di inferenza. Usare `ParallelRunStep` per creare il passaggio della pipeline di inferenza batch, che accetta tutti i parametri seguenti:
- `name`: nome del passaggio, con le restrizioni di denominazione seguenti: univoco, 3-32 caratteri ed espressione regolare ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: oggetto `ParallelRunConfig`, come definito in precedenza.
- `inputs`: uno o più set di dati di Azure Machine Learning di tipo singolo da partizionare per l'elaborazione parallela.
- `side_inputs`: uno o più set di dati o dati di riferimento usati come input secondari senza necessità di partizionamento.
- `output`: `OutputFileDatasetConfig` Oggetto che rappresenta il percorso di directory in cui verranno archiviati i dati di output.
- `arguments`: elenco di argomenti passati allo script utente. Usare unknown_args per recuperarli nello script di immissione (facoltativo).
- `allow_reuse`: indica se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito con le stesse impostazioni o gli stessi input. Se il parametro è `False`, durante l'esecuzione della pipeline viene sempre generata una nuova esecuzione per questo passaggio (Facoltativo. Il valore predefinito è `True`).

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Debug degli script dal contesto remoto

La transizione dal debug di uno script di assegnazione del punteggio in locale al debug di uno script di assegnazione del punteggio in una pipeline effettiva può risultare difficile. Per informazioni sull'individuazione dei log nel portale, vedere la  [sezione pipeline di machine learning per il debug di script da un contesto remoto](how-to-debug-pipelines.md). Le informazioni contenute nella sezione si applicano anche a ParallelRunStep.

Ad esempio, il file di log `70_driver_log.txt` contiene le informazioni del controller che avvia il codice ParallelRunStep.

A causa della natura distribuita dei processi ParallelRunStep, sono presenti log di origini diverse. Tuttavia, vengono creati due file consolidati che offrono informazioni di alto livello:

- `~/logs/job_progress_overview.txt`: questo file offre informazioni di alto livello sul numero di mini-batch (chiamati anche attività) creati e il numero di mini-batch elaborati. A questo scopo, mostra il risultato del processo. Se il processo non è riuscito, verrà visualizzato il messaggio di errore e il punto in cui avviare la risoluzione dei problemi.

- `~/logs/sys/master_role.txt`: Questo file fornisce la visualizzazione del nodo principale, nota anche come agente di orchestrazione, del processo in esecuzione. Include la creazione di attività, il monitoraggio dello stato di avanzamento e il risultato dell'esecuzione.

I log generati dallo script di immissione usando l'helper EntryScript e le istruzioni print sono disponibili nei file seguenti:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Questi file sono i log scritti da entry_script usando l'helper EntryScript.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Questi file sono i log da stdout (ad esempio, l'istruzione Print) del entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Questi file sono i log di stderr della entry_script.

Per una comprensione immediata degli errori nello script, è disponibile il file:

- `~/logs/user/error.txt`: questo file tenta di riepilogare gli errori nello script.

Per altre informazioni sugli errori nello script, è disponibile il file:

- `~/logs/user/error/`: Contiene le analisi dello stack complete delle eccezioni generate durante il caricamento e l'esecuzione dello script di immissione.

Quando è necessaria una conoscenza completa del modo in cui ogni nodo ha eseguito lo script del punteggio, esaminare i singoli log dei processi per ogni nodo. I log dei processi sono disponibili nella cartella `sys/node`, raggruppati in base ai nodi di lavoro:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Questo file fornisce informazioni dettagliate su ogni mini-batch quando viene selezionato o completato da un thread di lavoro. Per ogni mini-batch, il file include:

    - Indirizzo IP e PID del processo di lavoro. 
    - Il numero totale di elementi, il numero di elementi elaborati correttamente e il numero di elementi non elaborati.
    - L'ora di inizio, la durata, il tempo di elaborazione e l'ora del metodo run.

È anche possibile visualizzare i risultati dei controlli periodici dell'utilizzo delle risorse per ogni nodo. I file di registro e i file di installazione si trovano in questa cartella:

- `~/logs/perf`: Impostare `--resource_monitor_interval` per modificare l'intervallo di controllo in secondi. L'intervallo predefinito è `600` , che è di circa 10 minuti. Per arrestare il monitoraggio, impostare il valore su `0` . Ogni `<ip_address>` cartella include:

    - `os/`: Informazioni su tutti i processi in esecuzione nel nodo. Un controllo esegue un comando del sistema operativo e salva il risultato in un file. In Linux, il comando è `ps` . In Windows usare `tasklist` .
        - `%Y%m%d%H`: Il nome della sottocartella è l'ora di ora.
            - `processes_%M`: Il file termina con il minuto del tempo di controllo.
    - `node_disk_usage.csv`: Utilizzo dettagliato del disco del nodo.
    - `node_resource_usage.csv`: Panoramica sull'utilizzo delle risorse del nodo.
    - `processes_resource_usage.csv`: Panoramica sull'utilizzo delle risorse di ogni processo.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Come registrare dallo script utente personale da un contesto remoto?

ParallelRunStep può eseguire più processi in un nodo basato su process_count_per_node. Per organizzare i log da ogni processo nel nodo e combinare l'istruzione Print and log, è consigliabile usare ParallelRunStep logger, come illustrato di seguito. Si ottiene un logger da EntryScript e si fa in modo che i log vengano visualizzati nella cartella **logs/utente** nel portale.

**Script di immissione di esempio che usa il logger:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Come passare un input laterale, ad esempio uno o più file contenenti una tabella di ricerca, a tutti i ruoli di lavoro?

L'utente può passare i dati di riferimento allo script usando side_inputs parametro di ParalleRunStep. Tutti i set di impostazioni specificati come side_inputs verranno montati in ogni nodo di lavoro. L'utente può ottenere la posizione del montaggio passando un argomento.

Costruire un [set](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) di dati contenente i dati di riferimento e registrarli con l'area di lavoro. Passarlo al parametro `side_inputs` di `ParallelRunStep`. Inoltre, è possibile aggiungere il percorso nella `arguments` sezione per accedere facilmente al percorso montato:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Successivamente, è possibile accedervi nello script di inferenza, ad esempio nel metodo init(), come illustrato di seguito:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Come usare i set di dati di input con l'autenticazione basata su entità servizio

L'utente può passare set di dati di input con l'autenticazione basata su entità servizio usata nell'area di lavoro. L'uso di tale set di dati in ParallelRunStep richiede la registrazione del set di dati per la costruzione della configurazione ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere questi [notebook di Jupyter che dimostrano pipeline Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Vedere le informazioni di riferimento su SDK per il pacchetto [azureml-pipeline-Steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) . Visualizzare la [documentazione](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) di riferimento per la classe ParallelRunStep.

* Seguire l' [esercitazione avanzata](tutorial-pipeline-batch-scoring-classification.md) sull'uso di pipeline con ParallelRunStep. Nell'esercitazione viene illustrato come passare un altro file come input laterale.
