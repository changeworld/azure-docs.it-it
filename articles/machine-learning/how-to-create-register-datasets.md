---
title: Creare set di dati di Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come creare set di dati di Azure Machine Learning per accedere ai dati per le esecuzioni dell'esperimento di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 54b1fd14f97855dd42afde9a4bb34795373ff229
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417638"
---
# <a name="create-azure-machine-learning-datasets"></a>Creare set di dati di Azure Machine Learning

Questo articolo illustra come creare set di dati di Azure Machine Learning per accedere ai dati per gli esperimenti locali o remoti con Azure Machine Learning Python SDK. Per informazioni sul modo in cui i set di dati si adattano al flusso di lavoro di accesso ai dati complessivo di Azure Machine Learning, vedere l'articolo relativo ai [dati](concept-data.md#data-workflow) di

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i dati rimangono nella posizione esistente, non viene addebitato alcun costo aggiuntivo per l'archiviazione e l'integrità delle origini dati non è rischiosa. Inoltre, i set di impostazioni vengono valutati in modo differito, che facilitano le prestazioni del flusso È possibile creare set di dati da archivi dati, URL pubblici e set di dati [aperti di Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Per un'esperienza di basso livello, [creare Azure Machine Learning set di impostazioni con Azure Machine Learning Studio.](how-to-connect-data-ui.md#create-datasets)

Con Azure Machine Learning set di impostazioni è possibile:

* Conservare una sola copia dei dati nella risorsa di archiviazione, a cui fanno riferimento i set di dati.

* Accesso facile ai dati durante il training del modello senza doversi preoccupare di stringhe di connessione o percorsi di dati. [Vedere altre informazioni su come eseguire il training con i set di dati](how-to-train-with-datasets.md).

* Condividere i dati e collaborare con altri utenti.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](/python/api/overview/azure/ml/install), che include il pacchetto azureml-DataSets.

    * Creare un' [istanza di calcolo di Azure Machine Learning](how-to-create-manage-compute-instance.md), ovvero un ambiente di sviluppo completamente configurato e gestito che includa notebook integrati e SDK già installato.

    **OR**

    * Usare il notebook di Jupyter e installare l'SDK manualmente con [queste istruzioni](/python/api/overview/azure/ml/install).

> [!NOTE]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](/python/api/azureml-dataprep/) , che è compatibile solo con Python a 64 bit. Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) e CentOS (7). Se si usano distribuzioni non supportate, seguire [questa guida](/dotnet/core/install/linux) per installare .net core 2,1 per continuare. 

## <a name="compute-size-guidance"></a>Linee guida per le dimensioni di calcolo

Quando si crea un set di dati, verificare la potenza di elaborazione del calcolo e le dimensioni dei dati in memoria. Le dimensioni dei dati nell'archiviazione non corrispondono alle dimensioni dei dati in un frame di dati. Ad esempio, i dati nei file CSV possono espandersi fino a 10 volte in un frame di dati, quindi un file CSV da 1 GB può diventare 10 GB in un frame di dati. 

Se i dati sono compressi, possono espandersi ulteriormente; 20 GB di dati relativamente sparsi archiviati in formato parquet compresso possono espandersi fino a circa 800 GB di memoria. Poiché i file parquet archiviano i dati in un formato a colonne, se è necessaria solo la metà delle colonne, è sufficiente caricare circa 400 GB di memoria.

