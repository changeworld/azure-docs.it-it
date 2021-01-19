---
title: "Avvio rapido: Introduzione all'analisi con Spark"
description: Questa esercitazione illustra come analizzare i dati con Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118881"
---
# <a name="analyze-with-apache-spark"></a>Analizzare dati con Apache Spark

Questa esercitazione illustra i passaggi di base per caricare e analizzare i dati con Apache Spark per Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analizzare i dati del set di dati NYC Taxi nell'archiviazione BLOB con Spark

1. Nell'hub **Dati** selezionare **Aggiungi una nuova risorsa** (pulsante con il segno più sopra **Collegati**) > **Esplora raccolta**.
1. Trovare e selezionare **NYC Taxi & Limousine Commission - yellow taxi trip records**.
1. Nella parte inferiore della pagina selezionare **Continua** > **Aggiungi set di dati**.
1. Nell'hub **Dati** in **Collegati** fare clic il pulsante destro del mouse su **Archivio BLOB di Azure** e scegliere **Set di dati di esempio** > **nyc_tlc_yellow**.
1. Selezionare **Nuovo notebook** per creare un nuovo notebook con il codice seguente:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. Nel menu **Collega a** del notebook scegliere un pool di Spark serverless.
1. Selezionare **Esegui** nella cella.
1. Per vedere semplicemente lo schema del dataframe, eseguire una cella con il codice seguente:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Caricare i dati di NYC Taxi nel database nyctaxi di Spark

I dati sono disponibili in una tabella in **SQLPOOL1**. Caricarli in un database Spark denominato **nyctaxi**.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo).
1. Selezionare **+**  > **Notebook**.
1. Nella parte superiore del notebook impostare il valore per **Collega a** su **Spark1**.
1. Selezionare **Aggiungi codice** per aggiungere una cella di codice del notebook e quindi immettere il testo seguente:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Selezionare **Esegui** nella cella.
1. Nell'hub **Dati** fare clic con il pulsante destro del mouse su **Database** e quindi scegliere **Aggiorna**. Verranno visualizzati i database seguenti:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizzare i dati dell'esempio NYC Taxi con Spark e notebook

1. Tornare al notebook.
1. Creare una nuova cella di codice e immettere il testo seguente.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Eseguire quindi la cella per visualizzare i dati dei taxi di New York caricati nel database Spark **nyctaxi**.
1. Eseguire il codice seguente per ripetere la stessa analisi effettuata in precedenza con il pool SQL dedicato **SQLPOOL1**. Questo codice salva i risultati dell'analisi in una tabella denominata **nyctaxi.passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nei risultati della cella selezionare **Grafico** per visualizzare i dati.

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Caricare dati da una tabella Spark in una tabella del pool SQL dedicato

In precedenza sono stati copiati i dati dalla tabella del pool SQL dedicato **SQLPOOL1.dbo.Trip** nella tabella Spark **nyctaxi.trip**. In seguito i dati sono stati aggregati nella tabella Spark **nyctaxi.passengercountstats**. A questo punto, copiare i dati da **nyctaxi.passengercountstats** a una tabella del pool SQL dedicato denominata **SQLPOOL1.dbo.PassengerCountStats**.

Eseguire la cella seguente nel notebook. La tabella Spark aggregata verrà copiata nuovamente nella tabella del pool SQL dedicato.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati con un pool SQL serverless](get-started-analyze-sql-on-demand.md)
