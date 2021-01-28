---
title: Risoluzione dei problemi di distribuzione del modello locale
titleSuffix: Azure Machine Learning
description: Provare una distribuzione del modello locale come primo passaggio per la risoluzione degli errori di distribuzione del modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 225cb8d7bc35491ceefe444664afe175c7f7ea01
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954936"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Risoluzione dei problemi con una distribuzione del modello locale

Provare una distribuzione del modello locale come primo passaggio per la risoluzione dei problemi di distribuzione in istanze di contenitore di Azure (ACI) o in Azure Kubernetes Service (AKS).  L'uso di un servizio Web locale rende più semplice individuare e correggere gli errori comuni di distribuzione del servizio Web Docker Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* Opzione A (scelta **consigliata**)-debug locale in Azure Machine Learning istanza di calcolo
   * Area di lavoro di Azure Machine Learning con un' [istanza di calcolo](how-to-deploy-local-container-notebook-vm.md) in esecuzione
* Opzione B-eseguire il debug localmente nel calcolo
   * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
   * [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
   * [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
   * Disporre di un'installazione Docker funzionante nel sistema locale. 
   * Per verificare l'installazione Docker, usare il comando `docker run hello-world` da un terminale o da un prompt dei comandi. Per informazioni sull'installazione Docker o sulla risoluzione dei problemi relativi agli errori Docker, vedere la [Documentazione di Docker](https://docs.docker.com/).

## <a name="debug-locally"></a>Eseguire il debug in locale

Per esplorare un esempio eseguibile, è possibile trovare un [notebook di distribuzione locale](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) di esempio nel repository  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) .

> [!WARNING]
> Le distribuzioni di servizi Web locali non sono supportate per gli scenari di produzione.

Per eseguire la distribuzione localmente, modificare il codice per usare `LocalWebservice.deploy_configuration()` per creare una configurazione della distribuzione. Usare quindi `Model.deploy()` per distribuire il servizio. Nell'esempio seguente viene distribuito un modello (contenuto nella variabile del modello) come servizio Web locale:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Se si definisce una specifica conda specifica YAML, List azureml-defaults Version >= 1.0.45 come dipendenza PIP. Questo pacchetto è necessario per ospitare il modello come servizio Web.

A questo punto, è possibile usare il servizio come di consueto. Nel codice seguente viene illustrato l'invio di dati al servizio:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aggiornare il servizio

Durante i test locali, potrebbe essere necessario aggiornare il file `score.py` per aggiungere la registrazione o tentare di risolvere eventuali problemi individuati. Per ricaricare le modifiche apportate al file di `score.py`, usare `reload()`. Il codice seguente, ad esempio, consente di ricaricare lo script per il servizio e quindi di inviarvi dati. Il punteggio dei dati viene assegnato tramite il file `score.py` aggiornato:

> [!IMPORTANT]
> Il metodo `reload` è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione di a un'altra destinazione di calcolo, vedere [How to Update your webservice](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dalla posizione specificata dall'oggetto `InferenceConfig` usato dal servizio.

Per modificare il modello, le dipendenze Conda o la configurazione della distribuzione, usare [update()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-). Nell'esempio seguente viene aggiornato il modello usato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, usare [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Esaminare il log Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per ACI, AKS e distribuzioni locali. L'esempio seguente illustra come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Se la riga si `Booting worker with pid: <pid>` verifica più volte nei log, significa che non è disponibile memoria sufficiente per avviare il thread di lavoro.
È possibile risolvere l'errore aumentando il valore di `memory_gb` in `deployment_config`

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come risolvere i problemi delle distribuzioni remote](how-to-troubleshoot-deployment.md)
* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
* [Come eseguire ed eseguire il debug di esperimenti localmente](./how-to-debug-visual-studio-code.md)