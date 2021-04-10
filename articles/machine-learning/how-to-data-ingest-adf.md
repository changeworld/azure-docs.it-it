---
title: Inserimento dati con Azure Data Factory
titleSuffix: Azure Machine Learning
description: Informazioni sulle opzioni disponibili per la creazione di una pipeline di inserimento dati con Azure Data Factory e i vantaggi di ognuno di essi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98796153"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Inserimento dati con Azure Data Factory

In questo articolo vengono fornite informazioni sulle opzioni disponibili per la creazione di una pipeline di inserimento dati con [Azure Data Factory](../data-factory/introduction.md). Questa pipeline Azure Data Factory viene usata per inserire i dati da usare con [Azure Machine Learning](overview-what-is-azure-ml.md). Data Factory consente di estrarre, trasformare e caricare facilmente dati (ETL). Una volta che i dati sono stati trasformati e caricati nella risorsa di archiviazione, possono essere usati per eseguire il training dei modelli di Machine Learning in Azure Machine Learning.

Una semplice trasformazione dei dati può essere gestita con attività Data Factory native e strumenti come il [flusso di dati](../data-factory/control-flow-execute-data-flow-activity.md). Quando si tratta di scenari più complicati, i dati possono essere elaborati con codice personalizzato. Ad esempio, Python o codice R.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Confrontare Azure Data Factory pipeline di inserimento dati 
Sono disponibili diverse tecniche comuni di utilizzo di Data Factory per trasformare i dati durante l'inserimento. Ogni tecnica presenta vantaggi e svantaggi che consentono di determinare se è una scelta ottimale per un caso d'uso specifico:

| Tecnica | Vantaggi | Svantaggi |
| ----- | ----- | ----- |
| Data Factory e funzioni di Azure | <li> Bassa latenza, calcolo senza server<li>Funzioni con stato<li>Funzioni riutilizzabili | Valido solo per l'elaborazione a breve esecuzione |
| Data Factory + componente personalizzato | <li>Elaborazione parallela su larga scala<li>Adatto per algoritmi pesanti | <li>Richiede il wrapping del codice in un eseguibile<li>Complessità della gestione delle dipendenze e di IO |
| Data Factory + notebook Azure Databricks |<li> Apache Spark<li>Ambiente Python nativo |<li>Può essere costoso<li>La creazione di cluster inizialmente richiede tempo e aumenta la latenza

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory con funzioni di Azure

Funzioni di Azure consente di eseguire piccole parti di codice (funzioni) senza doversi preoccupare dell'infrastruttura dell'applicazione. In questa opzione i dati vengono elaborati con codice Python personalizzato racchiuso in una funzione di Azure. 

La funzione viene richiamata con l' [attività Azure Data Factory funzione di Azure](../data-factory/control-flow-azure-function-activity.md). Questo approccio è un'opzione efficace per le trasformazioni di dati lightweight. 

![Il diagramma mostra una pipeline di Azure Data Factory, con funzioni di Azure e una pipeline di elaborazione e una pipeline Azure Machine Learning, con Train Model e il modo in cui interagiscono con dati non elaborati e dati preparati.](media/how-to-data-ingest-adf/adf-function.png)



* Vantaggi:
    * I dati vengono elaborati in un calcolo senza server con latenza relativamente bassa
    * Data Factory pipeline può richiamare una [funzione di Azure durevole](../azure-functions/durable/durable-functions-overview.md) che può implementare un flusso di trasformazione dei dati sofisticato 
    * I dettagli della trasformazione dei dati sono estratti dalla funzione di Azure che può essere riutilizzata e richiamata da altre posizioni
* Svantaggi
    * È necessario creare le funzioni di Azure prima di usare con ADF
    * Funzioni di Azure è valido solo per l'elaborazione dei dati con esecuzione breve

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory con attività del componente personalizzato

In questa opzione i dati vengono elaborati con codice Python personalizzato racchiuso in un eseguibile. Viene richiamato con un' [ attività Azure Data Factory componente personalizzato](../data-factory/transform-data-using-dotnet-custom-activity.md). Questo approccio è più adatto per i dati di grandi dimensioni rispetto alla tecnica precedente.

