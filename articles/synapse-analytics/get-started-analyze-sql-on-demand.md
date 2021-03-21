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
ms.openlocfilehash: 5f0a7477df2e281748c053ea8c7e7d3e79626296
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588019"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analizzare i dati con un pool SQL senza server

In questa esercitazione si apprenderà come analizzare i dati con un pool SQL serverless usando i dati presenti nei database Spark. 

## <a name="the-built-in-serverless-sql-pool"></a>Pool SQL serverless predefinito

I pool SQL senza server consentono di usare SQL senza la necessità di riservare la capacità. La fatturazione per un pool SQL senza server è basata sulla quantità di dati elaborati per eseguire la query e non sul numero di nodi utilizzati per eseguire la query.

Ogni area di lavoro viene fornita con un pool SQL senza server preconfigurato denominato **predefinito**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analizzare i dati del set di dati NYC Taxi nell'archiviazione BLOB con un pool SQL serverless

In questa sezione si userà un pool SQL senza server per analizzare i dati dei taxi di NYC in un account di archiviazione BLOB di Azure.

1. In sinapsi Studio passare all'hub **sviluppo**
1. Creare un nuovo script SQL.
1. Incollare il codice seguente nello script.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Fare clic su **Run**

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati con un pool di Spark senza server](get-started-analyze-spark.md)
