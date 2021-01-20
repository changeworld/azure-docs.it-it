---
title: "Esercitazione: Introduzione all'analisi dei dati con un pool SQL serverless"
description: In questa esercitazione si apprenderà come analizzare i dati con un pool SQL serverless usando i dati presenti nei database Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209407"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analizzare i dati con un pool SQL serverless in Azure Synapse Analytics

In questa esercitazione si apprenderà come analizzare i dati con un pool SQL serverless usando i dati presenti nei database Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analizzare i dati del set di dati NYC Taxi nell'archiviazione BLOB con un pool SQL serverless

1. Nell'hub **dati** sotto **collegamento**, fare clic con il pulsante destro del mouse su **archiviazione BLOB di Azure > set di dati di esempio > nyc_tlc_yellow**, **nuovo Script SQL** e selezionare **Seleziona le prime 100 righe**
1. Verrà creato un nuovo script SQL con il codice seguente:

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Fare clic su **Run**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analizzare i dati del set di dati NYC Taxi nei database Spark usando un pool SQL serverless

Le tabelle nei database Spark sono automaticamente visibili e possono essere usate dal pool SQL serverless per l'esecuzione di query.

1. In Synapse Studio passare all'hub **Develop** (Sviluppo) e creare un nuovo script SQL.
1. Impostare **Connetti a** al pool SQL senza server **incorporato** .
1. Incollare il testo seguente nello script ed eseguire lo script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > La prima volta che si esegue una query che usa un pool SQL serverless, la raccolta delle risorse SQL necessarie per l'esecuzione delle query richiederà circa 10 secondi. Le query successive saranno molto più veloci.
  


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati nell'archiviazione](get-started-analyze-storage.md)
