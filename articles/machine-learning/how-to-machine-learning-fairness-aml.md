---
title: Valutazione dell'equità dei modelli ML in Python (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come valutare e mitigare l'equità dei modelli di apprendimento automatico usando Fairlearn e il Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: ee589aed2b8f3c53d21c1f762a5d2027f3762649
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222769"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Usare Azure Machine Learning con il pacchetto open source Fairlearn per valutare l'equità dei modelli di Machine Learning (anteprima)

In questa guida dettagliata si apprenderà come usare il pacchetto python open source di [Fairlearn](https://fairlearn.github.io/) con Azure Machine Learning per eseguire le attività seguenti:

* Valutare l'equità delle stime del modello. Per altre informazioni sull'equità in Machine Learning, vedere l' [articolo sull'equità in Machine Learning](concept-fairness-ml.md).
* Consente di caricare, elencare e scaricare informazioni sull'equità della valutazione da e verso Azure Machine Learning Studio.
* Per interagire con le informazioni sull'equità dei modelli, vedere un dashboard di valutazione dell'equità in Azure Machine Learning Studio.

>[!NOTE]
> La valutazione dell'equità non è un esercizio puramente tecnico. **Questo pacchetto consente di valutare l'equità di un modello di apprendimento automatico, ma è possibile solo configurare e prendere decisioni relative al modo in cui il modello viene eseguito.**  Sebbene questo pacchetto consenta di identificare le metriche quantitative per valutare l'equità, gli sviluppatori di modelli di Machine Learning devono anche eseguire un'analisi qualitativa per valutare l'equità dei propri modelli.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning Equity SDK 

