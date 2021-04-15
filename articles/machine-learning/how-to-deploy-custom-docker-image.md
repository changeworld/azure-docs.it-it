---
title: Distribuire modelli con un'immagine Docker personalizzata
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un'immagine di base Docker personalizzata per distribuire i Azure Machine Learning personalizzati. Sebbene Azure Machine Learning un'immagine di base predefinita, è anche possibile usare un'immagine di base personalizzata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: f621bb2a7d4543620d22ab85fb8b44752c9989ac
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376257"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Distribuire un modello usando un'immagine di base Docker personalizzata

Informazioni su come usare un'immagine di base Docker personalizzata quando si distribuiscono modelli con training con Azure Machine Learning.

Azure Machine Learning verrà utilizzata un'immagine Docker di base predefinita se non ne viene specificata nessuna. È possibile trovare l'immagine Docker specifica usata con `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . È anche possibile usare Azure Machine Learning __ambienti__ per selezionare un'immagine di base specifica o usarne una personalizzata.

Un'immagine di base viene usata come punto di partenza quando viene creata un'immagine per una distribuzione. Fornisce il sistema operativo e i componenti sottostanti. Il processo di distribuzione aggiunge quindi all'immagine componenti aggiuntivi, ad esempio il modello, l'ambiente Conda e altri asset.

In genere, si crea un'immagine di base personalizzata quando si vuole usare Docker per gestire le dipendenze, mantenere un controllo più stretto sulle versioni dei componenti o risparmiare tempo durante la distribuzione. È anche possibile installare il software richiesto dal modello, in cui il processo di installazione richiede molto tempo. L'installazione del software durante la creazione dell'immagine di base significa che non è necessario installarlo per ogni distribuzione.

> [!IMPORTANT]
> Quando si distribuisce un modello, non è possibile eseguire l'override dei componenti di base, ad esempio il server Web o IoT Edge componenti. Questi componenti forniscono un ambiente di lavoro noto testato e supportato da Microsoft.

> [!WARNING]
> Microsoft potrebbe non essere in grado di risolvere i problemi causati da un'immagine personalizzata. Se si verificano problemi, potrebbe essere richiesto di usare l'immagine predefinita o una delle immagini fornite da Microsoft per verificare se il problema è specifico dell'immagine.

Questo documento è suddiviso in due sezioni:

* Creare un'immagine di base personalizzata: fornisce informazioni agli amministratori e a DevOps sulla creazione di un'immagine personalizzata e sulla configurazione dell'autenticazione per un Registro Azure Container tramite l'interfaccia della riga di comando di Azure e l'interfaccia della riga di Machine Learning comando.
* Distribuire un modello usando un'immagine di base personalizzata: fornisce informazioni a data scientist e tecnici DevOps/ML sull'uso di immagini personalizzate durante la distribuzione di un modello con training da Python SDK o dall'interfaccia della riga di comando di Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [Creare un'area di](how-to-manage-workspace.md) lavoro.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install). 
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Un [Registro Azure Container](../container-registry/index.yml) o un altro registro Docker accessibile su Internet.
* I passaggi descritti in questo documento presuppongono che si abbia familiarità con la creazione e l'uso di un oggetto di configurazione __dell'inferenza__ come parte della distribuzione del modello. Per altre informazioni, vedere [Dove distribuire e come](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Creare un'immagine di base personalizzata

Le informazioni contenute in questa sezione presuppongono l'uso di un Registro Azure Container per archiviare le immagini Docker. Usare l'elenco di controllo seguente quando si pianifica la creazione di immagini personalizzate per Azure Machine Learning:

* Si userà il Registro Azure Container creato per l'area Azure Machine Learning lavoro o un'area di lavoro Registro Azure Container?

    Quando si usano immagini archiviate nel registro __contenitori per l'area di lavoro__, non è necessario eseguire l'autenticazione nel registro. L'autenticazione viene gestita dall'area di lavoro.

    > [!WARNING]
    > Il Registro Azure Container per l'area di lavoro viene creato la prima volta che si esegue il training o __si distribuisce un modello usando__ l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non è stato creato un modello, non Registro Azure Container esistente per l'area di lavoro.

    Quando si usano immagini archiviate in __un registro contenitori autonomo,__ è necessario configurare un'entità servizio che abbia almeno l'accesso in lettura. L'ID dell'entità servizio (nome utente) e la password vengono quindi forniti a chiunque usi le immagini del registro. L'eccezione si verifica se si rende accessibile pubblicamente il registro contenitori.

    Per informazioni sulla creazione di un Registro Azure Container privato, vedere [Creare un registro contenitori privato](../container-registry/container-registry-get-started-azure-cli.md).

    Per informazioni sull'uso delle entità servizio con Registro Azure Container, vedere [Registro Azure Container'autenticazione con le entità servizio](../container-registry/container-registry-auth-service-principal.md).

* Registro Azure Container e sull'immagine: specificare il nome dell'immagine a tutti gli utenti che devono usarla. Ad esempio, a un'immagine denominata , archiviata in un registro denominato , viene fatto riferimento come quando si `myimage` `myregistry` usa `myregistry.azurecr.io/myimage` l'immagine per la distribuzione del modello

### <a name="image-requirements"></a>Requisiti dell'immagine

Azure Machine Learning supporta solo immagini Docker che forniscono il software seguente:
* Ubuntu 16.04 o versione successiva.
* Conda 4.5.# o versione successiva.
* Python 3.6+.

Per usare i set di dati, installare il pacchetto libfuse-dev. Assicurarsi anche di installare eventuali pacchetti di spazio utente necessari.

Azure ML gestisce un set di immagini di base di CPU e GPU pubblicate in Microsoft Container Registry che è possibile usare (o fare riferimento) invece di creare un'immagine personalizzata. Per visualizzare i Dockerfile per tali immagini, vedere il repository [GitHub azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

Per le immagini GPU, Azure ML offre attualmente sia immagini di base cuda9 che cuda10. Le principali dipendenze installate in queste immagini di base sono:

| Dependencies | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| Mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| Cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

Le immagini della CPU sono create da ubuntu16.04. Le immagini GPU per cuda9 sono create da nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04. Le immagini GPU per cuda10 sono create da nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04.
<a id="getname"></a>

> [!IMPORTANT]
> Quando si usano immagini Docker personalizzate, è consigliabile aggiungere le versioni dei pacchetti per garantire una migliore riproducibilità.

### <a name="get-container-registry-information"></a>Ottenere informazioni sul registro contenitori

In questa sezione viene illustrato come ottenere il nome dell'Registro Azure Container per l'area Azure Machine Learning lavoro.

> [!WARNING]
> La Registro Azure Container per l'area di lavoro viene creata la prima volta che si esegue il training o si __distribuisce un modello usando__ l'area di lavoro. Se è stata creata una nuova area di lavoro, ma non è stato creato un modello, non Registro Azure Container esistente per l'area di lavoro.

Se è già stato creato il training o la distribuzione di modelli usando Azure Machine Learning, è stato creato un registro contenitori per l'area di lavoro. Per trovare il nome di questo registro contenitori, seguire questa procedura:

1. Aprire una nuova shell o un prompt dei comandi e usare il comando seguente per eseguire l'autenticazione nella sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

    Seguire le istruzioni per l'autenticazione alla sottoscrizione.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Usare il comando seguente per elencare il registro contenitori per l'area di lavoro. Sostituire `<myworkspace>` con il nome dell Azure Machine Learning area di lavoro. Sostituire `<resourcegroup>` con il gruppo di risorse di Azure che contiene l'area di lavoro:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Le informazioni restituite sono simili al testo seguente:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Il `<registry_name>` valore è il nome dell'Registro Azure Container per l'area di lavoro.

### <a name="build-a-custom-base-image"></a>Creare un'immagine di base personalizzata

I passaggi descritti in questa sezione illustrano come creare un'immagine Docker personalizzata nel Registro Azure Container. Per dockerfile di esempio, vedere il repository [GitHub Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

1. Creare un nuovo file di testo `Dockerfile` denominato e usare il testo seguente come contenuto:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Da una shell o da un prompt dei comandi usare quanto segue per eseguire l'autenticazione al Registro Azure Container. Sostituire con `<registry_name>` il nome del registro contenitori in cui archiviare l'immagine:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Per caricare il Dockerfile e compilarlo, usare il comando seguente. Sostituire `<registry_name>` con il nome del registro contenitori in cui si vuole archiviare l'immagine:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In questo esempio viene applicato un tag di `:v1` all'immagine. Se non viene specificato alcun tag, viene applicato un tag `:latest` di .

    Durante il processo di compilazione, le informazioni vengono trasmesse alla riga di comando. Se la compilazione ha esito positivo, viene visualizzato un messaggio simile al testo seguente:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Per altre informazioni sulla compilazione di immagini con un Registro Azure Container, vedere Compilare ed [eseguire un'immagine del contenitore usando Registro Azure Container attività](../container-registry/container-registry-quickstart-task-cli.md)

Per altre informazioni sul caricamento di immagini esistenti in un Registro Azure Container, vedere Eseguire il push della prima immagine in [un registro contenitori Docker privato.](../container-registry/container-registry-get-started-docker-cli.md)

## <a name="use-a-custom-base-image"></a>Usare un'immagine di base personalizzata

Per usare un'immagine personalizzata, sono necessarie le informazioni seguenti:

* Nome __dell'immagine__. Ad esempio, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` è il percorso di una semplice immagine Docker fornita da Microsoft.

    > [!IMPORTANT]
    > Per le immagini personalizzate create, assicurarsi di includere tutti i tag usati con l'immagine. Ad esempio, se l'immagine è stata creata con un tag specifico, ad esempio `:v1` . Se non è stato utilizzato un tag specifico durante la creazione dell'immagine, è stato applicato un tag `:latest` .