![Il diagramma mostra una pipeline di Azure Data Factory, con un componente personalizzato ed esegue la pipeline M L, una pipeline Azure Machine Learning, con Train Model e il modo in cui interagiscono con i dati non elaborati e i dati preparati.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Vantaggi:
    * I dati vengono elaborati nel pool di [Azure batch](../batch/batch-technical-overview.md) , che fornisce un calcolo parallelo e ad alte prestazioni su larga scala
    * Può essere usato per eseguire algoritmi pesanti ed elaborare quantità significative di dati
* Svantaggi:
    * È necessario creare il pool di Azure Batch prima di usarlo con Data Factory
    * Rispetto alla progettazione correlata al wrapping del codice Python in un eseguibile. Complessità della gestione delle dipendenze e dei parametri di input/output

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory con Azure Databricks notebook Python

[Azure Databricks](https://azure.microsoft.com/services/databricks/) è una piattaforma di analisi basata su Apache Spark nel cloud Microsoft.

In questa tecnica, la trasformazione dei dati viene eseguita da un [notebook di Python](../data-factory/transform-data-using-databricks-notebook.md), in esecuzione in un cluster Azure Databricks. Questo è probabilmente l'approccio più comune che sfrutta tutta la potenza di un servizio Azure Databricks. È progettato per l'elaborazione di dati distribuiti su larga scala.

![Il diagramma mostra una pipeline di Azure Data Factory, con Azure Databricks Python e l'esecuzione di una pipeline M L, una pipeline Azure Machine Learning, con Train Model e il modo in cui interagiscono con dati non elaborati e dati preparati.](media/how-to-data-ingest-adf/adf-databricks.png)

* Vantaggi:
    * I dati vengono trasformati nel servizio di Azure di elaborazione dati più potente, supportato da Apache Spark ambiente
    * Supporto nativo di Python insieme a data science Framework e librerie, tra cui TensorFlow, PyTorch e Scikit-learn
    * Non è necessario eseguire il wrapping del codice Python in funzioni o moduli eseguibili. Il codice funziona così com'è.
* Svantaggi:
    * È necessario creare Azure Databricks infrastruttura prima di usarla con Data Factory
    * Può essere costoso a seconda della configurazione di Azure Databricks
    * La rotazione dei cluster di calcolo dalla modalità "a freddo" richiede tempo che offre una latenza elevata alla soluzione 
    

## <a name="consume-data-in-azure-machine-learning"></a>Utilizzare i dati in Azure Machine Learning 

La pipeline Data Factory Salva i dati preparati nella memoria cloud, ad esempio BLOB di Azure o Azure datalake. <br>
Utilizzare i dati preparati in Azure Machine Learning da 

* Richiamo di una pipeline di Azure Machine Learning dalla pipeline di Data Factory.<br>**OR**
* Creazione di un [archivio dati Azure Machine Learning](how-to-access-data.md#create-and-register-datastores) e [Azure Machine Learning set di dati](how-to-create-register-datasets.md) da utilizzare in un secondo momento.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Richiama Azure Machine Learning pipeline da Data Factory

Questo metodo è consigliato per i [flussi di lavoro delle operazioni di Machine Learning (MLOps)](concept-model-management-and-deployment.md#what-is-mlops). Se non si vuole configurare una pipeline di Azure Machine Learning, vedere [leggere i dati direttamente dall'archivio](#read-data-directly-from-storage).

Ogni volta che viene eseguita la pipeline Data Factory, 

1. I dati vengono salvati in un percorso diverso nell'archivio. 
1. Per passare il percorso alla Azure Machine Learning, la pipeline Data Factory chiama una [pipeline di Azure Machine Learning](concept-ml-pipelines.md). Quando si chiama la pipeline ML, il percorso dei dati e l'ID esecuzione vengono inviati come parametri. 
1. La pipeline ML può quindi creare un archivio dati Azure Machine Learning e un set di dati con il percorso dei dati. Per altre informazioni, vedere [eseguire pipeline Azure Machine Learning in data factory](../data-factory/transform-data-machine-learning-service.md).

![Il diagramma mostra una pipeline di Azure Data Factory e una pipeline Azure Machine Learning e il modo in cui interagiscono con i dati non elaborati e i dati preparati. La pipeline Data Factory invia i dati al database dei dati preparato, che inserisce un archivio dati, che consente di eseguire il feed dei set di dati nell'area di lavoro Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> I set di dati [supportano il controllo delle versioni](./how-to-version-track-datasets.md), quindi la pipeline ml può registrare una nuova versione del set di dati che punta ai dati più recenti della pipeline di ADF.

Una volta che i dati sono accessibili tramite un archivio dati o un set di dati, è possibile usarli per eseguire il training di un modello ML. Il processo di training potrebbe far parte della stessa pipeline di ML che viene chiamata da ADF. Oppure potrebbe trattarsi di un processo separato, ad esempio la sperimentazione in un notebook di Jupyter.

Poiché i set di dati supportano il controllo delle versioni e ogni esecuzione dalla pipeline crea una nuova versione, è facile capire quale versione dei dati è stata usata per eseguire il training di un modello.

### <a name="read-data-directly-from-storage"></a>Leggere i dati direttamente dalla risorsa di archiviazione

Se non si vuole creare una pipeline di Machine Learning, è possibile accedere ai dati direttamente dall'account di archiviazione in cui vengono salvati i dati preparati con un archivio dati Azure Machine Learning e un set di dati. 

Il codice Python seguente illustra come creare un archivio dati che si connette all'archiviazione di Azure datalake Generation 2. [Altre informazioni sugli archivi dati e sulla posizione in cui trovare le autorizzazioni dell'entità servizio](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Successivamente, creare un set di dati per fare riferimento ai file che si vuole usare nell'attività di machine learning. 

Il codice seguente crea un TabularDataset da un file CSV, `prepared-data.csv` . Altre informazioni sui [tipi di set di dati e i formati di file accettati](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Da qui, usare `prepared_dataset` per fare riferimento ai dati preparati, come negli script di training. Informazioni su come eseguire il [training dei modelli con i set di impostazioni in Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire un notebook di databricks in Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Accedere ai dati nei servizi di archiviazione di Azure](./how-to-access-data.md#create-and-register-datastores)
* Eseguire il [training dei modelli con i set di impostazioni in Azure Machine Learning](./how-to-train-with-datasets.md).
* [DevOps per una pipeline di inserimento dati](./how-to-cicd-data-ingestion.md)