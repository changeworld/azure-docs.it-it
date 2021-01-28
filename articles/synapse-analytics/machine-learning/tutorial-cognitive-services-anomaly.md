---
title: 'Esercitazione: Rilevamento anomalie con Servizi cognitivi'
description: Informazioni su come usare servizi cognitivi per il rilevamento delle anomalie in Azure sinapsi Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943513"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Esercitazione: rilevamento delle anomalie con servizi cognitivi (anteprima)

In questa esercitazione si apprenderà come arricchire facilmente i dati in Azure sinapsi Analytics con [Servizi cognitivi di Azure](../../cognitive-services/index.yml). Si userà il [rilevatore di anomalie](../../cognitive-services/anomaly-detector/index.yml) per individuare le anomalie. Un utente di Azure Synapse può semplicemente selezionare una tabella per arricchire il rilevamento anomalie.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> - Procedura per ottenere un set di dati della tabella Spark contenente i dati delle serie temporali.
> - Uso di un'esperienza guidata in sinapsi di Azure per arricchire i dati usando il rilevatore di anomalie in Servizi cognitivi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Azure sinapsi Analytics](../get-started-create-workspace.md) con un Azure Data Lake storage Gen2 account di archiviazione configurato come risorsa di archiviazione predefinita. È necessario essere il *collaboratore dati BLOB di archiviazione* del data Lake storage Gen2 file System con cui si lavora.
- Pool di Spark nell'area di lavoro di Azure Synapse Analytics. Per i dettagli, vedere [Creare un pool di Spark in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Completamento dei passaggi di pre-configurazione nell'esercitazione [configurare i servizi cognitivi in una sinapsi di Azure](tutorial-configure-cognitive-services-synapse.md) .

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Creare una tabella Spark

Per questa esercitazione è necessaria una tabella di Spark.

1. Scaricare il file del notebook seguente che contiene il codice per generare una tabella Spark: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Caricare il file nell'area di lavoro di Azure Synapse.

   ![Screenshot che mostra le selezioni per il caricamento di un notebook.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Aprire il file del notebook e selezionare **Run all (Esegui tutto** ) per eseguire tutte le celle.

   ![Screenshot che mostra le selezioni per la creazione di una tabella Spark.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Nel database Spark predefinito verrà visualizzata una tabella Spark denominata **anomaly_detector_testing_data**.

## <a name="open-the-cognitive-services-wizard"></a>Aprire la procedura guidata Servizi cognitivi

1. Fare clic con il pulsante destro del mouse sulla tabella Spark creata nel passaggio precedente. Selezionare **Machine Learning**  >  **arricchisci con il modello esistente** per aprire la procedura guidata.

   ![Screenshot che mostra le selezioni per l'apertura della procedura guidata di assegnazione dei punteggi.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Viene visualizzato un pannello di configurazione e viene chiesto di selezionare un modello di servizi cognitivi. Selezionare **Rilevamento anomalie**.

   ![Screenshot che mostra la selezione del rilevatore di anomalie come modello.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Specificare i dettagli di autenticazione

Per eseguire l'autenticazione in Servizi cognitivi, è necessario fare riferimento al segreto nell'insieme di credenziali delle chiavi. Gli input seguenti dipendono dai [passaggi preliminari](tutorial-configure-cognitive-services-synapse.md) che devono essere completati prima di questo punto.

- **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure a cui appartiene l'insieme di credenziali delle chiavi.
- **Account servizi cognitivi**: immettere la risorsa analisi del testo a cui ci si connetterà.
- **Azure Key Vault servizio collegato**: come parte dei passaggi preliminari, è stato creato un servizio collegato alla risorsa analisi del testo. Selezionarlo qui.
- **Nome del segreto**: immettere il nome del segreto nell'insieme di credenziali delle chiavi che contiene la chiave per l'autenticazione alla risorsa Servizi cognitivi.

![Screenshot che mostra i dettagli di autenticazione per un insieme di credenziali delle chiavi.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Configurare il rilevatore di anomalie

Specificare i dettagli seguenti per configurare il rilevatore di anomalie:

- **Granularità**: la velocità con cui vengono campionati i dati. Scegliere **mensile**. 

- **Column timestamp**: colonna che rappresenta l'ora della serie. Scegliere **timestamp (String)**.

- **Colonna valore timeseries**: colonna che rappresenta il valore della serie al momento specificato dalla colonna timestamp. Scegliere **valore (Double)**.

- **Colonna di raggruppamento**: colonna che raggruppa la serie. ovvero tutte le righe che hanno lo stesso valore in questa colonna dovranno formare un'unica serie temporale. Scegliere **Group (String)**.

Al termine, selezionare **Apri Notebook**. Verrà generato un notebook con il codice PySpark che usa servizi cognitivi di Azure per rilevare le anomalie.

![Screenshot che mostra i dettagli di configurazione per il rilevatore di anomalie.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Eseguire il notebook

Il notebook appena aperto usa la [libreria mmlspark](https://github.com/Azure/mmlspark) per connettersi ai servizi cognitivi. I dettagli di Azure Key Vault forniti consentono di fare riferimento in modo sicuro ai segreti da questa esperienza senza divulgarli.

A questo punto è possibile eseguire tutte le celle per eseguire il rilevamento delle anomalie. Selezionare **Esegui tutto**. [Scopri di più sul rilevamento delle anomalie nei servizi cognitivi](../../cognitive-services/anomaly-detector/index.yml).

![Screenshot che mostra il rilevamento delle anomalie.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Analisi del sentiment con Servizi cognitivi di Azure](tutorial-cognitive-services-sentiment.md)
- [Esercitazione: assegnazione di punteggi ai modelli di Machine Learning nei pool SQL dedicati a sinapsi di Azure](tutorial-sql-pool-model-scoring-wizard.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics](what-is-machine-learning.md)