* Se l'immagine si trova in __un repository privato,__ sono necessarie le informazioni seguenti:

    * Indirizzo del Registro __di sistema__. Ad esempio: `myregistry.azureecr.io`.
    * Nome utente e __password dell'entità__ __servizio__ con accesso in lettura al Registro di sistema.

    Se non si dispone di queste informazioni, contattare l'amministratore per l'Registro Azure Container che contiene l'immagine.

### <a name="publicly-available-base-images"></a>Immagini di base disponibili pubblicamente

Microsoft offre diverse immagini Docker in un repository accessibile pubblicamente, che possono essere usate con i passaggi descritti in questa sezione:

| Immagine | Descrizione |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Immagine principale per Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contiene il runtime ONNX per l'inferenza della CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contiene il runtime ONNX e CUDA per GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contiene il runtime ONNX e TensorRT per GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Contiene ONNX Runtime e OpenVINO per Intel <sup></sup> Vision Accelerator Design basati su VPU Movidius<sup>TM</sup> MyriadX |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contiene le chiavi USB ONNX Runtime e OpenVINO per Intel <sup></sup> Movidius<sup>TM</sup> |

Per altre informazioni sulle immagini di base del runtime ONNX, vedere la sezione [dockerfile del runtime ONNX](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) nel repository GitHub.

