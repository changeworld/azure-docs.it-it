---
title: 'Guida introduttiva: leggere i dati da ADLS Gen2 a dataframe Pandas'
description: Leggere i dati da un account Azure Data Lake Storage Gen2 in un dataframe Pandas usando Python in sinapsi studio in Azure sinapsi Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969875"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Guida introduttiva: leggere i dati da ADLS Gen2 a dataframe Pandas in Azure sinapsi Analytics

Questa Guida introduttiva illustra come usare facilmente Python per leggere i dati da un Gen2 Azure Data Lake Storage (ADLS) in un dataframe di Pandas in Azure sinapsi Analytics.

Da un notebook di sinapsi studio è possibile:

- connettersi a un contenitore in Data Lake Storage Gen2 collegato all'area di lavoro di Azure sinapsi Analytics
- leggere i dati da un notebook di PySpark usando `spark.read.load`
- convertire i dati in un dataframe Pandas usando `.toPandas()`

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/).
- Area di lavoro di sinapsi Analytics con Data Lake Storage Gen2 configurato come risorsa di archiviazione predefinita: è necessario essere il **collaboratore dati BLOB di archiviazione** del file System Data Lake storage Gen2 con cui si lavora. Per informazioni dettagliate su come creare un'area di lavoro, vedere [creazione di un'area di lavoro sinapsi](get-started-create-workspace.md).
- Apache Spark pool nell'area di lavoro: vedere [creare un pool di Apache Spark senza server](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Carica dati di esempio in ADLS Gen2

1. Nella portale di Azure creare un contenitore nello stesso Data Lake Storage Gen2 usato da sinapsi Studio. È possibile ignorare questo passaggio se si vuole usare l'account di archiviazione collegato predefinito nell'area di lavoro di Azure sinapsi Analytics.

1. In sinapsi Studio fare clic su **dati**, selezionare la scheda **collegato** e selezionare il contenitore sotto **Azure Data Lake storage Gen2**.

1. Scaricare il file di esempio [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) e caricarlo nel contenitore.

1. Selezionare il file caricato, fare clic su **Proprietà** e copiare il valore del **percorso ABFSS** .

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Leggere i dati da ADLS Gen2 in un dataframe Pandas

1. Nel riquadro sinistro fare clic su **sviluppa**.

1. Fare clic **+** e selezionare "notebook" per creare un nuovo notebook.

1. In **Connetti a** selezionare il Pool di Apache Spark. Se non si dispone di un pool, fare clic su **crea Apache Spark pool**.

1. Nella cella del codice del notebook incollare il codice Python seguente, inserendo il percorso ABFSS copiato in precedenza:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Eseguire la cella.

Dopo alcuni minuti il testo visualizzato dovrebbe essere simile al seguente.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure Synapse Analytics?](overview-what-is.md)
- [Introduzione ad Azure sinapsi Analytics](get-started.md)
- [Creare un pool di Apache Spark serverless](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
