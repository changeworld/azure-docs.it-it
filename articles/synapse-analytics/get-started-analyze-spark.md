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
ms.date: 12/31/2020
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423714"
---
# <a name="analyze-with-apache-spark"></a>Analizzare dati con Apache Spark

Questa esercitazione illustra i passaggi di base per caricare e analizzare i dati con Apache Spark per Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analizzare i dati del set di dati NYC Taxi nell'archiviazione BLOB con Spark

1. Nell'hub **dati** fare clic sul **+** pulsante per **aggiungere una nuova risorsa**, quindi fare clic su >> **Sfoglia raccolta**. 
1. Trovare e selezionare **NYC Taxi & Limousine Commission - yellow taxi trip records**. 
1. Nella parte inferiore della pagina fare clic su **continua**, quindi su **Aggiungi set di dati**. 
1. Dopo qualche istante, nell'hub **dati** sotto **collegamento**, fare clic con il pulsante destro del mouse su **archiviazione BLOB di Azure >> set di dati di esempio >> nyc_tlc_yellow** e selezionare **nuovo notebook**, quindi **caricare nel frame di dati**.
1. Verrà creato un nuovo notebook con il codice seguente:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. Nel menu **Connetti a** del notebook scegliere il pool di Spark **Spark1** senza server creato in precedenza.
1. Selezionare **Esegui** nella cella. Se necessario, sinapsi avvierà una nuova sessione di Spark per eseguire la cella. Se è necessaria una nuova sessione di Spark, la creazione iniziale richiederà circa due secondi. 
1. Per vedere semplicemente lo schema del dataframe, eseguire una cella con il codice seguente:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Caricare i dati di NYC Taxi nel database nyctaxi di Spark

I dati sono disponibili in una tabella in **SQLPOOL1**. Caricarli in un database Spark denominato **nyctaxi**.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo).
1. Selezionare **+**  > **Notebook**.
1. Nella parte superiore del notebook impostare il valore per **Collega a** su **Spark1**.
1. Nella prima cella del codice del notebook, quindi immettere il codice seguente:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Eseguire lo script. Potrebbero essere necessari 2-3 minuti.
1. Nella scheda **area di lavoro** dell'hub **dati** fare clic con il pulsante destro del mouse su **database**, quindi scegliere **Aggiorna**. A questo punto verrà visualizzato il database **nyctaxi (Spark)** nell'elenco.


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizzare i dati dell'esempio NYC Taxi con Spark e notebook

1. Tornare al notebook.
1. Creare una nuova cella di codice e immettere il codice seguente. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Eseguire la cella per visualizzare i dati dei taxi di NYC caricati nel database **nyctaxi** Spark.
1. Creare una nuova cella di codice e immettere il codice seguente. Eseguire quindi la cella per eseguire la stessa analisi eseguita in precedenza con il pool SQL dedicato **SQLPOOL1**. Questo codice salva e Visualizza i risultati dell'analisi in una tabella denominata **nyctaxi. passengercountstats**.


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

In precedenza sono stati copiati i dati dalla tabella del pool SQL dedicato **SQLPOOL1.dbo.Trip** nella tabella Spark **nyctaxi.trip**. In seguito i dati sono stati aggregati nella tabella Spark **nyctaxi.passengercountstats**. A questo punto, copiare i dati da **nyctaxi. passengercountstats** in una tabella del pool SQL dedicata denominata **SQLPOOL1. dbo. passengercountstats**.

1. Creare una nuova cella di codice e immettere il codice seguente. Eseguire la cella nel notebook. La tabella Spark aggregata verrà copiata nuovamente nella tabella del pool SQL dedicato.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati con un pool SQL serverless](get-started-analyze-sql-on-demand.md)
