---
title: Integrazione di machine learning open source
titleSuffix: Azure Machine Learning
description: Informazioni su come usare framework di apprendimento automatico Python open source per eseguire il training, la distribuzione e la gestione di soluzioni di Machine Learning end-to-end in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98223075"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integrazione open source con progetti Azure Machine Learning

È possibile eseguire il training, la distribuzione e la gestione del processo di Machine Learning end-to-end in Azure Machine Learning usando librerie e piattaforme open source per l'apprendimento automatico di Python.  Usare gli strumenti di sviluppo, ad esempio Jupyter notebook e Visual Studio Code, per sfruttare i modelli e gli script esistenti in Azure Machine Learning.  

In questo articolo sono disponibili altre informazioni su queste librerie e piattaforme open source.

## <a name="train-open-source-machine-learning-models"></a>Training di modelli di apprendimento automatico Open Source

Il processo di training di Machine Learning comporta l'applicazione di algoritmi ai dati allo scopo di ottenere un'attività o risolvere un problema. A seconda del problema, è possibile scegliere algoritmi diversi che meglio si adattano all'attività e ai dati. Per altre informazioni sui diversi rami di Machine Learning, vedere l'articolo relativo a [Deep Learning e Machine](./concept-deep-learning-vs-machine-learning.md) Learning e il foglio informativo sugli [algoritmi di Machine Learning](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Mantieni la privacy dei dati con la privacy differenziale

Per eseguire il training di un modello di apprendimento automatico, sono necessari i dati. A volte i dati sono sensibili ed è importante assicurarsi che i dati siano protetti e privati. La privacy differenziale è una tecnica che prevede la riservatezza delle informazioni in un set di dati. Per altre informazioni, vedere l'articolo sulla [conservazione della privacy dei dati](concept-differential-privacy.md). 

I toolkit per la privacy differenziale open source come [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) consentono [di mantenere la privacy dei dati](how-to-differential-privacy.md) nelle soluzioni Azure Machine Learning.

### <a name="classical-machine-learning-scikit-learn"></a>Apprendimento automatico classico: Scikit-learn

Per le attività di training che coinvolgono gli algoritmi di Machine Learning classici, ad esempio la classificazione, il clustering e la regressione, è possibile usare qualcosa di simile a Scikit-learn. Per informazioni su come eseguire il training di un modello di classificazione dei fiori, vedere l' [articolo How to train with Scikit-learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Reti neurali: PyTorch, TensorFlow, keras

Gli algoritmi di apprendimento automatico Open Source noti come reti neurali, un subset di Machine Learning, sono utili per il training di modelli di Deep Learning in Azure Machine Learning.

I Framework di apprendimento avanzato e le guide alle procedure open source includono:

 *  [PyTorch](https://github.com/pytorch/pytorch): eseguire [il training di un modello di classificazione di immagini Deep Learning usando Transfer Learning](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [riconosce le cifre scritte a mano usando TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [creare una rete neurale per analizzare le immagini tramite keras](how-to-train-keras.md)

Il training di un modello di apprendimento avanzato da zero spesso richiede grandi quantità di tempo, dati e risorse di calcolo. È possibile abbreviare il processo di training usando Transfer Learning. Il trasferimento dell'apprendimento è una tecnica che applica le informazioni ottenute dalla risoluzione di un problema a un problema diverso ma correlato. Ciò significa che è possibile riutilizzare un modello esistente. Per altre informazioni sull'apprendimento del trasferimento, vedere l'articolo sull'apprendimento [automatico di Visual](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) Studio.

### <a name="reinforcement-learning-ray-rllib"></a>Apprendimento per rinforzo: Ray RLLib

L'apprendimento per rinforzo è una tecnica di intelligenza artificiale che consente di eseguire il training dei modelli usando azioni, Stati e premi: gli agenti di formazione per rinforzo imparano a eseguire una serie di azioni predefinite che massimizzano i premi specificati in base allo stato corrente dell'ambiente. 

Il progetto [Ray RLLib](https://github.com/ray-project/ray) include un set di funzionalità che consentono una scalabilità elevata durante il processo di training. Il processo iterativo è il tempo e l'utilizzo intensivo di risorse come gli agenti di formazione di rinforzo tentano di apprendere il modo ottimale per ottenere un'attività.  Ray RLLib supporta anche Framework di apprendimento avanzato come TensorFlow e PyTorch.  

Per informazioni su come usare Ray RLLib con Azure Machine Learning, vedere [come eseguire il training di un modello di apprendimento di rinforzo](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Monitorare le prestazioni del modello: TensorBoard

Il training di uno o più modelli richiede la visualizzazione e l'ispezione delle metriche desiderate per assicurarsi che il modello venga eseguito come previsto. È possibile [usare TensorBoard in Azure Machine Learning per tenere traccia e visualizzare le metriche degli esperimenti](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Framework per modelli interpretabili e corretti

I sistemi di Machine Learning vengono usati in diverse aree della società, ad esempio banche, istruzione e sanità. Di conseguenza, è importante che questi sistemi siano responsabili delle stime e delle raccomandazioni che fanno per evitare conseguenze impreviste.

Framework open source come [InterpretML](https://github.com/interpretml/interpret/) e Fairlearn ( https://github.com/fairlearn/fairlearn) collaborare con Azure Machine Learning per creare modelli di apprendimento automatico più trasparenti ed equi.

Per ulteriori informazioni su come compilare modelli corretti e interpretabili, vedere gli articoli seguenti:

- [Interpretabilità del modello in Machine Learning automatizzato](how-to-machine-learning-interpretability.md)
- [Interpretare e spiegare i modelli di apprendimento automatico](how-to-machine-learning-interpretability-aml.md)
- [Illustrare i modelli AutoML](how-to-machine-learning-interpretability-automl.md)
- [Attenuare l'equità nei modelli di Machine Learning](concept-fairness-ml.md)
- [Usare Azure Machine Learning per l'equità del modello asset](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Distribuzione del modello

Una volta che i modelli sono stati sottoposti a training e pronti per la produzione, è necessario scegliere la modalità di distribuzione. Azure Machine Learning fornisce varie destinazioni di distribuzione. Per ulteriori informazioni, vedere l' [articolo where and How to deploy](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Standardizzare i formati di modello con ONNX

Dopo il training, il contenuto del modello, ad esempio i parametri appresi, viene serializzato e salvato in un file. Ogni Framework dispone di un proprio formato di serializzazione. Quando si utilizzano Framework e strumenti diversi, significa che è necessario distribuire i modelli in base ai requisiti del Framework. Per standardizzare questo processo, è possibile utilizzare il formato Open Neural Network Exchange (ONNX). ONNX è un formato Open Source per i modelli di intelligenza artificiale. ONNX supporta l'interoperabilità tra framework. Ciò significa che è possibile eseguire il training di un modello in uno dei numerosi framework di apprendimento automatico diffusi, ad esempio PyTorch, convertirlo in formato ONNX e utilizzare il modello ONNX in un Framework diverso come ML.NET.

Per ulteriori informazioni su ONNX e su come utilizzare i modelli ONNX, vedere gli articoli seguenti:

- [Creare e accelerare i modelli ML con ONNX](concept-onnx.md)
- [Usare i modelli ONNX nelle applicazioni .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Creare un pacchetto e distribuire modelli come contenitori

Le tecnologie del contenitore, ad esempio Docker, rappresentano un modo per distribuire i modelli come servizi Web. I contenitori offrono una piattaforma e un modo indipendente dalle risorse per creare e orchestrare ambienti software riproducibili. Con queste tecnologie di base, è possibile usare gli [ambienti preconfigurati](./how-to-use-environments.md), le [Immagini del contenitore preconfigurate](./how-to-deploy-custom-docker-image.md) o quelle personalizzate per distribuire i modelli di Machine Learning in, ad esempio i [cluster Kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python). Per i flussi di lavoro con utilizzo intensivo della GPU, è possibile usare strumenti come il server di inferenza NVIDIA Triton per [eseguire stime tramite GPU](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Proteggere le distribuzioni con la crittografia omomorfe

La protezione delle distribuzioni è una parte importante del processo di distribuzione. Per [distribuire servizi di inferenza crittografati](how-to-homomorphic-encryption-seal.md), usare la `encrypted-inference` libreria Python open source. Il `encrypted inferencing` pacchetto fornisce associazioni basate su [Microsoft Seal](https://github.com/Microsoft/SEAL), una libreria di crittografia omomorfe.

## <a name="machine-learning-operations-mlops"></a>Operazioni di Machine Learning (MLOps)

Machine Learning Operations (MLOps), comunemente concepito come DevOps per Machine Learning, consente di creare flussi di lavoro di apprendimento automatico più trasparenti, resilienti e riproducibili. Vedere l' [articolo What is MLOps](./concept-model-management-and-deployment.md) per altre informazioni su MLOps. 

Usando le procedure DevOps, ad esempio l'integrazione continua (CI) e la distribuzione continua (CD), è possibile automatizzare il ciclo di vita di Machine Learning end-to-end e acquisire i dati di governance. È possibile definire la pipeline di integrazione continua/recapito continuo di [machine learning nelle azioni di GitHub](./how-to-github-actions-machine-learning.md) per eseguire Azure Machine Learning attività di training e distribuzione. 

Acquisire le dipendenze software, le metriche, i metadati, i dati e il controllo delle versioni dei modelli sono una parte importante del processo di MLOps per creare pipeline trasparenti, riproducibili e controllabili. Per questa attività, è possibile [usare MLFlow in Azure Machine Learning](how-to-use-mlflow.md) e quando si esegue il [training dei modelli di Machine Learning in Azure Databricks](./how-to-use-mlflow-azure-databricks.md). È anche possibile [distribuire i modelli MLflow come servizio Web di Azure](how-to-deploy-mlflow-models.md). 
