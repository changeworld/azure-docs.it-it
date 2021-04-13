---
title: 'Esercitazione: iniziare a analizzare i dati con un pool SQL senza server'
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
ms.openlocfilehash: 7c228bfe5897b45e6345234f2ed8e0f5cfbec73a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312791"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analizzare i dati con un pool SQL senza server

In questa esercitazione si apprenderà come analizzare i dati con un pool SQL senza server. 

## <a name="the-built-in-serverless-sql-pool"></a>Pool SQL serverless predefinito

I pool SQL senza server consentono di usare SQL senza la necessità di riservare la capacità. La fatturazione per un pool SQL senza server è basata sulla quantità di dati elaborati per eseguire la query e non sul numero di nodi utilizzati per eseguire la query.

Ogni area di lavoro viene fornita con un pool SQL senza server preconfigurato denominato **predefinito**. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analizzare i dati dei taxi di NYC con un pool SQL senza server


1. In sinapsi Studio passare all'hub **sviluppo**
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
1. Fare clic su **Run**

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati con un pool di Spark senza server](get-started-analyze-spark.md)
