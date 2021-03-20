---
title: 'Esercitazione: Analisi del sentiment con Servizi cognitivi'
description: Informazioni su come usare servizi cognitivi per l'analisi dei sentimenti in Azure sinapsi Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943699"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Esercitazione: analisi dei sentimenti con servizi cognitivi (anteprima)

In questa esercitazione si apprenderà come arricchire facilmente i dati in Azure sinapsi Analytics con [Servizi cognitivi di Azure](../../cognitive-services/index.yml). Si utilizzeranno le funzionalità [analisi del testo](../../cognitive-services/text-analytics/index.yml) per eseguire l'analisi dei sentimenti. 

Un utente in una sinapsi di Azure può semplicemente selezionare una tabella contenente una colonna di testo da arricchire con i sentimenti. Questi sentimenti possono essere positivi, negativi, misti o neutri. Verrà restituita anche una probabilità.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> - Procedura per ottenere un set di dati della tabella Spark che contiene una colonna di testo per l'analisi dei sentimenti.
> - Uso di un'esperienza guidata in sinapsi di Azure per arricchire i dati usando Analisi del testo in Servizi cognitivi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure sinapsi Analytics](../get-started-create-workspace.md) con un Azure Data Lake storage Gen2 account di archiviazione configurato come risorsa di archiviazione predefinita. È necessario essere il *collaboratore dati BLOB di archiviazione* del data Lake storage Gen2 file System con cui si lavora.
- Pool di Spark nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un pool di Spark in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Procedura di pre-configurazione descritta nell'esercitazione [configurare servizi cognitivi in sinapsi di Azure](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Creare una tabella Spark

Per questa esercitazione è necessaria una tabella di Spark.

1. Scaricare il file di [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) , che contiene un set di dati per analisi del testo. 

1. Caricare il file nell'account di archiviazione della sinapsi di Azure in Data Lake Storage Gen2.
  
   ![Screenshot che mostra le selezioni per il caricamento dei dati.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Creare una tabella Spark dal file con estensione CSV facendo clic con il pulsante destro del mouse sul file e selezionando **nuovo notebook**  >  **Crea tabella Spark**.

   ![Screenshot che mostra le selezioni per la creazione di una tabella Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Assegnare un nome alla tabella nella cella di codice ed eseguire il notebook in un pool di Spark. Ricordarsi di impostare `header=True` .

   ![Screenshot che mostra l'esecuzione di un notebook.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Aprire la procedura guidata Servizi cognitivi

1. Fare clic con il pulsante destro del mouse sulla tabella Spark creata nella procedura precedente. Selezionare **Machine Learning**  >  **arricchisci con il modello esistente** per aprire la procedura guidata.

   ![Screenshot che mostra le selezioni per l'apertura della procedura guidata di assegnazione dei punteggi.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Viene visualizzato un pannello di configurazione e viene chiesto di selezionare un modello di servizi cognitivi. Selezionare **analisi del testo-analisi del sentiment**.

   ![Screenshot che mostra la selezione di un modello di servizi cognitivi.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Specificare i dettagli di autenticazione

Per eseguire l'autenticazione in Servizi cognitivi, è necessario fare riferimento al segreto per l'insieme di credenziali delle chiavi. Gli input seguenti dipendono dai [passaggi prerequisiti](tutorial-configure-cognitive-services-synapse.md) che devono essere completati prima di questo punto.

- **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure a cui appartiene l'insieme di credenziali delle chiavi.
- **Account servizi cognitivi**: immettere la risorsa analisi del testo a cui ci si connetterà.
- **Azure Key Vault servizio collegato**: come parte dei passaggi preliminari, è stato creato un servizio collegato alla risorsa analisi del testo. Selezionarlo qui.
- **Nome del segreto**: immettere il nome del segreto nell'insieme di credenziali delle chiavi che contiene la chiave per l'autenticazione alla risorsa Servizi cognitivi.

![Screenshot che mostra i dettagli di autenticazione per un insieme di credenziali delle chiavi.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Configurare l'analisi dei sentimenti

Configurare quindi l'analisi dei sentimenti. Selezionare i dettagli seguenti:
- **Lingua**: selezionare **inglese** come lingua del testo su cui si vuole eseguire l'analisi dei sentimenti.
- **Colonna di testo**: selezionare il **commento (stringa)** come colonna di testo nel set di dati che si desidera analizzare per determinare l'opinione.

Al termine, selezionare **Apri Notebook**. Viene così generato un notebook con il codice PySpark che esegue l'analisi dei sentimenti con servizi cognitivi di Azure.

![Screenshot che mostra le selezioni per la configurazione dell'analisi dei sentimenti.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Eseguire il notebook

Il notebook appena aperto usa la [libreria mmlspark](https://github.com/Azure/mmlspark) per connettersi ai servizi cognitivi. I dettagli di Azure Key Vault forniti consentono di fare riferimento in modo sicuro ai segreti da questa esperienza senza divulgarli.

È ora possibile eseguire tutte le celle per arricchire i dati con i sentimenti. Selezionare **Esegui tutto**. 

Le opinioni vengono restituite come **positivo**, **negativo**, **neutro** o **misto**. Si ottengono anche probabilità per ogni sentimento. [Altre informazioni sull'analisi dei sentimenti in Servizi cognitivi](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Screenshot che mostra l'analisi dei sentimenti.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Passaggi successivi
- [Esercitazione: Rilevamento anomalie con Servizi cognitivi](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: assegnazione di punteggi ai modelli di Machine Learning nei pool SQL dedicati a sinapsi di Azure](tutorial-sql-pool-model-scoring-wizard.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)