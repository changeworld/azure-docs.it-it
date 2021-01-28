---
title: 'Esercitazione: Eseguire il training di un modello in Python con Machine Learning automatizzato'
description: Esercitazione su come eseguire il training di un modello di Machine Learning in Python usando Apache Spark e Machine Learning automatizzato.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: fc9909614a9d557c19a22e215b7513a038f88c33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942341"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Esercitazione: Eseguire il training di un modello in Python con Machine Learning automatizzato

Azure Machine Learning offre un ambiente basato sul cloud che consente di sottoporre a training, distribuire, automatizzare, gestire e monitorare i modelli di Machine Learning. 

In questa esercitazione si userà il servizio [Machine Learning automatizzato](../../machine-learning/concept-automated-ml.md) di Azure Machine Learning per creare un modello di regressione e prevedere i prezzi delle corse dei taxi di New York. Questo processo accetta impostazioni di configurazione e dati di training e scorre automaticamente le combinazioni di diversi metodi, modelli e impostazioni di iperparametri per ottenere il modello migliore.

In questa esercitazione verranno illustrate le procedure per:
- Scaricare i dati usando Apache Spark e i set di dati aperti di Azure.
- Trasformare e pulire i dati usando Apache Spark dataframes.
- Eseguire il training di un modello di regressione in Machine Learning automatizzato.
- Calcolare l'accuratezza del modello.

## <a name="before-you-begin"></a>Prima di iniziare

