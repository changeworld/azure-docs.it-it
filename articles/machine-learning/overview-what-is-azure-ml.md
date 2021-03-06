---
title: Informazioni su Azure Machine Learning
description: Azure Machine Learning è una soluzione di data science integrata che consente a data scientist e operatori di Machine Learning di modellare e distribuire applicazioni di Machine Learning nel cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: cd395f0ff29eed1e09f5bdac882482b201052c90
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814830"
---
# <a name="what-is-azure-machine-learning"></a>Informazioni su Azure Machine Learning

Questo articolo illustra Azure Machine Learning, un ambiente basato sul cloud utilizzabile per eseguire il training, distribuire, automatizzare, gestire e tenere traccia dei modelli di Machine Learning. 

È possibile usare Azure Machine Learning per qualsiasi tipo di Machine Learning, dalla forma classica al Deep Learning, con e senza supervisione. Che si preferisca scrivere codice Python o R con l'SDK oppure usare opzioni senza codice o con poco codice nello [Studio](#build-ml-models-in-the-studio), è possibile creare, eseguire il training e monitorare modelli di Machine Learning e Deep Learning in un'area di lavoro di Azure Machine Learning. 

Iniziare il training nel computer locale per poi aumentare il numero di istanze nel cloud. 

Il servizio interagisce anche con gli strumenti open source più diffusi di Deep Learning e apprendimento per rinforzo, ad esempio PyTorch, TensorFlow, scikit-learn e Ray RLlib. 

> [!Tip]
> **Versione di valutazione gratuita.**  Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree). Si ricevono così crediti da spendere in servizi di Azure. Quando i crediti saranno esauriti, sarà possibile mantenere l'account e usare i [servizi di Azure gratuiti](https://azure.microsoft.com/free/). Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito.


## <a name="what-is-machine-learning"></a>Che cos'è l'apprendimento automatico?

Machine Learning è una tecnica di analisi scientifica dei dati che consente ai computer di usare i dati esistenti per prevedere comportamenti, tendenze e risultati futuri. Con l'apprendimento automatico, i computer apprendono senza essere programmati in modo esplicito.

Queste previsioni o stime di Machine Learning possono rendere più intelligenti le app e i dispositivi. Quando si effettuano acquisti online, ad esempio, l'apprendimento automatico consiglia altri prodotti che potrebbero interessare in base a ciò che si è acquistato. Quando si usa la carta di credito, l'apprendimento automatico confronta la transazione con un database di transazioni e consente di rilevare eventuali frodi. Infine, quando il robot aspirapolvere aspira la polvere in una stanza, l'apprendimento automatico gli consente di decidere se il lavoro è stato completato.

## <a name="machine-learning-tools-to-fit-each-task"></a>Strumenti di Machine Learning adatti per ogni attività 

Azure Machine Learning offre tutti gli strumenti necessari agli sviluppatori e ai data scientist per i rispettivi flussi di lavoro di Machine Learning, tra cui:
+ Progettazione [Azure Machine Learning :](tutorial-designer-automobile-price-train-score.md)moduli drag-n-drop per compilare gli esperimenti e quindi distribuire pipeline in un ambiente a basso codice.

+ Notebook di Jupyter: usare i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks) forniti o creare notebook personalizzati per sfruttare gli esempi dell'<a href="/python/api/overview/azure/ml/intro" target="_blank">SDK per Python</a> per Machine Learning. 

+ Notebook o script R in cui usare l'<a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK per R</a> per scrivere codice personalizzato o usare i moduli R nella finestra di progettazione.

