---
title: Spiegabilità in Machine Learning Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni sul modo in cui il modello di Machine Learning automatico determina l'importanza della funzionalità ed esegue stime quando si usa il Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 709c85bed4a028c6c168c79cd9fffd6b7b40cb68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100008044"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpretabilità: spiegazioni del modello in Machine Learning automatizzato (anteprima)



In questo articolo si apprenderà come ottenere spiegazioni per automatizzare Machine Learning (AutoML) in Azure Machine Learning. AutoML consente di comprendere l'importanza della funzionalità dei modelli generati. 

Tutte le versioni dell'SDK dopo 1.0.85 impostate `model_explainability=True` per impostazione predefinita. Nella versione SDK 1.0.85 e nelle versioni precedenti, gli utenti devono impostare `model_explainability=True` nell'oggetto per poter `AutoMLConfig` usare l'interpretazione del modello. 

In questo articolo vengono illustrate le operazioni seguenti:

- Eseguire l'interpretazione durante il training per il modello migliore o qualsiasi modello.
- Abilitare le visualizzazioni per visualizzare i modelli nei dati e nelle spiegazioni.
- Implementare l'interpretazione durante l'inferenza o il punteggio.

## <a name="prerequisites"></a>Prerequisiti

- Funzionalità di interpretazione. Eseguire `pip install azureml-interpret` per ottenere il pacchetto necessario.
- Conoscenza della creazione di esperimenti AutoML. Per altre informazioni su come usare l'SDK Azure Machine Learning, completare questa [esercitazione sul modello di regressione](tutorial-auto-train-models.md) o vedere come [configurare gli esperimenti AutoML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretazione durante il training per il modello migliore

Recuperare la spiegazione da `best_run` , che include spiegazioni per le funzionalità non elaborate e progettate.

> [!NOTE]
> L'interpretazione, la migliore spiegazione del modello, non è disponibile per gli esperimenti di previsione di auto machine learning che suggeriscono gli algoritmi seguenti come modello ottimale: 
> * TCNForecaster
> * ARIMA
> * ExponentialSmoothing
> * Prophet
> * Media 
> * Naive
> * Media stagionale 
> * Naive stagionali

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Scarica le priorità della funzionalità progettata dalla migliore esecuzione

È possibile usare `ExplanationClient` per scaricare le spiegazioni delle funzionalità progettate dall'archivio di artefatti di `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Scarica le priorità della funzionalità RAW dalla migliore esecuzione

È possibile usare `ExplanationClient` per scaricare le spiegazioni sulle funzionalità non elaborate dall'archivio di artefatti di `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretazione durante il training di qualsiasi modello 

Quando si calcolano le spiegazioni dei modelli e le si visualizza, non si è limitati a una spiegazione del modello esistente per un modello AutoML. È anche possibile ottenere una spiegazione per il modello con dati di test diversi. I passaggi descritti in questa sezione illustrano come calcolare e visualizzare l'importanza della funzionalità progettata in base ai dati di test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperare qualsiasi altro modello di AutoML da training

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configurare le spiegazioni del modello

Usare `automl_setup_model_explanations` per ottenere le spiegazioni non elaborate e progettate. `fitted_model`Può generare gli elementi seguenti:

- Dati in primo piano da esempi di training o di test
- Elenchi di nomi di funzionalità progettati
- Classi trovabili nella colonna con etichetta negli scenari di classificazione

`automl_explainer_setup_obj`Contiene tutte le strutture dall'elenco precedente.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inizializzare il Explainer MIME per l'importanza della funzionalità

Per generare una spiegazione per i modelli AutoML, usare la `MimicWrapper` classe. È possibile inizializzare MimicWrapper con questi parametri:

- Oggetto di installazione di Explainer
- Area di lavoro
- Modello surrogato per la spiegazione del `fitted_model` modello AutoML

Il MimicWrapper accetta anche l' `automl_run` oggetto in cui verranno caricate le spiegazioni progettate.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Usare il Visualizzatore di simulazioni per l'elaborazione e la visualizzazione dell'importanza della funzionalità progettata

È possibile chiamare il `explain()` metodo in MimicWrapper con gli esempi di test trasformati per ottenere l'importanza della funzionalità per le funzionalità di progettazione generate. È anche possibile accedere a [Azure Machine Learning Studio](https://ml.azure.com/) per visualizzare la visualizzazione del dashboard dei valori di importanza delle funzionalità delle funzionalità di progettazione generate da AutoML featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Usare il Visualizzatore di simulazioni per l'elaborazione e la visualizzazione dell'importanza della funzionalità RAW

È possibile chiamare il `explain()` metodo in MimicWrapper con gli esempi di test trasformati per ottenere l'importanza della funzionalità per le funzionalità non elaborate. In [Machine Learning Studio](https://ml.azure.com/), è possibile visualizzare la visualizzazione del dashboard dei valori di importanza della funzionalità delle funzionalità non elaborate.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpretazione durante l'inferenza

In questa sezione si apprenderà come rendere operativo un modello AutoML con il Explainer usato per calcolare le spiegazioni nella sezione precedente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrare il modello e il spiegazione del Punteggio

Usare `TreeScoringExplainer` per creare il Explainer di assegnazione dei punteggi che calcolerà i valori di importanza della funzionalità progettata in fase di inferenza. Si inizializza il Explainer di assegnazione dei punteggi con il `feature_map` che è stato calcolato in precedenza. 

Salvare il Explainer di assegnazione dei punteggi, quindi registrare il modello e il Explainer di assegnazione dei punteggi con il servizio Gestione modelli. Eseguire il codice seguente:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Creare le dipendenze conda per la configurazione del servizio

Successivamente, creare le dipendenze di ambiente necessarie nel contenitore per il modello distribuito. Si noti che azureml-defaults con Version >= 1.0.45 deve essere elencato come una dipendenza PIP, perché contiene la funzionalità necessaria per ospitare il modello come servizio Web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Creare lo script di assegnazione dei punteggi

Scrivere uno script per caricare il modello e produrre stime e spiegazioni basate su un nuovo batch di dati.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Distribuire il servizio

Distribuire il servizio usando il file conda e il file di assegnazione dei punteggi dei passaggi precedenti.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inferenza con dati di test

Inferenza con alcuni dati di test per visualizzare il valore stimato del modello AutoML, attualmente supportato solo in Azure Machine Learning SDK. Consente di visualizzare le priorità della funzionalità che contribuiscono a un valore stimato. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizza per individuare i modelli nei dati e le spiegazioni in fase di training

È possibile visualizzare il grafico importanza funzionalità nell'area di lavoro in [Machine Learning Studio](https://ml.azure.com). Al termine dell'esecuzione del AutoML, selezionare **Visualizza dettagli modello** per visualizzare un'esecuzione specifica. Selezionare la scheda **spiegazioni** per visualizzare il dashboard di visualizzazione spiegazione.

[![Architettura di interpretazione Machine Learning](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Per altre informazioni sulla spiegazione delle visualizzazioni del dashboard e dei tracciati specifici, vedere la [documentazione relativa alle procedure](how-to-machine-learning-interpretability-aml.md)per l'interpretazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come abilitare le spiegazioni dei modelli e l'importanza delle funzionalità nelle aree di Azure Machine Learning SDK, ad eccezione dell'apprendimento automatico automatico, vedere l' [articolo relativo al concetto di interpretazione](how-to-machine-learning-interpretability.md).
