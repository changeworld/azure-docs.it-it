---
title: Usare Apache Spark in una pipeline di Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Collegare l'Azure Synapse Analytics lavoro alla pipeline di Azure Machine Learning per usare Apache Spark per la manipolazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576381"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Come usare i Apache Spark (con tecnologia Azure Synapse Analytics) nella pipeline di Machine Learning (anteprima)

Questo articolo illustra come usare pool di Apache Spark basati su Azure Synapse Analytics come destinazione di calcolo per un passaggio di preparazione dei dati in una pipeline Azure Machine Learning. Si apprenderà come una singola pipeline può usare le risorse di calcolo adatte per il passaggio specifico, ad esempio la preparazione dei dati o il training. Verrà illustrato come vengono preparati i dati per il passaggio Spark e come vengono passati al passaggio successivo. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) che conterrà tutte le risorse della pipeline.

* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare Azure Machine Learning SDK o usare un'istanza di calcolo [Azure Machine Learning](concept-compute-instance.md) con l'SDK già installato.

* Creare un'Azure Synapse Analytics di lavoro e un pool Apache Spark (vedere Guida introduttiva: Creare un [pool Apache Spark serverless usando Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Collegare l'area Azure Machine Learning lavoro e l'Azure Synapse Analytics lavoro 

È possibile creare e amministrare i pool Apache Spark in un'area Azure Synapse Analytics lavoro. Per integrare un pool Apache Spark con un'area Azure Machine Learning lavoro, è necessario [collegarsi all'area Azure Synapse Analytics lavoro](how-to-link-synapse-ml-workspaces.md). 

È possibile collegare un pool Apache Spark tramite studio di Azure Machine Learning'interfaccia utente usando la **pagina Servizi** collegati. È anche possibile eseguire questa operazione tramite la **pagina Calcolo** con l'opzione **Collega calcolo.**

È anche possibile collegare un pool di Apache Spark tramite SDK (come illustrato di seguito) o tramite un modello di Arm (vedere questo [modello arm di esempio).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json) 

È possibile usare la riga di comando per seguire il modello arm, aggiungere il servizio collegato e collegare il pool Apache Spark con il codice seguente:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Per collegarsi correttamente all'Azure Synapse Analytics lavoro, è necessario avere il ruolo Proprietario nella risorsa dell Azure Synapse Analytics area di lavoro. Controllare l'accesso nella portale di Azure.
> Il servizio collegato otterrà un'identità assegnata dal sistema (SAI) al momento della creazione. È necessario assegnare a questo servizio di collegamento SAI il ruolo "Amministratore di Synapse Apache Spark" da Synapse Studio in modo che possa inviare il processo Spark (vedere Come gestire le assegnazioni di ruolo del controllo degli accessi in base al ruolo di [Synapse in Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). È anche necessario assegnare all'utente dell Azure Machine Learning workspace il ruolo "Collaboratore" portale di Azure gestione delle risorse.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Creare o recuperare il collegamento tra l'area di Azure Synapse Analytics e l'area Azure Machine Learning lavoro

È possibile recuperare i servizi collegati nell'area di lavoro con codice come:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

In primo luogo, accede all'Azure Machine Learning di lavoro usando la configurazione in (vedere Esercitazione: Introduzione Azure Machine Learning `Workspace.from_config()` `config.json` [nell'ambiente di sviluppo).](tutorial-1st-experiment-sdk-setup-local.md) Quindi, il codice stampa tutti i servizi collegati disponibili nell'area di lavoro. Infine, `LinkedService.get()` recupera un servizio collegato denominato `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Collegare il pool Apache Spark come destinazione di calcolo per Azure Machine Learning

Per usare il pool di Apache Spark per alimentare un passaggio nella pipeline di Machine Learning, è necessario collegarlo come per il passaggio della pipeline, come illustrato `ComputeTarget` nel codice seguente.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

Il primo passaggio consiste nel configurare `SynapseCompute` . `linked_service`L'argomento `LinkedService` è l'oggetto creato o recuperato nel passaggio precedente. `type`L'argomento deve essere `SynapseSpark` . `pool_name`L'argomento in deve corrispondere a quello di un pool esistente `SynapseCompute.attach_configuration()` nell'area Azure Synapse Analytics lavoro. Per altre informazioni sulla creazione di un pool Apache Spark nell'area di lavoro Azure Synapse Analytics, vedere Avvio [rapido:](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)Creare un pool di Apache Spark server usando Synapse Studio . Il tipo di `attach_config` è `ComputeTargetAttachConfiguration`.

Dopo aver creato la configurazione, si crea un Machine Learning passando , e il nome con cui si desidera fare riferimento al calcolo `ComputeTarget` `Workspace` `ComputeTargetAttachConfiguration` all'interno dell'area di lavoro di Machine Learning. La chiamata a `ComputeTarget.attach()` è asincrona, quindi l'esempio si blocca fino al completamento della chiamata.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Creare un `SynapseSparkStep` oggetto che usa il pool di Apache Spark collegato

Il processo Spark del notebook [di esempio nel pool di Apache Spark](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) definisce una semplice pipeline di Machine Learning. In primo luogo, il notebook definisce un passaggio di preparazione dei dati basato `synapse_compute` su definito nel passaggio precedente. Il notebook definisce quindi un passaggio di training basato su una destinazione di calcolo più adatta per il training. Il notebook di esempio usa il database di sopravvivenza Titanic per illustrare l'input e l'output dei dati. non pulisce effettivamente i dati né crea un modello predittivo. Poiché non esiste un training reale in questo esempio, il passaggio di training usa una risorsa di calcolo economica e basata sulla CPU.

