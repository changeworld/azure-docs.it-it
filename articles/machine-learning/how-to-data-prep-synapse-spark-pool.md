---
title: Data wrangling con pool Apache Spark (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come collegare e avviare pool Apache Spark per data wrangling con Azure Synapse Analytics e Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 1852bfdb4bf8513aaa5d43993e2b6ff804808dbd
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575650"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Collegare Apache Spark pool (con tecnologia Azure Synapse Analytics) per data wrangling (anteprima)

Questo articolo illustra come collegare un pool di Apache Spark con tecnologia [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) all'area di lavoro di Azure Machine Learning, in modo da poterlo avviare ed eseguire data wrangling su larga scala. 

Questo articolo contiene indicazioni per l'data wrangling di attività in modo interattivo all'interno di una sessione di Synapse dedicata in un notebook di Jupyter [usando Azure Machine Learning Python SDK.](/python/api/overview/azure/ml/) Se si preferisce usare Azure Machine Learning pipeline, vedere Come usare Apache Spark (con tecnologia Azure Synapse Analytics) nella pipeline di Machine [Learning (anteprima).](how-to-use-synapsesparkstep.md)

Per indicazioni su come usare Azure Synapse Analytics con un'area di lavoro synapse, vedere la serie Azure Synapse Analytics [introduzione.](../synapse-analytics/get-started.md)

>[!IMPORTANT]
> L'Azure Machine Learning e Azure Synapse Analytics è disponibile in anteprima. Le funzionalità presentate in questo articolo utilizzano il `azureml-synapse` pacchetto che contiene funzionalità di anteprima [sperimentali](/python/api/overview/azure/ml/#stable-vs-experimental) che possono cambiare in qualsiasi momento.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>integrazione Azure Machine Learning e Azure Synapse Analytics (anteprima)

L Azure Synapse Analytics integrazione di Azure Machine Learning (anteprima) consente di collegare un pool di Apache Spark supportato da Azure Synapse per l'esplorazione interattiva dei dati e la preparazione. Con questa integrazione, è possibile avere un calcolo dedicato per data wrangling su larga scala, il tutto all'interno dello stesso notebook Python che si usa per il training dei modelli di Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Il [Azure Machine Learning Python SDK installato.](/python/api/overview/azure/ml/install) 

* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Creare un'Azure Synapse Analytics di lavoro in portale di Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Creare Apache Spark pool usando portale di Azure, strumenti Web o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Configurare l'ambiente di](how-to-configure-environment.md) sviluppo per installare Azure Machine Learning SDK o usare un'Azure Machine Learning [di](concept-compute-instance.md#create) calcolo con l'SDK già installato. 

* Installare il `azureml-synapse` pacchetto (anteprima) con il codice seguente:

  ```python
  pip install azureml-synapse
  ```

