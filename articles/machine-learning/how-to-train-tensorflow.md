---
title: Eseguire il training e la distribuzione di un modello TensorFlow
titleSuffix: Azure Machine Learning
description: Informazioni su Azure Machine Learning consente di aumentare il numero di scalabilità orizzontale di un processo di training tensorFlow usando risorse di calcolo cloud elastiche.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0eac999fa53679ef67c2a322bd8cc7841197d493
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819143"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Training di modelli TensorFlow su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training [tensorFlow](https://www.tensorflow.org/overview) su larga scala usando Azure Machine Learning.

Questo esempio esemplifica e registra un modello TensorFlow per classificare le cifre scritte a mano usando una rete neurale profonda (DNN).

Sia che si sviluppi un modello TensorFlow da zero o [](how-to-deploy-existing-model.md) che si usi un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare i processi di training open source per compilare, distribuire, versione e monitorare modelli di livello di produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:

 - Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

     - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella di deep learning degli esempi nel server notebook trovare un notebook completato ed espanso passando a questa directory: **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow** folder. 
 
 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` E `utils.py`
     
    È anche possibile trovare una versione [Jupyter Notebook completa di](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che illustrano l'ottimizzazione intelligente degli iperparameti, la distribuzione di modelli e i widget notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Importare prima di tutto le librerie Python necessarie.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L Azure Machine Learning workspace](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) oggetto .

Creare un oggetto area di lavoro dal `config.json` file creato nella sezione dei [prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Creare un set di dati di file

L'oggetto `FileDataset` fa riferimento a uno o più file nell'archivio dati dell'area di lavoro o negli URL pubblici. I file possono essere di qualsiasi formato e la classe offre la possibilità di scaricarli o montarli nel contesto di calcolo. Creando un oggetto `FileDataset`, si crea un riferimento alla posizione dell'origine dati. Anche le eventuali trasformazioni applicate al set di dati verranno archiviate nel set di dati. I dati rimangono nell'attuale posizione, quindi non si incorre in costi aggiuntivi di archiviazione. Per altre informazioni, vedere la guida alle [procedure](./how-to-create-register-datasets.md) del pacchetto `Dataset`.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Usare il metodo per registrare il set di dati nell'area di lavoro in modo che possa essere condiviso con altri utenti, riutilizzato in vari esperimenti e a cui si fa riferimento in base al nome nello `register()` script di training.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo in cui eseguire il processo TensorFlow. In questo esempio viene creato un cluster di calcolo Azure Machine Learning abilitato per la GPU.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Per altre informazioni sulle destinazioni di calcolo, vedere [l'articolo Che cos'è una destinazione di](concept-compute-target.md) calcolo.

### <a name="define-your-environment"></a>Definire l'ambiente

Per definire [l'ambiente](concept-environments.md) di Azure ML che incapsula le dipendenze dello script di training, è possibile definire un ambiente personalizzato o usare un ambiente curato di Azure ML.

#### <a name="use-a-curated-environment"></a>Usare un ambiente curato
Azure ML offre ambienti predefiniti e curati se non si vuole definire il proprio ambiente. Azure ML ha diversi ambienti curati per CPU e GPU per TensorFlow corrispondenti a versioni diverse di TensorFlow. Per altre informazioni, vedere [qui](resource-curated-environments.md).

Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Per visualizzare i pacchetti inclusi nell'ambiente curato, è possibile scrivere le dipendenze conda su disco:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Assicurarsi che l'ambiente curato includa tutte le dipendenze richieste dallo script di training. In caso contrario, sarà necessario modificare l'ambiente per includere le dipendenze mancanti. Si noti che se l'ambiente viene modificato, è necessario assegnargli un nuovo nome, perché il prefisso "AzureML" è riservato per gli ambienti curati. Se è stato modificato il file YAML delle dipendenze conda, è possibile creare un nuovo ambiente da esso con un nuovo nome, ad esempio:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Se invece è stato modificato direttamente l'oggetto ambiente curato, è possibile clonare l'ambiente con un nuovo nome:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato

È anche possibile creare un ambiente di Azure ML personalizzato che incapsula le dipendenze dello script di training.

Definire prima di tutto le dipendenze conda in un file YAML. In questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Creare un ambiente Azure ML da questa specifica dell'ambiente Conda. L'ambiente verrà inserito in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché questo esempio esegue il training in un cluster GPU, è necessario specificare un'immagine di base GPU con i driver e le dipendenze GPU necessari. Azure ML gestisce un set di immagini di base pubblicate in Microsoft Container Registry (MCR) che è possibile usare. Per altre informazioni, vedere il repository GitHub [azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Facoltativamente, è possibile acquisire tutte le dipendenze direttamente in un'immagine Docker personalizzata o Dockerfile e creare l'ambiente da tale immagine. Per altre informazioni, vedere [Training con un'immagine personalizzata.](how-to-train-with-custom-image.md)

Per altre informazioni sulla creazione e sull'uso di ambienti, vedere [Creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare uno ScriptRunConfig

Creare un oggetto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo. Tutti gli argomenti dello script di training verranno passati tramite la riga di comando, se specificati nel `arguments` parametro .

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si vuole caricare, usare un [file con](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) estensione ignore o non includerli nella directory di origine. Accedere invece ai dati usando un set di dati [di](how-to-train-with-datasets.md)Azure ML.

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [Configurare e inviare esecuzioni di training.](how-to-set-up-training-targets.md)

> [!WARNING]
> Se in precedenza si usava lo strumento di stima TensorFlow per configurare i processi di training di TensorFlow, si noti che gli estimatori sono stati deprecati a livello di versione dell'SDK 1.19.0. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il modo consigliato per configurare i processi di training, inclusi quelli che usano framework di Deep Learning. Per domande comuni sulla migrazione, vedere la [Guida alla migrazione da Estimator a ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Inviare un'esecuzione

[L'oggetto Run](/python/api/azureml-core/azureml.core.run%28class%29) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione
Durante l'esecuzione, l'esecuzione viene eseguita nelle fasi seguenti:

- **Preparazione:** viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono anche trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece viene specificato un ambiente curato, verrà usata l'immagine memorizzata nella cache che contiene tale ambiente curato.

- **Ridimensionamento:** il cluster tenta di aumentare le dimensioni se il cluster Batch per intelligenza artificiale richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In** esecuzione: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene `script` eseguito . Gli output di stdout e della cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione:** la **cartella ./outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md). Facoltativo: specificando i parametri `model_framework` , e , la distribuzione del modello senza codice diventa `model_framework_version` `resource_configuration` disponibile. In questo modo è possibile distribuire direttamente il modello come servizio Web dal modello registrato e l'oggetto definisce la risorsa di `ResourceConfiguration` calcolo per il servizio Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

È anche possibile scaricare una copia locale del modello usando l'oggetto Run. Nello script di training un oggetto salvatore TensorFlow rende persistente il modello in una cartella locale `tf_mnist.py` (locale per la destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Training distribuito

Azure Machine Learning supporta anche processi TensorFlow distribuiti a più nodi per poter ridimensionare i carichi di lavoro di training. È possibile eseguire facilmente processi TensorFlow distribuiti e Azure ML gestirà l'orchestrazione per l'utente.

Azure ML supporta l'esecuzione di processi TensorFlow distribuiti con l'API di training distribuita incorporata di Horovod e TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod è](https://github.com/uber/horovod) un framework open source di riduzione per il training distribuito sviluppato da Uber. Offre un percorso semplice per la scrittura di codice TensorFlow distribuito per il training.

Il codice di training dovrà essere instrumentato con Horovod per il training distribuito. Per altre informazioni sull'uso di Horovod con TensorFlow, vedere la documentazione di Horovod:

Per altre informazioni sull'uso di Horovod con TensorFlow, vedere la documentazione di Horovod:

* [Horovod con TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod con l'API Keras di TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Assicurarsi inoltre che l'ambiente di training includa **il pacchetto horovod.** Se si usa un ambiente curato da TensorFlow, horovod è già incluso come una delle dipendenze. Se si usa il proprio ambiente, assicurarsi che la dipendenza horovod sia inclusa, ad esempio:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Per eseguire un processo distribuito usando MPI/Horovod in Azure ML, è necessario specificare [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) al parametro del costruttore `distributed_job_config` ScriptRunConfig. Il codice seguente configurerà un processo distribuito a 2 nodi che esegue un processo per nodo. Se si desidera anche eseguire più processi per nodo ,ad esempio se lo SKU del cluster include più GPU, specificare anche il parametro `process_count_per_node` in MpiConfiguration (il valore predefinito è `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Per un'esercitazione completa sull'esecuzione di TensorFlow distribuito con Horovod in Azure ML, vedere [Distributed TensorFlow with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>tf.distribute

Se si usa [TensorFlow](https://www.tensorflow.org/guide/distributed_training) distribuito nativo nel codice di training, ad esempio l'API di TensorFlow 2.x, è anche possibile avviare il processo distribuito `tf.distribute.Strategy` tramite Azure ML. 

A tale scopo, specificare [tensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) per il `distributed_job_config` parametro del costruttore ScriptRunConfig. Se si usa `tf.distribute.experimental.MultiWorkerMirroredStrategy` , specificare `worker_count` in TensorflowConfiguration corrispondente al numero di nodi per il processo di training.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

In TensorFlow la variabile `TF_CONFIG` di ambiente è necessaria per il training in più computer. Azure ML configurerà e imposta la variabile in modo appropriato per ogni ruolo `TF_CONFIG` di lavoro prima di eseguire lo script di training. È possibile accedere `TF_CONFIG` dallo script di training se è necessario tramite `os.environ['TF_CONFIG']` .

Struttura di esempio di `TF_CONFIG` set in un nodo del ruolo di lavoro principale:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Se lo script di training usa la strategia del server di parametri per il training distribuito, ad esempio per TensorFlow 1.x legacy, sarà necessario specificare anche il numero di server di parametri da usare nel processo, ad esempio `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Distribuire un modello TensorFlow

La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente [alla](how-to-deploy-and-where.md#choose-a-compute-target) creazione di una destinazione di calcolo per la distribuzione, poiché è già presente un modello registrato.

### <a name="preview-no-code-model-deployment"></a>(Anteprima) Distribuzione di modelli senza codice

Invece della route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per TensorFlow. Registrando il modello come illustrato in precedenza con i parametri , e , è possibile usare semplicemente la `model_framework` `model_framework_version` funzione `resource_configuration` `deploy()` statica per distribuire il modello.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

La procedura [completa illustra la](how-to-deploy-and-where.md) distribuzione in Azure Machine Learning in modo più approfondito.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato training e registrato un modello TensorFlow e sono stati appresi i dettagli sulle opzioni per la distribuzione. Vedere questi altri articoli per altre informazioni su Azure Machine Learning.

* [Tracciare le metriche di esecuzione durante il training](how-to-log-view-metrics.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Architettura di riferimento per il training di Deep Learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
