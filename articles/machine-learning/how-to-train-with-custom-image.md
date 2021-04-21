---
title: Eseguire il training di un modello usando un'immagine Docker personalizzata
titleSuffix: Azure Machine Learning
description: Informazioni su come usare le proprie immagini Docker o quelle curate da Microsoft per eseguire il training dei modelli Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 953d43f93635e25da008515afd9baf9a9e9b7afa
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817072"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Eseguire il training di un modello usando un'immagine Docker personalizzata

Questo articolo illustra come usare un'immagine Docker personalizzata quando si esegue il training di modelli con Azure Machine Learning. Si useranno gli script di esempio in questo articolo per classificare le immagini degli animali domestici creando una rete neurale convoluzionale. 

Azure Machine Learning un'immagine di base Docker predefinita. È anche possibile usare Azure Machine Learning per specificare un'immagine di base diversa, ad esempio una delle immagini di [base](https://github.com/Azure/AzureML-Containers) Azure Machine Learning [personalizzate.](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) Le immagini di base personalizzate consentono di gestire attentamente le dipendenze e mantenere un controllo più stretto sulle versioni dei componenti durante l'esecuzione di processi di training.

## <a name="prerequisites"></a>Prerequisiti

Eseguire il codice in uno di questi ambienti:

* Azure Machine Learning'istanza di calcolo (non sono necessari download o installazione):
  * Completare [l'esercitazione Configurare l'ambiente e l'area](tutorial-1st-experiment-sdk-setup.md) di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
  * Nel repository Azure Machine Learning [esempi](https://github.com/Azure/azureml-examples)trovare un notebook completato nella directory **notebook**  >  **fastai**  >  **train-pets-resnet34.ipynb.** 
* Server Jupyter Notebook:
  * Creare un [file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
  * Installare [Azure Machine Learning SDK](/python/api/overview/azure/ml/install). 
  * Creare un [Registro Azure Container](../container-registry/index.yml) o un altro registro Docker disponibile su Internet.

## <a name="set-up-a-training-experiment"></a>Configurare un esperimento di training

In questa sezione viene configurato l'esperimento di training inizializzando un'area di lavoro, definendo l'ambiente e configurando una destinazione di calcolo.

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L Azure Machine Learning workspace](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata in cui lavorare con tutti gli elementi creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) oggetto .

Creare `Workspace` un oggetto dal config.jsfile creato come [prerequisito](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definire l'ambiente

Creare un `Environment` oggetto e abilitare Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

L'immagine di base specificata nel codice seguente supporta la libreria fast.ai, che consente funzionalità di Deep Learning distribuite. Per altre informazioni, vedere il [repository fast.ai Docker Hub .](https://hub.docker.com/u/fastdotai) 

Quando si usa l'immagine Docker personalizzata, è possibile che l'ambiente Python sia già configurato correttamente. In tal caso, impostare il `user_managed_dependencies` flag su per usare `True` l'ambiente Python predefinito dell'immagine personalizzata. Per impostazione predefinita, Azure Machine Learning compila un ambiente Conda con le dipendenze specificate. Il servizio esegue lo script in tale ambiente anziché usare le librerie Python installate nell'immagine di base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Usare un registro contenitori privato (facoltativo)

Per usare un'immagine di un registro contenitori privato non presente nell'area di lavoro, usare per specificare l'indirizzo del repository e un `docker.base_image_registry` nome utente e una password:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Usare un Dockerfile personalizzato (facoltativo)

È anche possibile usare un Dockerfile personalizzato. Usare questo approccio se è necessario installare pacchetti non Python come dipendenze. Ricordarsi di impostare l'immagine di base su `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning supporta solo immagini Docker che forniscono il software seguente:
> * Ubuntu 16.04 o versione successiva.
> * Conda 4.5.# o versione successiva.
> * Python 3.6+.

Per altre informazioni sulla creazione e la gestione di Azure Machine Learning, vedere [Creare e usare ambienti software.](how-to-use-environments.md) 

### <a name="create-or-attach-a-compute-target"></a>Creare o collegare una destinazione di calcolo

È necessario creare una destinazione [di calcolo per](concept-azure-machine-learning-architecture.md#compute-targets) il training del modello. In questa esercitazione si crea come `AmlCompute` risorsa di calcolo di training.

La creazione `AmlCompute` di richiede alcuni minuti. Se la `AmlCompute` risorsa è già presente nell'area di lavoro, questo codice ignora il processo di creazione.

Come per altri servizi di Azure, esistono limiti per determinate risorse (ad esempio, `AmlCompute` ) associati al servizio Azure Machine Learning. Per altre informazioni, vedere [Limiti predefiniti e come richiedere una quota superiore.](how-to-manage-quotas.md)

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Configurare il processo di training

Per questa esercitazione, usare lo script di training *train.py* su [GitHub.](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py) In pratica, è possibile prendere qualsiasi script di training personalizzato ed eseguirlo, così come, con Azure Machine Learning.

Creare una `ScriptRunConfig` risorsa per configurare il processo per l'esecuzione nella destinazione di calcolo [desiderata.](how-to-set-up-training-targets.md)

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Inviare il processo di training

Quando si invia un'esecuzione di training usando un `ScriptRunConfig` oggetto , il metodo restituisce un oggetto di tipo `submit` `ScriptRun` . `ScriptRun`L'oggetto restituito consente di accedere a livello di codice alle informazioni sull'esecuzione del training. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si vuole caricare, usare un [file con](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) estensione ignore o non includerli nella directory di origine. Accedere invece ai dati usando un [archivio dati](/python/api/azureml-core/azureml.data).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato addestrato un modello usando un'immagine Docker personalizzata. Vedere questi altri articoli per altre informazioni su Azure Machine Learning:
* [Tenere traccia delle metriche di esecuzione](how-to-log-view-metrics.md) durante il training.
* [Distribuire un modello](how-to-deploy-custom-docker-image.md) usando un'immagine Docker personalizzata.
