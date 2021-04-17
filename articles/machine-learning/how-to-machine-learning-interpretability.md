---
title: Interpretabilità del modello in Azure Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come comprendere & modo in cui il modello di Machine Learning esegue stime durante il training &'inferenza usando Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: c517cf2fc8491d62cf2379c87acd2eaadde8fe15
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576432"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Interpretabilità del modello in Azure Machine Learning (anteprima)


## <a name="model-interpretability-overview"></a>Panoramica dell'interpretabilità dei modelli

L'interpretabilità dei modelli è fondamentale per data scientist, revisori e responsabili delle decisioni aziendali per garantire la conformità ai criteri aziendali, agli standard di settore e alle normative governative:

+ I data scientist devono poter spiegare i propri modelli a dirigenti e stakeholder, in modo che possano comprendere il valore e l'accuratezza dei risultati. Richiedono anche l'interpretabilità per eseguire il debug dei modelli e prendere decisioni informate su come migliorarli. 

+ I revisori legali richiedono strumenti per convalidare i modelli in relazione alla conformità alle normative e monitorare l'impatto delle decisioni dei modelli sugli esseri umani. 

+ I responsabili delle decisioni aziendali devono avere la possibilità di offrire trasparenza agli utenti finali. Ciò consente loro di ottenere e mantenere la fiducia.

L'abilitazione della funzionalità di spiegazione di un modello di Machine Learning è importante durante due fasi principali dello sviluppo del modello:

+ Durante la fase di training, i progettisti di modelli e gli analizzatori possono usare l'output di interpretabilità di un modello per verificare le ipotesi e creare relazioni di trust con gli stakeholder. Usano anche le informazioni dettagliate sul modello per il debug, la convalida del comportamento del modello corrisponde ai relativi obiettivi e per verificare l'ingiustizia del modello o le funzionalità non significativi.

+ Durante la fase di inferenza, la trasparenza dei modelli distribuiti consente ai dirigenti di comprendere "quando viene distribuito" il funzionamento del modello e il modo in cui le decisioni prendono e influiscono sulle persone nella vita reale. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilità con Azure Machine Learning

Le classi di interpretabilità del modello vengono rese disponibili tramite il pacchetto SDK seguente: (Informazioni su come installare [i pacchetti SDK per](/python/api/overview/azure/ml/install)Azure Machine Learning )

* `azureml.interpret`, contiene le funzionalità supportate da Microsoft.

Usare `pip install azureml-interpret` per l'uso generale.

## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

Usando le classi e i metodi nell'SDK, è possibile:
+ Spiegare la stima del modello generando valori di importanza delle caratteristiche per l'intero modello e/o singoli punti dati. 
+ Ottenere l'interpretabilità del modello su set di dati reali su larga scala, durante il training e l'inferenza.
+ Usare un dashboard di visualizzazione interattiva per individuare modelli nei dati e spiegazioni in fase di training

In Machine Learning le **funzionalità sono** i campi dati usati per stimare un punto dati di destinazione. Ad esempio, per stimare il rischio di credito, è possibile usare i campi dati relativi a età, dimensioni dell'account e età dell'account. In questo caso, età, dimensioni dell'account e età dell'account sono **funzionalità**. L'importanza delle caratteristiche indica in che modo ogni campo dati ha inciso sulle stime del modello. Ad esempio, l'età può essere usata principalmente nella stima, mentre le dimensioni del conto e l'età non influiscono in modo significativo sui valori di stima. Questo processo consente ai data scientist di spiegare le stime risultanti, in modo che gli stakeholder siano visibili sulle funzionalità più importanti nel modello.

