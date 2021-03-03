---
title: Training di modelli PyTorch per Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di PyTorch su scala aziendale usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: cb556466a5a76cbb9447538e98a5a2385f7b5614
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661002"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Esegui il training dei modelli PyTorch su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training di [PyTorch](https://pytorch.org/) su scala aziendale usando Azure Machine Learning.

Gli script di esempio in questo articolo vengono usati per classificare le immagini di pollo e tacchino per creare una rete neurale per la formazione approfondita (DNN) basata sull' [esercitazione](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)per il trasferimento di PyTorch. Il trasferimento dell'apprendimento è una tecnica che applica le informazioni ottenute dalla risoluzione di un problema a un problema diverso ma correlato. Questo consente di scorciatoie per il processo di training richiedendo meno dati, tempo e risorse di calcolo rispetto alla formazione da zero. Per altre informazioni sull'apprendimento del trasferimento, vedere l'articolo sull'apprendimento [automatico di Visual](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) Studio.

Sia che si stia eseguendo il training di un modello PyTorch di apprendimento avanzato da zero o si stia portando un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare i processi di training open source con risorse di calcolo elastiche del cloud. Con Azure Machine Learning è possibile compilare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione. 

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

- Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

    - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella Samples Deep learning nel server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > pytorch > Train-iperparameter-Tune-deploy-with-pytorch** Folder. 
 
 - Server Jupyter Notebook personale
    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Prima di tutto, importare le librerie Python necessarie.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Ottenere i dati

Il set di dati è costituito da circa 120 di immagini di training per tacchini e polling, con immagini di convalida 100 per ogni classe. Il set di dati viene scaricato ed estratto come parte dello script di training `pytorch_train.py` . Le immagini sono un subset del [set di dati V5 Open images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Preparare lo script di training

In questa esercitazione, lo script di training `pytorch_train.py` è già disponibile. In pratica, è possibile usare qualsiasi script di training personalizzato, così come è, ed eseguirlo con Azure Machine Learning.

Creare una cartella per gli script di training.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per l'esecuzione del processo PyTorch. In questo esempio, creare un cluster di calcolo Azure Machine Learning abilitato per GPU.

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

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

### <a name="define-your-environment"></a>Definire l'ambiente

Per definire l' [ambiente](concept-environments.md) Azure ml che incapsula le dipendenze dello script di training, è possibile definire un ambiente personalizzato o usare un ambiente di Azure ml curato.

#### <a name="use-a-curated-environment"></a>Usare un ambiente curato
Azure ML offre ambienti predefiniti e curati se non si vuole definire un ambiente personalizzato. Azure ML dispone di diversi ambienti di CPU e GPU curati per PyTorch corrispondenti a versioni diverse di PyTorch. Per altre informazioni, vedere [qui](resource-curated-environments.md).

Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Per visualizzare i pacchetti inclusi nell'ambiente curato, è possibile scrivere le dipendenze conda sul disco:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Assicurarsi che l'ambiente curato includa tutte le dipendenze richieste dallo script di training. In caso contrario, sarà necessario modificare l'ambiente in modo da includere le dipendenze mancanti. Si noti che se l'ambiente viene modificato, sarà necessario assegnargli un nuovo nome, perché il prefisso ' AzureML ' è riservato per gli ambienti curati. Se il file YAML delle dipendenze conda è stato modificato, è possibile creare un nuovo ambiente con un nuovo nome, ad esempio:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Se invece l'oggetto ambiente curato è stato modificato direttamente, è possibile clonare tale ambiente con un nuovo nome:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato

È anche possibile creare un ambiente Azure ML personalizzato che incapsula le dipendenze dello script di training.

Definire innanzitutto le dipendenze conda in un file YAML; in questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Creare un ambiente Azure ML da questa specifica dell'ambiente conda. L'ambiente verrà incluso in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché in questo esempio viene eseguito il training in un cluster GPU, è necessario specificare un'immagine di base GPU con le dipendenze e i driver GPU necessari. Azure ML mantiene un set di immagini di base pubblicate in Microsoft Container Registry (AzureML) che è possibile usare. per ulteriori informazioni, vedere il repository GitHub [Azure/-container](https://github.com/Azure/AzureML-Containers) .

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Facoltativamente, è possibile acquisire solo tutte le dipendenze direttamente in un'immagine Docker personalizzata o Dockerfile e creare il proprio ambiente. Per altre informazioni, vedere eseguire il [training con un'immagine personalizzata](how-to-train-with-custom-image.md).

