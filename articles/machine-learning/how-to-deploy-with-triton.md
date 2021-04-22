---
title: Modello ad alte prestazioni che viene servito con Triton (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire il modello con NVIDIA Triton Inference Server in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 9b7b8fe9c05d0de64dcd0cf7c6c324e0d03cb1ac
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874154"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Prestazioni elevate con Triton Inference Server (anteprima) 

Informazioni su come usare [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) per migliorare le prestazioni del servizio Web usato per l'inferenza del modello.

Uno dei modi per distribuire un modello per l'inferenza è come servizio Web. Ad esempio, una distribuzione in servizio Azure Kubernetes o Istanze di Azure Container. Per impostazione predefinita, Azure Machine Learning un framework Web  di utilizzo generico a thread singolo per le distribuzioni di servizi Web.

Triton è un framework ottimizzato *per l'inferenza.* Offre un migliore utilizzo delle GPU e un'inferenza più conveniente. Sul lato server, invia in batch le richieste in ingresso e invia questi batch per l'inferenza. L'invio in batch usa meglio le risorse GPU ed è una parte fondamentale delle prestazioni di Triton.

> [!IMPORTANT]
> L'uso di Triton per la Azure Machine Learning è attualmente in __anteprima.__ La funzionalità di anteprima potrebbe non essere coperta dal supporto tecnico. Per altre informazioni, vedere Le condizioni per l'utilizzo supplementari [per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> I frammenti di codice in questo documento sono a scopo illustrativo e potrebbero non mostrare una soluzione completa. Per codice di esempio funzionante, [vedere gli esempi end-to-end](https://aka.ms/triton-aml-sample)di Triton in Azure Machine Learning .

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) è un software open source di terze parti integrato in Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non se ne possiede una, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* Familiarità con [come e dove distribuire un modello con](how-to-deploy-and-where.md) Azure Machine Learning.
* [L Azure Machine Learning SDK per Python o l'interfaccia](/python/api/overview/azure/ml/) della **riga** di comando [di Azure](/cli/azure/) e [l'estensione di Machine Learning](reference-azure-machine-learning-cli.md).
* Installazione funzionante di Docker per i test locali. Per informazioni sull'installazione e la convalida di Docker, vedere [Orientamento e configurazione](https://docs.docker.com/get-started/) nella documentazione di Docker.

## <a name="architectural-overview"></a>Panoramica dell'architettura

Prima di provare a usare Triton per il proprio modello, è importante comprendere come funziona con Azure Machine Learning e come viene confrontato con una distribuzione predefinita.

**Distribuzione predefinita senza Triton**

* Vengono avviati più thread di lavoro [Gunicorn](https://gunicorn.org/) per gestire contemporaneamente le richieste in ingresso.
* Questi thread di lavoro gestiscono la pre-elaborazione, la chiamata del modello e la post-elaborazione. 
* I client usano __l'URI di assegnazione dei punteggi di Azure ML.__ Ad esempio: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagramma dell'architettura di distribuzione normale, non tritone":::

**Distribuzione diretta con Triton**

* Le richieste vengono inviate direttamente al server Triton.
* Triton elabora le richieste in batch per ottimizzare l'utilizzo della GPU.
* Il client usa __l'URI Triton__ per effettuare richieste. Ad esempio: `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Distribuzione di Inferenceconfig solo con Triton e nessun middleware Python":::

**Distribuzione della configurazione dell'inferenza con Triton**

* Vengono avviati più thread di lavoro [Gunicorn](https://gunicorn.org/) per gestire contemporaneamente le richieste in ingresso.
* Le richieste vengono inoltrate al **server Triton**. 
* Triton elabora le richieste in batch per ottimizzare l'utilizzo della GPU.
* Il client usa __l'URI di assegnazione dei punteggi di Azure ML__ per effettuare richieste. Ad esempio: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Distribuzione con middleware Triton e Python":::

Il flusso di lavoro per usare Triton per la distribuzione del modello è:

1. Gestire direttamente il modello con Triton.
1. Verificare che sia possibile inviare richieste al modello distribuito da Triton.
1. (Facoltativo) Creare un livello di middleware Python per la pre-elaborazione e la post-elaborazione sul lato server

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Distribuzione di Triton senza pre-elaborazione e post-elaborazione di Python

Per prima cosa, seguire la procedura seguente per verificare che triton Inference Server sia in grado di gestire il modello.

### <a name="optional-define-a-model-config-file"></a>(Facoltativo) Definire un file di configurazione del modello

Il file di configurazione del modello indica a Triton il numero di input previsti e le dimensioni di tali input. Per altre informazioni sulla creazione del file di configurazione, vedere [Configurazione del modello](https://aka.ms/nvidia-triton-docs) nella documentazione di NVIDIA.

> [!TIP]
> L'opzione viene utilizzata quando si avvia triton Inference Server, pertanto non è necessario specificare un file per i modelli `--strict-model-config=false` `config.pbtxt` ONNX o TensorFlow.
> 
> Per altre informazioni su questa opzione, vedere [Configurazione del modello generata](https://aka.ms/nvidia-triton-docs) nella documentazione di NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Usare la struttura di directory corretta

Quando si registra un modello con Azure Machine Learning, è possibile registrare singoli file o una struttura di directory. Per usare Triton, la registrazione del modello deve essere per una struttura di directory contenente una directory denominata `triton` . La struttura generale di questa directory è:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Questa struttura di directory è un repository di modelli Triton ed è necessaria per il funzionamento dei modelli con Triton. Per altre informazioni, vedere [Triton Model Repositories](https://aka.ms/nvidia-triton-docs) nella documentazione di NVIDIA.

### <a name="register-your-triton-model"></a>Registrare il modello Triton

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Per altre informazioni su `az ml model register` , vedere la documentazione di [riferimento.](/cli/azure/ml/model)

Quando si registra il modello in Azure Machine Learning, il valore del parametro deve essere il nome della `--model-path  -p` cartella padre di Triton.  
Nell'esempio precedente  `--model-path` è "models".

Il valore per `--name  -n` il parametro, â€ ̃ my_triton_modelâ€™ nell'esempio, sarà il nome del modello noto per area di lavoro di Azure Machine Learning. 

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Per altre informazioni, vedere la documentazione relativa alla [classe Model.](/python/api/azureml-core/azureml.core.model.model)

---

### <a name="deploy-your-model"></a>Distribuire il modello

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Se si dispone di un cluster servizio Azure Kubernetes abilitato per GPU denominato "aks-gpu" creato tramite Azure Machine Learning, è possibile usare il comando seguente per distribuire il modello.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

Per [altre informazioni sulla distribuzione dei modelli, vedere questa documentazione.](how-to-deploy-and-where.md)

### <a name="call-into-your-deployed-model"></a>Chiamare il modello distribuito

Ottenere prima di tutto l'URI di assegnazione dei punteggi e i token di connessione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Assicurarsi quindi che il servizio sia in esecuzione eseguendo le seguenti attività: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Questo comando restituisce informazioni simili alle seguenti. Si noti `200 OK` . Questo stato indica che il server Web è in esecuzione.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Dopo aver eseguito un controllo di integrità, è possibile creare un client per inviare dati a Triton per l'inferenza. Per altre informazioni sulla creazione di un client, vedere gli esempi [di client](https://aka.ms/nvidia-client-examples) nella documentazione di NVIDIA. Sono disponibili anche [esempi python in Triton GitHub.](https://aka.ms/nvidia-triton-docs)

A questo punto, se non si vuole aggiungere la pre-elaborazione e la post-elaborazione python al servizio Web distribuito, è possibile che l'operazione sia stata completata. Se si vuole aggiungere questa logica di pre-elaborazione e post-elaborazione, leggere.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Facoltativo) Ridistribuire con uno script di immissione Python per la pre-elaborazione e la post-elaborazione

Dopo aver verificato che Triton sia in grado di gestire il modello, è possibile aggiungere codice di pre-elaborazione e post-elaborazione definendo uno _script di immissione_. Questo file è denominato `score.py` . Per altre informazioni sugli script di immissione, vedere [Definire uno script di immissione.](how-to-deploy-and-where.md#define-an-entry-script)

I due passaggi principali sono l'inizializzazione di un client HTTP Triton nel metodo e la chiamata a `init()` tale client nella `run()` funzione.

### <a name="initialize-the-triton-client"></a>Inizializzare il client Triton

Includere nel file codice simile all'esempio `score.py` seguente. Triton in Azure Machine Learning deve essere indirizzato su localhost, porta 8000. In questo caso, localhost si trova all'interno dell'immagine Docker per questa distribuzione, non una porta nel computer locale:

> [!TIP]
> Il `tritonhttpclient` pacchetto pip è incluso nell'ambiente curato, quindi non è necessario `AzureML-Triton` specificarlo come dipendenza pip.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modificare lo script di assegnazione dei punteggi per chiamare Triton

L'esempio seguente illustra come richiedere dinamicamente i metadati per il modello:

> [!TIP]
> È possibile richiedere dinamicamente i metadati dei modelli caricati con Triton usando il `.get_model_metadata` metodo del client Triton. Per un [esempio dell'uso,](https://aka.ms/triton-aml-sample) vedere il notebook di esempio.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Ridistribuire con una configurazione di inferenza

Una configurazione di inferenza consente di usare uno script di immissione, nonché il processo Azure Machine Learning di distribuzione usando Python SDK o l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> È necessario specificare `AzureML-Triton` [l'ambiente gestito.](./resource-curated-environments.md)
>
> L'esempio di codice Python clona `AzureML-Triton` in un altro ambiente denominato `My-Triton` . Anche il codice dell'interfaccia della riga di comando di Azure usa questo ambiente. Per altre informazioni sulla clonazione di un ambiente, vedere le informazioni di riferimento [su Environment.Clone().](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

> [!TIP]
> Per altre informazioni sulla creazione di una configurazione di inferenza, vedere lo schema di [configurazione dell'inferenza](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Al termine della distribuzione, viene visualizzato l'URI di assegnazione dei punteggi. Per questa distribuzione locale, sarà `http://localhost:6789/score` . Se si esegue la distribuzione nel cloud, è possibile usare il [comando az ml service show](/cli/azure/ml/service#az_ml_service_show) CLI per ottenere l'URI di assegnazione dei punteggi.

Per informazioni su come creare un client che invia richieste di inferenza all'URI di assegnazione dei punteggi, vedere Utilizzare un modello [distribuito come servizio Web.](how-to-consume-web-service.md)

### <a name="setting-the-number-of-workers"></a>Impostazione del numero di worker

Per impostare il numero di worker nella distribuzione, impostare la variabile di ambiente `WORKER_COUNT` . Dato che si dispone [di un](/python/api/azureml-core/azureml.core.environment.environment) oggetto Environment denominato , è possibile eseguire le `env` operazioni seguenti:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

In questo modo Azure ML verrà in grado di creare un numero elevato di computer di lavoro specificati.


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare l'area di lavoro Azure Machine Learning, ma si vuole eliminare il servizio distribuito, usare una delle opzioni seguenti:


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Risolvere problemi

* [Risolvere i problemi relativi a una](how-to-troubleshoot-deployment.md)distribuzione non riuscita, informazioni su come risolvere o risolvere gli errori comuni che possono verificarsi durante la distribuzione di un modello.

* Se i log di distribuzione indicano che **TritonServer** non è stato avviato, vedere la documentazione di [Nvidiaâ€™ open source.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Passaggi successivi

* [Vedere esempi end-to-end di Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Vedere gli [esempi di client Triton](https://aka.ms/nvidia-client-examples)
* Leggere la [documentazione di Triton Inference Server](https://aka.ms/nvidia-triton-docs)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