+ L’[acceleratore di soluzione molti modelli](https://aka.ms/many-models) (anteprima) si basa su Azure Machine Learning consente di eseguire il training, utilizzare e gestire centinaia o addirittura migliaia di modelli di Machine Learning.

+ [L'estensione di Machine Learning Visual Studio Code (anteprima)](how-to-set-up-vs-code-remote.md) offre un ambiente di sviluppo completo per la compilazione e la gestione dei progetti di Machine Learning.

+ [L'interfaccia della riga di](reference-azure-machine-learning-cli.md) comando di Machine Learning è un'estensione dell'interfaccia della riga di comando di Azure che fornisce comandi per la gestione con Azure Machine Learning risorse dalla riga di comando.

+ Integrazione con [framework open source](concept-open-source.md) come PyTorch, TensorFlow e scikit-learn e molto altro ancora per il training, la distribuzione e la gestione del processo di Machine Learning end-to-end.

+ [Apprendimento per rinforzo](how-to-use-reinforcement-learning.md) con Ray RLlib

È persino possibile usare [MLflow per monitorare le metriche e distribuire i modelli](how-to-use-mlflow.md) oppure Kubeflow per [creare pipeline di flussi di lavoro end-to-end](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Creare modelli di Machine Learning in Python o R

Iniziare il training nel computer locale usando Azure Machine Learning <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a> o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a> per poi aumentare il numero di istanze nel cloud. 

Grazie alla disponibilità di molte [destinazioni di calcolo](how-to-create-attach-compute-studio.md), ad esempio l'ambiente di calcolo di Azure Machine Learning e [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), e a [servizi avanzati per l'ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md), è possibile creare modelli migliori in modo più rapido, sfruttando la potenza del cloud.

È anche possibile [automatizzare il training e l'ottimizzazione dei modelli](tutorial-auto-train-models.md) usando l'SDK.

## <a name="build-ml-models-in-the-studio"></a>Creare modelli di Machine Learning nello Studio

[Azure Machine Learning Studio](https://studio.azureml.net) è un portale Web in Azure Machine Learning che contiene opzioni con codice ridotto e senza codice per il training dei modelli, la distribuzione e la gestione degli asset. Lo Studio si integra con Azure Machine Learning SDK per un'esperienza semplificata. Per altre informazioni, vedere [Informazioni su Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

+ **Finestra di progettazione di Azure Machine Learning**

  Usare la [finestra di progettazione](concept-designer.md) per eseguire il training e distribuire i modelli di Machine Learning senza scrivere codice. Per iniziare, provare l'esercitazione sulla [finestra di progettazione](tutorial-designer-automobile-price-train-score.md). 

  ![GIF animata dell'interfaccia di trascinamento selezione della finestra di progettazione di Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Tenere traccia degli esperimenti**

  Informazioni su come [tenere traccia e visualizzare gli esperimenti di data science](how-to-track-monitor-analyze-runs.md) nello Studio. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Dettagli dell'esecuzione in Azure Machine Learning Studio":::


+ **E molto altro ancora...**

  Visitare Azure Machine Learning Studio all'indirizzo [ml.azure.com](https://studio.azureml.net).


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Esecuzione della distribuzione e gestione del ciclo di vita
Dopo aver creato il modello appropriato, è possibile usarlo facilmente in un servizio Web, in un dispositivo IoT o in Power BI. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](how-to-deploy-and-where.md).

È quindi possibile gestire i modelli distribuiti usando l'[SDK di Azure Machine Learning per Python](/python/api/overview/azure/ml/), [Azure Machine Learning Studio](https://ml.azure.com) o l'[interfaccia della riga di comando di Machine Learning](reference-azure-machine-learning-cli.md).

Questi modelli possono essere utilizzati e restituiscono stime, [in tempo reale](how-to-consume-web-service.md) o [in modo asincrono](./tutorial-pipeline-batch-scoring-classification.md), su grandi quantità di dati.

È inoltre possibile usare [pipeline avanzate di Machine Learning](concept-ml-pipelines.md) per collaborare in tutti i passaggi di preparazione dei dati, training dei modelli, valutazione e distribuzione. Le pipeline consentono di:

* Automatizzare il processo di Machine Learning end-to-end nel cloud
* Riusare i componenti e ripetere i passaggi solo quando necessario
* Usare risorse di calcolo diverse in ogni passaggio
* Eseguire attività di assegnazione di punteggio batch

Se si vogliono usare gli script per automatizzare il flusso di lavoro di Machine Learning, l'[interfaccia della riga di comando di Machine Learning](reference-azure-machine-learning-cli.md) fornisce strumenti da riga di comando che eseguono attività comuni, ad esempio l'invio di un'esecuzione di training o la distribuzione di un modello.

Per iniziare a usare Azure Machine Learning, vedere [Passaggi successivi](#next-steps).

## <a name="integration-with-other-services"></a>Integrazione con altri servizi

Azure Machine Learning interagisce con altri servizi nella piattaforma Azure e si integra anche con strumenti open source come Git e MLFlow.

+ Destinazioni di calcolo come il __servizio Azure Kubernetes__, __Istanze di Azure Container__, __Azure Databricks__, __Azure Data Lake Analytics__ e __Azure HDInsight__. Per altre informazioni sulle destinazioni di calcolo, vedere [Informazioni sulle destinazioni di calcolo](concept-compute-target.md).
+ __Griglia di eventi di Azure__. Per altre informazioni, vedere [Utilizzare eventi di Azure Machine Learning](./how-to-use-event-grid.md).
+ __Monitoraggio di Azure__. Per altre informazioni, vedere [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).
+ Archivi dati come gli __account di archiviazione di Azure__, __Azure Data Lake Storage__, il __database SQL di Azure__, __Database di Azure per PostgreSQL__ e i __set di dati aperti di Azure__. Per altre informazioni, vedere [Accedere ai dati nei servizi di archiviazione di Azure](how-to-access-data.md) e [Creare set di dati con set di dati aperti di Azure](how-to-create-register-datasets.md).
+ __Reti virtuali di Azure__. Per altre informazioni, vedere [Panoramica della privacy e dell'isolamento rete virtuale](how-to-network-security-overview.md).
+ __Azure Pipelines__. Per altre informazioni, vedere [Eseguire il training e la distribuzione di modelli di Machine Learning](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Log di repository Git__. Per altre informazioni, vedere [Integrazione di Git](concept-train-model-git-integration.md).
+ __MLFlow__. Per altre informazioni, vedere [MLflow per tenere traccia delle metriche](how-to-use-mlflow.md) e [Distribuire modelli Mlflow come servizio Web](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__. Per altre informazioni, vedere come [creare pipeline di flussi di lavoro end-to-end](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Comunicazioni sicure

È possibile usare l'account di archiviazione di Azure, le destinazioni di calcolo e le altre risorse in modo sicuro all'interno di una rete virtuale per eseguire il training dei modelli e l'inferenza. Per altre informazioni, vedere [Panoramica della privacy e dell'isolamento rete virtuale](how-to-network-security-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Creare il primo esperimento con il metodo preferito:
- + [Introduzione ad Azure Machine Learning nel proprio ambiente di sviluppo](tutorial-1st-experiment-sdk-setup-local.md)
  + [Usare notebook di Jupyter in un'istanza di ambiente di calcolo per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-1st-experiment-sdk-setup.md)
  + [Usare il servizio Machine Learning automatizzato per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-first-experiment-automated-ml.md) 
  + [Gestire le risorse in Visual Studio Code](how-to-manage-resources-vscode.md)
  + [Usare Visual Studio Code per eseguire il training e distribuire un modello di classificazione delle immagini](tutorial-train-deploy-image-classification-model-vscode.md)
  + [Usare le funzionalità di trascinamento della selezione della finestra di progettazione per eseguire il training e la distribuzione](tutorial-designer-automobile-price-train-score.md) 
  + [Usare l'interfaccia della riga di comando di Machine Learning per eseguire il training e la distribuzione di un modello](tutorial-train-deploy-model-cli.md)

- Vedere le [pipeline di apprendimento automatico](concept-ml-pipelines.md) per compilare, ottimizzare e gestire gli scenari di Machine Learning.

- Vedere l'articolo di approfondimento su [architettura e concetti di Azure Machine Learning](concept-azure-machine-learning-architecture.md).
