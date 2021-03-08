---
title: Usare Apache Spark in una pipeline di Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Collegare l'area di lavoro di Azure sinapsi Analytics alla pipeline di Azure Machine Learning per usare Apache Spark per la manipolazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 1dc4e0b70b0d39d01bada26992eb2213c1e855c5
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455060"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Come usare Apache Spark (basati su Azure sinapsi Analytics) nella pipeline di Machine Learning (anteprima)

Questo articolo illustra come usare i pool di Apache Spark basati su Azure sinapsi Analytics come destinazione di calcolo per un passaggio di preparazione dei dati in una pipeline di Azure Machine Learning. Si apprenderà come una singola pipeline può usare le risorse di calcolo appropriate per il passaggio specifico, ad esempio la preparazione dei dati o la formazione. Verranno visualizzati i dati preparati per il passaggio Spark e il modo in cui vengono passati al passaggio successivo. 

## <a name="prerequisites"></a>Prerequisiti

* Creare un'[area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) che conterrà tutte le risorse della pipeline.

* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare il Azure Machine Learning SDK oppure usare un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) con l'SDK già installato.

* Creare un'area di lavoro di Azure sinapsi Analytics e un pool di Apache Spark (vedere [Guida introduttiva: creare un pool di Apache Spark senza server con sinapsi Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Collegare l'area di lavoro Azure Machine Learning e l'area di lavoro di Azure sinapsi Analytics 