* [Collegare l Azure Machine Learning area di lavoro e Azure Synapse Analytics'area di lavoro](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Ottenere un servizio collegato esistente
Prima di poter collegare un calcolo dedicato per data wrangling, è necessario avere un'area di lavoro di Machine Learning collegata a un'area di lavoro Azure Synapse Analytics, detta servizio collegato. 

Per recuperare e usare un servizio collegato esistente sono necessarie **le autorizzazioni Utente** o Collaboratore per l'area Azure Synapse Analytics lavoro.

Visualizza tutto i servizi collegati associati all'area di lavoro di Machine Learning. 

```python
from azureml.core import LinkedService

LinkedService.list(ws)
```

In questo esempio viene recuperato un servizio collegato esistente, `synapselink1` , dall'area di lavoro , `ws` con il metodo [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) .
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Collegare il pool Synapse Spark come calcolo

Dopo aver recuperato il servizio collegato, collegare un pool di Apache Spark Synapse come risorsa di calcolo dedicata per le data wrangling attività. 

È possibile collegare Apache Spark pool tramite,
* Azure Machine Learning Studio
* [Modelli di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* The Azure Machine Learning Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Collegare un pool tramite lo studio
Seguire questa procedura: 

1. Accedere al studio di Azure Machine Learning [.](https://ml.azure.com/)
1. Selezionare **Servizi collegati** nella sezione **Gestisci** del riquadro sinistro.
1. Selezionare l'area di lavoro Synapse.
1. Selezionare **Pool Spark collegati** in alto a sinistra. 
1. Selezionare **Allega**. 
1. Selezionare il Apache Spark pool di risorse dall'elenco e specificare un nome.  
    1. Questo elenco identifica i pool di Synapse Spark disponibili che possono essere collegati al calcolo. 
    1. Per creare un nuovo pool Synapse Spark, vedere Creare Apache Spark [pool con il Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Selezionare **Collega selezionato.** 

### <a name="attach-a-pool-with-the-python-sdk"></a>Collegare un pool con Python SDK

È anche possibile usare **Python SDK per** collegare un pool Apache Spark dati. 

Il codice seguente, 
1. Configura [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) l'oggetto con,

   1. Oggetto [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice) `linked_service` creato o recuperato nel passaggio precedente. 
   1. Tipo di destinazione di calcolo da collegare. `SynapseSpark`
   1. Nome del pool Apache Spark dati. Deve corrispondere a un pool Apache Spark esistente presente nell'area Azure Synapse Analytics lavoro.
   
1. Crea un machine learning [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) passando: 
   1. L'area di lavoro di Machine Learning che si vuole usare, `ws`
   1. Nome che si desidera fare riferimento al calcolo all'interno dell'area Azure Machine Learning lavoro. 
   1. Il attach_configuration specificato durante la configurazione del calcolo Synapse.
       1. La chiamata a ComputeTarget.attach() è asincrona, quindi l'esempio si blocca fino al completamento della chiamata.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Verificare che il pool Apache Spark sia collegato.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Avviare il pool di Synapse Spark per data wrangling attività

Per iniziare la preparazione dei dati con il pool Apache Spark, specificare il nome Apache Spark pool:

> [!IMPORTANT]
> Per continuare a usare il pool Apache Spark è necessario indicare quale risorsa di calcolo usare in tutte le attività di data wrangling con per singole righe di codice e `%synapse` `%%synapse` per più righe. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Dopo l'avvio della sessione, è possibile controllare i metadati della sessione.

```python
%synapse meta
```

È possibile specificare un [ambiente Azure Machine Learning da](concept-environments.md) usare durante la Apache Spark sessione. Verranno applicate solo le dipendenze Conda specificate nell'ambiente. L'immagine Docker non è supportata.

>[!WARNING]
>  Le dipendenze Python specificate nelle dipendenze Conda dell'ambiente non sono supportate nei Apache Spark virtuali. Attualmente sono supportate solo le versioni fisse di Python. Controllare la versione di Python  `sys.version_info` includendo nello script.

Il codice seguente crea l'ambiente , che installa la `myenv` `azureml-core` versione 1.20.0 e `numpy` la versione 1.17.0 prima dell'inizio della sessione. È quindi possibile includere questo ambiente nell'istruzione Apache Spark `start` di sessione.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Per iniziare la preparazione dei dati con il pool Apache Spark e l'ambiente personalizzato, specificare il nome del pool Apache Spark e l'ambiente da usare durante la Apache Spark sessione. È anche possibile specificare l'ID sottoscrizione, il gruppo di risorse dell'area di lavoro di Machine Learning e il nome dell'area di lavoro di Machine Learning.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Caricare i dati dall'archiviazione

Una volta avviata Apache Spark sessione, leggere i dati che si vuole preparare. Il caricamento dei dati è supportato per l'archiviazione BLOB di Azure Azure Data Lake Storage generazioni 1 e 2.

Esistono due modi per caricare i dati da questi servizi di archiviazione: 

* Caricare direttamente i dati dall'archiviazione usando il percorso Hadoop Distributed Files System (HDFS).

* Legge i dati da un set di [Azure Machine Learning esistente.](how-to-create-register-datasets.md)

Per accedere a questi servizi di archiviazione, sono necessarie **le autorizzazioni di lettore dati BLOB di** archiviazione. Se si prevede di scrivere di nuovo i dati in questi servizi di archiviazione, sono necessarie le **autorizzazioni collaboratore ai dati del** BLOB di archiviazione. [Altre informazioni sulle autorizzazioni di archiviazione e sui ruoli](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Caricare i dati con il percorso HdFS (Hadoop Distributed Files System)

Per caricare e leggere i dati dall'archiviazione con il percorso HDFS corrispondente, è necessario avere le credenziali di autenticazione di accesso ai dati immediatamente disponibili. Queste credenziali variano a seconda del tipo di archiviazione.  

Il codice seguente illustra come leggere i dati da un archivio BLOB di **Azure** in un dataframe Spark con il token di firma di accesso condiviso o la chiave di accesso. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Il codice seguente illustra come leggere i dati da **Azure Data Lake Storage Generazione 1 (ADLS Gen 1)** con le credenziali dell'entità servizio. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Il codice seguente illustra come leggere i dati da **Azure Data Lake Storage Generazione 2 (ADLS Gen 2)** con le credenziali dell'entità servizio. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Legge i dati dai set di dati registrati

È anche possibile ottenere un set di dati registrato esistente nell'area di lavoro ed eseguire la preparazione dei dati converterlo in un frame di dati Spark.

L'esempio seguente esegue l'autenticazione all'area di lavoro, ottiene un oggetto TabularDataset registrato, , che fa riferimento ai file nell'archivio BLOB e lo converte `blob_dset` in un frame di dati Spark. Quando si convertono i set di dati in un dataframe Spark, è possibile sfruttare le `pyspark` librerie di preparazione e esplorazione dei dati.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Eseguire data wrangling attività

Dopo aver recuperato ed esplorato i dati, è possibile eseguire data wrangling attività.

Il codice seguente si espande in base all'esempio HDFS nella sezione precedente e filtra i dati nel frame di dati Spark, , in base alla colonna Survivor e ai gruppi elencati in base `df` **all'età** 

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Salvare i dati nell'archiviazione e arrestare la sessione Spark

Al termine dell'esplorazione e della preparazione dei dati, archiviare i dati preparati per un uso successivo nell'account di archiviazione in Azure.

Nell'esempio seguente i dati preparati vengono scritti nell'archivio BLOB di Azure e sovrascrivono il `Titanic.csv` file originale nella directory `training_data` . Per eseguire il write back nell'archiviazione, sono necessarie **le autorizzazioni di Collaboratore ai dati dei BLOB di** archiviazione. [Altre informazioni sulle autorizzazioni di archiviazione e sui ruoli.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Dopo aver completato la preparazione dei dati e salvato i dati preparati nell'archiviazione, interrompere l'uso del pool Apache Spark con il comando seguente.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Creare un set di dati per rappresentare i dati preparati

Quando si è pronti per usare i dati preparati per il training del modello, connettersi all'archiviazione con un archivio dati [Azure Machine Learning](how-to-access-data.md)e specificare i file da usare con un set di [dati Azure Machine Learning](how-to-create-register-datasets.md).

L'esempio di codice seguente,

* Si presuppone che sia già stato creato un archivio dati che si connette al servizio di archiviazione in cui sono stati salvati i dati preparati.  
* Ottiene l'archivio dati esistente, `mydatastore` , dall'area di lavoro, `ws` con il metodo get().
* Crea un [oggetto FileDataset](how-to-create-register-datasets.md#filedataset), , che fa riferimento ai file di dati preparati `train_ds` che si trovano nella directory in `training_data` `mydatastore` .  
* Crea la variabile , che può essere usata in un secondo momento per rendere disponibili i file di dati del set di `input1` dati a una destinazione di `train_ds` calcolo.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```
## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Usare un `ScriptRunConfig` oggetto per inviare un'esecuzione dell'esperimento a un pool Synapse Spark

È anche possibile [sfruttare il cluster Synapse Spark](#attach-a-pool-with-the-python-sdk) collegato in precedenza come destinazione di calcolo per l'invio di un'esecuzione dell'esperimento con [un oggetto ScriptRunConfig.](/python/api/azureml-core/azureml.core.scriptrunconfig)

```Python
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--tabular_input", input1, 
                                                 "--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Dopo aver `ScriptRunConfig` configurato l'oggetto, è possibile inviare l'esecuzione.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```
Per altri dettagli, come lo `dataprep.py` script usato in questo esempio, vedere il notebook di [esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb).

## <a name="example-notebooks"></a>Notebook di esempio

Vedere questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb) per altri concetti e dimostrazioni delle funzionalità di Azure Synapse Analytics e Azure Machine Learning di integrazione.

## <a name="next-steps"></a>Passaggi successivi

* [Training di un modello](how-to-set-up-training-targets.md).
* [Training con il set Azure Machine Learning dati](how-to-train-with-datasets.md)
