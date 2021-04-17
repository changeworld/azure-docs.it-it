---
title: Spiegabilità in Machine Learning automatizzato (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni sul modo in cui il modello di Machine Learning automatizzato determina l'importanza delle funzionalità ed esegue stime quando si usa Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 3258a1d53c4aa5010758bcd93ef32c53611f4684
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576466"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpretabilità: spiegazioni del modello in Machine Learning automatizzato (anteprima)


In questo articolo si apprenderà come ottenere spiegazioni per machine learning automatizzato (machine learning automatizzato) in Azure Machine Learning usando Python SDK. Machine Learning automatizzato consente di comprendere l'importanza delle funzionalità dei modelli generati. 

Tutte le versioni dell'SDK successive alla 1.0.85 sono impostate `model_explainability=True` per impostazione predefinita. In SDK versione 1.0.85 e versioni precedenti gli utenti devono impostare nell'oggetto per usare `model_explainability=True` `AutoMLConfig` l'interpretabilità del modello. 


In questo articolo vengono illustrate le operazioni seguenti:

- Eseguire l'interpretabilità durante il training per un modello migliore o qualsiasi modello.
- Abilitare le visualizzazioni per visualizzare i modelli nei dati e nelle spiegazioni.
- Implementare l'interpretabilità durante l'inferenza o l'assegnazione dei punteggi.

## <a name="prerequisites"></a>Prerequisiti

- Funzionalità di interpretabilità. Eseguire `pip install azureml-interpret` per ottenere il pacchetto necessario.
- Conoscenza della creazione di esperimenti automatizzati di Machine Learning. Per altre informazioni su come usare Azure Machine Learning SDK, completare questa esercitazione sul modello di [regressione](tutorial-auto-train-models.md) o vedere come configurare esperimenti [automatizzati di Machine](how-to-configure-auto-train.md)Learning.

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretabilità durante il training per il modello migliore

Recuperare la spiegazione da , che include spiegazioni sia per le funzionalità non elaborate `best_run` che per le funzionalità progettate.

> [!NOTE]
> L'interpretabilità, la migliore spiegazione del modello, non è disponibile per gli esperimenti di previsione di Machine Learning automatico che consigliano gli algoritmi seguenti come modello migliore: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * Media 
> * Naive
> * Media stagionale 
> * Naive stagionale

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Scaricare le priorità delle funzionalità progettate dalla migliore esecuzione

È possibile usare `ExplanationClient` per scaricare le spiegazioni delle funzionalità progettate dall'archivio artefatti di `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Scaricare le priorità delle funzionalità non elaborati dalla migliore esecuzione

È possibile usare `ExplanationClient` per scaricare le spiegazioni delle funzionalità non elaborato dall'archivio artefatti di `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretabilità durante il training per qualsiasi modello 

Quando si calcolano le spiegazioni del modello e le si visualizza, non si è limitati a una spiegazione del modello esistente per un modello AutoML. È anche possibile ottenere una spiegazione per il modello con dati di test diversi. I passaggi descritti in questa sezione illustrano come calcolare e visualizzare l'importanza delle caratteristiche progettate in base ai dati di test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperare qualsiasi altro modello AutoML dal training

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configurare le spiegazioni del modello

Usare `automl_setup_model_explanations` per ottenere le spiegazioni progettate e non elaborate. può `fitted_model` generare gli elementi seguenti:

- Dati in primo piano da esempi di training o di test
- Elenchi di nomi di funzionalità progettate
- Classi individuabili nella colonna con etichetta negli scenari di classificazione

contiene `automl_explainer_setup_obj` tutte le strutture dell'elenco precedente.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inizializzare l'elemento Mimic Explainer per l'importanza delle funzionalità

Per generare una spiegazione per i modelli di Machine Learning automatizzato, usare la `MimicWrapper` classe . È possibile inizializzare MimicWrapper con questi parametri:

- Oggetto di configurazione dello spiegatore
- Area di lavoro
- Un modello surrogato per spiegare il modello di Machine Learning `fitted_model` automatizzato

MimicWrapper accetta anche l'oggetto in cui verranno caricate le spiegazioni `automl_run` progettate.

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

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Usare Mimic Explainer per il calcolo e la visualizzazione dell'importanza delle funzionalità progettate

È possibile chiamare il metodo in MimicWrapper con gli esempi di test trasformati per ottenere l'importanza delle funzionalità `explain()` generate. È anche possibile accedere a [studio di Azure Machine Learning](https://ml.azure.com/) visualizzare la visualizzazione del dashboard delle spiegazioni dei valori di importanza delle funzionalità generate dalle funzionalità di ML automatizzate.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
Per i modelli con training con Machine Learning automatizzato, è possibile ottenere il modello migliore usando il metodo e `get_output()` le spiegazioni di calcolo in locale.  È possibile visualizzare i risultati della spiegazione con `ExplanationDashboard` dal `interpret-community` pacchetto.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Usare Mimic Explainer per il calcolo e la visualizzazione dell'importanza delle funzionalità non elaborato

È possibile chiamare il metodo in MimicWrapper con gli esempi di test trasformati per ottenere l'importanza della funzionalità `explain()` per le funzionalità non elaborati. In Machine Learning [Studio](https://ml.azure.com/)è possibile visualizzare la visualizzazione del dashboard dei valori di importanza delle funzionalità non elaborati.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpretabilità durante l'inferenza

In questa sezione si apprenderà come operazionare un modello di Machine Learning automatizzato con lo strumento di spiegazione usato per calcolare le spiegazioni nella sezione precedente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrare il modello e lo spiegatore di assegnazione dei punteggi

Usare per creare lo strumento di spiegazione dei punteggi che calcola i valori di importanza delle funzionalità progettate `TreeScoringExplainer` al momento dell'inferenza. Inizializzare lo explainer di assegnazione dei punteggi con `feature_map` l'oggetto calcolato in precedenza. 

Salvare lo spiegatore di assegnazione dei punteggi e quindi registrare il modello e lo spiegatore di assegnazione dei punteggi con il Gestione modelli servizio. Eseguire il codice seguente:

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

Creare quindi le dipendenze di ambiente necessarie nel contenitore per il modello distribuito. Si noti che azureml-defaults con versione >= 1.0.45 deve essere elencato come dipendenza pip, perché contiene le funzionalità necessarie per ospitare il modello come servizio Web.

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

Scrivere uno script che carica il modello e produce stime e spiegazioni basate su un nuovo batch di dati.

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

Inferenza con alcuni dati di test per visualizzare il valore previsto dal modello AutoML, attualmente supportato solo in Azure Machine Learning SDK. Visualizzare le priorità delle caratteristiche che contribuiscono a un valore previsto. 

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

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizzare per individuare modelli nei dati e spiegazioni in fase di training

È possibile visualizzare il grafico di importanza delle funzionalità nell'area di lavoro in [studio di Azure Machine Learning](https://ml.azure.com). Al termine dell'esecuzione di AutoML, selezionare **Visualizza i dettagli del modello** per visualizzare un'esecuzione specifica. Selezionare la **scheda Spiegazioni** per visualizzare le visualizzazioni nel dashboard delle spiegazioni.

[![Machine Learning di interpretabilità dei dati](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Per altre informazioni sulle visualizzazioni del dashboard di spiegazione e sui tracciati specifici, vedere la documentazione sulle modalità [di interpretazione.](how-to-machine-learning-interpretability-aml.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in aree di Azure Machine Learning SDK diverse dall'apprendimento automatico, vedere l'articolo sui concetti [sull'interpretabilità.](how-to-machine-learning-interpretability.md)
