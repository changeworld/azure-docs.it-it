---
title: Creare esperimenti di Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come definire origini dati, calcoli e impostazioni di configurazione per gli esperimenti di Machine Learning automatizzati.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: d0a15b16c04a28bcc67caeeceedfcbad485b7157
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861464"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare esperimenti di ML automatizzato in Python


Questa guida illustra come definire diverse impostazioni di configurazione degli esperimenti di Machine Learning automatizzato con [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro). Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per un esempio end-to-end di un esperimento di Machine Learning automatizzato, vedere Esercitazione: Eseguire il training di un modello di classificazione [con Machine Learning automatizzato.](tutorial-auto-train-models.md)

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Classificazione, Regressione o Previsione serie temporale
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se si preferisce un'esperienza senza codice, è anche possibile [Creare gli esperimenti di Machine Learning automatizzato in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario, 
* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

* Il Azure Machine Learning Python SDK installato.
    Per installare l'SDK è possibile, 
    * Creare un'istanza di calcolo, che installa automaticamente l'SDK ed è preconfigurata per i flussi di lavoro di Machine Learning. Per [altre informazioni, vedere Creare](how-to-create-manage-compute-instance.md) e gestire Azure Machine Learning di calcolo. 

    * [Installare il `automl` pacchetto manualmente,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)che include [l'installazione predefinita](/python/api/overview/azure/ml/install#default-install) dell'SDK.
    
    > [!WARNING]
    > Python 3.8 non è compatibile con `automl` . 

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Machine Learning automatizzato supporta i tipi di attività `classification` , `regression` e `forecasting` . Altre informazioni sui [tipi di attività](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Il codice seguente usa il `task` parametro nel costruttore per specificare il tipo di esperimento come `AutoMLConfig` `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato

Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in un **dataframe Pandas** o in un **TabularDataset di Azure Machine Learning**. [Altre informazioni sui set di dati](how-to-create-register-datasets.md).

Requisiti per il training dei dati in Machine Learning:
- I dati devono essere in formato tabulare.
- Il valore da stimare, la colonna di destinazione, deve essere presente nei dati.

**Per gli esperimenti remoti,** i dati di training devono essere accessibili dal calcolo remoto. AutoML accetta solo [set di dati tabulari di Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset) quando si lavora su un calcolo remoto. 

I seti di dati di Azure Machine Learning espongono la funzionalità per:

* Trasferire facilmente i dati da file statici o origini URL nell'area di lavoro.
* rendere i dati disponibili per gli script di training durante l'esecuzione in risorse di calcolo sul cloud. Per [un esempio dell'uso](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) della classe per montare i dati nella destinazione di calcolo remota, vedere Come eseguire il training con i set di `Dataset` dati.

Il codice seguente crea un oggetto TabularDataset da un URL Web. Vedere [Creare un oggetto TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) per esempi di codice su come creare set di dati da altre origini, ad esempio file locali e archivi dati.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Per gli esperimenti di calcolo locali,** è consigliabile usare i dataframe pandas per tempi di elaborazione più rapidi.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Dati di training, convalida e test

È possibile specificare set di **dati di training e di convalida** separati direttamente nel `AutoMLConfig` costruttore. Altre informazioni su [come configurare le suddivisioni dei dati e la convalida incrociata](how-to-configure-cross-validation-data-splits.md) per gli esperimenti AutoML. 

Se non si specifica in modo esplicito un parametro o , Machine Learning automatizzato applica tecniche `validation_data` `n_cross_validation` predefinite per determinare come viene eseguita la convalida. Questa determinazione dipende dal numero di righe nel set di dati assegnato al `training_data` parametro. 

|Dimensioni &nbsp; dei dati di &nbsp; training| Tecnica di convalida |
|---|-----|
|**Più &nbsp; di &nbsp; 20.000 &nbsp; righe**| Viene applicata la suddivisione dei dati di training/convalida. L'impostazione predefinita accetta il 10% del set di dati di training iniziale come set di convalida. A sua volta, tale set di convalida viene usato per il calcolo delle metriche.
|**Minore &nbsp; di &nbsp; 20.000 &nbsp; righe**| Viene applicato l'approccio di convalida incrociata. Il numero predefinito di fold dipende dal numero di righe. <br> **Se il set di dati è minore di 1.000 righe,** vengono usate 10 fold. <br> **Se le righe sono tra 1.000 e 20.000,** vengono usate tre volte.

A questo momento, è necessario fornire dati di **test personalizzati per** la valutazione del modello. Per un esempio di codice che illustra come portare i propri dati di test per la valutazione del modello, vedere la **sezione Test** di questo notebook [di Jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti. Informazioni su [vantaggi e svantaggi delle opzioni di calcolo in locale e remoto](concept-automated-ml.md#local-remote). 

* Computer **locale,** ad esempio un desktop o un portatile locale: in genere quando si ha un set di dati di piccole dimensioni e si è ancora in fase di esplorazione. Vedere [questo notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) per un esempio di calcolo locale. 
 
* Un **computer** remoto nel cloud Azure Machine Learning [Managed Compute](concept-compute-target.md#amlcompute) è un servizio gestito che consente di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure. 

    Vedere [questo notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) per un esempio in remoto con il calcolo gestito di Azure Machine Learning. 

* Un **Azure Databricks cluster nella** sottoscrizione di Azure. Per altri dettagli, vedere Configurare un cluster Azure Databricks per Machine Learning [automatizzato.](how-to-configure-databricks-automl-environment.md) Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) per esempi di notebook con Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni di esperimento

Per configurare l'esperimento di Machine Learning automatizzato sono disponibili varie opzioni. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`. Vedere la [Classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) per un elenco completo dei parametri.

Di seguito sono riportati alcuni esempi:

1. Esperimento di classificazione con area sotto la curva (AUC) ponderata come metrica primaria con minuti di timeout dell'esperimento impostati su 30 minuti e due riduzioni di convalida incrociata.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. L'esempio seguente è un esperimento di regressione impostato per terminare dopo 60 minuti con cinque sezioni incrociate di convalida.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Le attività di previsione richiedono una configurazione aggiuntiva. Per altre informazioni, vedere [l'articolo Eseguire il training](how-to-auto-train-forecast.md) automatico di un modello di previsione di serie tempormente. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Modelli supportati

Il Machine Learning automatizzato prova diversi modelli e algoritmi durante il processo di automazione e ottimizzazione. Come utente, non è necessario specificare l'algoritmo. 

I tre diversi `task` valori dei parametri determinano l'elenco di algoritmi, o modelli, da applicare. Usare i parametri `allowed_models` o `blocked_models` per modificare ulteriormente le iterazioni con i modelli disponibili da includere o escludere. 

Nella tabella seguente vengono riepilogati i modelli supportati in base al tipo di attività. 

> [!NOTE]
> Se si prevede di esportare i modelli di Machine Learning automatizzato creati in un [modello ONNX](concept-onnx.md), solo gli algoritmi indicati con * possono essere converti nel formato ONNX. Altre informazioni sulla [conversione di modelli in ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Si noti anche che in questo momento ONNX supporta solo le attività di classificazione e regressione. 

Classificazione | Regressione | Previsione serie temporale
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Averaged Perceptron Classifier](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online Gradient Descent Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast Linear Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linear SVM Classifier](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Metrica primaria
Il `primary metric` parametro determina la metrica da usare durante il training del modello per l'ottimizzazione. Le metriche disponibili che è possibile selezionare sono determinate dal tipo di attività selezionato. La tabella seguente mostra le metriche primarie valide per ogni tipo di attività.

La scelta di una metrica primaria per l'ottimizzazione di Machine Learning automatizzato dipende da molti fattori. È consigliabile scegliere una metrica che rappresenti meglio le esigenze aziendali. Valutare quindi se la metrica è adatta per il profilo del set di dati (dimensioni dei dati, intervallo, distribuzione delle classi e così via).

Per informazioni sulle definizioni specifiche di queste metriche, vedere [Risultati del Machine Learning automatizzato](how-to-understand-automated-ml.md).

|Classificazione | Regressione | Previsione di serie temporali
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Metriche principali per gli scenari di classificazione 

Le metriche post-soglia, ad esempio , , e potrebbero non essere ottimizzate anche per i set di dati di piccole dimensioni, con asimmetria di classe molto grande (sbilanciamento della classe) o quando il valore della metrica previsto è molto vicino a `accuracy` `average_precision_score_weighted` `norm_macro_recall` `precision_score_weighted` 0,0 o 1,0. In questi casi, `AUC_weighted` può essere una scelta migliore per la metrica primaria. Al termine dell'apprendimento automatico, è possibile scegliere il modello di successo in base alla metrica più adatta alle esigenze aziendali.

| Metrica | Casi d'uso di esempio |
| ------ | ------- |
| `accuracy` | Classificazione delle immagini, analisi del sentiment, previsione della varianza |
| `AUC_weighted` | Rilevamento di frodi, classificazione delle immagini, rilevamento anomalie/spam |
| `average_precision_score_weighted` | Analisi del sentiment |
| `norm_macro_recall` | Previsione della varianza |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Metriche principali per gli scenari di regressione

Metriche come e possono rappresentare meglio la qualità del modello quando la scala del valore da stimare copre `r2_score` `spearman_correlation` molti ordini di grandezza. Ad esempio, la stima dello stipendio, in cui molte persone hanno uno stipendio da 20.000 a 100.000 dollari, ma la scala è molto elevata con alcune retribuzioni nell'intervallo di $ 100 milioni. 

`normalized_mean_absolute_error` e in questo caso tratta un errore di stima di $ 20.000 allo stesso modo per un ruolo di lavoro con uno stipendio di 30.000 dollari come un dipendente che effettua `normalized_root_mean_squared_error` 20 milioni di dollari. In realtà, prevedere solo $ 20.000 da uno stipendio di 20 milioni di dollari è molto vicino (una piccola differenza relativa dello 0,1%), mentre $20.000 da $ 30.000 non è vicino (una differenza relativa del 67% grande). `normalized_mean_absolute_error` e `normalized_root_mean_squared_error` sono utili quando i valori da stimare hanno una scala simile.

| Metrica | Casi d'uso di esempio |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Stima dei prezzi (house/product/tip), Review score prediction (Stima del punteggio di revisione) |
| `r2_score` | Ritardo della compagnia aerea, Stima dello stipendio, Tempo di risoluzione dei bug |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Metriche principali per gli scenari di previsione delle serie tempose

Vedere le note di regressione riportate sopra.

| Metrica | Casi d'uso di esempio |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Stima dei prezzi (previsione), Ottimizzazione inventario, Previsione della domanda |
| `r2_score` | Stima dei prezzi (previsione), Ottimizzazione inventario, Previsione della domanda |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Definizione delle funzionalità dei dati

In ogni esperimento di Machine Learning automatizzato i dati vengono dimensionati e normalizzati automaticamente per *determinati* algoritmi sensibili a funzionalità su scale diverse. Questo ridimensionamento e normalizzazione viene definito featurization. Per altri dettagli ed esempi di codice, vedere [Featurization in AutoML.](how-to-configure-auto-features.md#) 

Quando si configurano gli esperimenti `AutoMLConfig` nell'oggetto , è possibile abilitare o disabilitare l'impostazione `featurization` . Nella tabella seguente vengono illustrate le impostazioni accettate per la funzionalità [nell'oggetto AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configurazione della definizione delle funzionalità | Descrizione |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica che i passaggi di [protezione dati e definizione delle funzionalità](how-to-configure-auto-features.md#featurization) vengono eseguiti automaticamente come parte della pre-elaborazione. **Impostazione predefinita di**.|
|`"featurization": 'off'`| Indica che il passaggio di featurizzazione non deve essere eseguito automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica che deve essere usata la definizione delle funzionalità personalizzata. [Informazioni su come personalizzare la definizione delle funzionalità](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> I passaggi di definizione delle funzionalità di Machine Learning automatizzato (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione dei valori di testo in formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di definizione delle funzionalità applicati durante il training vengono automaticamente applicati ai dati di input.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Configurazione Ensemble

I modelli di ensemble sono abilitati per impostazione predefinita e vengono visualizzati come iterazioni di esecuzione finali in un'esecuzione AutoML. Attualmente **sono supportati VotingEnsemble** e **StackEnsemble.** 

Il voto implementa il voto soft, che usa medie ponderate. L'implementazione dello stacking usa un'implementazione a due livelli, in cui il primo livello ha gli stessi modelli dell'insieme di voti e il secondo modello di livello viene usato per trovare la combinazione ottimale dei modelli dal primo livello. 

Se si usano modelli ONNX **o** si ha la spiegabilità del modello abilitata, l'impilamento è disabilitato e viene utilizzato solo il voto.

Il training dell'insieme può essere disabilitato usando i `enable_voting_ensemble` parametri `enable_stack_ensemble` booleani e .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Per modificare il comportamento predefinito dell'insieme, è possibile specificare più argomenti predefiniti `kwargs` come in un oggetto `AutoMLConfig` .

> [!IMPORTANT]
>  I parametri seguenti non sono parametri espliciti della classe AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Durante la generazione dei modelli **VotingEnsemble** e **StackEnsemble** vengono scaricati più modelli adattati delle esecuzioni figlio precedenti. Se si verifica questo errore: `AutoMLEnsembleException: Could not find any models for running ensembling`, potrebbe essere necessario fornire più tempo per il download dei modelli. Il valore predefinito è 300 secondi per il download di questi modelli in parallelo e non è previsto un limite massimo di timeout. Se è necessario più tempo, configurare questo parametro con un valore superiore a 300 secondi. 

  > [!NOTE]
  >  Se viene raggiunto il timeout e sono stati scaricati modelli, l'Ensemble procede con il numero di modelli scaricati. Non è necessario che tutti i modelli vengano scaricati per terminare prima del timeout.

I parametri seguenti si applicano solo ai modelli **StackEnsemble**: 

* `stack_meta_learner_type`: il meta-apprendimento è un modello di cui è stato eseguito il training nell'output dei singoli modelli eterogenei. I modelli di meta-apprendimento predefiniti sono `LogisticRegression` per le attività di classificazione (oppure `LogisticRegressionCV` se è abilitata la convalida incrociata) e `ElasticNet` per le attività di regressione o previsione (oppure `ElasticNetCV` se è abilitata la convalida incrociata). Questo parametro può essere una delle stringhe seguenti: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` o `LinearRegression`.

* `stack_meta_learner_train_percentage`: specifica la proporzione del set di training (quando si sceglie il tipo training e convalida) da riservare per il training del meta-apprendimento. Il valore predefinito è `0.2`. 

* `stack_meta_learner_kwargs`: parametri facoltativi da passare all'inizializzatore del meta-apprendimento. Questi parametri e tipi di parametro rispecchiano i parametri e i tipi di parametro del costruttore del modello corrispondente e vengono trasmessi al costruttore del modello.

Il codice seguente mostra un esempio di specifica di un comportamento Ensemble personalizzato in un oggetto `AutoMLConfig`.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Criteri uscita

Esistono alcune opzioni che è possibile definire in AutoMLConfig per terminare l'esperimento.

|Criteri| Descrizione
|----|----
Nessun &nbsp; criterio | Se non si definiscono parametri di uscita, l'esperimento continua fino a quando non viene effettuato alcun ulteriore avanzamento sulla metrica primaria.
Dopo &nbsp; un periodo di &nbsp; &nbsp; &nbsp; tempo| Usare `experiment_timeout_minutes` nelle impostazioni per definire per quanto tempo, in minuti, l'esperimento deve continuare a essere eseguito. <br><br> Per evitare errori di timeout dell'esperimento, è necessario almeno 15 minuti o 60 minuti se le dimensioni della riga per colonna superano i 10 milioni.
È &nbsp; stato raggiunto un &nbsp; &nbsp; &nbsp; punteggio| Usa `experiment_exit_score` completa l'esperimento dopo che è stato raggiunto un punteggio della metrica primaria specificato.

## <a name="run-experiment"></a>Eseguire esperimento

Per il Machine Learning automatizzato, si crea un oggetto `Experiment` che è un oggetto denominato in una `Workspace` usata per eseguire gli esperimenti.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Avviare l'esperimento per eseguire e generare un modello. Passare il `AutoMLConfig` al metodo `submit` per generare il modello.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Le dipendenze vengono prima installate in un nuovo computer.  Potrebbero occorrere fino a 10 minuti prima che venga visualizzato l'output.
>Se si imposta `show_output` su `True`, l'output viene visualizzato nella console.

### <a name="multiple-child-runs-on-clusters"></a>Più esecuzioni figlio nei cluster

Le esecuzioni figlio dell'esperimento di Machine Learning automatizzato possono essere eseguite in un cluster che esegue già un altro esperimento. Tuttavia, la tempistica dipende dal numero di nodi del cluster e dal fatto che tali nodi siano disponibili per eseguire un esperimento diverso.

Ogni nodo del cluster funge da singola macchina virtuale (VM) in grado di eseguire una singola esecuzione di training. per Machine Learning automatizzato significa un'esecuzione figlio. Se tutti i nodi sono occupati, il nuovo esperimento viene accodato. Tuttavia, se sono presenti nodi gratuiti, il nuovo esperimento eseguirà esecuzioni figlio automatizzate di Machine Learning in parallelo nei nodi o nelle macchine virtuali disponibili.

Per gestire le esecuzioni figlio e quando possono essere eseguite, è consigliabile creare un cluster dedicato per ogni esperimento e associare il numero dell'esperimento al numero di `max_concurrent_iterations` nodi nel cluster. In questo modo, si usano tutti i nodi del cluster contemporaneamente con il numero di esecuzioni/iterazioni figlio simultanee desiderate.

Configurare  `max_concurrent_iterations` `AutoMLConfig` nell'oggetto . Se non è configurato, per impostazione predefinita è consentita una sola esecuzione/iterazione figlio simultanea per ogni esperimento.  

## <a name="explore-models-and-metrics"></a>Esplorare modelli e metriche

Machine Learning automatizzato offre opzioni per monitorare e valutare i risultati del training. 

* È possibile visualizzare i risultati del training in un widget o inline in un notebook. Per [altri dettagli, vedere](#monitor) Monitorare le esecuzioni di Machine Learning automatizzato.

* Per definizioni ed esempi dei grafici delle prestazioni e delle metriche forniti per ogni esecuzione, vedere Valutare i risultati dell'esperimento [di Machine Learning automatizzato.](how-to-understand-automated-ml.md) 

* Per ottenere un riepilogo delle funzionalità e comprendere quali funzionalità sono state aggiunte a un particolare modello, vedere [Trasparenza della creazione di funzionalità.](how-to-configure-auto-features.md#featurization-transparency) 

È possibile visualizzare gli iperparamemi, le tecniche di ridimensionamento e normalizzazione e l'algoritmo applicato a una specifica esecuzione automatizzata di Machine Learning con la soluzione di codice personalizzata seguente. 

Di seguito viene definito il metodo personalizzato, , che stampa gli iperparamemi di ogni passaggio `print_model()` della pipeline di training automatizzato di Machine Learning.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

Per un'esecuzione locale o remota appena inviata ed sottoposta a training dallo stesso notebook dell'esperimento, è possibile passare il modello migliore usando il `get_output()` metodo . 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

L'output seguente indica che:
 
* La tecnica StandardScalerWrapper è stata usata per ridimensionare e normalizzare i dati prima del training.

* L'algoritmo XGBoostClassifier è stato identificato come l'esecuzione migliore e mostra anche i valori degli iperparameboli. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

Per un'esecuzione esistente da un esperimento diverso nell'area di lavoro, ottenere l'ID esecuzione specifico che si vuole esplorare e passarlo al `print_model()` metodo . 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> Gli algoritmi adottati da Machine Learning automatizzato hanno una casualità intrinseca che può causare lievi variazioni nel punteggio finale delle metriche di un modello consigliato, ad esempio l'accuratezza. Machine Learning automatizzato esegue anche operazioni sui dati, ad esempio la suddivisione del test di training, la suddivisione della convalida del training o la convalida incrociata, se necessario. Pertanto, se si esegue un esperimento con le stesse impostazioni di configurazione e la stessa metrica primaria più volte, è probabile che si vedano variazioni in ogni punteggio delle metriche finali degli esperimenti a causa di questi fattori. 

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Monitorare le esecuzioni automatizzate di Machine Learning

Per le esecuzioni automatizzate di Machine Learning, per accedere ai grafici da un'esecuzione precedente, sostituire `<<experiment_name>>` con il nome dell'esperimento appropriato:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget di notebook di Jupyter per Machine Learning automatizzato](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>Registrare e distribuire modelli

È possibile registrare un modello, in modo da potervi tornare per usarlo in un secondo momento. 

Per registrare un modello da un'esecuzione automatizzata di Machine Learning, usare il [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) metodo . 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Per informazioni dettagliate su come creare una configurazione di distribuzione e distribuire un modello registrato in un servizio Web, vedere come [e dove distribuire un modello](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration).

> [!TIP]
> Per i modelli registrati, la distribuzione con un solo clic è disponibile tramite il [studio di Azure Machine Learning](https://ml.azure.com). Vedere [come distribuire modelli registrati da Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilità dei modelli

L'interpretabilità dei modelli consente di comprendere il motivo per cui i modelli hanno eseguito stime e i valori di importanza delle funzionalità sottostanti. L'SDK include diversi pacchetti per abilitare le funzionalità di interpretabilità dei modelli, sia in fase di training che di inferenza, per i modelli locali e distribuiti.

Per esempi di codice su come abilitare le funzionalità di interpretabilità in modo specifico negli esperimenti di Machine Learning automatizzato, vedere la [procedura](how-to-machine-learning-interpretability-automl.md).

Per informazioni generali su come abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in altre aree dell'SDK al di fuori del Machine Learning automatizzato, vedere l'articolo relativo al [concetto](how-to-machine-learning-interpretability.md) di interpretabilità.

> [!NOTE]
> Il modello ForecastTCN non è attualmente supportato dal client Explanation. Questo modello non restituirà un dashboard di spiegazione se viene restituito come modello migliore e non supporta le esecuzioni di spiegazioni su richiesta.

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

+ Altre informazioni su [come eseguire il training di un modello di regressione con Machine Learning automatizzato.](tutorial-auto-train-models.md)

+ Informazioni su come eseguire il training di più modelli con AutoML nell'acceleratore [di soluzioni Molti modelli](https://aka.ms/many-models).

+ [Risolvere i problemi degli esperimenti automatizzati di Machine Learning.](how-to-troubleshoot-auto-ml.md) 
