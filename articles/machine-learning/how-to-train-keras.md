---
title: Training dei modelli Keras di Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training e registrare un modello di classificazione di rete neurale profonda Keras in esecuzione in TensorFlow usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 555ec90bbd73cee401f6f35aa04598792d2f24f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817147"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Training di modelli Keras su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training keras con Azure Machine Learning.

Il codice di esempio in questo articolo illustra come eseguire il training e registrare un modello di classificazione Keras creato usando il back-end TensorFlow con Azure Machine Learning. Usa il [popolare](http://yann.lecun.com/exdb/mnist/) set di dati MNIST per classificare le cifre scritte a mano usando una rete neurale profonda (DNN) creata usando la libreria [Python Keras](https://keras.io) in esecuzione su [TensorFlow.](https://www.tensorflow.org/overview)

Keras è un'API di rete neurale di alto livello in grado di eseguire più framework DNN più diffusi per semplificare lo sviluppo. Con Azure Machine Learning, è possibile aumentare rapidamente i processi di training usando risorse di calcolo cloud elastiche. È anche possibile tenere traccia delle esecuzioni di training, dei modelli di versione, della distribuzione dei modelli e molto altro ancora.

Sia che si sviluppi un modello Keras da zero o che si crei un modello esistente nel cloud, Azure Machine Learning può aiutare a creare modelli pronti per la produzione.

> [!NOTE]
> Se si usa l'API Keras **tf.keras** incorporata in TensorFlow e non nel pacchetto Keras autonomo, fare invece riferimento a Eseguire il training [dei modelli TensorFlow](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:

- Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

     - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella samples del server notebook trovare un notebook completato ed espanso passando alla cartella **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras.**

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` E `utils.py`

    È anche possibile trovare una versione [Jupyter Notebook completa di](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che illustrano l'ottimizzazione intelligente degli iperparameti, la distribuzione di modelli e i widget notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando fileDataset per i dati di training di input, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Importare prima di tutto le librerie Python necessarie.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L Azure Machine Learning area di](concept-workspace.md) lavoro è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli artefatti dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) oggetto .

Creare un oggetto area di lavoro `config.json` dal file creato nella sezione dei [prerequisiti](#prerequisites).

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

È possibile usare il metodo per registrare il set di dati nell'area di lavoro in modo che possa essere condiviso con altri utenti, riutilizzato in vari esperimenti e a cui si fa riferimento in base al nome nello `register()` script di training.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo in cui eseguire il processo di training. In questo esempio viene creato un cluster di calcolo Azure Machine Learning abilitato per la GPU.

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

Definire l'ambiente [azure](concept-environments.md) machine learning che incapsula le dipendenze dello script di training.

Definire prima di tutto le dipendenze conda in un file YAML. In questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Creare un ambiente di Azure Machine Learning da questa specifica dell'ambiente Conda. L'ambiente verrà inserito in un pacchetto in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché questo esempio esegue il training in un cluster GPU, sarà necessario specificare un'immagine di base GPU con i driver e le dipendenze GPU necessari. Azure ML gestisce un set di immagini di base pubblicate in Microsoft Container Registry (MCR) che è possibile usare. Per altre informazioni, vedere il repository GitHub [Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Per altre informazioni sulla creazione e l'uso di ambienti, vedere [Creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare uno ScriptRunConfig
Per prima cosa, ottenere i dati dall'archivio dati dell'area di lavoro usando la `Dataset` classe .

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Creare un oggetto ScriptRunConfig per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo.

Eventuali argomenti dello script di training verranno passati tramite la riga di comando, se specificati nel `arguments` parametro . DatasetConsumptionConfig per FileDataset viene passato come argomento allo script di training per `--data-folder` l'argomento . Azure ML risolverà questo DatasetConsumptionConfig nel punto di montaggio dell'archivio dati di backup, accessibile dallo script di training.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [Configurare e inviare esecuzioni di training.](how-to-set-up-training-targets.md)

> [!WARNING]
> Se in precedenza si usava lo strumento di stima TensorFlow per configurare i processi di training keras, si noti che gli estimatori sono stati deprecati a livello di versione dell'SDK 1.19.0. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il modo consigliato per configurare i processi di training, inclusi quelli che usano framework di Deep Learning. Per domande comuni sulla migrazione, vedere la [Guida alla migrazione da Estimator a ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Inviare l'esecuzione

[L'oggetto Run](/python/api/azureml-core/azureml.core.run%28class%29) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Durante l'esecuzione, l'esecuzione viene eseguita nelle fasi seguenti:

- **Preparazione:** viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece viene specificato un ambiente curato, verrà usata l'immagine memorizzata nella cache che contiene l'ambiente curato.

- **Ridimensionamento:** il cluster tenta di aumentare le dimensioni se il cluster batch per intelligenza artificiale richiede più nodi per eseguire l'esecuzione rispetto a quelli attualmente disponibili.

- **In** esecuzione: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene `script` eseguito . Gli output di stdout e **della cartella ./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione:** la **cartella ./outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-the-model"></a>Registrare il modello

Dopo aver training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente [alla](how-to-deploy-and-where.md#choose-a-compute-target) creazione di una destinazione di calcolo per la distribuzione, poiché è già presente un modello registrato.

È anche possibile scaricare una copia locale del modello. Ciò può essere utile per eseguire ulteriori operazioni di convalida del modello in locale. Nello script di training, , un oggetto salvatore TensorFlow rende persistente il modello in una cartella locale `keras_mnist.py` (locale per la destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia dalla cronologia di esecuzione.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato training e registrato un modello Keras in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-log-view-metrics.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep Learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
