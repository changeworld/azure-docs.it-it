---
title: "Esercitazione: Introduzione all'analisi dei dati in account di archiviazione"
description: In questa esercitazione si apprenderà ad analizzare i dati presenti in un account di archiviazione.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b88a7e6a9851018fce255fac0e39a30563b9bf4
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363836"
---
# <a name="analyze-data-in-a-storage-account"></a>Analizzare i dati in un account di archiviazione

In questa esercitazione si apprenderà ad analizzare i dati presenti in un account di archiviazione.

## <a name="overview"></a>Panoramica

Fino a questo momento sono stati illustrati gli scenari in cui i dati risiedono nei database nell'area di lavoro. Verrà ora illustrato come usare i file negli account di archiviazione. In questo scenario viene usato l'account di archiviazione primario dell'area di lavoro e il contenitore specificato durante la creazione dell'area di lavoro stessa.

* Nome dell'account di archiviazione: **contosolake**
* Nome del contenitore nell'account di archiviazione: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Creare file CSV e Parquet nell'account di archiviazione

Eseguire il codice seguente in un notebook in una nuova cella di codice. per creare un file CSV e un file Parquet nell'account di archiviazione.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analizzare i dati in un account di archiviazione

È possibile analizzare i dati nell'account ADLS Gen2 predefinito dell'area di lavoro oppure collegare un account di archiviazione BLOB o ADLS Gen2 all'area di lavoro tramite "**Gestisci**" > "**Servizi collegati**" > "**Nuovo**" (la procedura seguente fa riferimento all'account ADLS Gen2 primario).

1. In Synapse Studio passare all'hub **Data** (Dati) e quindi selezionare **Linked** (Collegati).
1. Passare a **Azure Data Lake Storage Gen2**  >  **myworkspace (Primario - contosolake)**.
1. Selezionare **utenti (Primario)** . Verrà visualizzata la cartella **NYCTaxi**. All'interno dovrebbero essere visibili due cartelle denominate **PassengerCountStats_csvformat** e **PassengerCountStats_parquetformat**.
1. Aprire la cartella **PassengerCountStats_parquetformat**. All'interno sarà presente un file parquet con nome come `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Fare clic con il pulsante destro del mouse su **.parquet,** scegliere **Nuovo notebook,** **quindi selezionare Carica nel dataframe.** Viene creato un nuovo notebook con una cella simile alla seguente:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. Connettersi al pool Spark denominato **Spark1.** Eseguire la cella.
1. Selezionare di nuovo la **cartella users.** Fare di nuovo clic con il pulsante destro del mouse sul file con estensione **parquet** e quindi scegliere **Nuovo script SQL** SELECT TOP  >  **100 rows**. Verrà creato uno script SQL simile al seguente:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    Nella finestra dello script assicurarsi che il **campo Connetti** a sia impostato sul pool SQL **serverless** predefinito.

1. Eseguire lo script.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Orchestrare le attività con le pipeline](get-started-pipelines.md)
