---
title: Creare set di dati di Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come creare set Azure Machine Learning per accedere ai dati per le esecuzioni di esperimenti di Machine Learning.
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
ms.openlocfilehash: 18a39adfff572b81e5fbb9d7a42c71834b93ad13
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575751"
---
# <a name="create-azure-machine-learning-datasets"></a>Creare set di dati di Azure Machine Learning

Questo articolo illustra come creare set di Azure Machine Learning per accedere ai dati per gli esperimenti locali o remoti con Azure Machine Learning Python SDK. Per comprendere la posizione dei set di Azure Machine Learning nel flusso di lavoro di accesso ai dati complessivo, vedere l'articolo Accedere [in modo](concept-data.md#data-workflow) sicuro ai dati.

Creando un set di dati, si crea un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i dati rimangono nella posizione esistente, non si incorre in costi di archiviazione aggiuntivi e non si rischia l'integrità delle origini dati. Inoltre, i set di dati vengono valutati in modo lazidato, che consente di velocizzare le prestazioni del flusso di lavoro. È possibile creare set di dati da archivi dati, URL pubblici [e set di dati aperti di Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Per un'esperienza a basso livello di codice, [creare Azure Machine Learning set di dati con il studio di Azure Machine Learning.](how-to-connect-data-ui.md#create-datasets)

Con Azure Machine Learning set di dati, è possibile:

* Mantenere una singola copia dei dati nella memoria, a cui fanno riferimento i set di dati.

* Accedere facilmente ai dati durante il training del modello senza doversi preoccupare delle stringhe di connessione o dei percorsi dei dati. [Vedere altre informazioni su come eseguire il training con i set di dati](how-to-train-with-datasets.md).

* Condividere i dati e collaborare con altri utenti.

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di dati, è necessario:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Un'Azure Machine Learning di lavoro.](how-to-manage-workspace.md)

* È [Azure Machine Learning SDK per Python installato,](/python/api/overview/azure/ml/install)che include il pacchetto azureml-datasets.

    * Creare [un Azure Machine Learning di calcolo](how-to-create-manage-compute-instance.md)di , che è un ambiente di sviluppo completamente configurato e gestito che include notebook integrati e l'SDK già installato.

    **OR**

    * Usare jupyter notebook e installare manualmente l'SDK con [queste istruzioni.](/python/api/overview/azure/ml/install)

> [!NOTE]
> Alcune classi di set di dati hanno dipendenze dal pacchetto [azureml-dataprep,](https://pypi.org/project/azureml-dataprep/) compatibile solo con Python a 64 bit. Per gli utenti Linux, queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) e CentOS (7). Se si usano le distribuzione non [](/dotnet/core/install/linux) supportate, seguire questa guida per installare .NET Core 2.1 per continuare. 

## <a name="compute-size-guidance"></a>Linee guida sulle dimensioni di calcolo

Quando si crea un set di dati, esaminare la potenza di elaborazione del calcolo e le dimensioni dei dati in memoria. Le dimensioni dei dati nell'archiviazione non sono le stesse dei dati in un frame di dati. Ad esempio, i dati nei file CSV possono espandersi fino a 10x in un frame di dati, quindi un file CSV da 1 GB può diventare 10 GB in un frame di dati. 

Se i dati vengono compressi, possono espandersi ulteriormente. 20 GB di dati relativamente di tipo sparse archiviati in formato parquet compresso possono espandersi fino a ~800 GB in memoria. Poiché i file Parquet archiviano i dati in un formato a colonne, se è necessaria solo metà delle colonne, è sufficiente caricare ~400 GB in memoria.

[Altre informazioni sull'ottimizzazione dell'elaborazione dei dati in Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Tipi di set di dati

Esistono due tipi di set di dati, in base al modo in cui gli utenti li utilizzano nel training; FileDataset e TabularDataset. Entrambi i tipi possono essere usati in Azure Machine Learning di training che coinvolgono, stimatori, AutoML, hyperDrive e pipeline. 

### <a name="filedataset"></a>FileDataset

Un [oggetto FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Se i dati sono già puliti e pronti per l'uso negli esperimenti di training, è possibile scaricare o montare i file nel calcolo come oggetto FileDataset. [](how-to-train-with-datasets.md#mount-vs-download) 

È consigliabile usare FileDataset per i flussi di lavoro di Machine Learning, poiché i file di origine possono essere in qualsiasi formato, in modo da consentire una gamma più ampia di scenari di Machine Learning, incluso il Deep Learning.

Creare un oggetto FileDataset con [Python SDK](#create-a-filedataset) o [studio di Azure Machine Learning](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

Un [oggetto TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. In questo modo è possibile materializzare i dati in un dataframe Pandas o Spark in modo da poter usare librerie familiari di preparazione e training senza dover uscire dal notebook. È possibile creare un oggetto da `TabularDataset` file csv, tsv, [parquet,](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) [jsonl](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)e dai risultati [della query SQL.](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)

Con i set di dati tabulari è possibile specificare un timestamp da una colonna nei dati o da qualsiasi posizione in cui sono archiviati i dati del modello di percorso per abilitare un tratto di serie temporale. Questa specifica consente di filtrare in modo semplice ed efficiente in base al tempo. Per un esempio, vedere Demo dell'API relativa alla serie temporale [tabulare con dati meteo NOAA.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)

Creare un oggetto TabularDataset [con Python SDK o](#create-a-tabulardataset) [studio di Azure Machine Learning](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [I flussi di lavoro automatizzati](concept-automated-ml.md) di Machine Learning generati studio di Azure Machine Learning attualmente supportano solo i set di dati tabulari. 

## <a name="access-datasets-in-a-virtual-network"></a>Accedere ai set di dati in una rete virtuale

Se l'area di lavoro si trova in una rete virtuale, è necessario configurare il set di dati per ignorare la convalida. Per altre informazioni su come usare archivi dati e set di dati in una rete virtuale, vedere Proteggere un'area di [lavoro e le risorse associate.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Creare set di dati da archivi dati

Perché i dati siano accessibili da Azure Machine Learning, è necessario creare set di dati da percorsi [in](how-to-access-data.md) Azure Machine Learning o URL Web. 

> [!TIP] 
> È possibile creare set di dati direttamente dagli URL di archiviazione con accesso ai dati basato sull'identità. Per altre informazioni, [vedere Connettersi all'archiviazione con l'accesso ai dati basato sull'identità (anteprima)](how-to-identity-based-data-access.md)<br><br>
Questa funzionalità è una funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) e può cambiare in qualsiasi momento. 

 
Per creare set di dati da un archivio dati con Python SDK:

1. Verificare di avere o `contributor` `owner` l'accesso al servizio di archiviazione sottostante dell'archivio Azure Machine Learning dati. [Controllare le autorizzazioni dell'account di archiviazione nella portale di Azure](../role-based-access-control/check-access.md).

1. Creare il set di dati facendo riferimento ai percorsi nell'archivio dati. È possibile creare un set di dati da più percorsi in più archivi dati. Non esiste alcun limite rigido al numero di file o alle dimensioni dei dati da cui è possibile creare un set di dati. 

> [!NOTE]
> Per ogni percorso dati, al servizio di archiviazione verranno inviate alcune richieste per verificare se punta a un file o a una cartella. Questo sovraccarico può causare un peggioramento delle prestazioni o un errore. Un set di dati che fa riferimento a una cartella con 1000 file all'interno viene considerato come riferimento a un percorso dati. È consigliabile creare un set di dati che faccia riferimento a meno di 100 percorsi negli archivi dati per ottenere prestazioni ottimali.

### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Usare il [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) metodo nella classe per caricare i file in qualsiasi formato e per creare un oggetto `FileDatasetFactory` FileDataset non registrato. 

Se l'archiviazione si trova dietro una rete virtuale o un firewall, impostare il parametro `validate=False` nel `from_files()` metodo . In questo modo viene ignorato il passaggio di convalida iniziale e viene garantito che sia possibile creare il set di dati da questi file sicuri. Altre informazioni su come usare [gli archivi dati e i set di dati in una rete virtuale.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Per riutilizzare e condividere i set di dati nell'esperimento nell'area di lavoro, [registrare il set di dati](#register-datasets). 

> [!TIP] 
> Caricare file da una directory locale e creare un oggetto FileDataset in un singolo metodo con il metodo di anteprima [pubblico, upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Questo metodo è una [funzionalità di](/python/api/overview/azure/ml/#stable-vs-experimental) anteprima sperimentale e può cambiare in qualsiasi momento. 
> 
>  Questo metodo carica i dati nell'archiviazione sottostante e di conseguenza comporta costi di archiviazione. 

### <a name="create-a-tabulardataset"></a>Creare un oggetto TabularDataset

Usare il metodo nella classe per leggere i file in formato csv o tsv e per creare [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` un oggetto TabularDataset non registrato. Per leggere i file dal formato parquet, usare il [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) metodo . Se si sta leggendo da più file, i risultati verranno aggregati in un'unica rappresentazione tabulare. 

Per informazioni sui formati di file supportati, nonché sulla sintassi e sui modelli di progettazione, vedere la documentazione di riferimento di [TabularDatasetFactory.](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) 

Se l'archiviazione si trova dietro una rete virtuale o un firewall, impostare il parametro `validate=False` nel `from_delimited_files()` metodo . In questo modo viene ignorato il passaggio di convalida iniziale e viene garantito che sia possibile creare il set di dati da questi file sicuri. Altre informazioni su come usare [gli archivi dati e i set di dati in una rete virtuale.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

Il codice seguente ottiene l'area di lavoro esistente e l'archivio dati desiderato in base al nome. Quindi passa l'archivio dati e i percorsi dei file al `path` parametro per creare un nuovo tabularDataset, `weather_ds` .

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
### <a name="set-data-schema"></a>Impostare lo schema dei dati

Per impostazione predefinita, quando si crea un oggetto TabularDataset, i tipi di dati delle colonne vengono dedotto automaticamente. Se i tipi derivati non corrispondono alle aspettative, è possibile aggiornare lo schema del set di dati specificando i tipi di colonna con il codice seguente. Il parametro `infer_column_type` è applicabile solo per i set di dati creati da file delimitati. [Altre informazioni sui tipi di dati supportati.](/python/api/azureml-core/azureml.data.dataset_factory.datatype)


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Indice)|PassengerId|Survived|Pclass|Nome|Sesso|Età|SibSp|Parch|Ticket|Tariffe|Cabina|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Vero|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

Per riutilizzare e condividere i set di dati tra esperimenti nell'area di lavoro, [registrare il set di dati](#register-datasets).

## <a name="wrangle-data"></a>Dati Wrangle
Dopo aver creato e [registrato il](#register-datasets) set di dati, è possibile caricarlo nel notebook per [data wrangling'esplorazione](#explore-data) prima del training del modello. 

Se non è necessario eseguire alcuna data wrangling o esplorazione, vedere come usare i set di dati negli script di training per l'invio di esperimenti di Machine Learning in Eseguire il training con [i set di dati](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Filtrare i set di dati (anteprima)

Le funzionalità di filtro dipendono dal tipo di set di dati disponibili. 
> [!IMPORTANT]
> Il filtro dei set di dati con il metodo di anteprima pubblico è una funzionalità di anteprima sperimentale e [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) può cambiare in qualsiasi momento. [](/python/api/overview/azure/ml/#stable-vs-experimental) 
> 
**Per TabularDataset è** possibile mantenere o rimuovere colonne con i [metodi keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) [e drop_columns().](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-)

Per filtrare le righe in base a un valore di colonna specifico in un oggetto TabularDataset, usare il [metodo filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (anteprima). 

Negli esempi seguenti viene restituito un set di dati non registrato in base alle espressioni specificate.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**In FileDataset ogni** riga corrisponde a un percorso di un file, pertanto il filtro in base al valore della colonna non è utile. È tuttavia possibile [filtrare le righe in](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) base a metadati come CreationTime, Size e così via.

Negli esempi seguenti viene restituito un set di dati non registrato in base alle espressioni specificate.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**I set di dati con etichetta** creati dai progetti di [etichettatura dei dati](how-to-create-labeling-projects.md) sono un caso speciale. Questi set di dati sono un tipo di tabulareDataset costituito da file di immagine. Per questi tipi di set di dati, è possibile [filtrare() le immagini in](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) base ai metadati e in base ai valori di colonna come `label` e `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

## <a name="explore-data"></a>Esplorare i dati

Dopo aver eseguito la gestione dei dati, è possibile registrare il set di dati e caricarlo nel notebook per l'esplorazione dei dati prima del training del modello. [](#register-datasets)

Per i set di dati  FileDataset, è possibile montare o scaricare il set di dati e applicare le librerie Python usate normalmente per l'esplorazione dei dati.  [Altre informazioni sul montaggio e sul download.](how-to-train-with-datasets.md#mount-vs-download)

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Per i set di dati tabulari, usare il [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metodo per visualizzare i dati in un frame di dati. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Indice)|PassengerId|Survived|Pclass|Nome|Sesso|Età|SibSp|Parch|Ticket|Tariffe|Cabina|Intrapreso
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Vero|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Creare un set di dati da un dataframe pandas

Per creare un oggetto TabularDataset da un dataframe pandas in memoria, scrivere i dati in un file locale, ad esempio un file CSV, e creare il set di dati da tale file. Il codice seguente illustra questo flusso di lavoro.

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
> Creare e registrare un oggetto TabularDataset da un frame di dati spark o pandas in memoria con un singolo metodo con metodi di anteprima [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) pubblici e [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Questi metodi di registrazione sono [funzionalità sperimentali](/python/api/overview/azure/ml/#stable-vs-experimental) di anteprima e possono cambiare in qualsiasi momento. 
> 
>  Questi metodi caricano i dati nell'archiviazione sottostante e di conseguenza comportano costi di archiviazione. 

## <a name="register-datasets"></a>Registrare i set di dati

Per completare il processo di creazione, registrare i set di dati in un'area di lavoro. Usare il metodo per registrare i set di dati nell'area di lavoro per condividerli con altri utenti e riutilizzarli [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) tra esperimenti nell'area di lavoro:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Creare set di dati usando Azure Resource Manager

Esistono molti modelli in [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) che possono essere usati per creare set di dati.

Per informazioni sull'uso di questi modelli, vedere [Usare un modello Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Creare set di dati da set di dati aperti di Azure

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di dati includono dati di pubblico dominio relativi a meteo, censimento, festività, sicurezza pubblica e posizione, che consentono di eseguire il training di modelli di Machine Learning e arricchire le soluzioni predittive. I set di dati aperti sono nel cloud Microsoft Azure e sono inclusi sia nell'SDK che in Studio.

Informazioni su come creare Azure Machine Learning [set di dati da set di dati aperti di Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Eseguire il training con set di dati

Usare i set di dati negli esperimenti di Machine Learning per il training dei modelli di Machine Learning. [Vedere altre informazioni su come eseguire il training con i set di dati](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Set di dati della versione

È possibile registrare un nuovo set di dati con lo stesso nome creando una nuova versione. Una versione del set di dati è un modo per aggiungere un segnalibro allo stato dei dati in modo che sia possibile applicare una versione specifica del set di dati per la sperimentazione o la riproduzione futura. Altre informazioni sulle versioni [del set di dati.](how-to-version-track-datasets.md)
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

* Informazioni [su come eseguire il training con i set di dati](how-to-train-with-datasets.md).
* Usare Machine Learning automatizzato per eseguire [il training con TabularDatasets.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* Per altri esempi di training del set di dati, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