## <a name="supported-interpretability-techniques"></a>Tecniche di interpretabilità supportate

 `azureml-interpret` usa le tecniche di interpretabilità sviluppate in [Interpret-Community,](https://github.com/interpretml/interpret-community/)un pacchetto Python open source per il training di modelli interpretabili e per spiegare i sistemi di intelligenza artificiale blackbox. [Interpret-Community](https://github.com/interpretml/interpret-community/) funge da host per gli strumenti di spiegazione supportati di questo SDK e attualmente supporta le tecniche di interpretabilità seguenti:

|Tecnica di interpretabilità|Descrizione|Type|
|--|--|--------------------|
|Spiegazione dell'albero SHAP| [L'utilità di](https://github.com/slundberg/shap)spiegazione dell'albero di SHAP, incentrata sull'algoritmo di stima del valore SHAP veloce a tempo polinomiale specifico per alberi e **insiemi di alberi.**|Specifico del modello|
|Spiegazione approfondita di SHAP| In base alla spiegazione fornita da SHAP, Deep Explainer "è un algoritmo di approssimazione ad alta velocità per i valori SHAP nei modelli di Deep Learning basato su una connessione con Deep ELEVATOR descritto nel documento [SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Sono supportati i modelli **TensorFlow** e i modelli **Keras** che usano il back-end TensorFlow (è disponibile anche il supporto preliminare per PyTorch)".|Specifico del modello|
|Spiegazione lineare SHAP| Lo strumento di spiegazione lineare di SHAP calcola i valori SHAP per un modello **lineare,** eventualmente in base alle correlazioni tra funzionalità.|Specifico del modello|
|Spiegazione del kernel SHAP| Lo strumento di spiegazione kernel di SHAP usa una regressione lineare locale ponderata appositamente per stimare i valori SHAP per **qualsiasi modello.**|Indipendente dal modello|
|Mimic Explainer (Surrogato globale)| Mimic explainer si basa sull'idea di eseguire il training di modelli [surrogati globali](https://christophm.github.io/interpretable-ml-book/global.html) per simulare i modelli blackbox. Un modello surrogato globale è un modello intrinsecamente interpretabile con  training per approssimare le stime di qualsiasi modello black box nel modo più accurato possibile. I data scientist possono interpretare il modello surrogato per trarre conclusioni black box modello. È possibile usare uno dei modelli interpretabili seguenti come modello surrogato: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable Model (SGDExplainableModel) e Decision Tree (DecisionTreeExplainableModel).|Indipendente dal modello|
|Spiegazione dell'importanza delle funzionalità di permutazione (PFI)| Permutation Feature Importance è una tecnica usata per spiegare i modelli di classificazione e regressione ispirata al documento Sulle foreste casuali di [Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (vedere la sezione 10). A livello generale, il modo in cui funziona è lo shuffing casuale dei dati una funzionalità alla volta per l'intero set di dati e il calcolo di quanto cambia la metrica delle prestazioni di interesse. Maggiore è la modifica, maggiore è l'importanza della funzionalità. PFI può spiegare il comportamento complessivo di **qualsiasi modello sottostante,** ma non spiega le singole stime. |Indipendente dal modello|

Oltre alle tecniche di interpretabilità descritte in precedenza, è possibile supportare un altro spiegatore basato su SHAP, denominato `TabularExplainer` . A seconda del modello, `TabularExplainer` usa uno degli spiegatori SHAP supportati:

* TreeExplainer per tutti i modelli basati su albero
* DeepExplainer per i modelli DNN
* LinearExplainer per modelli lineari
* KernelExplainer per tutti gli altri modelli

`TabularExplainer` ha anche apportato miglioramenti significativi delle funzionalità e delle prestazioni rispetto ai spiegazioni SHAP diretti:

* **Riepilogo del set di dati di inizializzazione**. Nei casi in cui la velocità di spiegazione è più importante, viene riepilogato il set di dati di inizializzazione e viene generato un piccolo set di campioni rappresentativi, che velocizza la generazione di valori di importanza complessiva e delle singole caratteristiche.
* **Campionamento del set di dati di valutazione**. Se l'utente passa un set di campioni di valutazione di grandi dimensioni, ma non ha effettivamente bisogno di valutarli tutti, il parametro di campionamento può essere impostato su true per velocizzare il calcolo delle spiegazioni complessive del modello.

Il diagramma seguente illustra la struttura corrente degli spiegatori supportati.

[![Machine Learning di interpretabilità dei dati](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelli di Machine Learning supportati

Il `azureml.interpret` pacchetto dell'SDK supporta i modelli con training con i formati di set di dati seguenti:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Le funzioni di spiegazione accettano sia modelli che pipeline come input. Se viene fornito un modello, il modello deve implementare la funzione di stima `predict` o `predict_proba` conforme alla convenzione scikit. Se il modello non supporta questa funzionalità, è possibile eseguire il wrapping del modello in una funzione che genera lo stesso risultato di o in Scikit e usare tale funzione wrapper con `predict` `predict_proba` l'spiegazione selezionata. Se viene specificata una pipeline, la funzione di spiegazione presuppone che lo script della pipeline in esecuzione restituisca una stima. Usando questa tecnica di wrapping, può supportare i modelli con training tramite i framework di Deep `azureml.interpret` Learning PyTorch, TensorFlow e Keras, nonché i modelli di Machine Learning classici.

## <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locale e remota

Il pacchetto è progettato per funzionare con destinazioni di calcolo `azureml.interpret` sia locali che remote. Se vengono eseguite in locale, le funzioni SDK non contattano i servizi di Azure. 

È possibile eseguire la spiegazione in modalità remota Azure Machine Learning calcolo e registrare le informazioni di spiegazione nel Azure Machine Learning Run History Service. Dopo aver registrato queste informazioni, i report e le visualizzazioni della spiegazione sono immediatamente disponibili studio di Azure Machine Learning per l'analisi utente.


## <a name="next-steps"></a>Passaggi successivi

- Vedere la procedura [per abilitare l'interpretabilità](how-to-machine-learning-interpretability-aml.md) per i modelli di training sia in locale che Azure Machine Learning di calcolo remoto. 
- Informazioni su come abilitare [l'interpretabilità per i modelli di Machine Learning automatizzati.](how-to-machine-learning-interpretability-automl.md)
- Per altri [scenari, vedere i notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) di esempio. 
- Se si è interessati all'interpretabilità per scenari di testo, vedere [Interpret-text](https://github.com/interpretml/interpret-text), un repo open source correlato a [Interpret-Community](https://github.com/interpretml/interpret-community/), per le tecniche di interpretabilità per NLP. `azureml.interpret` il pacchetto attualmente non supporta queste tecniche, ma è possibile iniziare con un [notebook di esempio sulla classificazione del testo](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).