È possibile creare e amministrare i pool di Apache Spark in un'area di lavoro di Azure sinapsi Analytics. Per integrare un pool di Apache Spark con un'area di lavoro Azure Machine Learning, è necessario [collegarsi all'area di lavoro di Azure sinapsi Analytics](how-to-link-synapse-ml-workspaces.md). 

È possibile collegare un pool di Apache Spark tramite l'interfaccia utente di Azure Machine Learning Studio usando la pagina **servizi collegati** . È anche possibile eseguire questa operazione tramite la pagina **calcolo** con l'opzione **Connetti calcolo** .

È anche possibile allegare un pool di Apache Spark tramite SDK (come illustrato di seguito) o tramite un modello ARM (vedere questo [modello ARM di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

È possibile usare la riga di comando per seguire il modello ARM, aggiungere il servizio collegato e collegare il pool di Apache Spark con il codice seguente:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Per eseguire correttamente il collegamento all'area di lavoro di Azure sinapsi Analytics, è necessario avere il ruolo proprietario nella risorsa dell'area di lavoro di Azure sinapsi Analytics. Controllare l'accesso nella portale di Azure.
> Il servizio collegato otterrà un'identità assegnata dal sistema durante la creazione. È necessario assegnare questo servizio di collegamento SAI il ruolo "sinapsi Apache Spark amministratore" da sinapsi studio in modo da poter inviare il processo Spark (vedere [come gestire le assegnazioni di ruolo RBAC in sinapsi Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). È inoltre necessario concedere all'utente dell'area di lavoro Azure Machine Learning il ruolo "collaboratore" da portale di Azure della risorsa Management.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Creare o recuperare il collegamento tra l'area di lavoro di Azure sinapsi Analytics e l'area di lavoro Azure Machine Learning

È possibile recuperare i servizi collegati nell'area di lavoro con codice come il seguente:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Per prima cosa, `Workspace.from_config()` accedere all'area di lavoro di Azure Machine Learning usando la configurazione in `config.json` (vedere [esercitazione: Introduzione ai Azure Machine Learning nell'ambiente di sviluppo](tutorial-1st-experiment-sdk-setup-local.md)). Il codice stampa quindi tutti i servizi collegati disponibili nell'area di lavoro. Infine, `LinkedService.get()` Recupera un servizio collegato denominato `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Connettersi al pool Apache Spark come destinazione di calcolo per Azure Machine Learning

Per usare il pool Apache Spark per eseguire un passaggio nella pipeline di Machine Learning, è necessario collegarlo come `ComputeTarget` per il passaggio della pipeline, come illustrato nel codice seguente.

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

Il primo passaggio consiste nel configurare il `SynapseCompute` . L' `linked_service` argomento è l' `LinkedService` oggetto creato o recuperato nel passaggio precedente. L' `type` argomento deve essere `SynapseSpark` . L' `pool_name` argomento in `SynapseCompute.attach_configuration()` deve corrispondere a quello di un pool esistente nell'area di lavoro di Azure sinapsi Analytics. Per altre informazioni sulla creazione di un pool Apache Spark nell'area di lavoro di Azure sinapsi Analytics, vedere [Guida introduttiva: creare un pool di Apache Spark senza server con sinapsi Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Il tipo di `attach_config` è `ComputeTargetAttachConfiguration`.

Una volta creata la configurazione, è possibile creare un machine learning `ComputeTarget` passando `Workspace` , `ComputeTargetAttachConfiguration` e il nome con cui si vuole fare riferimento al calcolo nell'area di lavoro di machine learning. La chiamata a `ComputeTarget.attach()` è asincrona, pertanto l'esempio si blocca fino al completamento della chiamata.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Creare un `SynapseSparkStep` che usi il pool di Apache Spark collegato

Flussi di dati in una pipeline di Machine Learning tramite `DatasetConsumptionConfig` oggetti, che possono conservare dati tabulari o set di file. I dati provengono spesso da file nell'archiviazione BLOB nell'archivio dati di un'area di lavoro. Il codice seguente illustra un codice tipico per la creazione di input per una pipeline di Machine Learning:

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

Il codice precedente presuppone che il file `Titanic.csv` si trovi nell'archivio BLOB. Il codice Mostra come leggere il file come `TabularDataset` e come `FileDataset` . Questo codice è solo a scopo dimostrativo, in quanto creerebbe confusione per duplicare gli input o per interpretare una singola origine dati sia come risorsa che contiene una tabella che come file.

> [!Important]
> Per usare un `FileDataset` come input, la `azureml-core` versione deve essere almeno `1.20.0` . Come specificare questa impostazione usando la `Environment` classe, illustrata di seguito.

Al termine di un passaggio, è possibile scegliere di archiviare i dati di output usando codice simile al seguente:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

In questo caso, i dati verrebbero archiviati in `datastore` un file denominato `test` e sarebbero disponibili all'interno dell'area di lavoro di machine learning come `Dataset` con il nome `registered_dataset` .

Oltre ai dati, un passaggio della pipeline può avere dipendenze Python per passaggio. `SynapseSparkStep`I singoli oggetti possono specificare anche la propria precisa configurazione di Apache Spark di Azure sinapsi. Questa operazione viene illustrata nel codice seguente, che specifica che la `azureml-core` versione del pacchetto deve essere almeno `1.20.0` . Come indicato in precedenza, questo requisito per `azureml-core` è necessario per usare un `FileDataset` come input.

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

Il codice precedente specifica un singolo passaggio nella pipeline di Azure Machine Learning. Questo passaggio `environment` specifica una versione specifica `azureml-core` e potrebbe aggiungere altre dipendenze conda o PIP, se necessario.

Il `SynapseSparkStep` viene compresso e caricato dal computer locale nella sottodirectory `./code` . Tale directory verrà ricreata nel server di calcolo e il passaggio eseguirà il file `dataprep.py` da tale directory. `inputs`E `outputs` di tale passaggio sono gli `step1_input1` oggetti, `step1_input2` e `step1_output` descritti in precedenza. Il modo più semplice per accedere a tali valori nello `dataprep.py` script consiste nell'associarli a denominati `arguments` .

Il set successivo di argomenti per il `SynapseSparkStep` controllo costruttore Apache Spark. `compute_target`È l'oggetto `'link1-spark01'` collegato in precedenza come destinazione di calcolo. Gli altri parametri specificano la memoria e i core che si vuole usare.

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

Questo script di preparazione dei dati non esegue alcuna trasformazione dei dati reale, ma illustra come recuperare i dati, convertirli in un dataframe di Spark e come eseguire alcune operazioni di base Apache Spark manipolazione. È possibile trovare l'output in Azure Machine Learning Studio aprendo l'esecuzione figlio, scegliendo la scheda **output + log** e aprendo il `logs/azureml/driver/stdout` file, come illustrato nella figura seguente.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Screenshot di studio che mostra la scheda stdout dell'esecuzione figlio":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Usare `SynapseSparkStep` in una pipeline

Altri passaggi della pipeline possono avere ambienti univoci ed essere eseguiti su risorse di calcolo diverse appropriate per l'attività. Il notebook di esempio esegue il "passaggio di training" in un cluster di CPU di piccole dimensioni:

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

Il codice precedente crea la nuova risorsa di calcolo, se necessario. Quindi, il `step1_output` risultato viene convertito in input per il passaggio di training. L' `as_download()` opzione indica che i dati verranno spostati nella risorsa di calcolo, ottenendo un accesso più rapido. Se i dati erano talmente grandi da non essere adatti al disco rigido di calcolo locale, è possibile usare l' `as_mount()` opzione per eseguire lo streaming dei dati tramite il file System di fuse. Il `compute_target` di questo secondo passaggio è `'cpucluster'` , non la `'link1-spark01'` risorsa usata nel passaggio di preparazione dei dati. Questo passaggio usa un programma semplice `train.py` anziché quello `dataprep.py` usato nel passaggio precedente. È possibile visualizzare i dettagli di `train.py` nel notebook di esempio.

Dopo aver definito tutti i passaggi, è possibile creare ed eseguire la pipeline. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Il codice precedente crea una pipeline costituita dal passaggio di preparazione dei dati nei pool di Apache Spark basati su Azure sinapsi Analytics ( `step_1` ) e il passaggio di training ( `step_2` ). Azure calcola il grafico di esecuzione esaminando le dipendenze dei dati tra i passaggi. In questo caso, esiste solo una dipendenza semplice che `step2_input` richiede necessariamente `step1_output` .

La chiamata a `pipeline.submit` Crea, se necessario, un esperimento chiamato `synapse-pipeline` e avvia in modo asincrono un'esecuzione al suo interno. I singoli passaggi all'interno della pipeline vengono eseguiti come esecuzioni figlio dell'esecuzione principale e possono essere monitorati e rivisti nella pagina esperimenti di studio.

## <a name="next-steps"></a>Passaggi successivi

* [Pubblicare e monitorare le pipeline di Machine Learning](how-to-deploy-pipelines.md) 
* [Monitorare Azure Machine Learning](monitor-azure-machine-learning.md)
* [Usare Machine Learning automatiche in una pipeline Azure Machine Learning in Python](how-to-use-automlstep-in-pipelines.md)
