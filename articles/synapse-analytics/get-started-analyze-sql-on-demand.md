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
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567554"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analizzare i dati con un pool SQL serverless

In questa esercitazione si apprenderà come analizzare i dati con un pool SQL serverless. 

## <a name="the-built-in-serverless-sql-pool"></a>Pool SQL serverless predefinito

I pool SQL serverless consentono di usare SQL senza dover riservare capacità. La fatturazione per un pool SQL serverless si basa sulla quantità di dati elaborati per eseguire la query e non sul numero di nodi usati per eseguire la query.

Ogni area di lavoro include un pool SQL serverless preconfigurato denominato **Predefinito.** 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analizzare i dati dei taxi di New York con un pool SQL serverless

1. In Synapse Studio passare all'hub **Sviluppo**
1. Creare un nuovo script SQL.
1. Incollare il codice seguente nello script.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Fare clic su **Esegui**. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati con un pool Spark serverless](get-started-analyze-spark.md)
