---
title: Preparazione dei dati con pool Spark (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come associare pool Spark per la preparazione dei dati con la sinapsi di Azure e Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 87e03b6aee122c5a26d4388ca8b570aa6cdf7b55
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662893"
---
# <a name="attach-synapse-spark-pools-for-data-preparation-with-azure-synapse-preview"></a>Associare i pool di Spark sinapsi per la preparazione dei dati con la sinapsi di Azure (anteprima)

Questo articolo illustra come aggiungere e avviare un pool di Apache Spark supportato da [sinapsi di Azure](/synapse-analytics/overview-what-is.md) per la preparazione dei dati. 

>[!IMPORTANT]
> L'integrazione tra Azure Machine Learning e sinapsi di Azure è in anteprima. Le funzionalità illustrate in questo articolo usano il `azureml-synapse` pacchetto che contiene funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) che possono cambiare in qualsiasi momento.

## <a name="azure-machine-learning-and-azure-synapse-integration-preview"></a>Integrazione di Azure Machine Learning e sinapsi di Azure (anteprima)

L'integrazione di sinapsi di Azure con Azure Machine Learning (anteprima) consente di associare un pool di Apache Spark supportato da sinapsi di Azure per la preparazione e l'esplorazione interattiva dei dati. Grazie a questa integrazione, è possibile disporre di un calcolo dedicato per la preparazione dei dati su larga scala, tutto nello stesso notebook di Python usato per il training dei modelli di machine learning.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Creare un'area di lavoro sinapsi in portale di Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Creare Apache Spark pool con portale di Azure, strumenti Web o sinapsi Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Installare il Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) , che include il `azureml-synapse` pacchetto (anteprima). 
    * È anche possibile installarlo autonomamente, ma è compatibile solo con le versioni 1,20 o successive dell'SDK. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-assets"></a>Collegare l'area di lavoro di machine learning e le risorse sinapsi

Prima di poter collegare un pool di Spark di sinapsi per la preparazione dei dati, l'area di lavoro di Azure Machine Learning deve essere collegata all'area di lavoro di sinapsi di Azure. 

È possibile collegare l'area di lavoro ML e l'area di lavoro sinapsi tramite [Python SDK](#link-sdk) o il [Azure Machine Learning Studio](#link-studio). 

> [!IMPORTANT]
> Per eseguire correttamente il collegamento all'area di lavoro sinapsi, è necessario disporre del ruolo **proprietario** dell'area di lavoro sinapsi. Controllare l'accesso nella [portale di Azure](https://ms.portal.azure.com/).
>
> Se non si è un **proprietario** dell'area di lavoro sinapsi, ma si vuole usare un servizio collegato esistente, vedere [ottenere un servizio collegato esistente](#get-an-existing-linked-service).


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Collegamento di aree di lavoro con Python SDK

Il codice seguente usa le [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) classi e [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) per, 

* Collegare l'area di lavoro di Machine Learning `ws` con l'area di lavoro di Azure sinapsi. 
* Registrare l'area di lavoro sinapsi con Azure Machine Learning come servizio collegato.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> Viene creata un'identità gestita `system_assigned_identity_principal_id` per ogni servizio collegato. A questa identità gestita deve essere concesso il ruolo di **amministratore apache Spark sinapsi** dell'area di lavoro sinapsi prima di avviare la sessione sinapsi. [Assegnare il ruolo di amministratore apache Spark sinapsi all'identità gestita in sinapsi Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Per trovare il `system_assigned_identity_principal_id` di un servizio collegato specifico, usare `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Collegamento di aree di lavoro tramite studio

Collegare l'area di lavoro di machine learning e l'area di lavoro sinapsi tramite il Azure Machine Learning Studio con i passaggi seguenti: 

1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **servizi collegati** nella sezione **Gestisci** del riquadro sinistro.
1. Selezionare **Aggiungi integrazione**.
1. Nel form **area di lavoro collegamento** popolare i campi

   |Campo| Descrizione    
   |---|---
   |Nome| Specificare un nome per il servizio collegato. Questo nome è quello che verrà usato per fare riferimento a questo particolare servizio collegato.
   |Nome sottoscrizione | Selezionare il nome della sottoscrizione associata all'area di lavoro di machine learning. 
   |Area di lavoro sinapsi | Selezionare l'area di lavoro sinapsi a cui si desidera collegarsi. 
   
1. Selezionare **Next (avanti** ) per aprire il modulo **Select Spark pools (facoltativo)** . In questo modulo si seleziona il pool di Spark di sinapsi da connettersi all'area di lavoro

1. Selezionare **Avanti** per **aprire il modulo di verifica e** verificare le selezioni. 
1. Selezionare **Crea** per completare il processo di creazione del servizio collegato.

## <a name="get-an-existing-linked-service"></a>Ottenere un servizio collegato esistente

Per recuperare e usare un servizio collegato esistente, è necessario disporre delle autorizzazioni **utente o collaboratore** per l'area di lavoro sinapsi.

In questo esempio viene recuperato un servizio collegato esistente, `synapselink1` , dall'area `ws` di lavoro,, con il [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) metodo.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>Gestisci servizi collegati

Per scollegare le aree di lavoro, usare il `unregister()` Metodo

``` python
linked_service.unregister()
```

Visualizzare tutti i servizi collegati associati all'area di lavoro di machine learning. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Connetti il pool di Spark di sinapsi come calcolo

Una volta collegate le aree di lavoro, collegare un pool di Spark di sinapsi come risorsa di calcolo dedicata per le attività di preparazione dei dati. 

È possibile allegare pool Spark di sinapsi tramite,
* Azure Machine Learning Studio
* [Modelli di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

Seguire questa procedura per aggiungere un pool di Spark di sinapsi usando Studio. 

1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **servizi collegati** nella sezione **Gestisci** del riquadro sinistro.
1. Selezionare l'area di lavoro sinapsi.
1. Selezionare **pool Spark collegati** in alto a sinistra. 
1. Selezionare **Allega**. 
1. Selezionare il pool di Spark di sinapsi dall'elenco e specificare un nome.  
    1. Questo elenco identifica i pool di disponibilità delle sinapsi Spark che possono essere collegati al calcolo. 
    1. Per creare un nuovo pool di Spark di sinapsi, vedere [creare un pool di Apache Spark con sinapsi Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Selezionare **Connetti selezionato**. 


È anche possibile usare **Python SDK** per alleghi un pool di Spark di sinapsi. 

Il codice seguente, 
1. Configura SynapseCompute con,

   1. LinkedService, `linked_service` creato o recuperato nel passaggio precedente. 
   1. Tipo di destinazione di calcolo che si vuole alleghire, `SynapseSpark`
   1. Nome del pool di Spark di sinapsi. Deve corrispondere a un pool di Apache Spark esistente che si trova nell'area di lavoro sinapsi.
   
1. Crea un ComputeTarget di Machine Learning passando, 
   1. Area di lavoro di Machine Learning che si vuole usare, `ws`
   1. Il nome che si vuole fare riferimento al calcolo nell'area di lavoro di machine learning. 
   1. Il attach_configuration specificato durante la configurazione del SynapseCompute.
       1. La chiamata a ComputeTarget. allegato () è asincrona, pertanto l'esempio si blocca fino al completamento della chiamata.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Verificare che il pool di Spark di sinapsi sia collegato.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Avviare la sinapsi Spark pool per le attività di preparazione dei dati

È possibile specificare un [ambiente di Azure Machine Learning](concept-environments.md) da usare durante la sessione sinapsi. Solo le dipendenze conda specificate nell'ambiente diverranno effettive. L'immagine Docker non è supportata.

>[!WARNING]
>  Le dipendenze di Python specificate nelle dipendenze conda dell'ambiente non sono supportate nei pool sinapsi Spark. Attualmente sono supportate solo le versioni fisse di Python. Controllare la versione di Python includendo  `sys.version_info` nello script.

Il codice seguente crea l'ambiente, `myenv` , che installa la `azureml-core` versione 1.20.0 e `numpy` la versione 1.17.0 prima che inizi la sessione. È quindi possibile includere questo ambiente nell'istruzione della sessione sinapsi `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Per iniziare la preparazione dei dati con il pool di Spark sinapsi, specificare il nome del pool di sinapsi Spark e fornire l'ID sottoscrizione, il gruppo di risorse dell'area di lavoro di Machine Learning, il nome dell'area di lavoro di machine learning e l'ambiente da usare durante la sessione sinapsi. 

> [!IMPORTANT]
> Per continuare a usare il pool sinapsi Spark è necessario indicare la risorsa di calcolo da usare durante le attività di preparazione dei dati con `%synapse` per le singole righe di codice e `%%synapse` per più righe. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

Dopo l'avvio della sessione, è possibile controllare i metadati della sessione.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Caricare i dati dalla risorsa di archiviazione

Una volta avviata la sessione di Spark di sinapsi, leggere i dati da preparare. Il caricamento dei dati è supportato per archiviazione BLOB di Azure e Azure Data Lake Storage generazioni 1 e 2.

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
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

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

Nell'esempio seguente viene ottenuto un TabularDataset registrato, `blob_dset` , che fa riferimento ai file nell'archivio BLOB e lo converte in un frame di dati Spark. Quando si convertono i set di dati in un dataframe di Spark, è possibile sfruttare le `pyspark` librerie di esplorazione e preparazione dei dati.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>Eseguire attività di preparazione dei dati

Dopo aver recuperato ed esplorato i dati, è possibile eseguire attività di preparazione dei dati.

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

Dopo aver completato la preparazione dei dati e salvato i dati preparati nell'archivio, interrompere l'uso del pool di Spark di sinapsi con il comando seguente.

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

Vedere questo [notebook end-to-end](../synapse-analytics/overview-what-is.md) per un esempio di codice dettagliato su come eseguire la preparazione dei dati e il training del modello da un singolo notebook con la sinapsi di Azure e Azure Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire [il training di un modello](how-to-set-up-training-targets.md).
* [Eseguire il training con Azure Machine Learning DataSet](how-to-train-with-datasets.md)
* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md).