Il Azure Machine Learning Equity SDK, `azureml-contrib-fairness` , integra il pacchetto python open source, [Fairlearn](http://fairlearn.github.io), all'interno di Azure Machine Learning. Per altre informazioni sull'integrazione di Fairlearn in Azure Machine Learning, vedere questi [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Per ulteriori informazioni su Fairlearn, vedere la [Guida di esempio](https://fairlearn.github.io/master/auto_examples/) e i [notebook di esempio](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Usare i comandi seguenti per installare i `azureml-contrib-fairness` `fairlearn` pacchetti e:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Le versioni successive di Fairlearn dovrebbero funzionare anche nel codice di esempio seguente.



## <a name="upload-fairness-insights-for-a-single-model"></a>Caricare informazioni sull'equità per un singolo modello

Nell'esempio seguente viene illustrato come utilizzare il pacchetto di correttezza. Le informazioni dettagliate sull'equità del modello vengono caricate in Azure Machine Learning e viene visualizzato il dashboard di valutazione dell'equità in Azure Machine Learning Studio.

1. Eseguire il training di un modello di esempio in Jupyter Notebook. 

    Per il set di dati viene usato il noto set di dati del censimento per adulti, che viene recuperato da OpenML. Si Finge di avere un problema di decisione di prestito con l'etichetta che indica se un utente ha ripagato un prestito precedente. Si eseguirà il training di un modello per prevedere se gli utenti precedentemente non visti ripagheranno un prestito. Un modello di questo tipo può essere usato per prendere decisioni di prestito.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Accedere a Azure Machine Learning e registrare il modello.
   
    Il dashboard di equità può integrarsi con i modelli registrati o non registrati. Registrare il modello in Azure Machine Learning con i passaggi seguenti:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Pre-calcola le metriche di equità.

    Creare un dizionario del dashboard usando il `metrics` pacchetto di Fairlearn. Il `_create_group_metric_set` metodo ha argomenti simili al costruttore del dashboard, ad eccezione del fatto che le funzionalità sensibili vengono passate come dizionario (per garantire che i nomi siano disponibili). È inoltre necessario specificare il tipo di stima (classificazione binaria in questo caso) quando si chiama questo metodo.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Caricare le metriche di equità pre-calcolate.
    
    Importare ora `azureml.contrib.fairness` il pacchetto per eseguire il caricamento:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Creare un esperimento, quindi un'esecuzione e caricarvi il Dashboard:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Verificare il dashboard di equità da Azure Machine Learning Studio

    Se si completano i passaggi precedenti (caricamento di informazioni sull'equità generate per Azure Machine Learning), è possibile visualizzare il dashboard di equità in [Azure Machine Learning Studio](https://ml.azure.com). Questo dashboard è lo stesso dashboard di visualizzazione fornito in Fairlearn, che consente di analizzare le differenze tra i sottogruppi della funzionalità sensibile, ad esempio, maschio e femmina.
    Seguire uno di questi percorsi per accedere al dashboard di visualizzazione in Azure Machine Learning Studio:

    * **Riquadro esperimenti (anteprima)**
    1. Selezionare **Experiments (esperimenti** ) nel riquadro a sinistra per visualizzare un elenco di esperimenti eseguiti in Azure Machine Learning.
    1. Selezionare un esperimento specifico per visualizzare tutte le esecuzioni dell'esperimento.
    1. Selezionare un'esecuzione e quindi la scheda **equità** per visualizzare il dashboard spiegazione.


    [![Dashboard di equità](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Riquadro modelli**
    1. Se il modello originale è stato registrato seguendo i passaggi precedenti, è possibile selezionare **modelli** nel riquadro a sinistra per visualizzarlo.
    1. Selezionare un modello e quindi la scheda **equità** per visualizzare il dashboard di visualizzazione spiegazione.

    Per altre informazioni sul dashboard di visualizzazione e sul contenuto, vedere il [manuale dell'utente](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)di Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Caricare informazioni sull'equità per più modelli

Per confrontare più modelli e vedere in che modo le loro valutazioni sull'equità sono diverse, è possibile passare più di un modello al dashboard di visualizzazione e confrontare i compromessi per l'equità delle prestazioni.

1. Eseguire il training dei modelli:
    
    Viene ora creato un secondo classificatore, basato su uno strumento di stima del vettore di supporto e viene caricato un dizionario di dashboard equità usando il pacchetto di Fairlearn `metrics` . Si presuppone che il modello sottoposto a training precedente sia ancora disponibile.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Registrare i modelli

    Registrare quindi entrambi i modelli all'interno Azure Machine Learning. Per praticità, archiviare i risultati in un dizionario, che esegue il mapping dell'oggetto `id` del modello registrato (una stringa nel `name:version` formato) al predittore stesso:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Caricare il dashboard di Fairlearn localmente

    Prima di caricare le informazioni sull'equità in Azure Machine Learning, è possibile esaminare queste stime in un dashboard Fairlearn richiamato localmente. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Pre-calcola le metriche di equità.

    Creare un dizionario del dashboard usando il `metrics` pacchetto di Fairlearn.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Caricare le metriche di equità pre-calcolate.
    
    Importare ora `azureml.contrib.fairness` il pacchetto per eseguire il caricamento:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Creare un esperimento, quindi un'esecuzione e caricarvi il Dashboard:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Analogamente alla sezione precedente, è possibile seguire uno dei percorsi descritti in precedenza (tramite **esperimenti** o **modelli**) in Azure Machine Learning Studio per accedere al dashboard di visualizzazione e confrontare i due modelli in termini di equità e prestazioni.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Caricare informazioni dettagliate sull'equità non mitigate e mitigate

È possibile usare gli [algoritmi di mitigazione](https://fairlearn.github.io/master/user_guide/mitigation.html)di Fairlearn, confrontare i modelli di mitigazione generati con il modello originale non mitigato ed esplorare i compromessi di prestazioni/equità tra i modelli confrontati.

Per un esempio in cui viene illustrato l'uso dell'algoritmo di mitigazione della [ricerca Grid](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (che crea una raccolta di modelli mitigati con diversi compromessi in materia di equità e prestazioni), vedere questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Il caricamento delle informazioni sull'equità di più modelli in un'unica esecuzione consente il confronto dei modelli rispetto all'equità e alle prestazioni. È possibile fare clic su uno dei modelli visualizzati nel grafico di confronto del modello per visualizzare informazioni dettagliate sull'equità del modello specifico.


[![Dashboard di confronto del modello Fairlearn](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più sull'equità dei modelli](concept-fairness-ml.md)

[Estrai notebook di esempio Azure Machine Learning equità](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
