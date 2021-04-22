---
title: Come eseguire e distribuire in locale
titleSuffix: Azure Machine Learning
description: Questo articolo descrive come usare il computer locale come destinazione per il training, il debug o la distribuzione di modelli creati in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861217"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Distribuire modelli con training con Azure Machine Learning nei computer locali 

Questo articolo descrive come usare il computer locale come destinazione per il training o la distribuzione di modelli creati in Azure Machine Learning. Azure Machine Learning è sufficientemente flessibile da funzionare con la maggior parte dei framework di Machine Learning Python. Le soluzioni di Machine Learning hanno in genere dipendenze complesse che possono essere difficili da duplicare. Questo articolo illustra come bilanciare il controllo totale con facilità d'uso.

Gli scenari per la distribuzione locale includono:

* Eseguire rapidamente l'iterazione di dati, script e modelli nelle prime fasi di un progetto.
* Debug e risoluzione dei problemi nelle fasi successive.
* Distribuzione finale nell'hardware gestito dall'utente.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)
- Un modello e un ambiente. Se non si dispone di un modello con training, è possibile usare il modello e i file di dipendenza forniti in [questa esercitazione.](tutorial-train-models-with-aml.md)
- Sdk [Azure Machine Learning per Python.](/python/api/overview/azure/ml/intro)
- Un gestore Conda, ad esempio Anaconda o Miniconda, se si vuole eseguire il mirroring Azure Machine Learning dipendenze del pacchetto.
- Docker, se si vuole usare una versione in contenitori dell'ambiente Azure Machine Learning contenitore.

## <a name="prepare-your-local-machine"></a>Preparare il computer locale

Il modo più affidabile per eseguire un modello Azure Machine Learning locale è con un'immagine Docker. Un'immagine Docker offre un'esperienza isolata e in contenitori che duplica, ad eccezione dei problemi hardware, l'ambiente di esecuzione di Azure. Per altre informazioni sull'installazione e la configurazione di Docker per gli scenari di sviluppo, vedere [Panoramica dello sviluppo remoto docker in Windows.](/windows/dev-environment/docker/overview)

