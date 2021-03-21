---
title: 'Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning per i pool SQL dedicati'
description: Esercitazione su come usare la procedura guidata di assegnazione di punteggi a modelli di Machine Learning nei pool SQL dedicati.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935235"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning (anteprima) per i pool SQL dedicati

Informazioni su come arricchire facilmente i dati nei pool SQL dedicati con modelli predittivi di Machine Learning. I modelli creati dai data scientist sono ora facilmente accessibili ai professionisti dei dati per l'analisi predittiva. Un professionista dei dati in Azure sinapsi Analytics può semplicemente selezionare un modello dal registro di sistema del modello Azure Machine Learning per la distribuzione nei pool SQL sinapsi di Azure e avviare le stime per arricchire i dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Eseguire il training di un modello di apprendimento automatico predittivo e registrare il modello nel registro di sistema del modello Azure Machine Learning.
> - Utilizzare la procedura guidata per il Punteggio SQL per avviare le stime in un pool SQL dedicato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure sinapsi Analytics](../get-started-create-workspace.md) con un Azure Data Lake storage Gen2 account di archiviazione configurato come risorsa di archiviazione predefinita. È necessario essere il *collaboratore dati BLOB di archiviazione* del data Lake storage Gen2 file System con cui si lavora.
- Pool SQL dedicato nell'area di lavoro di Azure Synapse Analytics. Per informazioni dettagliate, vedere [Creare un pool SQL dedicato](../quickstart-create-sql-pool-studio.md).
- Servizio collegato di Azure Machine Learning nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un servizio collegato di Azure Machine Learning in Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Eseguire il training di un modello in Azure Machine Learning

Prima di iniziare, verificare che la versione in uso di sklearn sia 0.20.3.

Prima di eseguire tutte le celle nel notebook, verificare che l'istanza di calcolo sia in esecuzione.

![Screenshot che mostra la verifica del Azure Machine Learning di calcolo.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Passare all'area di lavoro Azure Machine Learning.

1. Scaricare il file [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Aprire l'area di lavoro Azure Machine Learning in [Azure Machine Learning Studio](https://ml.azure.com).

1. Passare a **notebook**  >  **caricare i file**. Selezionare quindi il file **pronostici NYC Taxi Tips. ipynb** scaricato e caricato.
   ![Screenshot del pulsante per il caricamento di un file.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Dopo aver caricato e aperto il notebook, selezionare **Esegui tutte le celle**.

   Una delle celle potrebbe non riuscire e richiedere di eseguire l'autenticazione in Azure. Controllare questa impostazione negli output della cella ed eseguire l'autenticazione nel browser attenendosi al collegamento e immettendo il codice. Quindi eseguire di nuovo il notebook.

1. Il notebook eseguirà il training di un modello ONNX e lo registrerà con MLflow. Passare a **modelli** per verificare che il nuovo modello sia registrato correttamente.
   ![Screenshot che mostra il modello nel registro di sistema.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. L'esecuzione del notebook comporta anche l'esportazione dei dati di test in un file CSV. Scaricare il file CSV nel sistema locale. Successivamente, si importerà il file CSV in un pool SQL dedicato e si useranno i dati per testare il modello.

   Il file CSV viene creato nella stessa cartella del file del notebook. Se non lo si visualizza immediatamente, selezionare **Aggiorna** in Esplora file.

   ![Screenshot che mostra il file C S V.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Avviare stime con la procedura guidata per il Punteggio SQL

1. Aprire l'area di lavoro sinapsi di Azure con sinapsi Studio.

1. Passare a **Data**  >  **Linked**  >  **Storage Accounts**. Caricare `test_data.csv` nell'account di archiviazione predefinito.

   ![Screenshot che mostra le selezioni per il caricamento dei dati.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Passare a **Sviluppo** > **Script SQL**. Creare un nuovo script SQL per caricare `test_data.csv` nel pool SQL dedicato.

   > [!NOTE]
   > Aggiornare l'URL del file in questo script prima di eseguirlo.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Caricare i dati nel pool SQL dedicato](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Passare a **Dati** > **Area di lavoro**. Aprire la procedura guidata di assegnazione di punteggi di SQL facendo clic con il pulsante destro del mouse sulla tabella Pool SQL dedicato. Scegliere **Machine Learning** > **Enrich with existing model** (Arricchisci con il modello esistente).

   > [!NOTE]
   > L'opzione di apprendimento automatico non viene visualizzata a meno che non sia stato creato un servizio collegato per Azure Machine Learning. (Vedere [prerequisiti](#prerequisites) all'inizio di questa esercitazione).

   ![Screenshot che mostra l'opzione di machine learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selezionare un'area di lavoro collegata di Azure Machine Learning nella casella di riepilogo a discesa. Questo passaggio carica un elenco di modelli di Machine Learning dal registro di sistema del modello dell'area di lavoro Azure Machine Learning scelta. Attualmente sono supportati solo i modelli ONNX, pertanto in questo passaggio verranno visualizzati solo i modelli di ONNX.

1. Selezionare il modello appena sottoposto a training, quindi selezionare **continua**.

   ![Screenshot che mostra la selezione del modello di Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Eseguire il mapping delle colonne della tabella agli input del modello e specificare gli output del modello. Se il modello viene salvato nel formato MLflow e la firma del modello viene popolata, il mapping verrà eseguito automaticamente usando una logica basata sulla somiglianza dei nomi. L'interfaccia supporta anche il mapping manuale.

   Selezionare **Continua**.

   ![Screenshot che mostra il mapping da tabella a modello.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Il codice T-SQL generato viene incapsulato all'interno di una stored procedure. Questo è il motivo per cui è necessario specificare un nome stored procedure. Il file binario del modello, inclusi i metadati (versione, descrizione e altre informazioni), verrà copiato fisicamente da Azure Machine Learning a una tabella del pool SQL dedicata. Quindi, è necessario specificare la tabella in cui salvare il modello. 

   È possibile scegliere una **tabella esistente** o **crearne una nuova**. Al termine, selezionare **Distribuisci modello + Apri script** per distribuire il modello e generare uno script di stima T-SQL.

   ![Screenshot che mostra le selezioni per la creazione di un stored procedure.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Dopo aver generato lo script, selezionare **Esegui** per eseguire il punteggio e ottenere stime.

   ![Screenshot che mostra il punteggio e le stime.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Synapse](quickstart-integrate-azure-machine-learning.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)
