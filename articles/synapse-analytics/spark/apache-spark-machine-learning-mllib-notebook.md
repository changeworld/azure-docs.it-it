---
title: "Esercitazione: Creare un'app di Machine Learning con MLlib di Apache Spark"
description: Esercitazione su come usare Apache Spark MLlib per creare un'app di Machine Learning che analizza un set di dati usando la classificazione tramite la regressione logistica.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 5caa41b852bf55a11489db6c0bab871b20720e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670671"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Esercitazione: Creare un'app di Machine Learning con MLlib di Apache Spark e Azure Synapse Analytics

Questo articolo illustra come usare [MLlib](https://spark.apache.org/mllib/) di Apache Spark per creare un'applicazione di Machine Learning che esegue una semplice analisi predittiva su un set di dati aperto di Azure. Spark offre librerie di apprendimento automatico predefinite. Questo esempio usa la *classificazione* tramite regressione logistica.

SparkML e MLlib sono librerie Spark di base che offrono numerose utilità utili per le attività di Machine Learning, incluse le utilità adatte a:

- Classificazione
- Regressione
- Clustering
- Modellazione di argomenti
- Scomposizione di valori singolari e analisi in componenti principali
- Testing e calcolo ipotetici di statistiche di esempio

## <a name="understand-classification-and-logistic-regression"></a>Informazioni sulla classificazione e la regressione logistica

La *classificazione*, un'attività comune di apprendimento automatico, è il processo di ordinamento dei dati in categorie. È compito di un algoritmo di classificazione determinare come assegnare *etichette* ai dati di input forniti dall'utente. Ad esempio, è possibile pensare a un algoritmo di Machine Learning che accetta informazioni sulle azioni come input e divide le scorte in due categorie: le scorte da vendere e le scorte che è opportuno tenere.

La *regressione logistica* è un algoritmo usato per la classificazione. L'API di regressione logistica di Spark è utile per la *classificazione binaria*, ovvero la classificazione di dati di input in uno di due gruppi. Per ulteriori informazioni sulla regressione logistica, vedere [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

In sintesi, il processo di regressione logistica produce una *funzione logistica* che è possibile usare per stimare la probabilità che un vettore di input appartenga a un gruppo o all'altro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Esempio di analisi predittiva sui dati dei taxi di NYC

In questo esempio, si usa Spark per eseguire un'analisi predittiva dei dati di Tip-Trip di taxi da New York. I dati sono disponibili tramite [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse in taxi, incluse informazioni su ogni corsa, l'ora di partenza e di arrivo, i percorsi, i costi e altri attributi interessanti.

> [!IMPORTANT]
> Per il pull dei dati dalla posizione di archiviazione potrebbero essere addebitati costi aggiuntivi.

Nei passaggi seguenti viene sviluppato un modello per stimare se una particolare corsa includa una mancia o meno.

## <a name="create-an-apache-spark-machine-learning-model"></a>Creare un modello di Apache Spark Machine Learning

1. Creare un notebook usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importare i tipi richiesti per l'applicazione. Copiare e incollare il codice seguente in una cella vuota e quindi premere MAIUSC + INVIO. In alternativa, eseguire la cella usando l'icona di riproduzione Blu a sinistra del codice.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.

## <a name="construct-the-input-dataframe"></a>Costruire il frame di dati di input

Poiché i dati non elaborati sono in formato parquet, è possibile usare il contesto Spark per eseguire direttamente il pull del file in memoria come frame di dati. Sebbene il codice nei passaggi seguenti usi le opzioni predefinite, è possibile forzare il mapping dei tipi di dati e di altri attributi dello schema, se necessario.

1. Eseguire le righe seguenti per creare un dataframe di Spark incollando il codice in una nuova cella. Questo passaggio consente di recuperare i dati tramite l'API set di dati aperti. Il pull di tutti questi dati genera circa 1,5 miliardi righe. 

   A seconda delle dimensioni del pool di Apache Spark senza server, i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per funzionare. È possibile filtrare i dati fino a ottenere un valore inferiore. Nell'esempio di codice seguente vengono usati `start_date` e `end_date` per applicare un filtro che restituisce un singolo mese di dati.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Il lato negativo del semplice filtro è che, dal punto di vista statistico, potrebbe introdurre distorsioni nei dati. Un altro approccio consiste nell'usare il campionamento incorporato in Spark. 

   Il codice seguente riduce il set di dati a circa 2.000 righe, se viene applicato dopo il codice precedente. È possibile utilizzare questo passaggio di campionamento anziché il filtro semplice o insieme al filtro semplice.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. È ora possibile esaminare i dati per vedere cosa è stato letto. In genere è preferibile rivedere i dati con un subset invece che con il set completo, a seconda delle dimensioni del set di dati. 

   Il codice seguente offre due modi per visualizzare i dati. Il primo metodo è Basic. Il secondo modo fornisce un'esperienza di griglia molto più ricca, oltre alla possibilità di visualizzare graficamente i dati.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. A seconda delle dimensioni del set di dati generato e della necessità di sperimentare o eseguire il notebook più volte, potrebbe essere necessario memorizzare il set di dati nella cache in locale nell'area di lavoro. Esistono tre modi per eseguire la memorizzazione esplicita nella cache:

   - Salvare il frame di frame in locale come file.
   - Salvare il frame di frame come una tabella o una vista temporanea.
   - Salvare il frame di frame come tabella permanente.

I primi due approcci sono inclusi negli esempi di codice seguenti.

La creazione di una tabella o di una vista temporanea fornisce percorsi di accesso diversi ai dati, ma dura solo per la durata della sessione dell'istanza di Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Preparare i dati

I dati nel relativo formato non elaborato non sono spesso adatti per passare direttamente a un modello. È necessario eseguire una serie di azioni sui dati per ottenerli in uno stato in cui il modello può utilizzarlo.

Nel codice seguente vengono eseguite quattro classi di operazioni:

- Rimozione di outlier o valori non corretti tramite filtro.
- Rimozione delle colonne non necessarie.
- Creazione di nuove colonne derivate dai dati non elaborati per rendere il modello più efficace. Questa operazione viene talvolta denominata conteggi.
- Etichette. Poiché si sta eseguendo la classificazione binaria (sarà presente una mancia o meno in un determinato viaggio), è necessario convertire l'importo della mancia in un valore 0 o 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Viene quindi effettuato un secondo passaggio dei dati per aggiungere le funzionalità finali.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Creare un modello di regressione logistica

L'attività finale consiste nel convertire i dati con etichetta in un formato che possa essere analizzato tramite la regressione logistica. L'input per un algoritmo di regressione logistica deve essere un set di *coppie di vettori di etichette/funzionalità*, in cui il *vettore di funzionalità* è un vettore di numeri che rappresentano il punto di input. 

Quindi, è necessario convertire le colonne categoriche in numeri. In particolare, è necessario convertire le `trafficTimeBins` `weekdayString` colonne e in rappresentazioni intere. Esistono diversi approcci per eseguire la conversione. Nell'esempio seguente `OneHotEncoder` viene adottato l'approccio, che è comune.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Questa azione consente di eseguire il training di un modello in un nuovo frame di dati con tutte le colonne nel formato corretto.

## <a name="train-a-logistic-regression-model"></a>Eseguire il training di un modello di regressione logistica

La prima attività consiste nel suddividere il set di dati in un set di training e in un set di test o di convalida. La divisione qui è arbitraria. Provare a usare diverse impostazioni di suddivisione per verificare se hanno effetto sul modello.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Ora che sono presenti due dataframe, l'attività successiva consiste nel creare la formula del modello ed eseguirla nel frame di training. È quindi possibile eseguire la convalida in base al frame di test. Provare a usare versioni diverse della formula del modello per vedere l'effetto di combinazioni diverse.

> [!Note]
> Per salvare il modello, è necessario il ruolo Azure di *collaboratore dati BLOB di archiviazione* . Nell'account di archiviazione passare a **controllo di accesso (IAM)** e selezionare **Aggiungi assegnazione ruolo**. Assegnare il ruolo di collaboratore dati BLOB di archiviazione al server del database SQL di Azure. Questa operazione può essere eseguita solo dai membri con privilegi di proprietario. 
>
>Per i vari ruoli predefiniti di Azure, vedere [questa guida](../../role-based-access-control/built-in-roles.md).

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

L'output di questa cella è:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Creare una rappresentazione visiva della stima

È ora possibile creare una visualizzazione finale per comprendere meglio i risultati di questo test. Una [curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) è un modo per esaminare il risultato.

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Grafico che mostra la curva ROC per la regressione logistica nel modello di tip.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Arrestare l'istanza di Spark

Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse chiudendo la scheda. In alternativa, selezionare **Termina sessione** dal pannello stato nella parte inferiore del notebook.

## <a name="see-also"></a>Vedere anche

- [Panoramica: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di .NET per Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Documentazione ufficiale di Apache Spark](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Alcune della documentazione ufficiale di Apache Spark si basano sull'uso della console di Spark, che non è disponibile in Apache Spark in Azure sinapsi Analytics. Usare al suo posto un [notebook](../quickstart-apache-spark-notebook.md) o [IntelliJ](../spark/intellij-tool-synapse.md).