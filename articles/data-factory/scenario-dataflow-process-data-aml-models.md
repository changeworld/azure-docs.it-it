---
title: Usare i flussi di dati per elaborare dati da modelli automatizzati di Machine Learning (AutoML)
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
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595380"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Elaborare dati da modelli di Machine Learning automatici usando flussi di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Automatizzato Machine Learning (AutoML) viene adottato dai progetti di machine learning per eseguire il training, l'ottimizzazione e il miglioramento automatico dei modelli migliori usando le metriche di destinazione specificate per la classificazione, la regressione e la previsione delle serie temporali.

Una sfida per AutoML è che i dati non elaborati di un data warehouse o di un database transazionale sono un set di dati di grandi dimensioni, possibilmente di 10 GB. Un set di dati di grandi dimensioni richiede più tempo per il training dei modelli, quindi è consigliabile ottimizzare l'elaborazione dei dati prima di eseguire il training di Azure Machine Learning modelli. Questa esercitazione illustra come usare Azure Data Factory per partizionare un set di dati in file AutoML per un set di dati di Machine Learning.

Il progetto AutoML include i tre scenari di elaborazione dati seguenti:

* Partizionare i dati di grandi dimensioni in file AutoML prima di eseguire il training dei modelli.

     Il [frame di dati Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) viene comunemente usato per elaborare i dati prima di eseguire il training dei modelli. Il frame di dati Pandas funziona bene per le dimensioni dei dati inferiori a 1 GB, ma se i dati hanno dimensioni maggiori di 1 GB, un frame di dati Pandas rallenta per elaborare i dati. In alcuni casi è possibile che venga anche ottenuto un messaggio di errore di memoria insufficiente. Si consiglia di usare un formato di [file parquet](https://parquet.apache.org/) per Machine Learning perché è un formato a colonne binario.
    
     Data Factory i flussi di dati di mapping sono le trasformazioni di dati progettate visivamente che liberano gli ingegneri di dati dalla scrittura di codice. Il mapping di flussi di dati è un modo efficace per elaborare dati di grandi dimensioni perché la pipeline USA cluster Spark con scalabilità orizzontale.

* Suddividere il set di dati di training e il set di dati di test.
    
    Il set di dati di training verrà utilizzato per un modello di training. Il set di dati di test verrà usato per valutare i modelli in un progetto di machine learning. L'attività Suddivisione condizionale per il mapping di flussi di dati suddividerebbe i dati di training e i dati di test.

* Rimuovere i dati non qualificati.

    Potrebbe essere necessario rimuovere dati non qualificati, ad esempio un file parquet con zero righe. In questa esercitazione si userà l'attività aggregate per ottenere un conteggio del numero di righe. Il conteggio delle righe sarà una condizione per rimuovere i dati non qualificati.

## <a name="preparation"></a>Preparazione

Usare la seguente tabella del database SQL di Azure.

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

Il flusso di dati seguente converte una tabella del database SQL in un formato di file parquet:

- **Set** di dati di origine: tabella delle transazioni del database SQL.
- **Set di dati sink**: archiviazione BLOB con formato parquet.

## <a name="remove-unqualified-data-based-on-row-count"></a>Rimuovere i dati non qualificati in base al numero di righe

Si supponga di dover rimuovere un numero di righe minore di due.

1. Utilizzare l'attività di aggregazione per ottenere un conteggio del numero di righe. Utilizzare il **raggruppamento in** base a Col2 e le **aggregazioni** con `count(1)` per il conteggio delle righe.

    ![Screenshot che Mostra come configurare l'attività di aggregazione per ottenere un conteggio del numero di righe.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Utilizzando l'attività sink, selezionare il **tipo di sink** come **cache** nella scheda **sink** . Selezionare quindi la colonna desiderata dall'elenco a discesa **colonne chiave** nella scheda **Impostazioni** .

    ![Screenshot che mostra la configurazione dell'attività CacheSink per ottenere un conteggio del numero di righe in un sink memorizzato nella cache.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Utilizzare l'attività colonna derivata per aggiungere una colonna Conteggio righe nel flusso di origine. Nella scheda **impostazioni della colonna derivata** usare l' `CacheSink#lookup` espressione per ottenere un conteggio di righe da CacheSink.

    ![Screenshot che mostra la configurazione dell'attività colonna derivata per aggiungere un conteggio del numero di righe in source1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Utilizzare l'attività Suddivisione condizionale per rimuovere i dati non qualificati. In questo esempio, il conteggio delle righe è basato sulla colonna Col2. La condizione consiste nel rimuovere un conteggio di righe minore di due, quindi verranno rimosse due righe (ID = 2 e ID = 7). I dati non qualificati verranno salvati nell'archiviazione BLOB per la gestione dei dati.

    ![Screenshot che Mostra come configurare l'attività di Suddivisione condizionale per ottenere i dati maggiori o uguali a due.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Creare una nuova origine per ottenere un conteggio del numero di righe che verranno usate nell'origine originale nei passaggi successivi.
>    * Usare CacheSink dal punto di vista delle prestazioni.

## <a name="split-training-data-and-test-data"></a>Suddividere dati di training e dati di test

Si vuole suddividere i dati di training e i dati di test per ogni partizione. In questo esempio, per lo stesso valore di col2, ottenere le prime due righe come dati di test e il resto delle righe come dati di training.

1. Utilizzare l'attività finestra per aggiungere un numero di riga di colonna per ogni partizione. Nella scheda **over** selezionare una colonna per la partizione. In questa esercitazione si eseguirà la partizione per Col2. Assegnare un ordine alla scheda **ordinamento** , che in questa esercitazione sarà basata sull'ID. Assegnare un ordine nella scheda **colonne finestra** per aggiungere una colonna come numero di riga per ogni partizione.

    ![Screenshot che mostra la configurazione dell'attività della finestra per aggiungere una nuova colonna che rappresenta il numero di riga.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Utilizzare l'attività Suddivisione condizionale per suddividere le prime due righe di ogni partizione nel set di dati di test e il resto delle righe nel set di dati di training. Nella scheda **Impostazioni Suddivisione condizionale** usare l'espressione `lesserOrEqual(RowNum,2)` come condizione.

    ![Screenshot che mostra la configurazione dell'attività Suddivisione condizionale per suddividere il set di dati corrente nel set di dati di training e nel set di dati di test.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Partizionare i set di risultati di training e test con formato parquet

Utilizzando l'attività sink, nella scheda **ottimizza** utilizzare un **valore univoco per partizione** per impostare una colonna come chiave di colonna per la partizione.

![Screenshot che mostra la configurazione dell'attività sink per impostare la partizione del set di dati di training.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Esaminiamo l'intera logica della pipeline.

![Screenshot che mostra la logica dell'intera pipeline.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Passaggi successivi

Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)del flusso di dati.
