---
title: Come distribuire modelli di Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come e dove distribuire modelli di Machine Learning. Distribuire in Istanze di Azure Container, servizio Azure Kubernetes, Azure IoT Edge e FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2
adobe-target: true
ms.openlocfilehash: f2128949090ce0ec2aa4ed66eb476384d662953a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872642"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Distribuire modelli di Machine Learning in Azure

Informazioni su come distribuire il modello di Machine Learning o Deep Learning come servizio Web nel cloud di Azure. È anche possibile eseguire la distribuzione Azure IoT Edge dispositivi.

Il flusso di lavoro è simile indipendentemente dal punto in cui si distribuisce il modello:

1. Registrare il modello (facoltativo, vedere di seguito).
1. Preparare una configurazione di inferenza (a meno che non si utilizzi [la distribuzione senza codice).](./how-to-deploy-no-code-deployment.md)
1. Preparare uno script di immissione (a meno che non si utilizzi [la distribuzione senza codice](./how-to-deploy-no-code-deployment.md)).
1. Scegliere una destinazione di calcolo.
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il servizio Web risultante.

Per altre informazioni sui concetti relativi al flusso di lavoro di distribuzione di Machine Learning, vedere [Gestire,](concept-model-management-and-deployment.md)distribuire e monitorare i modelli con Azure Machine Learning .

