---
title: Cosa sono le pipeline di apprendimento automatico?
titleSuffix: Azure Machine Learning
description: Informazioni su come le pipeline di Machine Learning consentono di creare, ottimizzare e gestire flussi di lavoro di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 57f5da06909436e0cbce92559c29c309ca9e20e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819233"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Che cosa sono Azure Machine Learning pipeline?

Questo articolo illustra come una pipeline di Machine Learning consente di compilare, ottimizzare e gestire il flusso di lavoro di Machine Learning. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Quale tecnologia di pipeline di Azure è consigliabile usare?

Il cloud di Azure offre diversi tipi di pipeline, ognuno con uno scopo diverso. Nella tabella seguente sono elencate le diverse pipeline e per cosa vengono usate:

| Scenario | Persona primaria | Offerta di Azure | Offerta OSS | Pipe canonica | Punti di forza | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orchestrazione del modello (Machine Learning) | Data scientist | Azure Machine Learning Pipelines | Pipeline kubeflow | Modello di > dati | Distribuzione, memorizzazione nella cache, code-first, riutilizzo | 
| Orchestrazione dei dati (preparazione dei dati) | Data engineer | [Pipeline di Azure Data Factory](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Dati -> dati | Spostamento fortemente tipizzato, attività incentrate sui dati |
| Orchestrazione &'app code-& (CI/CD) | Sviluppatore di app/ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model -> App/Service | Supporto delle attività più aperto e flessibile, code di approvazione, fasi con controllo | 

## <a name="what-can-machine-learning-pipelines-do"></a>Cosa possono fare le pipeline di Machine Learning?

Una Azure Machine Learning pipeline è un flusso di lavoro eseguibile indipendente di un'attività completa di Machine Learning. Le sottoattività sono incapsulate come una serie di passaggi all'interno della pipeline. Una Azure Machine Learning pipeline può essere semplice quanto una che chiama uno script Python, quindi _può_ eseguire qualsiasi operazione. Le pipeline _devono concentrarsi_ sulle attività di Machine Learning, ad esempio:

+ Preparazione dei dati, tra cui importazione, convalida e pulizia, munging e trasformazione, normalizzazione e gestione temporanea
+ Configurazione del training, tra cui parametrizzazione di argomenti, percorsi file e configurazioni di registrazione/report
+ Training e convalida in modo efficiente e ripetuto. L'efficienza può derivare dalla specifica di subset di dati specifici, risorse di calcolo hardware diverse, elaborazione distribuita e monitoraggio dello stato di avanzamento
+ Distribuzione, inclusi controllo delle versioni, ridimensionamento, provisioning e controllo degli accessi

I passaggi indipendenti consentono a più data scientist di lavorare contemporaneamente sulla stessa pipeline senza sovravasare le risorse di calcolo. Passaggi separati semplificano anche l'uso di tipi/dimensioni di calcolo diversi per ogni passaggio.

Dopo che la pipeline è stata progettata, vi è spesso un'ulteriore ottimizzazione di tutto il ciclo di training della pipeline. Quando si esegue nuovamente una pipeline, l'esecuzione passa ai passaggi che devono essere eseguiti di nuovo, ad esempio uno script di training aggiornato. I passaggi che non devono essere eseguiti di nuovo vengono ignorati. 

Con le pipeline, è possibile scegliere di usare hardware diverso per attività diverse. Azure coordina le varie [destinazioni di calcolo usate,](concept-azure-machine-learning-architecture.md) in modo che i dati intermedi sfocino facilmente nelle destinazioni di calcolo downstream.

È possibile [tenere traccia delle metriche per gli esperimenti della pipeline](./how-to-log-view-metrics.md) direttamente in portale di Azure o nella pagina di destinazione dell'area di lavoro [(anteprima).](https://ml.azure.com) Dopo la pubblicazione di una pipeline, è possibile configurare un endpoint REST, che consente di eseguire nuovamente la pipeline da qualsiasi piattaforma o stack.

In breve, tutte le attività complesse del ciclo di vita di Machine Learning possono essere aiutate con le pipeline. Altre tecnologie di pipeline di Azure hanno i propri punti di forza. [Azure Data Factory pipeline sono](../data-factory/concepts-pipelines-activities.md) lo strumento più efficace per lavorare con i dati [e Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) è lo strumento giusto per l'integrazione e la distribuzione continue. Tuttavia, se l'obiettivo è machine learning, Azure Machine Learning pipeline sono probabilmente la scelta migliore per le esigenze del flusso di lavoro. 

### <a name="analyzing-dependencies"></a>Analisi delle dipendenze

Molti ecosistemi di programmazione hanno strumenti che orchestrano le dipendenze di risorse, librerie o compilazione. In genere, questi strumenti usano timestamp di file per calcolare le dipendenze. Quando un file viene modificato, solo il file e i relativi dipendenti vengono aggiornati (scaricati, ricompilati o in pacchetto). Azure Machine Learning pipeline estendono questo concetto. Come gli strumenti di compilazione tradizionali, le pipeline calcolano le dipendenze tra i passaggi ed eseguono solo i ricalcoli necessari. 

L'analisi delle dipendenze Azure Machine Learning pipeline è più sofisticata rispetto ai timestamp semplici. Ogni passaggio può essere eseguito in un ambiente hardware e software diverso. La preparazione dei dati potrebbe richiedere molto tempo, ma non deve essere eseguita su hardware con GPU potenti, alcuni passaggi potrebbero richiedere software specifico del sistema operativo, potrebbe essere necessario usare il training distribuito e così via. 

Azure Machine Learning automaticamente tutte le dipendenze tra i passaggi della pipeline. Questa orchestrazione può includere la rotazione e la discesa delle immagini Docker, il collegamento e lo scollegamento delle risorse di calcolo e lo spostamento dei dati tra i passaggi in modo coerente e automatico.

### <a name="coordinating-the-steps-involved"></a>Coordinamento dei passaggi necessari

Quando si crea ed esegue un `Pipeline` oggetto, si verificano i passaggi di alto livello seguenti:

+ Per ogni passaggio, il servizio calcola i requisiti per:
    + Risorse di calcolo hardware
    + Risorse del sistema operativo (immagini Docker)
    + Risorse software (dipendenze Conda/virtualenv)
    + Input di dati 
+ Il servizio determina le dipendenze tra i passaggi, determinando un grafico di esecuzione dinamica
+ Quando viene eseguito ogni nodo nel grafico di esecuzione:
    + Il servizio configura l'ambiente hardware e software necessario ,ad esempio riutilizzando le risorse esistenti
    + Il passaggio viene eseguito, fornendo informazioni di registrazione e monitoraggio al relativo oggetto `Experiment` contenitore
    + Al termine del passaggio, i relativi output vengono preparati come input per il passaggio successivo e/o scritti nell'archiviazione
    + Le risorse che non sono più necessarie vengono finalizzate e scollegate

![Passaggi della pipeline](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Compilazione di pipeline con Python SDK

[Nell'Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)una pipeline è un oggetto Python definito nel `azureml.pipeline.core` modulo. Un [oggetto Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) contiene una sequenza ordinata di uno o più oggetti [PipelineStep.](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) La classe è astratta e i passaggi effettivi saranno di `PipelineStep` sottoclassi come [EstimatorStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)o [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) La [classe ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) contiene una sequenza riutilizzabile di passaggi che possono essere condivisi tra le pipeline. Un `Pipeline` oggetto viene eseguito come parte di un oggetto `Experiment` .

Una pipeline di Azure Machine Learning è associata a un'area di lavoro Azure Machine Learning e un passaggio della pipeline è associato a una destinazione di calcolo disponibile all'interno di tale area di lavoro. Per altre informazioni, vedere [Creare e](./how-to-manage-workspace.md) gestire Azure Machine Learning di lavoro nel portale di Azure o Cosa sono le destinazioni di calcolo in [Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Una semplice pipeline Python

Questo frammento di codice mostra gli oggetti e le chiamate necessarie per creare ed eseguire `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Il frammento inizia con oggetti Azure Machine Learning, `Workspace` , `Datastore` , [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)e `Experiment` . Il codice crea quindi gli oggetti per contenere `input_data` e `prepped_data_path` . è `input_data` un'istanza di [FileDataset](/python/api/azureml-core/azureml.data.filedataset) e `prepped_data_path` è un'istanza di [OutputFileDatasetConfig.](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) Per il comportamento predefinito, copiare l'output nell'archivio dati nel percorso , dove è `OutputFileDatasetConfig` `workspaceblobstore` `/dataset/{run-id}/{output-name}` `run-id` l'ID dell'esecuzione e è un valore generato automaticamente se non specificato `output-name` dallo sviluppatore.

Il codice di preparazione dei dati (non visualizzato) scrive i file delimitati in `prepped_data_path` . Questi output del passaggio di preparazione dei dati vengono passati `prepped_data` come al passaggio di training. 

La matrice `steps` contiene i due e `PythonScriptStep` `dataprep_step` `train_step` . Azure Machine Learning verrà analizzata la dipendenza dei dati di `prepped_data` ed eseguita prima di `dataprep_step` `train_step` . 

Quindi, il codice crea un'istanza `Pipeline` dell'oggetto stesso, passando l'area di lavoro e la matrice dei passaggi. La chiamata a `experiment.submit(pipeline)` avvia l'esecuzione della pipeline di Azure ML. La chiamata a `wait_for_completion()` si blocca fino al completamento della pipeline. 

Per altre informazioni sulla connessione della pipeline ai dati, vedere gli articoli Accesso ai dati [in Azure Machine Learning](concept-data.md) e Spostamento di dati in e tra passaggi della pipeline di Machine Learning [(Python).](how-to-move-data-in-out-of-pipelines.md) 

## <a name="building-pipelines-with-the-designer"></a>Compilazione di pipeline con la finestra di progettazione

Gli sviluppatori che preferiscono un'area di progettazione visiva possono usare Azure Machine Learning per creare pipeline. È possibile accedere a questo strumento dalla selezione **Progettazione** nella home page dell'area di lavoro.  La finestra di progettazione consente di trascinare i passaggi nell'area di progettazione. 

Quando si progettano visivamente pipeline, gli input e gli output di un passaggio vengono visualizzati in modo visibile. È possibile trascinare e rilasciare connessioni dati, consentendo di comprendere e modificare rapidamente il flusso di dati della pipeline.

![Esempio di finestra di progettazione di Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Vantaggi principali

I vantaggi principali dell'uso di pipeline per i flussi di lavoro di Machine Learning sono:

|Vantaggi principali|Descrizione|
|:-------:|-----------|
|**Esecuzione&nbsp;automatica**|Pianificare i passaggi da eseguire in parallelo o in sequenza in modo affidabile e automatico. La preparazione e la modellazione dei dati possono durare giorni o settimane e le pipeline consentono di concentrarsi su altre attività durante l'esecuzione del processo. |
|**Calcolo eterogeneo**|Usare più pipeline coordinate in modo affidabile tra risorse di calcolo eterogenee e scalabili e posizioni di archiviazione. Usare in modo efficiente le risorse di calcolo disponibili eseguendo singoli passaggi della pipeline in destinazioni di calcolo diverse, ad esempio HDInsight, VM di data science GPU e Databricks.|
|**Riutilizzabilità**|Creare modelli di pipeline per scenari specifici, ad esempio il training e l'assegnazione dei punteggi in batch. Attivare pipeline pubblicate da sistemi esterni tramite semplici chiamate REST.|
|**Rilevamento e controllo delle versioni**|Invece di tenere traccia manualmente dei dati e dei percorsi dei risultati durante l'iterazione, usare l'SDK delle pipeline per denominare esplicitamente e controllare la versione di origini dati, input e output. È anche possibile gestire gli script e i dati separatamente per una maggiore produttività.|
| **Modularità** | La separazione delle aree di problematiche e l'isolamento delle modifiche consente al software di evolversi a una velocità più rapida con una qualità superiore. | 
|**Collaborazione**|Le pipeline consentono ai data scientist di collaborare in tutte le aree del processo di progettazione di Machine Learning, pur essendo in grado di lavorare contemporaneamente ai passaggi della pipeline.|

## <a name="next-steps"></a>Passaggi successivi

Azure Machine Learning pipeline sono una potente funzionalità che inizia a offrire valore nelle prime fasi di sviluppo. Il valore aumenta man mano che il team e il progetto aumentano. Questo articolo ha illustrato come le pipeline vengono specificate con il Azure Machine Learning Python SDK e orchestrate in Azure. Si è visto codice sorgente semplice ed è stata introdotta alcune delle `PipelineStep` classi disponibili. È necessario avere un'idea di quando usare Azure Machine Learning pipeline e come vengono eseguiti da Azure. 

+ Informazioni su come [creare la prima pipeline.](./how-to-create-machine-learning-pipelines.md)

+ Informazioni su come [eseguire stime batch su dati di grandi dimensioni.](tutorial-pipeline-batch-scoring-classification.md )

+ Vedere la documentazione di riferimento dell'SDK per i [passaggi principali e](/python/api/azureml-pipeline-core/) della [pipeline.](/python/api/azureml-pipeline-steps/)

+ Provare i notebook di Jupyter di esempio [che mostrano Azure Machine Learning pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Informazioni su come [eseguire notebook per esplorare questo servizio.](samples-notebooks.md)