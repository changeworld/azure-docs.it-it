---
title: 'Esercitazione: Rilevamento anomalie con Servizi cognitivi'
description: Esercitazione su come sfruttare Servizi cognitivi per il rilevamento anomalie in Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464458"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Esercitazione: Rilevamento anomalie con Servizi cognitivi (anteprima)

Questa esercitazione descrive come arricchire facilmente i dati in Azure Synapse con [Servizi cognitivi](https://go.microsoft.com/fwlink/?linkid=2147492). Verrà usato il servizio [Rilevamento anomalie](https://go.microsoft.com/fwlink/?linkid=2147493) per eseguire il rilevamento anomalie. Un utente di Azure Synapse può semplicemente selezionare una tabella per arricchire il rilevamento anomalie.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> - Procedura per ottenere un set di dati di una tabella Spark contenente dati di serie temporali.
> - Usare un'esperienza di procedura guidata in Azure Synapse per arricchire i dati con Rilevamento anomalie di Servizi cognitivi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure Synapse Analytics](../get-started-create-workspace.md) con un account di archiviazione di ADLS Gen2 configurato come risorsa archiviazione predefinita. È necessario essere il **collaboratore dei dati del BLOB di archiviazione** del file system di ADLS Gen2 che si vuole usare.
- Pool di Spark nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un pool di Spark in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Prima di seguire questa esercitazione, è anche necessario completare i passaggi di preconfigurazione descritti qui. [Configurare Servizi cognitivi in Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Creare una tabella Spark

Per questa esercitazione è necessaria una tabella Spark.

1. Scaricare il file del notebook seguente contenente il codice per generare una tabella Spark: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Caricare il file nell'area di lavoro di Azure Synapse.
![Caricare il notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Aprire il file del notebook e scegliere **Esegui tutte le celle**.
![Creazione di una tabella Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Nel database Spark predefinito verrà visualizzata una tabella Spark denominata **anomaly_detector_testing_data**.

## <a name="launch-cognitive-services-wizard"></a>Avviare la procedura guidata di Servizi cognitivi

1. Fare clic con il pulsante destro del mouse sulla tabella Spark creata nel passaggio precedente. Scegliere "Machine Learning-> Enrich with existing model" (Arricchisci con il modello esistente) per aprire la procedura guidata.
![Avviare la procedura guidata di assegnazione di punteggi](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Verrà visualizzato un pannello di configurazione e verrà chiesto di selezionare un modello di Servizi cognitivi. Selezionare Rilevamento anomalie.

![Avviare la procedura guidata di assegnazione di punteggi - Passaggio 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Specificare i dettagli di autenticazione

Per eseguire l'autenticazione in Servizi cognitivi, è necessario fare riferimento al segreto da usare in Key Vault. Gli input seguenti dipendono dai [passaggi prerequisiti](tutorial-configure-cognitive-services-synapse.md) che dovrebbero essere stati completati prima di questo passaggio.

- **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure a cui appartiene l'insieme di credenziali delle chiavi.
- **Account Servizi cognitivi**: la risorsa Analisi del testo a cui ci si connette.
- **Servizio collegato Azure Key Vault**: nel corso dei passaggi sui prerequisiti è stato creato un servizio collegato alla risorsa Analisi del testo. Selezionarlo qui.
- **Nome segreto**: il nome del segreto nell'insieme di credenziali delle chiavi che contiene la chiave per eseguire l'autenticazione nella risorsa di Servizi cognitivi.

![Specificare i dettagli di Azure Key Vault](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Configurare Rilevamento anomalie

Successivamente, è necessario configurare il rilevamento anomalie. Specificare i dettagli seguenti:

- Granularità: la frequenza con cui i dati vengono campionati. Se ad esempio i dati hanno un valore per ogni minuto, la granularità sarà ogni minuto. Scegliere **mensile** 

- Timestamp: colonna che rappresenta il valore temporale della serie. Scegliere **timestamp**

- Timeseries value (Valore serie temporale): colonna che rappresenta il valore della serie nel momento specificato dalla colonna Timestamp. Scegliere **valore**

- Raggruppamento: colonna che raggruppa la serie, ovvero tutte le righe che hanno lo stesso valore in questa colonna dovranno formare un'unica serie temporale. Scegliere **gruppo**

Al termine, selezionare **Apri notebook**. Verrà generato automaticamente un notebook con il codice PySpark che esegue il rilevamento anomalie tramite Servizi cognitivi.

![Configurare il rilevamento anomalie](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Aprire ed eseguire il notebook

Il notebook appena aperto usa la [libreria mmlspark](https://github.com/Azure/mmlspark) per connettersi a Servizi cognitivi.

I dettagli di Azure Key Vault forniti consentono di fare riferimento in modo sicuro ai segreti di questa esperienza senza rivelarli.

È ora possibile scegliere **Esegui tutte le celle** per avviare il rilevamento anomalie. Vedere altre informazioni su [Servizi cognitivi - Rilevamento anomalie](https://go.microsoft.com/fwlink/?linkid=2147493).

![Eseguire il rilevamento anomalie](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Analisi del sentiment con Servizi cognitivi di Azure](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: Assegnazione di punteggi al modello di Machine Learning nei pool SQL dedicati di Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)