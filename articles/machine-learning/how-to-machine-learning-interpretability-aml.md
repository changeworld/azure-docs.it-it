---
title: Interpretare & illustrare i modelli di ML in Python (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni sul modo in cui il modello di Machine Learning determina l'importanza della funzionalità ed esegue stime quando si usa il Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 74ddaaf7a2d279439c0cd27ba0840f02f297877b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119561"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Usare il pacchetto di interpretazione per spiegare i modelli ML & le stime in Python (anteprima)



In questa guida dettagliata viene illustrato come usare il pacchetto di interpretazione del Azure Machine Learning Python SDK per eseguire le attività seguenti:


* Spiegare in locale l'intero comportamento del modello o le singole stime nel computer personale.

* Abilitare le tecniche di interpretazione per le funzionalità progettate.

* Illustra il comportamento per l'intero modello e per le singole stime in Azure.

* Usare un dashboard di visualizzazione per interagire con le spiegazioni del modello.

* Distribuire un Explainer di assegnazione dei punteggi insieme al modello per osservare le spiegazioni durante l'inferenza.



Per altre informazioni sulle tecniche di interpretazione e sui modelli di apprendimento automatico supportati, vedere interpretazione dei modelli [in Azure Machine Learning](how-to-machine-learning-interpretability.md) e [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Genera il valore di importanza della funzionalità nel computer personale 
L'esempio seguente illustra come usare il pacchetto di interpretazione nel computer personale senza contattare i servizi di Azure.

1. Installare il pacchetto `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Eseguire il training di un modello di esempio in un Jupyter Notebook locale.

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

3. Chiamare il Explainer in locale.
   * Per inizializzare un oggetto Explainer, passare il modello e alcuni dati di training al costruttore del sistema di spiegazione.
   * Per rendere più istruttive le spiegazioni e le visualizzazioni, è possibile scegliere di passare i nomi delle funzionalità e i nomi delle classi di output se si esegue la classificazione.

   I blocchi di codice seguenti illustrano come creare un'istanza di un oggetto Explainer con `TabularExplainer` , `MimicExplainer` e `PFIExplainer` localmente.
   * `TabularExplainer` chiama uno dei tre Explainer SHAP sotto ( `TreeExplainer` , `DeepExplainer` o `KernelExplainer` ).
   * `TabularExplainer` Seleziona automaticamente quello più appropriato per il caso d'uso, ma è possibile chiamare direttamente ciascuno dei tre Explainer sottostanti.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Illustrare l'intero comportamento del modello (spiegazione globale) 

Vedere l'esempio seguente per ottenere i valori di importanza della funzionalità di aggregazione (globale).

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Descrizione di una singola stima (spiegazione locale)
Ottenere i valori di importanza delle singole funzionalità di punti di riferimento diversi chiamando le spiegazioni per una singola istanza o un gruppo di istanze.
> [!NOTE]
> `PFIExplainer` non supporta le spiegazioni locali.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Trasformazioni di funzionalità non elaborate

È possibile scegliere di ottenere spiegazioni in termini di funzionalità non elaborate e non trasformate piuttosto che di funzioni progettate. Per questa opzione, è possibile passare la pipeline di trasformazione della funzionalità a Explainer in `train_explain.py` . In caso contrario, il Explainer fornisce spiegazioni in termini di funzionalità progettate.

Il formato delle trasformazioni supportate è identico a quello descritto in [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). In generale, tutte le trasformazioni sono supportate finché operano su una singola colonna, in modo che sia chiaro che sono uno-a-molti.

Ottenere una spiegazione per le funzionalità RAW usando un `sklearn.compose.ColumnTransformer` o un elenco di Tuple Transformer adattate. Nell'esempio seguente viene utilizzato `sklearn.compose.ColumnTransformer` .

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

Se si vuole eseguire l'esempio con l'elenco delle tuple di Transformer adattate, usare il codice seguente:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generare valori di importanza della funzionalità tramite esecuzioni remote

Nell'esempio seguente viene illustrato come è possibile utilizzare la `ExplanationClient` classe per abilitare l'interpretazione del modello per le esecuzioni remote. È concettualmente simile al processo locale, ad eccezione del fatto che:

* Utilizzare il `ExplanationClient` nell'esecuzione remota per caricare il contesto di interpretazione.
* Scaricare il contesto in un secondo momento in un ambiente locale.

1. Installare il pacchetto `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Creare uno script di training in un Jupyter Notebook locale. Ad esempio: `train_explain.py`.

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

1. Configurare un Azure Machine Learning calcolo come destinazione di calcolo e inviare l'esecuzione del training. Per istruzioni, vedere [creare e gestire cluster di calcolo Azure Machine Learning](how-to-create-attach-compute-cluster.md) . È anche possibile trovare i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) utili.

1. Scaricare la spiegazione nel Jupyter Notebook locale.

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

Dopo aver scaricato le spiegazioni nel Jupyter Notebook locale, è possibile usare il dashboard di visualizzazione per comprendere e interpretare il modello. Per caricare il widget del dashboard di visualizzazione nella Jupyter Notebook, usare il codice seguente:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

La visualizzazione supporta le spiegazioni sulle funzionalità progettate e non elaborate. Le spiegazioni non elaborate sono basate sulle funzionalità del set di dati originale e le spiegazioni progettate sono basate sulle funzionalità del set di dati con la progettazione delle funzionalità applicata.

Quando si tenta di interpretare un modello rispetto al set di dati originale, è consigliabile utilizzare spiegazioni non elaborate, perché ogni importanza della funzionalità corrisponderà a una colonna del set di dati originale. Uno scenario in cui le spiegazioni progettate potrebbero essere utili quando si esamina l'effetto delle singole categorie da una funzionalità categorica. Se una codifica One-Hot viene applicata a una funzionalità categorica, le spiegazioni derivate per la progettazione includeranno un valore di importanza differente per categoria, una per ogni funzionalità di ingegneria a caldo. Questa operazione può essere utile quando si restringe la parte del set di dati più informativa sul modello.

> [!NOTE]
> Le spiegazioni progettate e non elaborate vengono calcolate in modo sequenziale. Viene creata prima di tutto una spiegazione progettata in base al modello e alla pipeline conteggi. Quindi, la spiegazione non elaborata viene creata in base a questa spiegazione progettata aggregando l'importanza delle funzionalità progettate che provengono dalla stessa funzionalità non elaborata.

### <a name="create-edit-and-view-dataset-cohorts"></a>Creare, modificare e visualizzare le coorti di set di dati

La barra multifunzione superiore mostra le statistiche complessive sul modello e sui dati. È possibile sezionare i dati in coorti di set di dati, o sottogruppi, per analizzare o confrontare le prestazioni e le spiegazioni del modello in questi sottogruppi definiti. Confrontando le statistiche e le spiegazioni dei set di dati in questi sottogruppi, è possibile capire il motivo per cui si verificano gli errori in un gruppo rispetto a un altro.

[![Creazione, modifica e visualizzazione di coorti di set di dati](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Informazioni sul comportamento del modello completo (spiegazione globale) 

Le prime tre schede del dashboard spiegazione forniscono un'analisi complessiva del modello sottoposto a training insieme alle relative stime e spiegazioni.

#### <a name="model-performance"></a>Prestazioni del modello
Valutare le prestazioni del modello esplorando la distribuzione dei valori di stima e i valori delle metriche delle prestazioni del modello. È possibile esaminare ulteriormente il modello esaminando un'analisi comparativa delle prestazioni tra coorti o sottogruppi diversi del set di dati. Selezionare i filtri lungo il valore y e il valore x per tagliarli tra dimensioni diverse. Visualizzare le metriche, ad esempio accuratezza, precisione, richiamo, tasso falso positivo (ad) e frequenza negativa falsa (FNR).

[![Scheda prestazioni modello nella visualizzazione spiegazione](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Esplora DataSet
Esplorare le statistiche del set di dati selezionando filtri diversi lungo gli assi X, Y e colore per sezionare i dati in base a dimensioni diverse. Creare le coorti del set di dati per analizzare le statistiche dei set di dati con filtri quali risultato stimato, funzionalità del set di dati e gruppi di errori. Usare l'icona a forma di ingranaggio nell'angolo superiore destro del grafico per modificare i tipi di grafico.

[![Scheda Esplora DataSet nella visualizzazione spiegazione](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Importanza della funzionalità di aggregazione
Esplora le principali funzionalità importanti che influiscano sulle stime complessive del modello (note anche come spiegazione globale). Utilizzare il dispositivo di scorrimento per visualizzare i valori di importanza della funzionalità decrescente. Selezionare fino a tre coorti per visualizzare i valori di importanza della funzionalità side-by-side. Fare clic su una delle barre delle funzionalità nel grafico per visualizzare il modo in cui i valori della funzionalità selezionata hanno un effetto sulla stima del modello nel tracciato di dipendenza riportato di seguito.

[![Scheda importanza funzionalità di aggregazione nella visualizzazione spiegazione](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Informazioni sulle singole stime (spiegazione locale) 

La quarta scheda della scheda spiegazione consente di eseguire il drill-down di un singolo punto di interesse e delle rispettive caratteristiche. È possibile caricare il tracciato di importanza singola funzionalità per qualsiasi punto dati facendo clic su uno dei singoli punti dati nel grafico a dispersione principale o selezionando un punto dati specifico nella creazione guidata pannello a destra.

|Grafico|Descrizione|
|----|-----------|
|Importanza della singola funzionalità|Mostra le principali funzionalità importanti per una singola stima. Consente di illustrare il comportamento locale del modello sottostante in un punto dati specifico.|
|Analisi What-If|Consente di modificare i valori delle funzionalità del punto dati reale selezionato e osservare le modifiche risultanti al valore di stima generando un punto dati ipotetico con i nuovi valori della funzionalità.|
|Singola previsione condizionale (ICE)|Consente di modificare il valore della funzionalità da un valore minimo a un valore massimo. Consente di illustrare le modifiche apportate alla stima del punto dati quando una funzionalità viene modificata.|

[![Importanza delle singole funzionalità e scheda simulazione in Dashboard spiegazione](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Si tratta di spiegazioni basate su molte approssimazioni e non sono la "cause" delle stime. Senza una rigida solidità matematica dell'inferenza causale, gli utenti non sono tenuti a prendere decisioni reali in base alle perturbazioni delle funzionalità dello strumento What-If. Questo strumento è principalmente per la comprensione del modello e del debug.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualizzazione in Azure Machine Learning Studio

Se si completano i passaggi di [interpretazione remota](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (caricando la spiegazione generata per Azure Machine Learning cronologia di esecuzione), è possibile visualizzare il dashboard di visualizzazione in [Azure Machine Learning Studio](https://ml.azure.com). Questo dashboard è una versione più semplice del dashboard di visualizzazione illustrato in precedenza. What-If i tracciati di generazione e di data point sono disabilitati perché non sono presenti risorse di calcolo attive in Azure Machine Learning Studio in grado di eseguire i calcoli in tempo reale.

Se sono disponibili le descrizioni del set di dati, globali e locali, i dati popolano tutte le schede. Se è disponibile solo una spiegazione globale, la scheda priorità singola funzionalità verrà disabilitata.

Seguire uno di questi percorsi per accedere al dashboard di visualizzazione in Azure Machine Learning Studio:

* Riquadro **esperimenti** (anteprima)
  1. Selezionare **Experiments (esperimenti** ) nel riquadro a sinistra per visualizzare un elenco di esperimenti eseguiti in Azure Machine Learning.
  1. Selezionare un esperimento specifico per visualizzare tutte le esecuzioni dell'esperimento.
  1. Selezionare un'esecuzione e quindi la scheda **spiegazioni** per il dashboard di visualizzazione spiegazione.

   [![Dashboard di visualizzazione con importanza della funzionalità di aggregazione in AzureML studio negli esperimenti](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Riquadro **modelli**
  1. Se il modello originale è stato registrato seguendo la procedura descritta in [distribuire modelli con Azure Machine Learning](./how-to-deploy-and-where.md), è possibile selezionare **modelli** nel riquadro a sinistra per visualizzarlo.
  1. Selezionare un modello e quindi la scheda **spiegazioni** per visualizzare il dashboard di visualizzazione spiegazione.

## <a name="interpretability-at-inference-time"></a>Interpretazione in fase di inferenza

È possibile distribuire il Explainer insieme al modello originale e usarlo in fase di inferenza per fornire i singoli valori di importanza della funzionalità (spiegazione locale) per qualsiasi nuovo elemento DataPoint. Sono inoltre disponibili spiegatori per il punteggio più leggeri per migliorare le prestazioni di interpretazione in fase di inferenza, attualmente supportata solo in Azure Machine Learning SDK. Il processo di distribuzione di un Explainer con punteggio più chiaro è simile alla distribuzione di un modello e include i passaggi seguenti:

1. Creare un oggetto spiegazione. Ad esempio, è possibile usare `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Creare una descrizione del punteggio con l'oggetto spiegazione.

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

1. Come passaggio facoltativo, è possibile recuperare la descrizione del punteggio dal cloud e testare le spiegazioni.

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

1. Distribuire l'immagine in una destinazione di calcolo attenendosi alla procedura seguente:

   1. Se necessario, registrare il modello di stima originale seguendo la procedura descritta in [distribuire modelli con Azure Machine Learning](./how-to-deploy-and-where.md).

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

         Questa configurazione dipende dai requisiti del modello. Nell'esempio seguente viene definita una configurazione che utilizza un core CPU e un GB di memoria.

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

   1. Creare un dockerfile personalizzato con g + + installato.

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

* **Dati di tipo sparse non supportati**: il dashboard di spiegazione del modello si interrompe in modo sostanziale con un numero elevato di funzionalità, pertanto attualmente non è supportato il formato dati sparse. Inoltre, i problemi generali di memoria si verificano con set di impostazioni di grandi dimensioni e un numero elevato di funzionalità. 

* **Modelli di previsione non supportati con le spiegazioni del modello**: l'interpretazione, la migliore spiegazione del modello, non è disponibile per gli esperimenti di previsione AutoML che suggeriscono gli algoritmi seguenti come modello ottimale: TCNForecaster, autoarima, Prophet, ExponentialSmoothing, Average, Naive, Seasonal Average e Seasonal Naive. La previsione di AutoML include modelli di regressione che supportano le spiegazioni. Tuttavia, nel dashboard spiegazione, la scheda "importanza singola funzionalità" non è supportata per la previsione a causa della complessità delle pipeline di dati.

* **Spiegazione locale per l'indice dati**: il dashboard spiegazione non supporta i valori di importanza locale per un identificatore di riga dal set di dati di convalida originale se il set di dati è maggiore di 5000 punti dati perché il dashboard downsampling delle in modo casuale i dati. Tuttavia, il dashboard Mostra i valori delle funzionalità del set di dati non elaborati per ogni punto dati passato nel dashboard nella scheda relativa all'importanza delle singole funzionalità. Gli utenti possono mappare le priorità locali al set di dati originale tramite la corrispondenza dei valori delle funzionalità del set di dati non elaborati. Se la dimensione del set di dati di convalida è inferiore a 5000 esempi, la `index` funzionalità in AzureML Studio corrisponderà all'indice nel set di dati di convalida.

* **Tracciati di simulazione e di ghiaccio non supportati in studio**: What-If e i tracciati di singoli aspetti condizionali (Ice) non sono supportati in Azure Machine Learning Studio nella scheda spiegazioni perché la spiegazione caricata richiede un calcolo attivo per ricalcolare le stime e le probabilità di funzionalità perturbate. È attualmente supportato nei notebook di Jupyter quando viene eseguito come widget usando l'SDK.


## <a name="next-steps"></a>Passaggi successivi

[Scopri di più sull'interpretazione dei modelli](how-to-machine-learning-interpretability.md)

[Estrarre Azure Machine Learning notebook di esempio di interpretazione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
