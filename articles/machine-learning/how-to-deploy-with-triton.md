---
title: Modello a prestazioni elevate con Triton (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire il modello con il server di inferenza NVIDIA Triton in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3d2e01b645c1661d4b44520193b9c4557cbc1ea0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652175"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Servizio a prestazioni elevate con il server di inferenza Triton (anteprima) 

Informazioni su come utilizzare il [server di inferenza NVIDIA Triton](https://aka.ms/nvidia-triton-docs) per migliorare le prestazioni del servizio Web utilizzato per l'inferenza del modello.

Uno dei modi per distribuire un modello per l'inferenza è come un servizio Web. Ad esempio, una distribuzione in Azure Kubernetes Service o istanze di contenitore di Azure. Per impostazione predefinita, Azure Machine Learning usa un framework Web a thread singolo per *le* distribuzioni di servizi Web.

Triton è un Framework *ottimizzato per l'inferenza*. Offre un utilizzo migliore delle GPU e un'inferenza più economica. Sul lato server, il batch invia le richieste in ingresso e invia questi batch per l'inferenza. La suddivisione in batch USA meglio le risorse GPU ed è una parte essenziale delle prestazioni di Triton.

> [!IMPORTANT]
> L'uso di Triton per la distribuzione da Azure Machine Learning è attualmente in fase di __Anteprima__. Le funzionalità di anteprima potrebbero non essere coperte dal supporto tecnico. Per ulteriori informazioni, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> I frammenti di codice in questo documento sono a scopo illustrativo e potrebbero non mostrare una soluzione completa. Per il codice di esempio funzionante, vedere gli [esempi end-to-end di Triton in Azure Machine Learning](https://aka.ms/triton-aml-sample).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non se ne possiede una, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* Familiarità con le [modalità di distribuzione di un modello](how-to-deploy-and-where.md) con Azure Machine Learning.
* [SDK Azure Machine Learning per Python](/python/api/overview/azure/ml/?view=azure-ml-py) **o** l'interfaccia della riga di comando di [Azure](/cli/azure/?view=azure-cli-latest) e l' [estensione Machine Learning](reference-azure-machine-learning-cli.md).
* Installazione funzionante di Docker per i test locali. Per informazioni sull'installazione e la convalida di Docker, vedere [orientamento e configurazione](https://docs.docker.com/get-started/) nella documentazione di Docker.

## <a name="architectural-overview"></a>Panoramica dell'architettura

Prima di provare a usare Triton per un modello personalizzato, è importante comprenderne il funzionamento con Azure Machine Learning e il modo in cui viene confrontato con una distribuzione predefinita.

**Distribuzione predefinita senza Triton**

* Sono stati avviati più [Gunicorn](https://gunicorn.org/) Worker per gestire simultaneamente le richieste in ingresso.
* Questi thread di lavoro gestiscono la pre-elaborazione, la chiamata al modello e la post-elaborazione. 
* I client usano l'URI di assegnazione dei __punteggi di Azure ml__. Ad esempio: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagramma dell'architettura di distribuzione normale, non Triton":::

**Distribuzione diretta con Triton**

* Le richieste vengono indirizzate direttamente al server Triton.
* Triton elabora le richieste in batch per ottimizzare l'utilizzo della GPU.
* Il client usa l' __URI Triton__ per eseguire le richieste. Ad esempio: `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Distribuzione Inferenceconfig solo con Triton e nessun middleware Python":::

**Distribuzione della configurazione dell'inferenza con Triton**

* Sono stati avviati più [Gunicorn](https://gunicorn.org/) Worker per gestire simultaneamente le richieste in ingresso.
* Le richieste vengono inviate al **Server Triton**. 
* Triton elabora le richieste in batch per ottimizzare l'utilizzo della GPU.
* Il client usa l'URI di assegnazione dei __punteggi di Azure ml__ per eseguire le richieste. Ad esempio: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Distribuzione con il middleware Triton e Python":::

Il flusso di lavoro per usare Triton per la distribuzione del modello è il seguente:

1. Fornire direttamente il modello con Triton.
1. Verificare che sia possibile inviare richieste al modello distribuito da Triton.
1. Opzionale Creare un livello di middleware Python per la pre-elaborazione e post-elaborazione sul lato server

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Deploying Triton senza Python pre-e post-elaborazione

Per prima cosa, attenersi alla procedura seguente per verificare che il server di inferenza Triton possa gestire il modello.

### <a name="optional-define-a-model-config-file"></a>Opzionale Definire un file di configurazione del modello

Il file di configurazione del modello indica a Triton il numero di input da prevedere e le dimensioni di tali input. Per ulteriori informazioni sulla creazione del file di configurazione, vedere [configurazione del modello](https://aka.ms/nvidia-triton-docs) nella documentazione di NVIDIA.

> [!TIP]
> Si usa l' `--strict-model-config=false` opzione all'avvio del server di inferenza di Triton, che significa che non è necessario specificare un `config.pbtxt` file per i modelli ONNX o TensorFlow.
> 
> Per ulteriori informazioni su questa opzione, vedere la pagina relativa alla [configurazione del modello generata](https://aka.ms/nvidia-triton-docs) nella documentazione di NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Usa la struttura di directory corretta

Quando si registra un modello con Azure Machine Learning, è possibile registrare singoli file o una struttura di directory. Per usare Triton, la registrazione del modello deve essere relativa a una struttura di directory contenente una directory denominata `triton` . La struttura generale di questa directory è la seguente:

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
> Questa struttura di directory è un repository di modelli Triton ed è necessaria per il funzionamento dei modelli con Triton. Per ulteriori informazioni, vedere la pagina relativa ai [repository di modelli Triton](https://aka.ms/nvidia-triton-docs) nella documentazione di NVIDIA.

### <a name="register-your-triton-model"></a>Registrare il modello Triton

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Per ulteriori informazioni su `az ml model register` , consultare la [documentazione di riferimento](/cli/azure/ext/azure-cli-ml/ml/model).

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
Per ulteriori informazioni, vedere la documentazione relativa alla [classe del modello](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py).

---

### <a name="deploy-your-model"></a>Distribuire il modello

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Se si dispone di un cluster di servizi Azure Kubernetes abilitato per GPU denominato "AKS-GPU" creato tramite Azure Machine Learning, è possibile usare il comando seguente per distribuire il modello.

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

[Per ulteriori informazioni sulla distribuzione di modelli, vedere questa documentazione](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Chiamare il modello distribuito

Per prima cosa, ottenere l'URI di assegnazione dei punteggi e i token di porta.

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

Assicurarsi quindi che il servizio sia in esecuzione eseguendo le operazioni seguenti: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Questo comando restituisce informazioni simili alle seguenti. Si noti `200 OK` che questo stato indica che il server Web è in esecuzione.

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

Dopo aver eseguito un controllo di integrità, è possibile creare un client per inviare i dati a Triton per l'inferenza. Per ulteriori informazioni sulla creazione di un client, vedere gli [esempi client](https://aka.ms/nvidia-client-examples) nella documentazione di NVIDIA. Sono disponibili anche [Esempi Python in GitHub di Triton](https://aka.ms/nvidia-triton-docs).

A questo punto, se non si vuole aggiungere la pre-elaborazione e la post-elaborazione di Python al servizio Web distribuito, è possibile che l'operazione sia stata completata. Se si vuole aggiungere la logica di pre-elaborazione e post-elaborazione, vedere.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Opzionale Eseguire di nuovo la distribuzione con uno script di immissione Python per la pre-e post-elaborazione

Dopo aver verificato che Triton è in grado di gestire il modello, è possibile aggiungere il codice di pre e post-elaborazione definendo uno _script di immissione_. Questo file è denominato `score.py` . Per ulteriori informazioni sugli script di immissione, vedere [definire uno script di immissione](how-to-deploy-and-where.md#define-an-entry-script).

I due passaggi principali sono l'inizializzazione di un client HTTP Triton nel `init()` metodo e la chiamata al client nella `run()` funzione.

### <a name="initialize-the-triton-client"></a>Inizializzare il client Triton

Includere il codice come l'esempio seguente nel `score.py` file. Triton in Azure Machine Learning prevede di essere risolto su localhost, porta 8000. In questo caso, localhost si trova all'interno dell'immagine Docker per questa distribuzione, non a una porta nel computer locale:

> [!TIP]
> Il `tritonhttpclient` pacchetto pip è incluso nell' `AzureML-Triton` ambiente curato, quindi non è necessario specificarlo come dipendenza PIP.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modificare lo script di assegnazione dei punteggi per effettuare una chiamata a Triton

Nell'esempio seguente viene illustrato come richiedere dinamicamente i metadati per il modello:

> [!TIP]
> È possibile richiedere dinamicamente i metadati dei modelli che sono stati caricati con Triton usando il `.get_model_metadata` metodo del client Triton. Per un esempio di utilizzo, vedere il [notebook di esempio](https://aka.ms/triton-aml-sample) .

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

Una configurazione di inferenza consente di usare uno script di immissione, nonché il processo di distribuzione Azure Machine Learning usando Python SDK o l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> È necessario specificare l' `AzureML-Triton` [ambiente curato](./resource-curated-environments.md).
>
> L'esempio di codice Python viene clonato `AzureML-Triton` in un altro ambiente denominato `My-Triton` . Il codice dell'interfaccia della riga di comando di Azure usa anche questo ambiente. Per ulteriori informazioni sulla clonazione di un ambiente, vedere la Guida di riferimento a [Environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) .

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

> [!TIP]
> Per ulteriori informazioni sulla creazione di una configurazione di inferenza, vedere lo [schema di configurazione dell'inferenza](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

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

Al termine della distribuzione, viene visualizzato l'URI di assegnazione dei punteggi. Per questa distribuzione locale, sarà `http://localhost:6789/score` . Se si esegue la distribuzione nel cloud, è possibile usare il comando [AZ ml Service Show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI per ottenere l'URI di assegnazione dei punteggi.

Per informazioni su come creare un client che invii le richieste di inferenza all'URI di assegnazione dei punteggi, vedere [utilizzare un modello distribuito come servizio Web](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Impostazione del numero di ruoli di lavoro

Per impostare il numero di ruoli di lavoro nella distribuzione, impostare la variabile di ambiente `WORKER_COUNT` . Poiché è presente un oggetto [Environment](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) denominato `env` , è possibile eseguire le operazioni seguenti:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

In questo modo si dirà ad Azure ML di creare il numero di ruoli di lavoro specificati.


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a utilizzare l'area di lavoro Azure Machine Learning, ma si desidera eliminare il servizio distribuito, utilizzare una delle seguenti opzioni:


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Passaggi successivi

* [Vedere gli esempi end-to-end di Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Vedere [esempi di client Triton](https://aka.ms/nvidia-client-examples)
* Leggere la [documentazione del server di inferenza Triton](https://aka.ms/nvidia-triton-docs)
* [Risolvere una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)