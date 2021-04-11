---
title: 'Esercitazione: creare applicazioni di Machine Learning usando Microsoft Machine Learning per Apache Spark (anteprima)'
description: Informazioni su come usare Microsoft Machine Learning per Apache Spark per creare applicazioni di Machine Learning in Azure sinapsi Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 5258d8f16e288e7df7e1286eb1902cc6ba6d10f7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227722"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Esercitazione: creare applicazioni di Machine Learning usando Microsoft Machine Learning per Apache Spark (anteprima)

In questo articolo si apprenderà come usare Microsoft Machine Learning per Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) per creare applicazioni di machine learning. MMLSpark espande la soluzione di Machine Learning distribuita di Apache Spark aggiungendo molti strumenti di apprendimento avanzato e data science, ad esempio [Servizi cognitivi di Azure](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) e altro ancora.  MMLSpark consente di creare modelli predittivi e analitici avanzati e a scalabilità elevata da diverse origini dati Spark.
Sinapsi Spark fornisce librerie MMLSpark predefinite, tra cui:

- [Vowpal Wabbit](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) -servizi di libreria per machine learning per abilitare l'analisi del testo, ad esempio l'analisi dei sentimenti nei Tweet.
- [Servizi cognitivi in Spark](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) : per combinare la funzionalità dei servizi cognitivi di Azure nelle pipeline SparkML per derivare la progettazione di soluzioni per i servizi di modellazione dei dati cognitivi come il rilevamento delle anomalie.
- [LightGBM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – LightGBM è un Framework con boosting a gradienti che usa algoritmi di apprendimento basati su albero. È progettato per essere distribuito e più efficiente.
- Modelli KNN condizionali KNN-scalabili con query condizionali.
- [Http in Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) : consente l'orchestrazione di microservizi distribuiti nell'integrazione dell'accessibilità basata sul protocollo Spark e http.

Questa esercitazione illustra i campioni che usano servizi cognitivi di Azure in MMLSpark per 

- Analisi del testo: consente di ottenere la valutazione o il tono di un set di frasi.
- Visione artificiale: consente di ottenere i tag (descrizioni di una parola) associati a un set di immagini.
- Ricerca immagini Bing: consente di cercare nel Web immagini correlate a una query in linguaggio naturale.
- Rilevamento anomalie: consente di rilevare anomalie nei dati di una serie temporale.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti 

- [Area di lavoro di Azure sinapsi Analytics](../get-started-create-workspace.md) con un Azure Data Lake storage Gen2 account di archiviazione configurato come risorsa di archiviazione predefinita. È necessario essere il *collaboratore dati BLOB di archiviazione* del data Lake storage Gen2 file System con cui si lavora.
- Pool di Spark nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un pool di Spark in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Procedura di pre-configurazione descritta nell'esercitazione [configurare servizi cognitivi in sinapsi di Azure](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Introduzione
Per iniziare, importare mmlspark e configurare le chiavi del servizio. 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# Your linked key vault for Synapse workspace
key_vault = "YOUR_KEY_VAULT_NAME"


cognitive_service_key = mssparkutils.credentials.getSecret(key_vault, service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret(key_vault, bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret(key_vault, anomaly_key)

```


## <a name="text-analytics-sample"></a>Esempio di analisi del testo

Il servizio [Analisi del testo](../../cognitive-services/text-analytics/index.yml) fornisce diversi algoritmi per estrarre dati analitici intelligenti dal testo. Ad esempio, è possibile trovare la valutazione del testo di input specificato. Il servizio restituirà un punteggio compreso tra 0.0 e 1.0 in cui punteggi bassi indicano una valutazione negativa e un punteggio alto indica una valutazione positiva. In questo esempio vengono usate tre frasi semplici e viene restituita la valutazione per ognuna di esse.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Risultati previsti

|text | Valutazione|
|--|--|
| Sono frustrato da questa ora di punta del traffico. | negativo |
| Questo è un cane | neutrale |
| I am so happy today, its sunny! | positivo |

## <a name="computer-vision-sample"></a>Esempio di visione artificiale
[Visione artificiale](../../cognitive-services/computer-vision/index.yml) analizza le immagini per identificare la struttura, ad esempio visi, oggetti e descrizioni in linguaggio naturale. In questo esempio viene contrassegnata l'immagine seguente. I tag sono descrizioni costituite da una sola parola di elementi presenti nell'immagine, come persone, scenari, azioni e oggetti riconoscibili.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Risultati previsti

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skate, Person, uomo, outdoor, equitazione, sport, skateboard, Young, Board, shirt, Air, Park, Boy, Side, jumping, Ramp, Trick, doing, Flying] |

## <a name="bing-image-search-sample"></a>Esempio di ricerca immagini Bing
[Ricerca immagini Bing](../../cognitive-services/bing-image-search/overview.md) cerca nel Web e recupera immagini correlate a una query dell'utente in linguaggio naturale. In questo esempio viene usata una query di testo che cerca immagini con virgolette. Restituisce un elenco di URL di immagini contenenti foto correlate alla query.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Risultati previsti

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Esempio di rilevamento anomalie

[Rilevamento anomalie](../../cognitive-services/anomaly-detector/index.yml) è ideale per rilevare irregolarità nei dati della serie temporale. In questo esempio viene usato il servizio per individuare le anomalie nell'intera serie temporale.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Risultati previsti

|timestamp | Valore | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|false|
|1972-02-01T00:00:00Z|799,0|false|
|1972-03-01T00:00:00Z|890,0|false|
|1972-04-01T00:00:00Z|900,0|false|
|1972-05-01T00:00:00Z|766,0|false|
|1972-06-01T00:00:00Z|805,0|false|
|1972-07-01T00:00:00Z|821,0|false|
|1972-08-01T00:00:00Z|20000,0|true|
|1972-09-01T00:00:00Z|883,0|false|
|1972-10-01T00:00:00Z|898,0|false|
|1972-11-01T00:00:00Z|957,0|false|
|1972-12-01T00:00:00Z|924,0|false|
|1973-01-01T00:00:00Z|881,0|false|
|1973-02-01T00:00:00Z|837,0|false|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Passaggi successivi

* [Estrai notebook di esempio sinapsi](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [Repository GitHub MMLSpark](https://github.com/Azure/mmlspark)
