---
title: Machine Learning pipeline YAML
titleSuffix: Azure Machine Learning
description: Informazioni su come definire una pipeline di Machine Learning usando un file YAML. Le definizioni della pipeline YAML vengono usate con l'estensione di Machine Learning per l'interfaccia della riga di comando di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1c44f018d558b9426ba6271c0cbb1c2356a2a9b2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478285"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definire pipeline di Machine Learning in YAML

Informazioni su come definire le pipeline di Machine Learning in [YAML.](https://yaml.org/) Quando si usa l'estensione di Machine Learning per l'interfaccia della riga di comando di Azure, molti comandi correlati alla pipeline prevedono un file YAML che definisce la pipeline.

La tabella seguente elenca cosa è e non è attualmente supportato quando si definisce una pipeline in YAML:

| Tipo di passaggio | Supportata |
| ----- | :-----: |
| PythonScriptStep | Sì |
| ParallelRunStep | Sì |
| AdlaStep | Sì |
| Passaggio di AzureBatchStep | Sì |
| DatabricksStep | Sì |
| DataTransferStep | Sì |
| AutoMLStep | No |
| HyperDriveStep | No |
| ModuleStep | Sì |
| MPIStep | No |
| EstimatorStep | No |

## <a name="pipeline-definition"></a>Definizione della pipeline

Una definizione di pipeline usa le chiavi seguenti, che corrispondono alla [classe Pipelines:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline)

| Chiave YAML | Descrizione |
| ----- | ----- |
| `name` | Descrizione della pipeline. |
| `parameters` | Parametri alla pipeline. |
| `data_reference` | Definisce come e dove devono essere resi disponibili i dati in un'esecuzione. |
| `default_compute` | Destinazione di calcolo predefinita in cui vengono eseguiti tutti i passaggi nella pipeline. |
| `steps` | Passaggi usati nella pipeline. |

## <a name="parameters"></a>Parametri

La `parameters` sezione usa le chiavi seguenti, che corrispondono alla classe [PipelineParameter:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)

| Chiave YAML | Descrizione |
| ---- | ---- |
| `type` | Tipo del valore del parametro. I tipi validi `string` sono , , , o `int` `float` `bool` `datapath` . |
| `default` | Il valore predefinito. |

Ogni parametro è denominato. Ad esempio, il frammento YAML seguente definisce tre parametri denominati `NumIterationsParameter` , `DataPathParameter` e `NodeCountParameter` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Riferimenti ai dati

La `data_references` sezione usa le chiavi seguenti, che corrispondono a [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference):

| Chiave YAML | Descrizione |
| ----- | ----- |
| `datastore` | Archivio dati a cui fare riferimento. |
| `path_on_datastore` | Percorso relativo nell'archivio di backup per il riferimento ai dati. |

Ogni riferimento ai dati è contenuto in una chiave. Ad esempio, il frammento YAML seguente definisce un riferimento ai dati archiviato nella chiave denominata `employee_data` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Passaggi

I passaggi definiscono un ambiente di calcolo, insieme ai file da eseguire nell'ambiente. Per definire il tipo di un passaggio, usare la `type` chiave :

| Tipo di passaggio | Descrizione |
| ----- | ----- |
| `AdlaStep` | Esegue uno script U-SQL con Azure Data Lake Analytics. Corrisponde alla [classe AdlaStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) |
| `AzureBatchStep` | Esegue i processi usando Azure Batch. Corrisponde alla [classe AzureBatchStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) |
| `DatabricsStep` | Aggiunge un notebook di Databricks, uno script Python o un file JAR. Corrisponde alla [classe DatabricksStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) |
| `DataTransferStep` | Trasferisce i dati tra le opzioni di archiviazione. Corrisponde alla [classe DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) |
| `PythonScriptStep` | Esegue uno script Python. Corrisponde alla [classe PythonScriptStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) |
| `ParallelRunStep` | Esegue uno script Python per elaborare grandi quantità di dati in modo asincrono e parallelo. Corrisponde alla [classe ParallelRunStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) |

### <a name="adla-step"></a>Passaggio di ADLA

| Chiave YAML | Descrizione |
| ----- | ----- |
| `script_name` | Nome dello script U-SQL (relativo a `source_directory` ). |
| `compute` | Destinazione di calcolo di Azure Data Lake da usare per questo passaggio. |
| `parameters` | [Parametri](#parameters) per la pipeline. |
| `inputs` | Gli input possono essere [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)o [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Gli output possono essere [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) o [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | Directory che contiene lo script, gli assembly e così via. |
| `priority` | Valore di priorità da utilizzare per il processo corrente. |
| `params` | Dizionario di coppie nome-valore. |
| `degree_of_parallelism` | Grado di parallelismo da usare per questo processo. |
| `runtime_version` | Versione di runtime del motore Data Lake Analytics motore. |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene una definizione di passaggio ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch passaggio

| Chiave YAML | Descrizione |
| ----- | ----- |
| `compute` | La Azure Batch di calcolo da usare per questo passaggio. |
| `inputs` | Gli input possono essere [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)o [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Gli output possono essere [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) o [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | Directory che contiene i file binari del modulo, l'eseguibile, gli assembly e così via. |
| `executable` | Nome del comando o dell'eseguibile che verrà eseguito come parte di questo processo. |
| `create_pool` | Flag booleano che indica se creare il pool prima di eseguire il processo. |
| `delete_batch_job_after_finish` | Flag booleano che indica se eliminare il processo dall'account Batch al termine. |
| `delete_batch_pool_after_finish` | Flag booleano che indica se eliminare il pool al termine del processo. |
| `is_positive_exit_code_failure` | Flag booleano che indica se il processo ha esito negativo se l'attività termina con un codice positivo. |
| `vm_image_urn` | Se `create_pool` è e la macchina virtuale usa `True` `VirtualMachineConfiguration` . |
| `pool_id` | ID del pool in cui verrà eseguito il processo. |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene una definizione Azure Batch passaggio di esecuzione:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Passaggio di Databricks

| Chiave YAML | Descrizione |
| ----- | ----- |
| `compute` | La Azure Databricks di calcolo da usare per questo passaggio. |
| `inputs` | Gli input possono essere [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)o [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Gli output possono essere [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) o [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `run_name` | Nome in Databricks per questa esecuzione. |
| `source_directory` | Directory che contiene lo script e altri file. |
| `num_workers` | Numero statico di worker per il cluster di esecuzione di Databricks. |
| `runconfig` | Percorso di un `.runconfig` file. Questo file è una rappresentazione YAML della [classe RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration) Per altre informazioni sulla struttura di questo file, vedererunconfigschema.js[ su](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di Databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Passaggio di trasferimento dei dati

| Chiave YAML | Descrizione |
| ----- | ----- |
| `compute` | La Azure Data Factory di calcolo da usare per questo passaggio. |
| `source_data_reference` | Connessione di input che funge da origine delle operazioni di trasferimento dei dati. I valori supportati [sono InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)o [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `destination_data_reference` | Connessione di input che funge da destinazione delle operazioni di trasferimento dei dati. I valori supportati [sono PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) e [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di trasferimento dei dati:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Istruzione di script Python

| Chiave YAML | Descrizione |
| ----- | ----- |
| `inputs` | Gli input possono essere [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)o [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Gli output possono essere [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) o [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | Nome dello script Python (relativo a `source_directory` ). |
| `source_directory` | Directory che contiene lo script, l'ambiente Conda e così via. |
| `runconfig` | Percorso di un `.runconfig` file. Questo file è una rappresentazione YAML della [classe RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration) Per altre informazioni sulla struttura di questo file, vedererunconfig.js[ su](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di script Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>Passaggio di esecuzione parallela

| Chiave YAML | Descrizione |
| ----- | ----- |
| `inputs` | Gli input possono essere [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)o [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Gli output possono essere [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) o [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | Nome dello script Python (relativo a `source_directory` ). |
| `source_directory` | Directory che contiene lo script, l'ambiente Conda e così via. |
| `parallel_run_config` | Percorso di un `parallel_run_config.yml` file. Questo file è una rappresentazione YAML della [classe ParallelRunConfig.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) |
| `allow_reuse` | Determina se il passaggio deve riutilizzare i risultati precedenti quando viene eseguito di nuovo con le stesse impostazioni. |

L'esempio seguente contiene un passaggio di esecuzione parallela:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Pipeline con più passaggi 

| Chiave YAML | Descrizione |
| ----- | ----- |
| `steps` | Sequenza di una o più definizioni PipelineStep. Si noti `destination` che le chiavi di un passaggio diventano le chiavi del passaggio `outputs` `source` `inputs` successivo.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Pianificazioni

Quando si definisce la pianificazione per una pipeline, può essere attivata dall'archivio dati o ricorrente in base a un intervallo di tempo. Di seguito sono riportate le chiavi usate per definire una pianificazione:

| Chiave YAML | Descrizione |
| ----- | ----- |
| `description` | Descrizione della pianificazione. |
| `recurrence` | Contiene le impostazioni di ricorrenza, se la pianificazione è ricorrente. |
| `pipeline_parameters` | Tutti i parametri richiesti dalla pipeline. |
| `wait_for_provisioning` | Indica se attendere il completamento del provisioning della pianificazione. |
| `wait_timeout` | Numero di secondi di attesa prima del timeout. |
| `datastore_name` | Archivio dati da monitorare per i BLOB modificati/aggiunti. |
| `polling_interval` | Intervallo di tempo, in minuti, tra il polling per i BLOB modificati/aggiunti. Valore predefinito: 5 minuti. Supportato solo per le pianificazioni dell'archivio dati. |
| `data_path_parameter_name` | Nome del parametro della pipeline del percorso dati da impostare con il percorso BLOB modificato. Supportato solo per le pianificazioni dell'archivio dati. |
| `continue_on_step_failure` | Se continuare l'esecuzione di altri passaggi nella pipeline inviataEsegui se un passaggio ha esito negativo. Se specificato, eseguirà l'override `continue_on_step_failure` dell'impostazione della pipeline.
| `path_on_datastore` | facoltativo. Percorso nell'archivio dati da monitorare per i BLOB modificati/aggiunti. Il percorso si trova nel contenitore per l'archivio dati, quindi il percorso effettivo monitorato dalla pianificazione è container/ `path_on_datastore` . Se non è disponibile, viene monitorato il contenitore dell'archivio dati. Le aggiunte/modifiche apportate in una sottocartella di `path_on_datastore` non vengono monitorate. Supportato solo per le pianificazioni dell'archivio dati. |

L'esempio seguente contiene la definizione per una pianificazione attivata dall'archivio dati:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Quando si definisce una **pianificazione ricorrente,** usare le chiavi seguenti in `recurrence` :

| Chiave YAML | Descrizione |
| ----- | ----- |
| `frequency` | Frequenza con cui si ripete la pianificazione. I valori validi `"Minute"` sono , , , o `"Hour"` `"Day"` `"Week"` `"Month"` . |
| `interval` | Frequenza di esecuzione della pianificazione. Il valore intero è il numero di unità di tempo di attesa fino a quando la pianificazione non viene riescita. |
| `start_time` | Ora di inizio della pianificazione. Il formato stringa del valore è `YYYY-MM-DDThh:mm:ss` . Se non viene specificata alcuna ora di inizio, il primo carico di lavoro viene eseguito immediatamente e i carichi di lavoro futuri vengono eseguiti in base alla pianificazione. Se l'ora di inizio è nel passato, il primo carico di lavoro viene eseguito alla successiva esecuzione calcolata. |
| `time_zone` | Fuso orario per l'ora di inizio. Se non viene specificato alcun fuso orario, viene usata l'ora UTC. |
| `hours` | Se è o , è possibile specificare uno o più numeri interi da 0 a `frequency` `"Day"` 23, separati da virgole, come ore del giorno in cui deve essere eseguita `"Week"` la pipeline. È `time_of_day` possibile usare solo o e `hours` `minutes` . |
| `minutes` | Se è o , è possibile specificare uno o più numeri interi da 0 a `frequency` `"Day"` 59, separati da virgole, come minuti dell'ora in cui deve essere eseguita `"Week"` la pipeline. È `time_of_day` possibile usare solo o e `hours` `minutes` . |
| `time_of_day` | Se `frequency` è o , è possibile specificare `"Day"` `"Week"` un'ora del giorno per l'esecuzione della pianificazione. Il formato stringa del valore è `hh:mm` . È `time_of_day` possibile usare solo o e `hours` `minutes` . |
| `week_days` | Se `frequency` è , è possibile specificare uno o più giorni, separati da `"Week"` virgole, quando deve essere eseguita la pianificazione. I valori validi `"Monday"` sono , , , , , e `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

L'esempio seguente contiene la definizione per una pianificazione ricorrente:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare [l'estensione dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando per Azure Machine Learning .
