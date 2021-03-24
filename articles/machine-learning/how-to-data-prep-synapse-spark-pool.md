---
title: Dati con pool di Apache Spark (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come associare e avviare pool di Apache Spark per data wrangling con analisi e Azure Machine Learning di sinapsi di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 9ced4da7f71a0499e538e499644d89240611f1ea
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956214"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Connetti pool di Apache Spark (basati su Azure sinapsi Analytics) per data wrangling (anteprima)

Questo articolo illustra come aggiungere e avviare un pool di Apache Spark basato su [Azure sinapsi Analytics](/synapse-analytics/overview-what-is.md) per data wrangling su larga scala. 

Questo articolo contiene informazioni aggiuntive per l'esecuzione interattiva di data wrangling attività all'interno di una sessione sinapsi dedicata in un notebook di Jupyter. Se si preferisce usare pipeline di Azure Machine Learning, vedere [How to use Apache Spark (Powered by Azure sinapsi Analytics) nella pipeline di Machine Learning (anteprima)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> L'integrazione di Azure Machine Learning e Azure sinapsi Analytics è in anteprima. Le funzionalità illustrate in questo articolo usano il `azureml-synapse` pacchetto che contiene funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) che possono cambiare in qualsiasi momento.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Integrazione di Azure Machine Learning e analisi sinapsi di Azure (anteprima)

L'integrazione di Azure sinapsi Analytics con Azure Machine Learning (anteprima) consente di associare un pool di Apache Spark supportato da Azure sinapsi per la preparazione e l'esplorazione interattiva dei dati. Grazie a questa integrazione, è possibile disporre di un calcolo dedicato per data wrangling su larga scala, tutto nello stesso notebook di Python usato per il training dei modelli di machine learning.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Creare un'area di lavoro di Azure sinapsi Analytics in portale di Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Creare Apache Spark pool con portale di Azure, strumenti Web o sinapsi Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per installare il Azure Machine Learning SDK oppure usare un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md#create) con l'SDK già installato. 

* Installare il `azureml-synapse` pacchetto (anteprima) con il codice seguente:

  ```python
  pip install azureml-synapse
  ```

* [Collegare Azure machine learning area di lavoro e l'area di lavoro di Azure sinapsi Analytics](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Ottenere un servizio collegato esistente
Prima di poter collegare un calcolo dedicato per data wrangling, è necessario disporre di un'area di lavoro ML collegata a un'area di lavoro di Azure sinapsi Analytics. questa operazione viene definita servizio collegato. 

Per recuperare e usare un servizio collegato esistente, è necessario disporre delle autorizzazioni **utente o collaboratore** per l'area di lavoro di Azure sinapsi Analytics.

Visualizzare tutti i servizi collegati associati all'area di lavoro di machine learning. 

```python
LinkedService.list(ws)
```

In questo esempio viene recuperato un servizio collegato esistente, `synapselink1` , dall'area `ws` di lavoro,, con il [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metodo.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Connetti il pool di Spark di sinapsi come calcolo

Una volta recuperato il servizio collegato, collegare un pool di Apache Spark sinapsi come risorsa di calcolo dedicata per le attività data wrangling. 

È possibile allegare Apache Spark pool tramite,
* Azure Machine Learning Studio
* [Modelli di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Allegare un pool tramite studio
A tale scopo, seguire questa procedura: 

1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **servizi collegati** nella sezione **Gestisci** del riquadro sinistro.
1. Selezionare l'area di lavoro sinapsi.
1. Selezionare **pool Spark collegati** in alto a sinistra. 
1. Selezionare **Allega**. 
1. Selezionare il pool di Apache Spark dall'elenco e specificare un nome.  
    1. Questo elenco identifica i pool di disponibilità delle sinapsi Spark che possono essere collegati al calcolo. 
    1. Per creare un nuovo pool di Spark di sinapsi, vedere [creare un pool di Apache Spark con sinapsi Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Selezionare **Connetti selezionato**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Associare un pool con Python SDK

È anche possibile usare **Python SDK** per alleghi un pool di Apache Spark. 

Il codice seguente, 
1. Configura SynapseCompute con,

   1. LinkedService, `linked_service` creato o recuperato nel passaggio precedente. 
   1. Tipo di destinazione di calcolo che si vuole alleghire, `SynapseSpark`
   1. Nome del pool di Apache Spark. Deve corrispondere a un pool di Apache Spark esistente che si trova nell'area di lavoro di Azure sinapsi Analytics.
   
1. Crea un ComputeTarget di Machine Learning passando, 
   1. Area di lavoro di Machine Learning che si vuole usare, `ws`
   1. Il nome che si vuole fare riferimento al calcolo nell'area di lavoro Azure Machine Learning. 
   1. Il attach_configuration specificato durante la configurazione del calcolo sinapsi.
       1. La chiamata a ComputeTarget. allegato () è asincrona, pertanto l'esempio si blocca fino al completamento della chiamata.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Verificare che il pool di Apache Spark sia collegato.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Avviare la sinapsi Spark pool per le attività di preparazione dei dati

Per iniziare la preparazione dei dati con il pool di Apache Spark, specificare il nome del pool di Apache Spark:

> [!IMPORTANT]
> Per continuare a usare il pool di Apache Spark è necessario indicare la risorsa di calcolo da usare in tutte le attività di data wrangling con `%synapse` per le singole righe di codice e `%%synapse` per più righe. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Dopo l'avvio della sessione, è possibile controllare i metadati della sessione.

```python
%synapse meta
```

È possibile specificare un [ambiente di Azure Machine Learning](concept-environments.md) da usare durante la sessione di Apache Spark. Solo le dipendenze conda specificate nell'ambiente diverranno effettive. L'immagine Docker non è supportata.

>[!WARNING]
>  Le dipendenze Python specificate nelle dipendenze conda dell'ambiente non sono supportate nei pool di Apache Spark. Attualmente sono supportate solo le versioni fisse di Python. Controllare la versione di Python includendo  `sys.version_info` nello script.

Il codice seguente crea l'ambiente, `myenv` , che installa la `azureml-core` versione 1.20.0 e `numpy` la versione 1.17.0 prima che inizi la sessione. È quindi possibile includere questo ambiente nell'istruzione Apache Spark sessione `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Per iniziare la preparazione dei dati con il pool di Apache Spark e l'ambiente personalizzato, specificare il nome del pool di Apache Spark e l'ambiente da usare durante la sessione di Apache Spark. Inoltre, è possibile fornire l'ID sottoscrizione, il gruppo di risorse dell'area di lavoro di machine learning e il nome dell'area di lavoro di machine learning.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Caricare i dati dalla risorsa di archiviazione

Una volta avviata la sessione di Apache Spark, leggere i dati da preparare. Il caricamento dei dati è supportato per archiviazione BLOB di Azure e Azure Data Lake Storage generazioni 1 e 2.

Esistono due modi per caricare i dati da questi servizi di archiviazione: 

* Caricare direttamente i dati dall'archiviazione utilizzando il relativo percorso HDFS (Hadoop Distributed files System).

* Leggere i dati da un set di dati di [Azure Machine Learning](how-to-create-register-datasets.md)esistente.

Per accedere a questi servizi di archiviazione, sono necessarie le autorizzazioni di **lettura dati BLOB di archiviazione** . Se si prevede di scrivere di nuovo i dati in questi servizi di archiviazione, sono necessarie le autorizzazioni di **collaboratore dati BLOB di archiviazione** . [Altre informazioni sulle autorizzazioni e sui ruoli di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Caricare i dati con il percorso HDFS (Hadoop Distributed file System)

Per caricare e leggere i dati in dalla risorsa di archiviazione con il percorso HDFS corrispondente, è necessario disporre delle credenziali di autenticazione per l'accesso ai dati prontamente disponibili. Queste credenziali variano a seconda del tipo di archiviazione.  

Il codice seguente illustra come leggere i dati da un **Archivio BLOB di Azure** in un dataframe di Spark con il token di firma di accesso condiviso o la chiave di accesso. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Il codice seguente illustra come leggere i dati in da **Azure Data Lake storage generazione 1 (ADLS gen 1)** con le credenziali dell'entità servizio. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Il codice seguente illustra come leggere i dati in da **Azure Data Lake storage generazione 2 (ADLS gen 2)** con le credenziali dell'entità servizio. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Leggere i dati dai set di dati registrati

È anche possibile ottenere un set di dati registrato esistente nell'area di lavoro ed eseguire la preparazione dei dati tramite la conversione in un frame di dati Spark.

L'esempio seguente esegue l'autenticazione all'area di lavoro, ottiene un TabularDataset registrato, `blob_dset` , che fa riferimento ai file nell'archivio BLOB e lo converte in un frame di dati Spark. Quando si convertono i set di dati in un dataframe di Spark, è possibile sfruttare le `pyspark` librerie di esplorazione e preparazione dei dati.  

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

## <a name="perform-data-wrangling-tasks"></a>Eseguire attività data wrangling

Dopo aver recuperato ed esplorato i dati, è possibile eseguire data wrangling attività.

Il codice seguente, si espande sull'esempio HDFS nella sezione precedente e filtra i dati nel frame di dati Spark, `df` , in base alla colonna **Survivor** e ai gruppi che elencano per **età**

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Salvare i dati nell'archiviazione e arrestare la sessione Spark

Al termine dell'esplorazione e della preparazione dei dati, archiviare i dati preparati per un uso successivo nell'account di archiviazione in Azure.

Nell'esempio seguente i dati preparati vengono riscritti nell'archiviazione BLOB di Azure e sovrascrivono il `Titanic.csv` file originale nella `training_data` Directory. Per eseguire il writeback nella risorsa di archiviazione, sono necessarie le autorizzazioni di **collaboratore dati BLOB di archiviazione** . [Altre informazioni sulle autorizzazioni e sui ruoli di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Dopo aver completato la preparazione dei dati e salvato i dati preparati nell'archivio, interrompere l'uso del pool di Apache Spark con il comando seguente.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Crea set di dati per rappresentare i dati preparati

Quando si è pronti a utilizzare i dati preparati per il training del modello, connettersi alla risorsa di archiviazione con un [archivio dati Azure Machine Learning](how-to-access-data.md)e specificare i file da usare con un set di dati di [Azure Machine Learning](how-to-create-register-datasets.md).

Nell'esempio di codice seguente,

* Presuppone che sia già stato creato un archivio dati che si connette al servizio di archiviazione in cui sono stati salvati i dati preparati.  
* Ottiene l'archivio dati esistente, `mydatastore` , dall'area di lavoro, `ws` con il metodo Get ().
* Crea un [oggetto filedataset](how-to-create-register-datasets.md#filedataset), `train_ds` , che fa riferimento ai file di dati preparati presenti nella `training_data` directory in `mydatastore` .  
* Crea la variabile `input1` , che può essere usata in un secondo momento per rendere disponibili i file di dati del `train_ds` set di dati a una destinazione di calcolo.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Notebook di esempio

Vedere questo [notebook end-to-end](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) per un esempio di codice dettagliato su come eseguire la preparazione dei dati e il training del modello da un singolo notebook con Azure sinapsi Analytics e Azure Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire [il training di un modello](how-to-set-up-training-targets.md).
* [Eseguire il training con Azure Machine Learning DataSet](how-to-train-with-datasets.md)
