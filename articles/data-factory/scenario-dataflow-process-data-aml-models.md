---
title: Usare il flusso di dati per elaborare i dati dei modelli di Machine Learning (AutoML) automatici
description: Informazioni su come usare i flussi di dati Azure Data Factory per elaborare i dati dei modelli di Machine Learning (AutoML) automatici.
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521016"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Elaborare dati da modelli automatizzati di Machine Learning (AutoML) utilizzando il flusso di dati

Automatizzata Machine Learning (AutoML) viene adottata dai progetti di machine learning per eseguire il training, ottimizzare e ottenere automaticamente il modello migliore utilizzando la metrica di destinazione specificata per la classificazione, la regressione e la previsione delle serie temporali. 

Un problema consiste nel fatto che i dati non elaborati del database di data warehouse o transazionale sono un set di dati di grandi dimensioni, ad esempio 10 GB, il set di dati di grandi dimensioni richiede più tempo per eseguire il training dei modelli, quindi è consigliabile ottimizzare l'elaborazione dei dati Azure Machine Learning prima di Questa esercitazione illustra come usare ADF per partizionare il set di dati in file parquet per Azure Machine Learning set di dati. 

Nel progetto di Machine Learning automatico (AutoML) si applicano i tre scenari di elaborazione dati seguenti:

* Partizionare i dati di grandi dimensioni in file parquet prima del training dei modelli. 

     Il [frame di dati Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) viene comunemente usato per elaborare i dati prima del training dei modelli. Il frame di dati Pandas funziona bene per le dimensioni dei dati inferiori a 1 GB, ma se i dati sono di dimensioni superiori a 1 GB, il frame di dati Pandas rallenta per elaborare i dati, talvolta anche il messaggio di errore memoria insufficiente. I formati di [file parquet](https://parquet.apache.org/) sono consigliati per l'apprendimento automatico poiché si tratta di un formato a colonne binario.
    
    Le data factory di Azure che eseguono il mapping di flussi di dati sono progettate in modo visivo per le trasformazioni di dati senza codice per i tecnici. È potente per elaborare dati di grandi dimensioni poiché la pipeline USA cluster Spark con scalabilità orizzontale.

* Set di dati di training e set di dati di test.
    
    Il set di dati di training verrà usato per il modello di training. verrà usato il set di dati di test per valutare i modelli nel progetto di machine learning. Il mapping di flussi di dati attività di Suddivisione condizionale suddivide i dati di training e i dati di test. 

* Rimuovere i dati non qualificati.

    Potrebbe essere necessario rimuovere dati non qualificati, ad esempio un file parquet con zero righe. In questa esercitazione verrà usata l'attività aggregata per ottenere i numeri di conteggio delle righe, il conteggio delle righe sarà una condizione per rimuovere i dati non qualificati. 


## <a name="preparation"></a>Preparazione
Usare la tabella seguente del database SQL di Azure. 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Convertire il formato dei dati in parquet

Il flusso di dati converte una tabella del database SQL di Azure in formato di file parquet. 

**Set** di dati di origine: tabella delle transazioni del database SQL di Azure

**Set di dati sink**: archiviazione BLOB con formato parquet


## <a name="remove-unqualified-data-based-on-row-count"></a>Rimuovere i dati non qualificati in base al numero di righe

Si supponga di rimuovere il numero di righe inferiore a 2. 

1. Usare l'attività di aggregazione per ottenere il numero di righe di conteggio: **Group by** in base a Col2 e **aggregazioni** con Count (1) per il conteggio delle righe. 

    ![configurare l'attività di aggregazione per ottenere il numero di righe del conteggio](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Usare attività sink, scegliere **tipo di sink** come cache nella scheda **sink** , quindi scegliere colonna desiderata dall'elenco a discesa **colonne chiave** nella scheda **Impostazioni** . 

    ![configurare l'attività CacheSink per ottenere il numero di righe nel sink memorizzato nella cache](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Utilizzare l'attività colonna derivata per aggiungere la colonna Conteggio righe nel flusso di origine. Nella scheda **impostazioni della colonna derivata usare l'** espressione CacheSink # Lookup ottenendo il numero di righe da SinkCache.
    ![configurare l'attività colonna derivata per aggiungere il numero di righe nell'origine 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Utilizzare l'attività di Suddivisione condizionale per rimuovere i dati non qualificati. In questo esempio, il conteggio delle righe basato sulla colonna Col2 e la condizione consiste nel rimuovere il numero di righe minore di 2, quindi verranno rimosse due righe (ID = 2 e ID = 7). I dati non qualificati verranno salvati in un archivio BLOB per la gestione dei dati. 

    ![configurare l'attività di Suddivisione condizionale per ottenere i dati maggiori o uguali a 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Creare una nuova origine per ottenere il numero di righe che verranno usate nell'origine originale nei passaggi successivi. 
>    *    Usare CacheSink dal punto di vista delle prestazioni. 

## <a name="split-training-data-and-test-data"></a>Suddividere dati di training e dati di test 

1. Si vuole suddividere i dati di training e testare i dati per ogni partizione. In questo esempio, per lo stesso valore di col2, ottenere le prime 2 righe come dati di test e le righe Rest come dati di training. 

    Utilizzare attività finestra per aggiungere un numero di riga di colonna per ogni partizione. Nella **scheda Seleziona** colonna per partizione (in questa esercitazione, partiziona per Col2), assegna ordine nella scheda **Ordina** (in questa esercitazione, si basa sull'ID da ordinare) e nella scheda **colonne finestra** per aggiungere una colonna come numero di riga per ogni partizione. 
    ![Configura attività finestra per aggiungere una nuova colonna che è il numero di riga](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Usare l'attività di Suddivisione condizionale per dividere ogni partizione le prime 2 righe in set di dati di test e le righe REST per il training del set di dati. Nella scheda **Impostazioni Suddivisione condizionale** usare Expression LesserOrEqual (rowNum, 2) come condizione. 

    ![configurare l'attività di Suddivisione condizionale per suddividere il set di dati corrente nel set di dati di training e](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Set di dati di training della partizione e set di dati di test con formato parquet

1. Usare l'attività sink, nella scheda **ottimizza** , usando un **valore univoco per partizione** per impostare una colonna come chiave di colonna per la partizione. 
    ![configurare l'attività sink per impostare la partizione del set di dati di training](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Esaminiamo l'intera logica della pipeline.
    ![Logica dell'intera pipeline](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.