- Creare un pool di Apache Spark senza server seguendo la Guida introduttiva alla [creazione di un pool di Apache Spark senza server](../quickstart-create-apache-spark-pool-studio.md) .
- Completare l'esercitazione sull' [impostazione dell'area di lavoro Azure Machine Learning](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) se non è presente un'area di lavoro Azure Machine Learning esistente. 

## <a name="understand-regression-models"></a>Informazioni sui modelli di regressione

I *modelli di regressione* prevedono valori di output numerici basati su predittori indipendenti. Nella regressione l'obiettivo è stabilire la relazione tra le variabili indipendenti del predittore stimando il modo in cui una variabile influisce sulle altre.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Esempio basato sui dati dei taxi di New York

In questo esempio, si usa Spark per eseguire alcune analisi sui dati dei taxi-trip di New York City (NYC). I dati sono disponibili tramite [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse dei taxi, incluse informazioni su ogni corsa, l'ora e il luogo di partenza e di arrivo e i costi.

> [!IMPORTANT]
> Per il pull dei dati dalla posizione di archiviazione potrebbero essere addebitati costi aggiuntivi. Nei passaggi successivi verrà sviluppato un modello per prevedere i prezzi delle corse dei taxi di New York. 

##  <a name="download-and-prepare-the-data"></a>Scaricare e preparare i dati

Ecco come:

1. Creare un notebook usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.
  
2. Poiché i dati non elaborati sono in formato parquet, è possibile usare il contesto Spark per eseguire il pull del file direttamente in memoria come frame di dati. Creare un frame di dati Spark recuperando i dati tramite l'API set di dati aperti. In questo caso, è possibile usare le proprietà del dataframe `schema on read` di Spark per dedurre i tipi di oggetto e lo schema. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. A seconda delle dimensioni del pool di Spark, i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per poter essere usati. È possibile filtrare i dati in base a un valore più piccolo, ad esempio un mese di dati, usando i ```start_date``` ```end_date``` filtri e. Dopo aver filtrato un frame di dati, è anche possibile eseguire la ```describe()``` funzione sul nuovo dataframe per visualizzare le statistiche di riepilogo per ogni campo. 

   In base alle statistiche riepilogative, si possono notare alcune irregolarità nei dati. Le statistiche indicano, ad esempio, che la distanza minima delle corse è minore di 0. È necessario filtrare questi punti dati irregolari per rimuoverli.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Generare le funzionalità dal set di dati selezionando un set di colonne e creando varie funzionalità basate sul tempo dal `datetime` campo pickup. Filtrare gli outlier identificati dal passaggio precedente, quindi rimuovere le ultime colonne perché non sono necessarie per il training.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Come si può notare, verrà creato un nuovo dataframe con colonne aggiuntive per il giorno del mese, l'ora di prelievo, il giorno della settimana e il tempo totale di viaggio. 

   ![Immagine del dataframe di taxi.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Generare i set di dati di test e di convalida

Una volta ottenuto il set di dati finale, è possibile dividere i dati in set di training e di test usando la funzione ```random_ split ``` in Spark. Utilizzando i pesi specificati, questa funzione suddivide in modo casuale i dati nel set di dati di training per il training del modello e il set di dati di convalida per il test.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Questo passaggio assicura che per testare il modello finito vengano usati punti dati non usati per eseguirne il training. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Connettersi a un'area di lavoro di Azure Machine Learning
In Azure Machine Learning, workspace è una classe che accetta le informazioni sulla sottoscrizione e sulle risorse di Azure. Crea inoltre una risorsa cloud per monitorare le esecuzioni del modello e tenerne traccia. In questo passaggio verrà creato un oggetto workspace dall'area di lavoro di Azure Machine Learning esistente.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Convertire un frame di dati in un set di dati Azure Machine Learning
Per inviare un esperimento remoto, convertire il set di dati in un'istanza di Azure Machine Learning ```TabularDatset``` . [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) rappresenta i dati in formato tabulare tramite l'analisi dei file forniti.

Il codice seguente ottiene l'area di lavoro esistente e l'archivio dati predefinito Azure Machine Learning. Passa quindi l'archivio dati e i percorsi dei file al parametro Path per creare una nuova ```TabularDataset``` istanza. 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Immagine del set di dati caricato.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Inviare un esperimento automatizzato

Le sezioni seguenti illustrano il processo di invio di un esperimento di Machine Learning automatizzato.

### <a name="define-training-settings"></a>Definire le impostazioni di training
1. Per inviare un esperimento, è necessario definire le relative impostazioni dei parametri e del modello per il training. Per l'elenco completo delle impostazioni, vedere [Configurare esperimenti di Machine Learning automatizzato in Python](../../machine-learning/how-to-configure-auto-train.md).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Passare le impostazioni di training definite come parametro `kwargs` a un oggetto `AutoMLConfig`. Poiché si usa Spark, è anche necessario passare il contesto di Spark, accessibile automaticamente tramite la variabile ```sc```. Inoltre, si specificano i dati di training e il tipo di modello, che in questo caso è regressione.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>I passaggi di pre-elaborazione di Machine Learning automatizzato diventano parte del modello sottostante. Questi passaggi includono la normalizzazione delle funzionalità, la gestione dei dati mancanti e la conversione di testo in valori numerici. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="train-the-automatic-regression-model"></a>Eseguire il training del modello di regressione automatica 
Ora creare un oggetto esperimento nell'area di lavoro di Azure Machine Learning. Un esperimento funge da contenitore per le singole esecuzioni. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Al termine dell'esperimento, l'output restituisce informazioni dettagliate sulle iterazioni completate. Per ogni iterazione, vengono visualizzati il tipo di modello, la durata dell'esecuzione e l'accuratezza del training. Il `BEST` campo tiene traccia del Punteggio di training migliore in base al tipo di metrica.

![Screenshot dell'output del modello.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Una volta inviato, l'esperimento di Machine Learning automatizzato esegue varie iterazioni e tipi di modello. Questa esecuzione richiede in genere da 60 a 90 minuti. 

### <a name="retrieve-the-best-model"></a>Recuperare il modello migliore
Per selezionare il modello migliore dalle iterazioni, si userà la funzione ```get_output```, che restituisce l'esecuzione migliore e il modello adattato. Il codice seguente recupera l'esecuzione migliore e il modello adattato per qualsiasi metrica registrata o per un'iterazione specifica.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Testare l'accuratezza del modello
1. Per testare l'accuratezza del modello, usare il modello migliore per eseguire previsioni relative alle tariffe dei taxi con il set di dati di test. La ```predict``` funzione usa il modello migliore e stima i valori di `y` (importo della tariffa) dal set di dati di convalida. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. La radice dell'errore quadratico medio viene usata spesso come misura delle differenze tra i valori campione previsti dal modello e i valori osservati. Per calcolare l'errore radice-medio-quadrato dei risultati, confrontare il `y_test` frame di dati con i valori stimati dal modello. 

   La funzione ```mean_squared_error``` calcola l'errore quadratico medio tra due matrici di valori. Eseguire quindi la radice quadrata del risultato. Questa metrica indica approssimativamente la distanza tra le stime delle tariffe taxi rispetto ai valori effettivi delle tariffe.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   La radice dell'errore quadratico medio è una misura valida del livello di accuratezza con cui il modello prevede la risposta. Nei risultati si può notare che la previsione delle tariffe dei taxi eseguita dal modello in base alle caratteristiche del set di dati è piuttosto valida, in genere entro $ 2,00 in eccesso o in difetto.

1. Eseguire il codice seguente per calcolare l'errore percentuale assoluto medio. Questa metrica esprime l'accuratezza come percentuale dell'errore. Calcola una differenza assoluta tra ogni valore previsto ed effettivo e quindi somma tutte le differenze. Quindi esprime tale somma come percentuale del totale dei valori effettivi.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Dalle due metriche dell'accuratezza si nota che la previsione delle tariffe dei taxi eseguita del modello in base alle caratteristiche del set di dati è piuttosto valida. 

1. Dopo aver adattato un modello di regressione lineare, è ora necessario determinare quanto si adatta ai dati. A questo scopo, tracciare i valori delle tariffe effettive rispetto all'output previsto. Calcolare inoltre la misura del coefficiente di determinazione per capire quanto sono vicini i dati alla linea di regressione adattata.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Screenshot di un tracciato di regressione.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Nei risultati è possibile osservare che la misura del coefficiente di determinazione rappresenta il 95% della varianza. Questo dato viene anche confermato dal tracciato dei valori effettivi rispetto a quelli osservati. Maggiore è la varianza per la quale il modello di regressione è più vicino alla linea di regressione adatta, più vicini sono i punti dati.  

## <a name="register-the-model-to-azure-machine-learning"></a>Registrare il modello in Azure Machine Learning
Dopo aver convalidato il modello migliore, è possibile registrarlo in Azure Machine Learning. Quindi, scaricare o distribuire il modello registrato per ricevere tutti i file registrati.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Visualizzare i risultati in Azure Machine Learning
È anche possibile accedere ai risultati delle iterazioni passando all'esperimento nell'area di lavoro Azure Machine Learning. Qui è possibile ottenere altri dettagli sullo stato dell'esecuzione, dei modelli tentati e di altre metriche del modello. 

![Screenshot di un'area di lavoro Azure Machine Learning.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Passaggi successivi
- [Azure Synapse Analytics](../index.yml)
- [Esercitazione: Creare un'app di Machine Learning con MLlib di Apache Spark e Azure Synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)