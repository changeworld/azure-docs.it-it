---
title: Interpretare & i modelli di Machine Learning in Python (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni sul modo in cui il modello di Machine Learning determina l'importanza delle funzionalità e esegue stime quando si usa Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 2d60c6dbedb24847b95ce268bedafcb073421319
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576517"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Usare il pacchetto di interpretabilità per spiegare i modelli di Machine Learning & stime in Python (anteprima)

In questa guida dettagliata si apprenderà a usare il pacchetto di interpretabilità di Azure Machine Learning Python SDK per eseguire le attività seguenti:


* Spiegare l'intero comportamento del modello o le singole stime nel computer personale in locale.

* Abilitare le tecniche di interpretabilità per le funzionalità progettate.

* Illustrare il comportamento per l'intero modello e le singole stime in Azure.

* Usare un dashboard di visualizzazione per interagire con le spiegazioni del modello.

* Distribuire uno spiegatore di assegnazione dei punteggi insieme al modello per osservare le spiegazioni durante l'inferenza.



Per altre informazioni sulle tecniche di interpretabilità supportate e sui modelli di Machine Learning, vedere Interpretabilità dei modelli [Azure Machine Learning](how-to-machine-learning-interpretability.md) e [notebook di esempio.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generare il valore di importanza della funzionalità nel computer personale 
L'esempio seguente illustra come usare il pacchetto di interpretabilità nel computer personale senza contattare i servizi di Azure.

1. Installare il pacchetto `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Training di un modello di esempio in un Jupyter Notebook.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. Chiamare lo spiegatore in locale.
   * Per inizializzare un oggetto dello spiegatore, passare il modello e alcuni dati di training al costruttore dello explainer.
   * Per rendere le spiegazioni e le visualizzazioni più informative, è possibile scegliere di passare i nomi delle funzionalità e i nomi delle classi di output se si effettua la classificazione.

   I blocchi di codice seguenti illustrano come creare un'istanza di un oggetto explainer con `TabularExplainer` `MimicExplainer` , e in `PFIExplainer` locale.
   * `TabularExplainer` chiama uno dei tre spiegatori SHAP sottostanti ( `TreeExplainer` , `DeepExplainer` o `KernelExplainer` ).
   * `TabularExplainer` seleziona automaticamente quello più appropriato per il caso d'uso, ma è possibile chiamare direttamente ognuno dei tre spiegatori sottostanti.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    oppure

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    oppure

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Spiegare l'intero comportamento del modello (spiegazione globale) 

Fare riferimento all'esempio seguente per ottenere i valori di importanza delle caratteristiche di aggregazione (globali).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Spiegare una stima singola (spiegazione locale)
Ottenere i singoli valori di importanza delle caratteristiche di punti dati diversi chiamando spiegazioni per una singola istanza o un gruppo di istanze.
> [!NOTE]
> `PFIExplainer` non supporta spiegazioni locali.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Trasformazioni di funzionalità non elaborati

È possibile scegliere di ottenere spiegazioni in termini di funzionalità non elaborati e non trasformate anziché in termini di funzionalità progettate. Per questa opzione, si passa la pipeline di trasformazione delle funzionalità all'oggetto explainer in `train_explain.py` . In caso contrario, l'spiegazione fornisce spiegazioni in termini di funzionalità progettate.

Il formato delle trasformazioni supportate è identico a quello descritto in [sklearn-pandas.](https://github.com/scikit-learn-contrib/sklearn-pandas) In generale, tutte le trasformazioni sono supportate purché opevano su una singola colonna in modo che sia chiaro che sono uno-a-molti.

Ottenere una spiegazione delle funzionalità non elaborati usando o `sklearn.compose.ColumnTransformer` con un elenco di tuple di trasformatori adattate. Nell'esempio seguente viene utilizzato `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Se si vuole eseguire l'esempio con l'elenco di tuple di trasformatori adattate, usare il codice seguente:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generare valori di importanza delle funzionalità tramite esecuzioni remote

Nell'esempio seguente viene illustrato come utilizzare la classe `ExplanationClient` per abilitare l'interpretabilità del modello per le esecuzioni remote. Dal punto di vista concettuale è simile al processo locale, con la differenza che:

* Usare `ExplanationClient` nell'esecuzione remota per caricare il contesto di interpretabilità.
* Scaricare il contesto in un secondo momento in un ambiente locale.

1. Installare il pacchetto `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Creare uno script di training in un Jupyter Notebook. Ad esempio: `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Configurare un'istanza Azure Machine Learning calcolo come destinazione di calcolo e inviare l'esecuzione del training. Per istruzioni, vedere [Creare e Azure Machine Learning cluster di calcolo.](how-to-create-attach-compute-cluster.md) È anche possibile trovare utili [i notebook di](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) esempio.

1. Scaricare la spiegazione nel percorso Jupyter Notebook.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Visualizzazioni

Dopo aver scaricato le spiegazioni nel Jupyter Notebook locale, è possibile usare le visualizzazioni nel dashboard delle spiegazioni per comprendere e interpretare il modello. Per caricare il widget del dashboard delle spiegazioni nel Jupyter Notebook, usare il codice seguente:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Le visualizzazioni supportano spiegazioni sulle funzionalità progettate e non elaborate. Le spiegazioni non elaborate si basano sulle funzionalità del set di dati originale e le spiegazioni progettate si basano sulle funzionalità del set di dati con la progettazione delle funzionalità applicata.

Quando si tenta di interpretare un modello rispetto al set di dati originale, è consigliabile usare spiegazioni non elaborate perché ogni importanza della funzionalità corrisponderà a una colonna del set di dati originale. Uno scenario in cui le spiegazioni progettate possono essere utili è quando si esamina l'impatto delle singole categorie da una funzionalità categorica. Se a una funzionalità categorica viene applicata una codifica one-hot, le spiegazioni ingegnerizzate risultanti includeranno un valore di importanza diverso per categoria, uno per ogni funzionalità progettata con un solo hot engineer. Ciò può essere utile quando si restringe la parte del set di dati più informativa per il modello.

> [!NOTE]
> Le spiegazioni progettate e non elaborate vengono calcolate in sequenza. Per prima cosa viene creata una spiegazione progettata in base al modello e alla pipeline di featurization. La spiegazione non elaborata viene quindi creata in base a tale spiegazione progettata aggregando l'importanza delle funzionalità progettate provenienti dalla stessa funzionalità non elaborata.

### <a name="create-edit-and-view-dataset-cohorts"></a>Creare, modificare e visualizzare coorti di set di dati

La barra multifunzione superiore mostra le statistiche complessive sul modello e sui dati. È possibile suddividere e suddividere i dati in coorti di set di dati, o sottogruppi, per analizzare o confrontare le prestazioni e le spiegazioni del modello in questi sottogruppi definiti. Confrontando le statistiche e le spiegazioni dei set di dati tra questi sottogruppi, è possibile ottenere un'idea del motivo per cui si verificano errori in un gruppo rispetto a un altro.

[![Creazione, modifica e visualizzazione di coorti di set di dati](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Comprendere il comportamento dell'intero modello (spiegazione globale) 

Le prime tre schede del dashboard di spiegazione forniscono un'analisi complessiva del modello con training insieme alle stime e alle spiegazioni.

#### <a name="model-performance"></a>Prestazioni del modello
Valutare le prestazioni del modello esplorando la distribuzione dei valori di stima e i valori delle metriche delle prestazioni del modello. È possibile analizzare ulteriormente il modello esaminando un'analisi comparativa delle relative prestazioni in coorti o sottogruppi diversi del set di dati. Selezionare i filtri lungo il valore y e il valore x per tagliare dimensioni diverse. Visualizzare metriche come accuratezza, precisione, richiamo, tasso di falsi positivi (FPR) e tasso di falsi negativi (FNR).

[![Scheda Prestazioni del modello nella visualizzazione della spiegazione](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Esplora set di dati
Esplorare le statistiche del set di dati selezionando filtri diversi lungo gli assi X, Y e colore per sezionare i dati in base a dimensioni diverse. Creare coorti di set di dati precedenti per analizzare le statistiche del set di dati con filtri come il risultato previsto, le funzionalità del set di dati e i gruppi di errori. Usare l'icona a forma di ingranaggio nell'angolo superiore destro del grafico per modificare i tipi di grafico.

[![Scheda Dataset Explorer (Esplora set di dati) nella visualizzazione spiegazione](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Importanza delle caratteristiche di aggregazione
Esplorare le principali caratteristiche importanti che influiscono sulle stime complessive del modello (note anche come spiegazione globale). Usare il dispositivo di scorrimento per visualizzare i valori di importanza delle caratteristiche decrescenti. Selezionare fino a tre coorti per visualizzarne i valori di importanza delle caratteristiche affiancati. Fare clic su una delle barre delle caratteristiche nel grafico per vedere in che modo i valori della funzionalità selezionata influiscono sulla stima del modello nel grafico delle dipendenze riportato di seguito.

[![Scheda Priorità delle caratteristiche di aggregazione nella visualizzazione della spiegazione](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Informazioni sulle singole stime (spiegazione locale) 

La quarta scheda della scheda spiegazione consente di esaminare un singolo punto dati e le relative priorità delle singole funzionalità. È possibile caricare il singolo tracciato di importanza delle caratteristiche per qualsiasi punto dati facendo clic su uno dei singoli punti dati nel grafico a dispersione principale o selezionando un punto dati specifico nella procedura guidata del pannello a destra.

|Grafico|Descrizione|
|----|-----------|
|Importanza delle singole funzionalità|Mostra le principali caratteristiche importanti per una singola stima. Illustra il comportamento locale del modello sottostante in un punto dati specifico.|
|What-If analisi|Consente di apportare modifiche ai valori delle caratteristiche del punto dati reale selezionato e osservare le modifiche risultanti al valore di stima generando un ipotetico punto dati con i nuovi valori di funzionalità.|
|Aspettativa condizionale individuale (ICE)|Consente di modificare il valore della funzionalità da un valore minimo a un valore massimo. Illustra come cambia la stima del punto dati quando cambia una funzionalità.|

[![Importanza delle singole funzionalità e scheda What-if nel dashboard di spiegazione](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Si tratta di spiegazioni basate su molte approssimazioni e non sono la "causa" delle stime. Senza una rigida affidabilità matematica dell'inferenza causale, non si consiglia agli utenti di prendere decisioni reali in base alle perturbazioni delle funzionalità dello What-If strumenti. Questo strumento è principalmente per comprendere il modello e il debug.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualizzazione in studio di Azure Machine Learning

Se si completano i passaggi di [interpretabilità](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) remota (caricamento delle spiegazioni generate Azure Machine Learning cronologia di esecuzione), è possibile visualizzare le visualizzazioni nel dashboard delle spiegazioni in [studio di Azure Machine Learning](https://ml.azure.com). Questo dashboard è una versione più semplice del widget del dashboard generato all'interno del notebook di Jupyter. What-If generazione di punti dati e tracciati ICE sono disabilitati perché non è presente alcun calcolo attivo in studio di Azure Machine Learning in grado di eseguire i calcoli in tempo reale.

Se il set di dati, le spiegazioni globali e locali sono disponibili, i dati popolano tutte le schede. Se è disponibile solo una spiegazione globale, la scheda Priorità singola funzionalità verrà disabilitata.

Seguire uno di questi percorsi per accedere al dashboard delle spiegazioni in studio di Azure Machine Learning:

* **Riquadro Esperimenti** (anteprima)
  1. Selezionare **Esperimenti** nel riquadro sinistro per visualizzare un elenco degli esperimenti eseguiti in Azure Machine Learning.
  1. Selezionare un esperimento specifico per visualizzare tutte le esecuzioni in tale esperimento.
  1. Selezionare un'esecuzione e quindi la **scheda Explanations (Spiegazioni)** nel dashboard di visualizzazione della spiegazione.

   [![Dashboard di visualizzazione con importanza delle funzionalità di aggregazione in AzureML Studio negli esperimenti](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Riquadro** Modelli
  1. Se il modello originale è stato registrato seguendo la procedura descritta in Distribuire modelli con [Azure Machine Learning](./how-to-deploy-and-where.md), è possibile selezionare **Modelli** nel riquadro sinistro per visualizzarlo.
  1. Selezionare un modello e quindi la **scheda Spiegazioni** per visualizzare il dashboard delle spiegazioni.

## <a name="interpretability-at-inference-time"></a>Interpretabilità in fase di inferenza

È possibile distribuire lo strumento di spiegazione insieme al modello originale e usarlo in fase di inferenza per fornire i singoli valori di importanza delle caratteristiche (spiegazione locale) per qualsiasi nuovo punto dati. Sono anche disponibili spiegazioni per il punteggio più leggero per migliorare le prestazioni di interpretabilità in fase di inferenza, attualmente supportata solo in Azure Machine Learning SDK. Il processo di distribuzione di uno spiegatore di assegnazione dei punteggi più leggero è simile alla distribuzione di un modello e include i passaggi seguenti:

1. Creare un oggetto spiegazione. Ad esempio, è possibile usare `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Creare uno spiegatore di assegnazione dei punteggi con l'oggetto spiegazione.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurare e registrare un'immagine che usa il modello di spiegazione dei punteggi.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Come passaggio facoltativo, è possibile recuperare lo spiegazione dei punteggi dal cloud e testare le spiegazioni.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuire l'immagine in una destinazione di calcolo seguendo questa procedura:

   1. Se necessario, registrare il modello di stima originale seguendo la procedura descritta in Distribuire [modelli con Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Creare un file di assegnazione dei punteggi.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Definire la configurazione della distribuzione.

         Questa configurazione dipende dai requisiti del modello. L'esempio seguente definisce una configurazione che usa un core CPU e un GB di memoria.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Creare un file con dipendenze dell'ambiente.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Creare un dockerfile personalizzato con g++ installato.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Distribuire l'immagine creata.
   
         Questo processo richiede circa cinque minuti.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Testare la distribuzione.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Eseguire la pulizia.

   Per eliminare un servizio Web distribuito, usare `service.delete()`.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Dati di tipo sparse** non supportati: il dashboard di spiegazione del modello si interrompe o rallenta in modo sostanziale con un numero elevato di funzionalità, pertanto attualmente non è supportato il formato di dati di tipo sparse. Inoltre, si verificano problemi di memoria generali con set di dati di grandi dimensioni e un numero elevato di funzionalità. 

* Modelli di previsione non supportati con spiegazioni del **modello:** l'interpretabilità, la migliore spiegazione del modello, non è disponibile per gli esperimenti di previsione AutoML che consigliano gli algoritmi seguenti come modello migliore: TCNForecaster, AutoArima, ExponentialSmoothing, Average, Naive, Seasonal Average e Seasonal Naive. La previsione AutoML include modelli di regressione che supportano le spiegazioni. Tuttavia, nel dashboard di spiegazione, la scheda "Importanza delle singole funzionalità" non è supportata solo per la previsione a causa della complessità nelle pipeline di dati.

* **Spiegazione** locale per l'indice dei dati: il dashboard di spiegazione non supporta la relazione tra i valori di importanza locale e un identificatore di riga del set di dati di convalida originale se tale set di dati è maggiore di 5000 punti dati quando il dashboard esegue in modo casuale il campionamento dei dati. Tuttavia, il dashboard mostra i valori delle funzionalità del set di dati non elaborati per ogni punto dati passato nel dashboard nella scheda Priorità singola funzionalità. Gli utenti possono eseguire il mapping delle priorità locali al set di dati originale tramite la corrispondenza dei valori delle funzionalità del set di dati non elaborati. Se le dimensioni del set di dati di convalida sono inferiori a 5000 esempi, la funzionalità in AzureML Studio corrisponderà all'indice nel `index` set di dati di convalida.

* Tracciati di **what-if/ICE** non supportati in Studio: i tracciati What-If e Ice (Individual Conditional Expectation) non sono supportati in studio di Azure Machine Learning nella scheda Spiegazioni perché la spiegazione caricata richiede un calcolo attivo per ricalcolare le stime e le probabilità di caratteristiche perturbate. È attualmente supportata nei notebook di Jupyter quando viene eseguita come widget usando l'SDK.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'interpretabilità dei modelli](how-to-machine-learning-interpretability.md)

[Vedere i Azure Machine Learning notebook di esempio di interpretabilità](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