> [!TIP]
> Poiché queste immagini sono disponibili pubblicamente, non è necessario specificare un indirizzo, un nome utente o una password quando vengono utilizzati.

Per altre informazioni, vedere [Azure Machine Learning repository di contenitori](https://github.com/Azure/AzureML-Containers) in GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usare un'immagine con Azure Machine Learning SDK

Per usare un'immagine archiviata nel **Registro Azure Container** per l'area di lavoro o un registro contenitori accessibile pubblicamente, impostare gli attributi [Environment](/python/api/azureml-core/azureml.core.environment.environment) seguenti:

+ `docker.enabled=True`
+ `docker.base_image`: impostata sul Registro di sistema e sul percorso dell'immagine.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Per usare un'immagine da __un__ registro contenitori privato che non si trova nell'area di lavoro, è necessario usare per specificare l'indirizzo del repository e un nome utente `docker.base_image_registry` e una password:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

È necessario aggiungere azureml-defaults con versione >= 1.0.45 come dipendenza pip. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. È anche necessario impostare inferencing_stack_version nell'ambiente su "latest". Verranno installati pacchetti apt specifici necessari per il servizio Web. 

Dopo aver definito l'ambiente, usarlo con un [oggetto InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) per definire l'ambiente di inferenza in cui verranno eseguiti il modello e il servizio Web.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

A questo punto, è possibile continuare con la distribuzione. Ad esempio, il frammento di codice seguente distribuirebbe un servizio Web in locale usando la configurazione di inferenza e l'immagine personalizzata:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per altre informazioni sulla distribuzione, vedere [Distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usare un'immagine con l'interfaccia della riga Machine Learning comando

> [!IMPORTANT]
> Attualmente l'Machine Learning dell'interfaccia della riga di comando può usare immagini Registro Azure Container per l'area di lavoro o i repository accessibili pubblicamente. Non può usare immagini da registri privati autonomi.

Prima di distribuire un modello usando l'interfaccia della Machine Learning comando, creare un [ambiente](/python/api/azureml-core/azureml.core.environment.environment) che usa l'immagine personalizzata. Creare quindi un file di configurazione dell'inferenza che fa riferimento all'ambiente. È anche possibile definire l'ambiente direttamente nel file di configurazione dell'inferenza. Il documento JSON seguente illustra come fare riferimento a un'immagine in un registro contenitori pubblico. In questo esempio l'ambiente è definito inline:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Questo file viene usato con il `az ml model deploy` comando . Il `--ic` parametro viene usato per specificare il file di configurazione dell'inferenza.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Per altre informazioni sulla distribuzione di un modello tramite l'interfaccia della riga di comando di Machine Learning, vedere la sezione "Registrazione, profilatura e distribuzione del modello" dell'estensione dell'interfaccia della riga di comando [Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) articolo.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Dove distribuire e su come](how-to-deploy-and-where.md).
* Informazioni su come eseguire [il training e distribuire modelli di Machine Learning usando Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning).