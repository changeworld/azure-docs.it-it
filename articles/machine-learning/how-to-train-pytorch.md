---
title: Training di modelli PyTorch di Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training pytorch su scala aziendale usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5a955424f3fb91a38e9061966ed742922913f1c4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819161"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Training di modelli PyTorch su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training [di PyTorch](https://pytorch.org/) su scala aziendale usando Azure Machine Learning.

Gli script di esempio in questo articolo vengono usati per classificare immagini di pollo e di turco per creare una rete neurale di Deep Learning (DNN) basata sull'esercitazione sull'apprendimento sul trasferimento [di](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)PyTorch. L'apprendimento per il trasferimento è una tecnica che applica le conoscenze acquisite dalla risoluzione di un problema a un problema diverso ma correlato. Questo consente di creare un collegamento al processo di training richiedendo meno dati, tempo e risorse di calcolo rispetto al training da zero. Vedere [l'articolo Deep Learning e Machine Learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) per altre informazioni sull'apprendimento per il trasferimento.

Sia che si eserciti il training di un modello PyTorch di deep learning da zero o che si usi un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare orizzontalmente i processi di training open source usando risorse di calcolo cloud elastiche. È possibile compilare, distribuire, versione e monitorare modelli di livello di produzione con Azure Machine Learning. 

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:

- Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

    - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella di deep learning degli esempi nel server notebook trovare un notebook completato ed espanso passando a questa directory: **how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch** folder. 
 
 - Server Jupyter Notebook personale
    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    È anche possibile trovare una versione [Jupyter Notebook completa di](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che illustrano l'ottimizzazione intelligente degli iperparameti, la distribuzione di modelli e i widget notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Importare prima di tutto le librerie Python necessarie.

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

[L Azure Machine Learning area di](concept-workspace.md) lavoro è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli artefatti dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) oggetto .

Creare un oggetto area di lavoro `config.json` dal file creato nella sezione dei [prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Ottenere i dati

Il set di dati è costituito da circa 120 immagini di training per ogni classe, ognuna delle quali è costituita da 100 immagini di convalida. Il set di dati verrà scaricato ed estratto come parte dello script di training `pytorch_train.py` . Le immagini sono un subset del set di [dati Open Images v5.](https://storage.googleapis.com/openimages/web/index.html)

### <a name="prepare-training-script"></a>Preparare lo script di training

In questa esercitazione è già disponibile lo script `pytorch_train.py` di training . In pratica, è possibile eseguire qualsiasi script di training personalizzato così come è ed eseguirlo con Azure Machine Learning.

Creare una cartella per gli script di training.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo in cui eseguire il processo PyTorch. In questo esempio viene creato un cluster di calcolo Azure Machine Learning abilitato per la GPU.

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
Azure ML offre ambienti predefiniti e curati se non si vuole definire il proprio ambiente. Azure ML ha diversi ambienti curati per CPU e GPU per PyTorch corrispondenti a versioni diverse di PyTorch. Per altre informazioni, vedere [qui](resource-curated-environments.md).

Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Per visualizzare i pacchetti inclusi nell'ambiente curato, è possibile scrivere le dipendenze conda su disco:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Assicurarsi che l'ambiente curato includa tutte le dipendenze richieste dallo script di training. In caso contrario, sarà necessario modificare l'ambiente per includere le dipendenze mancanti. Si noti che se l'ambiente viene modificato, sarà necessario assegnargli un nuovo nome, perché il prefisso "AzureML" è riservato agli ambienti curati. Se è stato modificato il file YAML delle dipendenze conda, è possibile creare un nuovo ambiente da esso con un nuovo nome, ad esempio:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Se invece è stato modificato direttamente l'oggetto ambiente curato, è possibile clonare l'ambiente con un nuovo nome:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato

È anche possibile creare un ambiente Azure ML personalizzato che incapsula le dipendenze dello script di training.

Definire prima di tutto le dipendenze conda in un file YAML. In questo esempio il file è denominato `conda_dependencies.yml` .

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

Creare un ambiente Azure ML da questa specifica dell'ambiente Conda. L'ambiente verrà inserito in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché questo esempio esegue il training in un cluster GPU, è necessario specificare un'immagine di base GPU con i driver e le dipendenze gpu necessari. Azure ML gestisce un set di immagini di base pubblicate in Microsoft Container Registry (MCR) che è possibile usare. Per altre informazioni, vedere il repository GitHub [azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Facoltativamente, è possibile acquisire tutte le dipendenze direttamente in un'immagine Docker personalizzata o Dockerfile e creare l'ambiente da tale immagine. Per altre informazioni, vedere [Training con un'immagine personalizzata.](how-to-train-with-custom-image.md)

Per altre informazioni sulla creazione e sull'uso di ambienti, vedere [Creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare uno ScriptRunConfig

Creare un oggetto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo. Tutti gli argomenti dello script di training verranno passati tramite la riga di comando, se specificato nel `arguments` parametro . 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si vuole caricare, usare un [file con](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) estensione ignore o non includerli nella directory di origine. Accedere invece ai dati usando un set di dati [di](how-to-train-with-datasets.md)Azure Ml.

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [Configurare e inviare esecuzioni di training.](how-to-set-up-training-targets.md)

> [!WARNING]
> Se in precedenza si usava lo strumento di stima di PyTorch per configurare i processi di training di PyTorch, si noti che estimator è stato deprecato a data 1.19.0 SDK. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il modo consigliato per configurare i processi di training, inclusi quelli che usano framework di Deep Learning. Per domande comuni sulla migrazione, vedere la guida [alla migrazione Da Estimator a ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Inviare l'esecuzione

[L'oggetto Run](/python/api/azureml-core/azureml.core.run%28class%29) fornisce l'interfaccia per la cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione
Durante l'esecuzione, l'esecuzione viene eseguita nelle fasi seguenti:

- **Preparazione:** viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono anche trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece viene specificato un ambiente curato, verrà usata l'immagine memorizzata nella cache che contiene tale ambiente curato.

- **Ridimensionamento:** il cluster tenta di aumentare le dimensioni se il cluster Batch per intelligenza artificiale richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In** esecuzione: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene `script` eseguito . Gli output di stdout e della cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione:** la **cartella ./outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver creato il training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente [alla](how-to-deploy-and-where.md#choose-a-compute-target) creazione di una destinazione di calcolo per la distribuzione, poiché è già presente un modello registrato.

È anche possibile scaricare una copia locale del modello usando l'oggetto Esegui. Nello script di training un oggetto di salvataggio PyTorch rende persistente il modello `pytorch_train.py` in una cartella locale (locale per la destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Training distribuito

Azure Machine Learning supporta anche processi PyTorch distribuiti a più nodi in modo da poter ridimensionare i carichi di lavoro di training. È possibile eseguire facilmente processi PyTorch distribuiti e Azure ML gestirà l'orchestrazione per l'utente.

Azure ML supporta l'esecuzione di processi PyTorch distribuiti con il modulo DistributedDataParallel incorporato di Horovod e PyTorch.

### <a name="horovod"></a>Horovod
[Horovod è](https://github.com/uber/horovod) un framework open source di riduzione per il training distribuito sviluppato da Uber. Offre un percorso semplice per la scrittura di codice PyTorch distribuito per il training.

Il codice di training dovrà essere instrumentato con Horovod per il training distribuito. Per altre informazioni sull'uso di Horovod con PyTorch, vedere la documentazione [di Horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Assicurarsi inoltre che l'ambiente di training includa **il pacchetto horovod.** Se si usa un ambiente curato da PyTorch, horovod è già incluso come una delle dipendenze. Se si usa il proprio ambiente, assicurarsi che la dipendenza horovod sia inclusa, ad esempio:

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

Per eseguire un processo distribuito usando MPI/Horovod in Azure ML, è necessario specificare [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) al parametro del costruttore `distributed_job_config` ScriptRunConfig. Il codice seguente configurerà un processo distribuito a 2 nodi che esegue un processo per nodo. Se si vuole anche eseguire più processi per nodo ,ad esempio se lo SKU del cluster ha più GPU, specificare anche il parametro `process_count_per_node` in MpiConfiguration (il valore predefinito è `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Per un'esercitazione completa sull'esecuzione di PyTorch distribuito con Horovod in Azure ML, vedere [PyTorch distribuito con Horovod.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)

### <a name="distributeddataparallel"></a>DistributedDataParallel
Se si usa il modulo [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) incorporato di PyTorch creato usando il pacchetto **torch.distributed** nel codice di training, è anche possibile avviare il processo distribuito tramite Azure ML.

Per avviare un processo PyTorch distribuito in Azure ML, sono disponibili due opzioni:
1. Avvio per processo: specificare il numero totale di processi di lavoro da eseguire e Azure ML gestirà l'avvio di ogni processo.
2. Avvio per nodo con `torch.distributed.launch` : specificare il comando che si vuole eseguire in ogni `torch.distributed.launch` nodo. L'utilità di avvio della torch gestirà l'avvio dei processi di lavoro in ogni nodo.

Non esistono differenze fondamentali tra queste opzioni di avvio. è in gran parte a seconda delle preferenze dell'utente o delle convenzioni dei framework/librerie create sulla base di Vanilla PyTorch (ad esempio Lightning o Hugging Face).

#### <a name="per-process-launch"></a>Avvio per processo
Per usare questa opzione per eseguire un processo PyTorch distribuito, eseguire le operazioni seguenti:
1. Specificare lo script di training e gli argomenti
2. Creare un [elemento PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) e specificare `process_count` e `node_count` . corrisponde `process_count` al numero totale di processi da eseguire per il processo. Questo valore deve in genere essere uguale al numero di GPU per nodo moltiplicato per il numero di nodi. Se `process_count` non è specificato, Azure ML avvierà per impostazione predefinita un processo per ogni nodo.

Azure ML imposta le variabili di ambiente seguenti:
* `MASTER_ADDR` - Indirizzo IP del computer che ospiterà il processo con classificazione 0.
* `MASTER_PORT` - Una porta libera nel computer che ospiterà il processo con classificazione 0.
* `NODE_RANK` : classificazione del nodo per il training a più nodi. I valori possibili sono da 0 a (numero totale di nodi - 1).
* `WORLD_SIZE` : numero totale di processi. Deve essere uguale al numero totale di dispositivi (GPU) usati per il training distribuito.
* `RANK` - Classificazione (globale) del processo corrente. I valori possibili sono da 0 a (dimensioni del mondo - 1).
* `LOCAL_RANK` - Classificazione locale (relativa) del processo all'interno del nodo. I valori possibili sono da 0 a (# di processi nel nodo - 1).

Poiché le variabili di ambiente necessarie verranno impostate automaticamente da Azure ML, è possibile usare il metodo di inizializzazione delle variabili di ambiente predefinito per inizializzare il gruppo di processi nel codice di training. [](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization)

Il frammento di codice seguente configura un processo PyTorch a 2 nodi e 2 processi per nodo:
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
> Per usare questa opzione per il training multi-processo per nodo, è necessario usare Azure ML Python SDK >= 1.22.0, come è stato introdotto `process_count` nella versione 1.22.0.

> [!TIP]
> Se lo script di training passa informazioni come la classificazione locale o la classificazione come argomenti di script, è possibile fare riferimento alle variabili di ambiente negli argomenti: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Avvio per nodo con `torch.distributed.launch`
PyTorch fornisce un'utilità di avvio in [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) che gli utenti possono usare per avviare più processi per nodo. Il `torch.distributed.launch` modulo genera più processi di training in ognuno dei nodi.

I passaggi seguenti illustrano come configurare un processo PyTorch con un'utilità di avvio per nodo in Azure ML che avrà l'equivalente di eseguire il comando seguente:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Fornire il `torch.distributed.launch` comando al parametro del `command` `ScriptRunConfig` costruttore. Azure ML eseguirà questo comando in ogni nodo del cluster di training. `--nproc_per_node` deve essere minore o uguale al numero di GPU disponibili in ogni nodo. `MASTER_ADDR`, e sono tutti impostati da Azure ML, quindi è possibile fare riferimento `MASTER_PORT` solo alle variabili di ambiente nel comando `NODE_RANK` . Azure ML imposta su 6105, ma è possibile passare un `MASTER_PORT` valore diverso all'argomento del `--master_port` `torch.distributed.launch` comando, se lo si desidera. L'utilità di avvio reimposta le variabili di ambiente.
2. Creare un `PyTorchConfiguration` oggetto e specificare `node_count` . Non è necessario impostare perché Azure ML avvierà per impostazione predefinita un processo per nodo, che eseguirà `process_count` il comando di avvio specificato.

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

Per un'esercitazione completa sull'esecuzione di PyTorch distribuito in Azure ML, vedere [PyTorch distribuito con DistributedDataParallel.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)

### <a name="troubleshooting"></a>Risoluzione dei problemi

* **Horovod** è stato arrestato: nella maggior parte dei casi, se si verifica "AbortedError: Horovod è stato arrestato", si è verificata un'eccezione sottostante in uno dei processi che ha causato l'arresto di Horovod. Ogni classificazione nel processo MPI ottiene un suo file di log dedicato in Azure ML. Tali log sono denominati `70_driver_logs`. Nel caso di training distribuito, per rendere più semplice differenziare i log, i nomi dei log sono seguiti dal suffisso `_rank`. Per trovare l'errore esatto che ha causato l'arresto di Horovod, esaminare tutti i file di log e cercare la fine `Traceback` dei file driver_log. Uno di questi file genera l'eccezione sottostante effettiva. 

## <a name="export-to-onnx"></a>Eseguire l'esportazione in ONNX

Per ottimizzare l'inferenza con [il runtime ONNX,](concept-onnx.md)convertire il modello PyTorch sottoposto a training nel formato ONNX. L'inferenza, o punteggio del modello, è la fase in cui il modello distribuito viene usato per la stima, in genere sui dati di produzione. Per un [esempio,](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) vedere l'esercitazione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato appreso e registrato un deep learning e una rete neurale usando PyTorch in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-log-view-metrics.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep Learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
