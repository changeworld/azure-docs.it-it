---
title: Usare e distribuire modelli esistenti
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training dei modelli di Machine Learning all'esterno di Azure nel cloud di Azure con Azure Machine Learning. Distribuire quindi il modello come servizio Web o modulo IoT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46dc9e48ba68189253885ae823457a62c3c4426e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877805"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Distribuire il modello esistente con Azure Machine Learning


Questo articolo illustra come registrare e distribuire un modello di Machine Learning di cui è stato Azure Machine Learning. È possibile eseguire la distribuzione come servizio Web o in un IoT Edge dispositivo.  Dopo la distribuzione, è possibile monitorare il modello e rilevare la deriva dei dati Azure Machine Learning. 

Per altre informazioni sui concetti e i termini di questo articolo, vedere [Gestire, distribuire e monitorare i modelli di Machine Learning.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Prerequisiti

* [Un'Azure Machine Learning di lavoro](how-to-manage-workspace.md)
  + Gli esempi Python presuppongono `ws` che la variabile sia impostata sull'area Azure Machine Learning lavoro. Per altre informazioni su come connettersi all'area di lavoro, vedere la documentazione di [Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/#workspace).
  
  + Negli esempi dell'interfaccia della riga di comando vengono utilizzati i segnaposto di e , che è necessario sostituire con il nome dell'area di lavoro e il gruppo `myworkspace` di risorse che la `myresourcegroup` contiene.

* Il [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install).  

* [L'interfaccia della riga di](/cli/azure/install-azure-cli) comando di Azure e [Machine Learning'interfaccia della riga di comando di Azure.](reference-azure-machine-learning-cli.md)

* Un modello con training. Il modello deve essere salvato in modo permanente in uno o più file nell'ambiente di sviluppo. <br><br>Per illustrare la registrazione di un modello con training, il codice di esempio in questo articolo usa i modelli del progetto di analisi del sentiment [twitter di Paolo Ripamonti.](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)

## <a name="register-the-models"></a>Registrare i modelli

La registrazione di un modello consente di archiviare, versione e tenere traccia dei metadati relativi ai modelli nell'area di lavoro. Negli esempi python e dell'interfaccia della riga di comando seguenti la directory contiene i file `models` `model.h5` , , e `model.w2v` `encoder.pkl` `tokenizer.pkl` . Questo esempio carica i file contenuti nella directory come `models` nuova registrazione del modello denominata `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Per altre informazioni, vedere le [informazioni di riferimento su Model.register().](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> È anche possibile impostare gli `tags` oggetti add `properties` e dictionary sul modello registrato. Questi valori possono essere usati in un secondo momento per identificare un modello specifico. Ad esempio, il framework usato, i parametri di training e così via.

Per altre informazioni, vedere il riferimento [a az ml model register.](/cli/azure/ml/model#az_ml_model_register)


Per altre informazioni sulla registrazione dei modelli in generale, vedere [Gestire, distribuire e monitorare i modelli di Machine Learning.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Definire la configurazione dell'inferenza

La configurazione dell'inferenza definisce l'ambiente usato per eseguire il modello distribuito. La configurazione dell'inferenza fa riferimento alle entità seguenti, usate per eseguire il modello quando viene distribuito:

* Uno script di immissione, denominato `score.py` , carica il modello all'avvio del servizio distribuito. Questo script è anche responsabile della ricezione dei dati, del passaggio al modello e della restituzione di una risposta.
* Ambiente [Azure Machine Learning.](how-to-use-environments.md) Un ambiente definisce le dipendenze software necessarie per eseguire il modello e lo script di immissione.

L'esempio seguente illustra come usare l'SDK per creare un ambiente e quindi usarlo con una configurazione di inferenza:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Per altre informazioni, vedere gli articoli seguenti:

+ [Come usare gli ambienti](how-to-use-environments.md).
+ [Informazioni di riferimento su InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)


L'interfaccia della riga di comando carica la configurazione dell'inferenza da un file YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Con l'interfaccia della riga di comando, l'ambiente Conda è definito nel file a `myenv.yml` cui fa riferimento la configurazione di inferenza. Il codice YAML seguente è il contenuto di questo file:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Per altre informazioni sulla configurazione dell'inferenza, vedere [Distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Script di immissione (score.py)

Lo script di immissione ha solo due funzioni `init()` obbligatorie, e `run(data)` . Queste funzioni vengono usate per inizializzare il servizio all'avvio ed eseguire il modello usando i dati della richiesta passati da un client. Il resto dello script gestisce il caricamento e l'esecuzione dei modelli.

> [!IMPORTANT]
> Non è disponibile uno script di immissione generico che funziona per tutti i modelli. È sempre specifico del modello usato. Deve comprendere come caricare il modello, il formato dati previsto dal modello e come segnare i dati usando il modello.

Il codice Python seguente è uno script di immissione di esempio ( `score.py` ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Per altre informazioni sugli script di immissione, vedere [Distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definire la distribuzione

Il [pacchetto del servizio](/python/api/azureml-core/azureml.core.webservice) Web contiene le classi usate per la distribuzione. La classe utilizzata determina dove viene distribuito il modello. Ad esempio, per eseguire la distribuzione come servizio Web in servizio Azure Kubernetes, [usare AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) per creare la configurazione della distribuzione.

Il codice Python seguente definisce una configurazione di distribuzione per una distribuzione locale. Questa configurazione distribuisce il modello come servizio Web nel computer locale.

> [!IMPORTANT]
> Una distribuzione locale richiede un'installazione funzionante [di Docker](https://www.docker.com/) nel computer locale:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Per altre informazioni, vedere le informazioni [di riferimento LocalWebservice.deploy_configuration().](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-)

L'interfaccia della riga di comando carica la configurazione della distribuzione da un file YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

La distribuzione in una destinazione di calcolo diversa, ad esempio servizio Azure Kubernetes nel cloud di Azure, è semplice come la modifica della configurazione della distribuzione. Per altre informazioni, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Distribuire il modello

Nell'esempio seguente vengono caricate informazioni sul modello registrato denominato e quindi vengono distribuite `sentiment` come servizio denominato `sentiment` . Durante la distribuzione, la configurazione dell'inferenza e la configurazione della distribuzione vengono usate per creare e configurare l'ambiente del servizio:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Per altre informazioni, vedere le [informazioni di riferimento su Model.deploy().](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Per distribuire il modello dall'interfaccia della riga di comando, usare il comando seguente. Questo comando distribuisce la versione 1 del modello registrato ( ) usando l'inferenza e la configurazione di distribuzione `sentiment:1` archiviate nei `inferenceConfig.json` file e `deploymentConfig.json` :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Per altre informazioni, vedere le informazioni di [riferimento su az ml model deploy.](/cli/azure/ml/model#az_ml_model_deploy)

Per altre informazioni sulla distribuzione, vedere [Come e dove distribuire i modelli.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Utilizzo richiesta-risposta

Dopo la distribuzione, viene visualizzato l'URI di assegnazione dei punteggi. Questo URI può essere usato dai client per inviare richieste al servizio. L'esempio seguente è un semplice client Python che invia i dati al servizio e visualizza la risposta:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Per altre informazioni su come usare il servizio distribuito, vedere [Creare un client](how-to-consume-web-service.md).

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i modelli Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Come creare un client per un modello distribuito](how-to-consume-web-service.md)