[Altre informazioni sull'ottimizzazione dell'elaborazione dei dati in Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Tipi di set di dati

Esistono due tipi di set di dati, in base al modo in cui gli utenti li utilizzano nel training; Filedatasets e TabularDatasets. Entrambi i tipi possono essere usati nei flussi di lavoro di training Azure Machine Learning che coinvolgono, estimatori, AutoML, iperguida e pipeline. 

### <a name="filedataset"></a>Filedataset

Un [filedataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Se i dati sono già stati puliti e sono pronti per l'uso negli esperimenti di training, è possibile [scaricare o montare](how-to-train-with-datasets.md#mount-vs-download) i file nel calcolo come oggetto filedataset. 

Si consiglia di usare i filedataset per i flussi di lavoro di Machine Learning, perché i file di origine possono essere in qualsiasi formato, consentendo una gamma più ampia di scenari di apprendimento automatico, tra cui l'apprendimento avanzato.

Creare un filedataset con [Python SDK](#create-a-filedataset) o con [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

Un [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. Questo consente di materializzare i dati in un frame di dati Pandas o Spark, in modo da poter usare le librerie di preparazione e formazione dei dati familiari senza dover uscire dal notebook. È possibile creare un `TabularDataset` oggetto da file con estensione CSV, TSV, parquet e JSON e dai [Risultati della query SQL](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Con TabularDatasets è possibile specificare un timestamp da una colonna nei dati o da qualsiasi posizione in cui vengono archiviati i dati del modello di percorso per abilitare un tratto della serie temporale. Questa specifica consente un filtro semplice ed efficiente in base al tempo. Per un esempio, vedere la [demo dell'API relativa alle serie temporali tabulari con i dati meteo NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Creare una TabularDataset con [Python SDK](#create-a-tabulardataset) o [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> I flussi di lavoro di [ml automatizzati](concept-automated-ml.md) generati tramite il Azure Machine Learning Studio supportano attualmente solo TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Accedere ai set di impostazioni in una rete virtuale

Se l'area di lavoro si trova in una rete virtuale, è necessario configurare il set di dati per ignorare la convalida. Per altre informazioni su come usare gli archivi dati e i set di dati in una rete virtuale, vedere [proteggere un'area di lavoro e le risorse associate](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Creare set di dati da archivi dati

Affinché i dati siano accessibili da parte di Azure Machine Learning, è necessario creare i set di dati da percorsi in [Azure Machine Learning archivi dati](how-to-access-data.md) o URL Web. 

> [!TIP] 
> È possibile creare set di dati direttamente da URL di archiviazione con accesso ai dati basato su identità. Altre informazioni sono disponibili in [connettersi all'archiviazione con accesso ai dati basato su identità (anteprima)](how-to-identity-based-data-access.md)<br><br>
Questa funzionalità è una funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) e può cambiare in qualsiasi momento. 

 
Per creare set di dati da un archivio dati con Python SDK:

1. Verificare di avere `contributor` o `owner` accedere al servizio di archiviazione sottostante dell'archivio dati registrato Azure Machine Learning. [Controllare le autorizzazioni dell'account di archiviazione nell'portale di Azure](../role-based-access-control/check-access.md).

1. Creare il set di dati facendo riferimento a percorsi nell'archivio dati. È possibile creare un set di dati da più percorsi in più archivi dati. Non esiste un limite fisso per il numero di file o le dimensioni dei dati da cui è possibile creare un set di dati. 

> [!NOTE]
> Per ogni percorso dati, verranno inviate alcune richieste al servizio di archiviazione per verificare se punta a un file o a una cartella. Questo sovraccarico può causare un peggioramento delle prestazioni o degli errori. Un set di dati che fa riferimento a una cartella con 1000 di file in viene considerato come riferimento a un percorso dati. Per ottenere prestazioni ottimali, è consigliabile creare un set di dati che fa riferimento a meno di 100 percorsi negli archivi dati.

### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Utilizzare il [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) Metodo sulla `FileDatasetFactory` classe per caricare file in qualsiasi formato e per creare un oggetto filedataset non registrato. 

Se lo spazio di archiviazione è dietro una rete virtuale o un firewall, impostare il parametro `validate=False` nel `from_files()` metodo. In questo modo viene ignorato il passaggio di convalida iniziale e si garantisce che sia possibile creare il set di dati da questi file protetti. Altre informazioni su come [usare gli archivi dati e i set di dati in una rete virtuale](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Per riutilizzare e condividere set di dati attraverso l'esperimento nell'area di lavoro, [registrare il set di dati](#register-datasets). 

> [!TIP] 
> Caricare i file da una directory locale e creare un oggetto filedataset in un unico metodo con il metodo di anteprima pubblica, [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Questo metodo è una funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) e può cambiare in qualsiasi momento. 
> 
>  Questo metodo carica i dati nell'archivio sottostante e, di conseguenza, comporta costi di archiviazione. 

### <a name="create-a-tabulardataset"></a>Creare un TabularDataset

Usare il [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) Metodo sulla `TabularDatasetFactory` classe per leggere i file in formato CSV o TSV e per creare un TabularDataset non registrato. Se si esegue la lettura da più file, i risultati verranno aggregati in una rappresentazione tabulare. 

Se lo spazio di archiviazione è dietro una rete virtuale o un firewall, impostare il parametro `validate=False` nel `from_delimited_files()` metodo. In questo modo viene ignorato il passaggio di convalida iniziale e si garantisce che sia possibile creare il set di dati da questi file protetti. Altre informazioni su come usare gli [archivi dati e i set di dati in una rete virtuale](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Il codice seguente ottiene l'area di lavoro esistente e l'archivio dati desiderato in base al nome. Quindi passa l'archivio dati e i percorsi dei file al `path` parametro per creare un nuovo TabularDataset `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Imposta schema dati

Per impostazione predefinita, quando si crea un TabularDataset, i tipi di dati della colonna vengono dedotti automaticamente. Se i tipi dedotti non corrispondono alle aspettative, è possibile aggiornare lo schema del set di dati specificando i tipi di colonna con il codice seguente. Il parametro `infer_column_type` è applicabile solo per i set di dati creati da file delimitati. [Altre informazioni sui tipi di dati supportati](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indice|PassengerId|Survived|Pclass|Nome|Sesso|Età|SibSp|Parch|Ticket|Tariffe|Abitacolo|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Vero|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7,9250||S

Per riutilizzare e condividere set di dati tra gli esperimenti nell'area di lavoro, [registrare il set di dati](#register-datasets).

## <a name="wrangle-data"></a>Disputa dati
Dopo aver creato e [registrato](#register-datasets) il set di dati, è possibile caricarlo nel notebook per data wrangling e l' [esplorazione](#explore-data) prima del training del modello. 

Se non è necessario eseguire alcuna data wrangling o esplorazione, vedere How to User datasets negli script di training per l'invio di esperimenti di Machine Learning in [Train with DataSets](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Filtra set di impostazioni (anteprima)
Le funzionalità di filtro dipendono dal tipo di set di dati. 
> [!IMPORTANT]
> Il filtraggio dei set di impostazioni con il metodo di anteprima pubblica [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) è una funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) che può cambiare in qualsiasi momento. 
> 
**Per TabularDatasets**, è possibile salvare o rimuovere colonne con i metodi [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) e [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) .

Per filtrare le righe in base a un valore di colonna specifico in un TabularDataset, usare il metodo [Filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (anteprima). 

Negli esempi seguenti viene restituito un set di dati non registrato basato sulle espressioni specificate.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**In Filedatasets** ogni riga corrisponde a un percorso di un file, pertanto il filtro in base al valore della colonna non è utile. È tuttavia possibile [filtrare ()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) righe in base a metadati come, creationTime, dimensioni e così via.

Negli esempi seguenti viene restituito un set di dati non registrato basato sulle espressioni specificate.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

I set di dati con **etichetta** creati dai progetti di [assegnazione di etichette dei dati](how-to-create-labeling-projects.md) costituiscono un caso speciale. Questi DataSet sono un tipo di TabularDataset costituito da file di immagine. Per questi tipi di set di impostazioni è possibile [filtrare le immagini ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) in base ai metadati e ai valori di colonna quali `label` e `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

## <a name="explore-data"></a>Esplorare i dati

Al termine dell'operazione, è possibile [registrare](#register-datasets) il set di dati e caricarlo nel notebook per l'esplorazione dei dati prima di eseguire il training del modello.

Per i filedataset è possibile **montare** o **scaricare** il set di dati e applicare le librerie di Python usate normalmente per l'esplorazione dei dati. [Altre informazioni sul download di Mount vs](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Per TabularDatasets, usare il [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metodo per visualizzare i dati in un frame di dati. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indice|PassengerId|Survived|Pclass|Nome|Sesso|Età|SibSp|Parch|Ticket|Tariffe|Abitacolo|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Vero|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Creare un set di dati da Pandas dataframe

Per creare un TabularDataset da un frame di dati Pandas in memoria, scrivere i dati in un file locale, ad esempio un volume CSV, e creare il set di dati da tale file. Il codice seguente illustra questo flusso di lavoro.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Creare e registrare un TabularDataset da un dataframe di Spark o Pandas in memoria con un unico metodo con metodi di anteprima pubblici, [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) e [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Questi metodi di registrazione sono funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) e possono cambiare in qualsiasi momento. 
> 
>  Questi metodi caricano i dati nell'archiviazione sottostante e, di conseguenza, comportano costi di archiviazione. 

## <a name="register-datasets"></a>Registrare i set di impostazioni

Per completare il processo di creazione, registrare i set di impostazioni con un'area di lavoro. Usare il [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) metodo per registrare i set di elementi con l'area di lavoro per condividerli con altri utenti e riutilizzarli tra gli esperimenti nell'area di lavoro:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Creare set di impostazioni usando Azure Resource Manager

Sono disponibili molti modelli in [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) che possono essere usati per creare set di impostazioni.

Per informazioni sull'uso di questi modelli, vedere [usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Creare set di impostazioni da Azure Open DataSet

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di dati includono dati di pubblico dominio relativi a meteo, censimento, festività, sicurezza pubblica e posizione, che consentono di eseguire il training di modelli di Machine Learning e arricchire le soluzioni predittive. I set di impostazioni aperti si trovano nel cloud in Microsoft Azure e sono inclusi sia nell'SDK che in studio.

Informazioni su come creare [set di impostazioni di Azure Machine Learning da Azure Open DataSets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Eseguire il training con set di dati

Usare i set di impostazioni negli esperimenti di machine learning per il training dei modelli ML. [Vedere altre informazioni su come eseguire il training con i set di dati](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Set di impostazioni di versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. Una versione del set di dati è un modo per aggiungere un segnalibro allo stato dei dati in modo che sia possibile applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Altre informazioni sulle [versioni del set di dati](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).
* USA Machine Learning automatizzato per eseguire il [training con TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Per altri esempi di training del set di dati, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