Per altre informazioni sulla creazione e sull'uso degli ambienti, vedere [creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig

Creare un oggetto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo. Eventuali argomenti dello script di training verranno passati tramite la riga di comando se specificati nel `arguments` parametro. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [set](how-to-train-with-datasets.md)di dati di Azure ml.

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [configurare e inviare le esecuzioni di training](how-to-set-up-training-targets.md).

> [!WARNING]
> Se in precedenza si usa PyTorch Estimator per configurare i processi di training di PyTorch, si noti che gli estimatori sono stati deprecati a partire dalla versione SDK di 1.19.0. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il metodo consigliato per configurare i processi di training, inclusi quelli che usano Framework di apprendimento avanzato. Per domande comuni sulla migrazione, vedere la [Guida alla migrazione da Estimator a ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Invia la tua esecuzione

L' [oggetto Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece si specifica un ambiente curato, verrà utilizzata l'immagine memorizzata nella cache che supporta l'ambiente curato.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e `script` viene eseguito. Gli output da stdout e la cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella **./Outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È inoltre possibile scaricare una copia locale del modello utilizzando l'oggetto Esegui. Nello script di training `pytorch_train.py` , un oggetto PyTorch Save Salva in modo permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Training distribuito

Azure Machine Learning supporta anche processi PyTorch distribuiti a più nodi, in modo che sia possibile ridimensionare i carichi di lavoro di training. È possibile eseguire facilmente processi PyTorch distribuiti e Azure ML gestirà l'orchestrazione.

Azure ML supporta l'esecuzione di processi PyTorch distribuiti con il modulo DistributedDataParallel incorporato Horovod e PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un framework open source di riduzione per la formazione distribuita sviluppata da uber. Offre un percorso facile per la scrittura di codice PyTorch distribuito per il training.

Il codice di training dovrà essere instrumentato con Horovod per il training distribuito. Per ulteriori informazioni sull'utilizzo di Horovod con PyTorch, vedere la [documentazione di Horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Assicurarsi inoltre che l'ambiente di training includa il pacchetto **horovod** . Se si usa un ambiente curato PyTorch, horovod è già incluso come una delle dipendenze. Se si usa un ambiente personalizzato, verificare che sia inclusa la dipendenza horovod, ad esempio:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Per eseguire un processo distribuito con MPI/Horovod in Azure ML, è necessario specificare un [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) per il `distributed_job_config` parametro del costruttore ScriptRunConfig. Il codice seguente consente di configurare un processo distribuito a 2 nodi che esegue un processo per nodo. Se si desidera eseguire più processi per nodo, ad esempio se lo SKU del cluster ha più GPU, specificare anche il `process_count_per_node` parametro in MpiConfiguration (il valore predefinito è `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Per un'esercitazione completa sull'esecuzione di PyTorch distribuiti con Horovod in Azure ML, vedere [Distributed PyTorch with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Se si usa il modulo [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) incorporato di PyTorch compilato con il pacchetto **torcia. Distributed** nel codice di training, è anche possibile avviare il processo distribuito tramite Azure ml.

Per avviare un processo PyTorch distribuito in Azure ML, sono disponibili due opzioni:
1. Avvio per processo: specificare il numero totale di processi di lavoro che si vuole eseguire e Azure ML gestirà l'avvio di ogni processo.
2. Avvio per nodo con `torch.distributed.launch` : specificare il `torch.distributed.launch` comando che si vuole eseguire in ogni nodo. L'utilità di avvio torcia gestirà l'avvio dei processi di lavoro in ogni nodo.

Non esistono differenze fondamentali tra queste opzioni di avvio; in gran parte, le preferenze dell'utente o le convenzioni dei Framework o delle librerie basate su Vanilla PyTorch (ad esempio, fulmini o abbracci).

#### <a name="per-process-launch"></a>Avvio per processo
Per usare questa opzione per eseguire un processo PyTorch distribuito, eseguire le operazioni seguenti:
1. Specificare lo script di training e gli argomenti
2. Creare un [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) e specificare e `process_count` `node_count` . `process_count`Corrisponde al numero totale di processi che si desidera eseguire per il processo. Questo deve essere in genere uguale al numero di GPU per nodo moltiplicato per il numero di nodi. Se `process_count` non viene specificato, Azure ml avvierà per impostazione predefinita un processo per ogni nodo.

Azure ML imposta le variabili di ambiente seguenti:
* `MASTER_ADDR` -Indirizzo IP del computer che ospiterà il processo con rango 0.
* `MASTER_PORT` : Porta libera nel computer che ospiterà il processo con rango 0.
* `NODE_RANK` : Rango del nodo per la formazione a più nodi. I valori possibili sono compresi tra 0 e (numero totale di nodi-1).
* `WORLD_SIZE` : Numero totale di processi. Deve essere uguale al numero totale di dispositivi (GPU) usati per il training distribuito.
* `RANK` -Rango (globale) del processo corrente. I valori possibili sono compresi tra 0 e (World size-1).
* `LOCAL_RANK` : Rango locale (relativo) del processo all'interno del nodo. I valori possibili sono da 0 a (numero di processi nel nodo-1).

Poiché le variabili di ambiente obbligatorie verranno impostate automaticamente da Azure ML, è possibile usare [il metodo di inizializzazione della variabile di ambiente predefinito](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) per inizializzare il gruppo di processi nel codice di training.

Il frammento di codice seguente configura un processo PyTorch a 2 nodi, 2 processi per nodo:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> Per usare questa opzione per il training a più processi per nodo, è necessario usare Azure ML Python SDK >= 1.22.0, come `process_count` è stato introdotto in 1.22.0.

> [!TIP]
> Se lo script di training passa informazioni come rango locale o rango come argomenti di script, è possibile fare riferimento alle variabili di ambiente negli argomenti: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Avvio per nodo con `torch.distributed.launch`
PyTorch fornisce un'utilità di avvio in [torcia. Distributed. Launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) che gli utenti possono usare per avviare più processi per nodo. Il `torch.distributed.launch` modulo genera più processi di training in ogni nodo.

I passaggi seguenti illustrano come configurare un processo PyTorch con un utilità di avvio per nodo in Azure ML che otterrà l'equivalente dell'esecuzione del comando seguente:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Fornire il `torch.distributed.launch` comando al `command` parametro del `ScriptRunConfig` costruttore. Azure ML eseguirà questo comando in ogni nodo del cluster di training. `--nproc_per_node` deve essere minore o uguale al numero di GPU disponibili in ogni nodo. `MASTER_ADDR`, `MASTER_PORT` e `NODE_RANK` sono tutti impostati da Azure ml, quindi è possibile fare riferimento solo alle variabili di ambiente nel comando. Azure ML imposta `MASTER_PORT` su 6105, ma è possibile passare un valore diverso all' `--master_port` argomento del `torch.distributed.launch` comando, se lo si desidera. L'utilità di avvio Reimposta le variabili di ambiente.
2. Creare un oggetto `PyTorchConfiguration` e specificare `node_count` . Per impostazione predefinita, non è necessario impostare `process_count` perché Azure ml avvierà un processo per nodo, che eseguirà il comando di avvio specificato.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

Per un'esercitazione completa sull'esecuzione di PyTorch distribuiti in Azure ML, vedere [Distributed PyTorch with DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel).

### <a name="troubleshooting"></a>Risoluzione dei problemi

* **Horovod è stato arrestato**: nella maggior parte dei casi, se si verifica "AbortedError: Horovod è stato arrestato", si è verificata un'eccezione sottostante in uno dei processi che hanno causato l'arresto di Horovod. Ogni classificazione nel processo MPI ottiene un suo file di log dedicato in Azure ML. Tali log sono denominati `70_driver_logs`. Nel caso di training distribuito, per rendere più semplice differenziare i log, i nomi dei log sono seguiti dal suffisso `_rank`. Per individuare l'errore esatto che ha causato l'arresto di Horovod, esaminare tutti i file di log e cercare `Traceback` alla fine dei file di driver_log. Uno di questi file fornirà l'effettiva eccezione sottostante. 

## <a name="export-to-onnx"></a>Eseguire l'esportazione in ONNX

Per ottimizzare l'inferenza con il [Runtime ONNX](concept-onnx.md), convertire il modello PyTorch sottoposto a training nel formato ONNX. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione. Per un esempio, vedere l' [esercitazione](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di una rete neurale di apprendimento avanzato con PyTorch in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