## <a name="prerequisites"></a>Prerequisiti

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)
- Un modello. Se non si ha un modello con training, è possibile usare il modello e i file di dipendenza forniti in [questa esercitazione.](https://aka.ms/azml-deploy-cloud)
- Estensione [dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando di Azure per il Machine Learning servizio .

# <a name="python"></a>[Python](#tab/python)

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)
- Un modello. Se non si ha un modello con training, è possibile usare il modello e i file di dipendenza forniti in [questa esercitazione.](https://aka.ms/azml-deploy-cloud)
- Il [Azure Machine Learning Software Development Kit (SDK) per Python.](/python/api/overview/azure/ml/intro)

---

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Seguire le istruzioni nella documentazione dell'interfaccia della riga di comando di Azure per [impostare il contesto della sottoscrizione.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

Eseguire quindi:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

per visualizzare le aree di lavoro a cui si ha accesso.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Per altre informazioni sull'uso dell'SDK per connettersi a un'area di lavoro, vedere la [documentazione Azure Machine Learning SDK per Python.](/python/api/overview/azure/ml/intro#workspace)


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registrare il modello (facoltativo)

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Ad esempio, se si dispone di un modello archiviato in più file, è possibile registrarli come un singolo modello nell'area di lavoro. Dopo aver registrato i file, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP] 
> La registrazione di un modello per il rilevamento delle versioni è consigliata ma non obbligatoria. Se si preferisce procedere senza registrare un modello, è necessario specificare una directory di origine in [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) o [inferenceconfig.jsin](./reference-azure-machine-learning-cli.md#inference-configuration-schema) e assicurarsi che il modello si trovi all'interno di tale directory di origine.

> [!TIP]
> Quando si registra un modello, si specifica il percorso di una posizione cloud (da un'esecuzione di training) o di una directory locale. Questo percorso consente solo di individuare i file da caricare come parte del processo di registrazione. Non è necessario che corrisponda al percorso usato nello script di immissione. Per altre informazioni, vedere [Individuare i file di modello nello script di immissione.](./how-to-deploy-advanced-entry-script.md#load-registered-models)

> [!IMPORTANT]
> Quando si usa l'opzione Filtra per nella pagina Modelli di Azure Machine Learning Studio, invece di usare i clienti è consigliabile `Tags` `TagName : TagValue` usare `TagName=TagValue` (senza spazio)

Negli esempi seguenti viene illustrato come registrare un modello.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrare un modello da un'esecuzione di training di Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

Il `--asset-path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, `--asset-path` impostare sul percorso di una cartella che contiene i file.

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Per includere più file nella registrazione del modello, `-p` impostare sul percorso di una cartella che contiene i file.

Per altre informazioni su `az ml model register` , vedere la documentazione di [riferimento](/cli/azure/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrare un modello da un'esecuzione di training di Azure ML

  Quando si usa l'SDK per eseguire il training di un modello, è possibile ricevere un oggetto [Run](/python/api/azureml-core/azureml.core.run.run) o [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) a seconda di come è stato eseguito il training del modello. Ogni oggetto può essere usato per registrare un modello creato da un'esecuzione dell'esperimento.

  + Registrare un modello da un `azureml.core.Run` oggetto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Il `model_path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, `model_path` impostare sul percorso di una cartella che contiene i file. Per altre informazioni, vedere la documentazione [Run.register_model.](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registrare un modello da un `azureml.train.automl.run.AutoMLRun` oggetto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In questo esempio i parametri e non vengono specificati, quindi verrà registrata l'iterazione con la `metric` `iteration` metrica primaria migliore. Il `model_id` valore restituito dall'esecuzione viene usato invece di un nome di modello.

    Per altre informazioni, vedere la documentazione [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

    Per distribuire un modello registrato da un , è consigliabile farlo tramite il pulsante distribuisci con un clic `AutoMLRun` in Azure Machine Learning [Studio.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

È possibile registrare un modello specificando il percorso locale del modello. È possibile specificare il percorso di una cartella o di un singolo file. È possibile usare questo metodo per registrare i modelli con training con Azure Machine Learning e quindi scaricati. È anche possibile usare questo metodo per registrare i modelli di cui è stato Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Uso di SDK e ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Per includere più file nella registrazione del modello, `model_path` impostare sul percorso di una cartella che contiene i file.

Per altre informazioni, vedere la documentazione relativa alla [classe Model.](/python/api/azureml-core/azureml.core.model.model)

Per altre informazioni sull'uso di modelli di cui è stato Azure Machine Learning, vedere [Come distribuire un modello esistente.](how-to-deploy-existing-model.md)

---

## <a name="define-an-entry-script"></a>Definire uno script di immissione

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definire una configurazione di inferenza


Una configurazione di inferenza descrive come configurare il servizio Web contenente il modello. Viene usato in un secondo momento, quando si distribuisce il modello.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Una configurazione di inferenza minima può essere scritta come:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir",
    "environment": {
    "docker": {
        "arguments": [],
        "baseDockerfile": null,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
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
                "conda-forge",
                "pytorch"
            ],
            "dependencies": [
                "python=3.6.2",
                "torchvision"
                {
                    "pip": [
                        "azureml-defaults",
                        "azureml-telemetry",
                        "scikit-learn==0.22.1",
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
```

Questo specifica che la distribuzione di Machine Learning userà il file nella directory per elaborare le richieste in ingresso e che userà l'immagine Docker con i pacchetti Python specificati `score.py` `./working_dir` `project_environment` nell'ambiente.

[Per una descrizione](./reference-azure-machine-learning-cli.md#inference-configuration-schema) più approfondita delle configurazioni di inferenza, vedere questo articolo. 

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente illustra:

1. caricamento di un [ambiente curato dall'area](resource-curated-environments.md) di lavoro
1. Clonazione dell'ambiente
1. Specifica di `scikit-learn` come dipendenza.
1. Uso dell'ambiente per creare un inferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Per altre informazioni sugli ambienti, vedere [Creare e gestire ambienti per il training e la distribuzione.](how-to-use-environments.md)

Per altre informazioni sulla configurazione dell'inferenza, vedere la documentazione relativa alla [classe InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)

---

> [!TIP] 
> Per informazioni sull'uso di un'immagine Docker personalizzata con una configurazione di inferenza, vedere Come distribuire un modello [usando un'immagine Docker personalizzata.](how-to-deploy-custom-docker-image.md)

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definire una configurazione di distribuzione

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Le opzioni disponibili per una configurazione di distribuzione variano a seconda della destinazione di calcolo scelta.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Per altre informazioni, vedere [questo riferimento.](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)

# <a name="python"></a>[Python](#tab/python)

Prima di distribuire il modello, è necessario definire la configurazione della distribuzione. *La configurazione della distribuzione è specifica della destinazione di calcolo che ospiterà il servizio Web.* Ad esempio, quando si distribuisce un modello in locale, è necessario specificare la porta in cui il servizio accetta le richieste. La configurazione della distribuzione non fa parte dello script di immissione. Viene usato per definire le caratteristiche della destinazione di calcolo che ospiterà il modello e lo script di ingresso.

Potrebbe anche essere necessario creare la risorsa di calcolo, se, ad esempio, all'area di lavoro non è già associata un'istanza di servizio Azure Kubernetes (AKS).

La tabella seguente fornisce un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:

| Destinazione del calcolo | Esempio di configurazione della distribuzione |
| ----- | ----- |
| Locale | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanze di Azure Container | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Le classi per i servizi Web locali, Istanze di Azure Container e del servizio Web del servizio Web AKS possono essere importate da `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Distribuire il modello di Machine Learning

A questo punto è possibile distribuire il modello. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Uso di un modello registrato

Se il modello è stato registrato nell'area Azure Machine Learning, sostituire "mymodel:1" con il nome del modello e il relativo numero di versione.

```azurecli-interactive
az ml model deploy -n tutorial -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Uso di un modello locale

Se si preferisce non registrare il modello, è possibile passare il parametro "sourceDirectory" nel inferenceconfig.jsper specificare una directory locale da cui gestire il modello.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente illustra una distribuzione locale. La sintassi varia a seconda della destinazione di calcolo scelta nel passaggio precedente.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per altre informazioni, vedere la documentazione [per LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Informazioni sullo stato del servizio

Durante la distribuzione del modello, è possibile che lo stato del servizio cambi durante la distribuzione completa.

Nella tabella seguente vengono descritti i diversi stati del servizio:

| Stato del servizio Web | Descrizione | Stato finale?
| ----- | ----- | ----- |
| Transizione | Il servizio è in fase di distribuzione. | No |
| Unhealthy | Il servizio è stato distribuito ma non è attualmente raggiungibile.  | No |
| Non configurabile | Il servizio non può essere distribuito in questo momento a causa della mancanza di risorse. | No |
| Non riuscito | La distribuzione del servizio non è riuscita a causa di un errore o di un arresto anomalo. | Sì |
| Healthy | Il servizio è integro e l'endpoint è disponibile. | Sì |

> [!TIP]
> Durante la distribuzione, le immagini Docker per le destinazioni di calcolo vengono compilate e caricate da Registro Azure Container (ACR). Per impostazione predefinita, Azure Machine Learning crea un ACR che usa il livello *di servizio* Basic. La modifica di ACR per l'area di lavoro al livello Standard o Premium può ridurre il tempo necessario per compilare e distribuire immagini nelle destinazioni di calcolo. Per altre informazioni, vedere [Livelli di servizio di Registro Azure Container](../container-registry/container-registry-skus.md).

> [!NOTE]
> Se si distribuisce un modello nel servizio Servizio Azure Kubernetes, è [](../azure-monitor/containers/container-insights-enable-existing-clusters.md) Monitoraggio di Azure per il cluster. Ciò consente di comprendere l'integrità complessiva del cluster e l'utilizzo delle risorse. Potrebbero essere utili anche le risorse seguenti:
>
> * [Verificare la presenza Integrità risorse eventi che influiscono sul cluster del servizio Web Disassoce](../aks/aks-resource-health.md)
> * [servizio Azure Kubernetes diagnostica](../aks/concepts-diagnostics.md)
>
> Se si sta tentando di distribuire un modello in un cluster non integro o sovraccarico, si prevede che si verifichino problemi. Per assistenza per la risoluzione dei problemi del cluster del servizio Web Disatteso, contattare il supporto del servizio AKS.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Inferenza batch
Azure Machine Learning le destinazioni di calcolo vengono create e gestite da Azure Machine Learning. Possono essere usati per la stima batch da Azure Machine Learning pipeline.

Per una procedura dettagliata sull'inferenza batch con Azure Machine Learning calcolo, vedere [Come eseguire stime batch.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge inferenza
Il supporto per la distribuzione nel perimetro è in anteprima. Per altre informazioni, vedere [Deploy Azure Machine Learning as an IoT Edge module](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Eliminare le risorse

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Per eliminare un servizio Web distribuito, usare `az ml service <name of webservice>` .

Per eliminare un modello registrato dall'area di lavoro, usare `az ml model delete <model id>`

Altre informazioni [sull'eliminazione di un servizio Web](/cli/azure/ml/service#az_ml_service_delete) e [sull'eliminazione di un modello](/cli/azure/ml/model#az_ml_model_delete).

# <a name="python"></a>[Python](#tab/python)

Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per altre informazioni, vedere la documentazione [per WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) e [Model.delete().](/python/api/azureml-core/azureml.core.model.model#delete--)

---

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creare applicazioni client per l'utilizzo dei servizi Web](how-to-consume-web-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Distribuzione con un clic per l'esecuzione automatica di Machine Learning nel studio di Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger di eventi per le distribuzioni di modelli](how-to-use-event-grid.md)