---
title: 'Esercitazione: Analisi del sentiment con Servizi cognitivi'
description: Esercitazione su come sfruttare Servizi cognitivi per l'analisi del sentiment in Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465226"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Esercitazione: Analisi del sentiment con Servizi cognitivi (anteprima)

Questa esercitazione descrive come arricchire facilmente i dati in Azure Synapse con [Servizi cognitivi](https://go.microsoft.com/fwlink/?linkid=2147492). Verranno usate le funzionalità di [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) per eseguire l'analisi del sentiment. Un utente di Azure Synapse può semplicemente selezionare una tabella contenente una colonna di testo per arricchirlo con sentiment. I sentiment possono essere positivi, negativi, misti o neutrali e verrà restituita anche una probabilità.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> - Procedura per ottenere il set di dati di una tabella Spark contenente una colonna di testo per l'analisi del sentiment.
> - Usare un'esperienza di procedura guidata in Azure Synapse per arricchire i dati con Analisi del testo di Servizi cognitivi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure Synapse Analytics](../get-started-create-workspace.md) con un account di archiviazione di ADLS Gen2 configurato come risorsa archiviazione predefinita. È necessario essere il **collaboratore dei dati del BLOB di archiviazione** del file system di ADLS Gen2 che si vuole usare.
- Pool di Spark nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un pool di Spark in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Prima di seguire questa esercitazione, è anche necessario completare i passaggi di preconfigurazione descritti qui. [Configurare Servizi cognitivi in Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Creare una tabella Spark

Per questa esercitazione è necessaria una tabella Spark.

1. Scaricare il file CSV seguente contenente un set di dati per l'analisi del testo: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Caricare il file nell'account di archiviazione di Azure Synapse ADLSGen2.
![Caricare i dati](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Fare clic con il pulsante destro del mouse sul file CSV e scegliere **Nuovo notebook -> Create Spark table** (Crea tabella Spark) per creare una tabella Spark.
![Creazione di una tabella Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Assegnare un nome alla tabella nella cella di codice ed eseguire il notebook in un pool di Spark. Assicurarsi di impostare "header = True".
![Eseguire un notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Avviare la procedura guidata di Servizi cognitivi

1. Fare clic con il pulsante destro del mouse sulla tabella Spark creata nel passaggio precedente. Scegliere "Machine Learning-> Enrich with existing model" (Arricchisci con il modello esistente) per aprire la procedura guidata.
![Avviare la procedura guidata di assegnazione di punteggi](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Verrà visualizzato un pannello di configurazione e verrà chiesto di selezionare un modello di Servizi cognitivi. Selezionare Analisi del testo - Analisi del sentiment.

![Avviare la procedura guidata di assegnazione di punteggi - Passaggio 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Specificare i dettagli di autenticazione

Per eseguire l'autenticazione in Servizi cognitivi, è necessario fare riferimento al segreto da usare in Key Vault. Gli input seguenti dipendono dai [passaggi prerequisiti](tutorial-configure-cognitive-services-synapse.md) che dovrebbero essere stati completati prima di questo passaggio.

- **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure a cui appartiene l'insieme di credenziali delle chiavi.
- **Account Servizi cognitivi**: la risorsa Analisi del testo a cui ci si connette.
- **Servizio collegato Azure Key Vault**: nel corso dei passaggi sui prerequisiti è stato creato un servizio collegato alla risorsa Analisi del testo. Selezionarlo qui.
- **Nome segreto**: il nome del segreto nell'insieme di credenziali delle chiavi che contiene la chiave per eseguire l'autenticazione nella risorsa di Servizi cognitivi.

![Specificare i dettagli di Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Configurare l'analisi del sentiment

Successivamente, è necessario configurare l'analisi del sentiment. Selezionare i dettagli seguenti:
- **Lingua**: selezionare la lingua del testo di cui eseguire l'analisi del sentiment. Selezionare **EN**.
- **Colonna testo**: la colonna di testo del set di dati da analizzare per determinare il sentiment. Selezionare la colonna della tabella **commento**.

Al termine, fare clic su **Apri notebook**. Verrà generato automaticamente un notebook con il codice PySpark che esegue l'analisi del sentiment con Servizi cognitivi.

![Configurare l'analisi del sentiment](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Aprire ed eseguire il notebook

Il notebook appena aperto usa la [libreria mmlspark](https://github.com/Azure/mmlspark) per connettersi a Servizi cognitivi.

I dettagli di Azure Key Vault forniti consentono di fare riferimento in modo sicuro ai segreti di questa esperienza senza rivelarli.

È ora possibile scegliere **Esegui tutte le celle** per arricchire i dati con i sentiment. Verranno restituiti i sentiment positivi, negativi, neutrali e misti, oltre alle probabilità per ognuno. Per altre informazioni, vedere [Servizi cognitivi - Analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2147792).

![Eseguire l'analisi del sentiment](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Passaggi successivi
- [Esercitazione: Rilevamento anomalie con Servizi cognitivi](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: Assegnazione di punteggi al modello di Machine Learning nei pool SQL dedicati di Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)