È possibile collegare un debugger a un processo in esecuzione in Docker. Vedere [Connettersi a un contenitore in esecuzione.](https://code.visualstudio.com/docs/remote/attach-container) È tuttavia consigliabile eseguire il debug e l'iterazione del codice Python senza coinvolgere Docker. In questo scenario è importante che il computer locale usi le stesse librerie usate quando si esegue l'esperimento in Azure Machine Learning. Per gestire le dipendenze python, Azure usa [conda](https://docs.conda.io/). È possibile ri-creare l'ambiente usando altri gestori pacchetti, ma l'installazione e la configurazione di conda nel computer locale è il modo più semplice per eseguire la sincronizzazione. 

## <a name="prepare-your-entry-script"></a>Preparare lo script di immissione

Anche se si usa Docker per gestire il modello e le dipendenze, lo script di assegnazione dei punteggi Python deve essere locale. Lo script deve avere due metodi:

- Metodo `init()` che non accetta argomenti e non restituisce alcun valore 
- Metodo `run()` che accetta una stringa in formato JSON e restituisce un oggetto serializzabile JSON

L'argomento `run()` del metodo sarà nel formato seguente: 

```json
{
    "data": <model-specific-data-structure>
}
```

L'oggetto restituito dal `run()` metodo deve implementare `toJSON() -> string` .

L'esempio seguente illustra come caricare un modello scikit-learn registrato e impostarlo come punteggio usando dati NumPy. Questo esempio si basa sul modello e sulle dipendenze di [questa esercitazione.](tutorial-train-models-with-aml.md)

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Per esempi più avanzati, tra cui la generazione automatica dello schema Swagger e l'assegnazione di punteggi ai dati binari (ad esempio, immagini), vedere Creazione di script di [immissione avanzata](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Distribuire come servizio Web locale usando Docker

Il modo più semplice per replicare l'ambiente Azure Machine Learning è distribuire un servizio Web usando Docker. Con Docker in esecuzione nel computer locale, si:

1. Connettersi all'Azure Machine Learning di lavoro in cui è registrato il modello.
1. Creare un `Model` oggetto che rappresenta il modello.
1. Creare un `Environment` oggetto che contiene le dipendenze e definisce l'ambiente software in cui verrà eseguito il codice.
1. Creare un `InferenceConfig` oggetto che associa lo script di immissione a `Environment` .
1. Creare un `DeploymentConfiguration` oggetto della sottoclasse `LocalWebserviceDeploymentConfiguration` .
1. Usare `Model.deploy()` per creare un oggetto `Webservice` . Questo metodo scarica l'immagine Docker e la associa a `Model` `InferenceConfig` , e `DeploymentConfiguration` .
1. Attivare `Webservice` l'oggetto utilizzando `Webservice.wait_for_deployment()` .

Il codice seguente illustra questi passaggi:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

La chiamata a `Model.deploy()` può richiedere alcuni minuti. Dopo aver distribuito inizialmente il servizio Web, è più efficiente usare il metodo `update()` anziché iniziare da zero. Vedere [Aggiornare un servizio Web distribuito.](how-to-deploy-update-web-service.md)

### <a name="test-your-local-deployment"></a>Testare la distribuzione locale

Quando si esegue lo script di distribuzione precedente, verrà restituito l'URI in cui è possibile registrare i dati per l'assegnazione dei punteggi (ad esempio, `http://localhost:6789/score` ). L'esempio seguente mostra uno script che ottiene i punteggi dei dati di esempio usando il `"sklearn-mnist-local"` modello distribuito in locale. Se il training del modello è stato eseguito correttamente, deduce che `normalized_pixel_values` deve essere interpretato come "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Scaricare ed eseguire il modello direttamente

L'uso di Docker per distribuire il modello come servizio Web è l'opzione più comune. È tuttavia possibile eseguire il codice direttamente usando script Python locali. Sono necessari due componenti importanti: 

- Il modello stesso
- Dipendenze su cui si basa il modello 

È possibile scaricare il modello:  

- Nel portale selezionare la scheda **Modelli,** selezionare il modello desiderato e nella **pagina** Dettagli selezionare **Scarica**.
- Dalla riga di comando, usando `az ml model download` . Vedere il [download del modello.](/cli/azure/ml/model#az_ml_model_download)
- Usando il metodo Python `Model.download()` SDK. Vedere [Classe Model.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-)

Un modello di Azure è uno o più oggetti Python serializzati, in pacchetto come file pickle Python (estensione pkl). Il contenuto del file pickle dipende dalla libreria o dalla tecnica di Machine Learning usata per eseguire il training del modello. Ad esempio, se si usa il modello dell'esercitazione, è possibile caricare il modello con:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Le dipendenze sono sempre difficili da ottenere, in particolare con Machine Learning, in cui spesso può esserci un web vertiginoso di requisiti di versione specifici. È possibile creare nuovamente un ambiente Azure Machine Learning nel computer locale come ambiente conda completo o come immagine Docker usando il metodo `build_local()` della `Environment` classe : 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Se si imposta `build_local()` `useDocker` l'argomento su `True` , la funzione creerà un'immagine Docker anziché un ambiente conda. Se si vuole un maggiore controllo, è possibile usare il metodo di , che scrive conda_dependencies.yml e azureml_environment.jsnei file di definizione che è possibile ottimizzare e usare come base per `save_to_directory()` `Environment` l'estensione. 

La classe include diversi altri metodi per sincronizzare gli ambienti tra l'hardware di calcolo, l'area di lavoro `Environment` di Azure e le immagini Docker. Per altre informazioni, vedere [Classe Environment](/python/api/azureml-core/azureml.core.environment(class)).

Dopo aver scaricato il modello e risolto le relative dipendenze, non sono presenti restrizioni definite da Azure su come eseguire l'assegnazione dei punteggi, ottimizzare il modello, usare l'apprendimento per il trasferimento e così via. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Caricare un modello di cui è stato nuovamente Azure Machine Learning

Se si dispone di un modello con training locale o di nuovo training, è possibile registrarlo in Azure. Dopo la registrazione, è possibile continuare a eseguire l'ottimizzazione usando il calcolo di Azure o distribuirlo usando funzionalità di Azure come [servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) [o Triton Inference Server (anteprima).](how-to-deploy-with-triton.md)

Per poter essere usato con Azure Machine Learning Python SDK, un modello deve essere archiviato come oggetto Python serializzato in formato pickle (file con estensione pkl). Deve inoltre implementare un `predict(data)` metodo che restituisce un oggetto serializzabile in JSON. Ad esempio, è possibile archiviare un modello scikit-learn diabetes con training locale con: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Per rendere disponibile il modello in Azure, è quindi possibile usare il `register()` metodo della `Model` classe :

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

È quindi possibile trovare il modello appena registrato nella scheda Azure Machine Learning **modello:**

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Screenshot della Azure Machine Learning modello, che mostra un modello caricato.":::

Per altre informazioni sul caricamento e l'aggiornamento di modelli e ambienti, vedere Registrare il modello [e distribuirlo in locale con utilizzi avanzati.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione degli ambienti, vedere [Creare & usare ambienti software in Azure Machine Learning](how-to-use-environments.md).
- Per informazioni sull'accesso ai dati dall'archivio dati, vedere [Connettersi ai servizi di archiviazione in Azure.](how-to-access-data.md)