I dati passano in una pipeline di Machine Learning tramite `DatasetConsumptionConfig` oggetti , che possono contenere dati tabulari o set di file. I dati provengono spesso da file nell'archivio BLOB nell'archivio dati di un'area di lavoro. Il codice seguente illustra un codice tipico per la creazione di input per una pipeline di Machine Learning:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

Il codice precedente presuppone che il file si `Titanic.csv` trova nell'archivio BLOB. Il codice illustra come leggere il file come `TabularDataset` e come `FileDataset` . Questo codice è solo a scopo dimostrativo, perché potrebbe generare confusione per duplicare gli input o interpretare una singola origine dati sia come risorsa contenente tabelle che come file.

> [!Important]
> Per usare come `FileDataset` input, la versione `azureml-core` deve essere almeno `1.20.0` . Di seguito viene descritto come specificare questa opzione `Environment` usando la classe .

Al termine di un passaggio, è possibile scegliere di archiviare i dati di output usando codice simile al seguente:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

In questo caso, i dati verrebbero archiviati in in un file denominato e sarebbero disponibili all'interno dell'area di lavoro di Machine Learning come con `datastore` `test` il nome `Dataset` `registered_dataset` .

Oltre ai dati, un passaggio della pipeline può avere dipendenze Python per passaggio. Anche `SynapseSparkStep` i singoli oggetti possono specificare Azure Synapse Apache Spark configurazione. Come illustrato nel codice seguente, che specifica che la versione `azureml-core` del pacchetto deve essere almeno `1.20.0` . Come accennato in precedenza, `azureml-core` questo requisito per è necessario per usare come `FileDataset` input.

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Il codice precedente specifica un singolo passaggio nella pipeline di Azure Machine Learning. Questo passaggio specifica una versione specifica e `environment` `azureml-core` può aggiungere altre dipendenze conda o pip in base alle esigenze.

Verrà `SynapseSparkStep` compressa e caricata dal computer locale la sottodirectory `./code` . Tale directory verrà ricreata nel server di calcolo e il passaggio eseguirà il file `dataprep.py` da tale directory. E `inputs` di tale passaggio sono gli oggetti , e descritti in `outputs` `step1_input1` `step1_input2` `step1_output` precedenza. Il modo più semplice per accedere a tali valori all'interno dello script è `dataprep.py` associarli a denominato `arguments` .

Set successivo di argomenti per il controllo `SynapseSparkStep` costruttore Apache Spark. è `compute_target` `'link1-spark01'` l'oggetto collegato in precedenza come destinazione di calcolo. Gli altri parametri specificano la memoria e i core che si desidera usare.

Il notebook di esempio usa il codice seguente per `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Questo script di preparazione dei dati non esegue alcuna trasformazione dei dati reali, ma illustra come recuperare dati, convertirli in un frame di dati Spark e come eseguire alcune operazioni di base Apache Spark manipolazione. È possibile trovare l'output in Azure Machine Learning Studio aprendo l'esecuzione figlio, scegliendo la scheda **Output e** log e aprendo il file, come illustrato nella `logs/azureml/driver/stdout` figura seguente.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Screenshot di Studio che mostra la scheda stdout dell'esecuzione figlio":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Usare `SynapseSparkStep` in una pipeline

Nell'esempio seguente viene utilizzato l'output `SynapseSparkStep` di creato nella sezione [precedente.](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool) Altri passaggi nella pipeline possono avere ambienti univoci ed essere eseguiti in risorse di calcolo diverse appropriate per l'attività a portata di mano. Il notebook di esempio esegue il "passaggio di training" in un cluster CPU di piccole dimensioni:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Il codice precedente crea la nuova risorsa di calcolo, se necessario. Il risultato viene `step1_output` quindi convertito in input per il passaggio di training. `as_download()`L'opzione indica che i dati verranno spostati nella risorsa di calcolo, con conseguente accesso più rapido. Se i dati erano così grandi da non rientrare nel disco rigido di calcolo locale, è possibile usare l'opzione per trasmettere i dati tramite il `as_mount()` file system FUSE. Il `compute_target` di questo secondo passaggio è , non la risorsa usata nel passaggio di preparazione dei `'cpucluster'` `'link1-spark01'` dati. Questo passaggio usa un programma `train.py` semplice anziché quello usato nel passaggio `dataprep.py` precedente. È possibile visualizzare i dettagli di `train.py` nel notebook di esempio.

Dopo aver definito tutti i passaggi, è possibile creare ed eseguire la pipeline. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Il codice precedente crea una pipeline costituita dal passaggio di preparazione dei dati Apache Spark pool basati su Azure Synapse Analytics ( ) e dal passaggio `step_1` di training ( `step_2` ). Azure calcola il grafico di esecuzione esaminando le dipendenze dei dati tra i passaggi. In questo caso, esiste solo una dipendenza semplice che `step2_input` richiede necessariamente `step1_output` .

La chiamata a `pipeline.submit` crea, se necessario, un esperimento denominato e avvia in `synapse-pipeline` modo asincrono un'esecuzione al suo interno. I singoli passaggi all'interno della pipeline vengono eseguiti come esecuzioni figlio di questa esecuzione principale e possono essere monitorati e esaminati nella pagina Esperimenti di Studio.

## <a name="next-steps"></a>Passaggi successivi

* [Pubblicare e tenere traccia delle pipeline di Machine Learning](how-to-deploy-pipelines.md) 
* [Monitorare Azure Machine Learning](monitor-azure-machine-learning.md)
* [Usare ml automatizzato in una pipeline Azure Machine Learning in Python](how-to-use-automlstep-in-pipelines.md)
