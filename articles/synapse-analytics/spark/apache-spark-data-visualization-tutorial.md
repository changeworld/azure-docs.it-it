---
title: Visualizzare dati con Apache Spark
description: Creare visualizzazioni di dati avanzate usando Apache Spark e i notebook di Azure sinapsi Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942276"
---
# <a name="analyze-data-with-apache-spark"></a>Analizzare dati con Apache Spark

In questa esercitazione si apprenderà come eseguire l'analisi esplorativa dei dati usando i set di dati aperti di Azure e Apache Spark. È quindi possibile visualizzare i risultati in un notebook di sinapsi studio in Azure sinapsi Analytics.

In particolare, verrà analizzato il set di dati di [New York City (NYC) taxi](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . I dati sono disponibili tramite set di dati aperti di Azure. Questo subset del set di dati contiene informazioni sulle corse di taxi gialle: informazioni su ogni corsa, ora di inizio e di fine e località, costo e altri attributi interessanti.
  
## <a name="before-you-begin"></a>Prima di iniziare
Creare un pool di Apache Spark seguendo le istruzioni di questa [esercitazione](../articles/../quickstart-create-apache-spark-pool-studio.md). 

## <a name="download-and-prepare-the-data"></a>Scaricare e preparare i dati
1. Creare un notebook usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
   > [!Note]
   > Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.

2. In questa esercitazione verranno usate diverse librerie per facilitare la visualizzazione del set di dati. Per eseguire questa analisi, importare le librerie seguenti: 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. Poiché i dati non elaborati sono in formato parquet, è possibile usare il contesto Spark per eseguire direttamente il pull del file in memoria come frame di dati. Creare un frame di dati Spark recuperando i dati tramite l'API set di dati aperti. In questo esempio viene usato lo schema di dataframe di Spark sulle proprietà di *lettura* per dedurre i tipi di oggetto e lo schema.

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. Dopo aver letto i dati, è opportuno eseguire un filtro iniziale per pulire il set di dati. È possibile rimuovere le colonne non necessarie e aggiungere colonne che estraggono informazioni importanti. Verranno inoltre filtrate le anomalie all'interno del set di dati.

   ```python
   # Filter the dataset 
   from pyspark.sql.functions import *

   filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                               .filter((df.passengerCount > 0)\
                                   & (df.tipAmount >= 0)\
                                   & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                   & (df.tripDistance > 0) & (df.tripDistance <= 200))

   filtered_df.createOrReplaceTempView("taxi_dataset")
   ```

## <a name="analyze-data"></a>Analizzare i dati
Gli analisti dei dati hanno a disposizione una vasta gamma di strumenti che consentono di estrarre informazioni dettagliate dai dati. In questa parte dell'esercitazione verranno illustrati alcuni strumenti utili disponibili all'interno dei notebook di Azure sinapsi Analytics. In questa analisi si desidera comprendere i fattori che producono più suggerimenti per i taxi per il periodo selezionato.

### <a name="apache-spark-sql-magic"></a>Apache Spark Magic SQL 
Prima di tutto si eseguirà l'analisi esplorativa dei dati tramite Apache Spark comandi SQL e Magic con il notebook di Azure sinapsi. Una volta ottenuta la query, i risultati verranno visualizzati usando la funzionalità incorporata ```chart options``` . 

1. All'interno del notebook creare una nuova cella e copiare il codice seguente. Utilizzando questa query, è necessario comprendere come sono stati modificati gli importi della mancia media nel periodo selezionato. Questa query consentirà anche di identificare altre informazioni utili, tra cui l'importo minimo/massimo della mancia al giorno e l'importo medio della tariffa.
   
   ```sql
   %%sql
   SELECT 
       day_of_month
       , MIN(tipAmount) AS minTipAmount
       , MAX(tipAmount) AS maxTipAmount
       , AVG(tipAmount) AS avgTipAmount
       , AVG(fareAmount) as fareAmount
   FROM taxi_dataset 
   GROUP BY day_of_month
   ORDER BY day_of_month ASC
   ```

2. Al termine dell'esecuzione della query, è possibile visualizzare i risultati passando alla visualizzazione grafico. In questo esempio viene creato un grafico a linee specificando il ```day_of_month``` campo come chiave e ```avgTipAmount``` come valore. Dopo aver effettuato le selezioni, selezionare **applica** per aggiornare il grafico. 
   
## <a name="visualize-data"></a>Visualizzare i dati
Oltre alle opzioni predefinite per la creazione di grafici dei notebook, è possibile usare le librerie open source più diffuse per creare visualizzazioni personalizzate. Negli esempi seguenti verranno usati Seaborn e matplotlib. Si tratta di librerie Python comunemente usate per la visualizzazione dei dati. 

> [!Note]
> Per impostazione predefinita, ogni pool di Apache Spark in Azure sinapsi Analytics contiene un set di librerie predefinite e di uso comune. È possibile visualizzare l'elenco completo delle librerie nella documentazione di [runtime di Azure sinapsi](../spark/apache-spark-version-support.md) . Inoltre, per rendere disponibile per le applicazioni codice di terze parti o compilato localmente, è possibile [installare una libreria](../spark/apache-spark-azure-portal-add-libraries.md) in uno dei pool Spark.

1. Per rendere più semplice e meno costoso lo sviluppo, Downsample il set di dati. Verrà usata la funzionalità di campionamento Apache Spark incorporata. Inoltre, Seaborn e matplotlib richiedono un dataframe Pandas o una matrice NumPy. Per ottenere un dataframe Pandas, utilizzare il ```toPandas()``` comando per convertire il frame di frame.

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. Si vuole comprendere la distribuzione dei suggerimenti nel set di dati. Si userà matplotlib per creare un istogramma che mostra la distribuzione dell'importo della Mancia e del conteggio. In base alla distribuzione, è possibile notare che i suggerimenti sono inclinati verso un importo minore o uguale a $10.

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![Istogramma dei suggerimenti.](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Si desidera ora comprendere la relazione tra i suggerimenti per una determinata corsa e il giorno della settimana. Usare Seaborn per creare un plot box che riepiloga le tendenze per ogni giorno della settimana. 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![Grafico che mostra la distribuzione di suggerimenti al giorno.](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Un'altra ipotesi potrebbe essere la presenza di una relazione positiva tra il numero di passeggeri e l'importo totale delle mance dei taxi. Per verificare questa relazione, eseguire il codice seguente per generare un box plot che illustra la distribuzione dei suggerimenti per ogni conteggio passeggeri. 
   
   ```python
   # How many passengers tipped by various amounts 
   ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
   ax2.set_title('Tip amount by Passenger count')
   ax2.set_xlabel('Passenger count')
   ax2.set_ylabel('Tip Amount ($)')
   ax2.set_ylim(0,30)
   plt.suptitle('')
   plt.show()
   ```
   ![Grafico che mostra un tracciato box baffi.](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Infine, vogliamo comprendere la relazione tra l'importo della tariffa e l'importo della Mancia. In base ai risultati, si noterà che sono presenti diverse osservazioni, in cui gli utenti non sono suggerimento. Tuttavia, viene anche visualizzata una relazione positiva tra la tariffa complessiva e gli importi delle mance.
   
   ```python
   # Look at the relationship between fare and tip amounts

   ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
   ax.set_title('Tip amount by Fare amount')
   ax.set_xlabel('Fare Amount ($)')
   ax.set_ylabel('Tip Amount ($)')
   plt.axis([-2, 80, -2, 20])
   plt.suptitle('')
   plt.show()
   ```
   ![Tracciato a dispersione dell'importo del suggerimento.](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Arrestare l'istanza di Spark

Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse. Chiudere la scheda o selezionare **Termina sessione** dal pannello stato nella parte inferiore del notebook.

## <a name="see-also"></a>Vedere anche

- [Panoramica: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Creare un modello di Machine Learning con Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](../index.yml)
- [Documentazione ufficiale di Apache Spark](https://spark.apache.org/docs/latest/)