---
title: Training con set di dati di Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come rendere i dati disponibili per il calcolo locale o remoto per il training del modello Azure Machine Learning set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: edb7ebc94d2706d1bf20db8ed9a869107163ff8d
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387990"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Training di modelli con Azure Machine Learning set di dati 

Questo articolo illustra come usare i set di [Azure Machine Learning per](/python/api/azureml-core/azureml.core.dataset%28class%29) eseguire il training dei modelli di Machine Learning.  È possibile usare i set di dati nella destinazione di calcolo locale o remota senza preoccuparsi delle stringhe di connessione o dei percorsi dati. 

Azure Machine Learning set di dati offrono una facile integrazione con Azure Machine Learning di training, ad [esempio ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive)e Azure Machine Learning [pipeline.](./how-to-create-machine-learning-pipelines.md)

Se non si è pronti a rendere disponibili i dati per il training del modello, ma si vuole caricare i dati nel notebook per l'esplorazione dei dati, vedere come esplorare i dati [nel set di dati.](how-to-create-register-datasets.md#explore-data) 

## <a name="prerequisites"></a>Prerequisiti

Per creare ed eseguire il training con i set di dati, è necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Un'Azure Machine Learning di lavoro .](how-to-manage-workspace.md)

* [L Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/install) installato (>= 1.13.0), che include il `azureml-datasets` pacchetto.

> [!Note]
> Alcune classi Dataset hanno dipendenze dal [pacchetto azureml-dataprep.](https://pypi.org/project/azureml-dataprep/) Per gli utenti Linux, queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Usare set di dati negli script di training di Machine Learning

Se i dati strutturati non sono ancora registrati come set di dati, creare un oggetto TabularDataset e usarlo direttamente nello script di training per l'esperimento locale o remoto.

In questo esempio viene creato un oggetto [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) non registrato e viene specificato come argomento script nell'oggetto [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) per il training. Se si vuole riutilizzare questo tabularDataset con altri esperimenti nell'area di lavoro, vedere come registrare i set di [dati nell'area di lavoro.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Creare un oggetto TabularDataset

Il codice seguente crea un oggetto TabularDataset non registrato da un URL Web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Gli oggetti TabularDataset consentono di caricare i dati nell'oggetto TabularDataset in un dataframe Pandas o Spark in modo da poter usare librerie familiari di preparazione e training dei dati senza dover uscire dal notebook.

### <a name="access-dataset-in-training-script"></a>Accedere al set di dati nello script di training

Il codice seguente configura un argomento script che verrà specificato quando si configura `--input-data` l'esecuzione del training (vedere la sezione successiva). Quando il set di dati tabulare viene passato come valore dell'argomento, Azure ML lo risolve nell'ID del set di dati, che è quindi possibile usare per accedere al set di dati nello script di training senza dover hardcoded il nome o l'ID del set di dati nello script. Usa quindi il metodo per caricare il set di dati in un frame di dati Pandas per un'ulteriore esplorazione e preparazione dei dati [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) prima del training.

> [!Note]
> Se l'origine dati originale contiene NaN, stringhe vuote o valori vuoti, quando si usa , tali valori `to_pandas_dataframe()` vengono sostituiti come *valore Null.*

Se è necessario caricare i dati preparati in un nuovo set di dati da un frame di dati Pandas in memoria, scrivere i dati in un file locale, ad esempio un parquet, e creare un nuovo set di dati da tale file. Altre informazioni su [come creare set di dati](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Configurare l'esecuzione del training

Un [oggetto ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) viene usato per configurare e inviare l'esecuzione del training.

Questo codice crea un oggetto ScriptRunConfig, `src` , che specifica

* Directory di script per gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione.
* Script di training, *train_titanic.py*.
* Set di dati di input per il `titanic_ds` training, , come argomento di script. Azure ML risolverà questo problema nell'ID corrispondente del set di dati quando viene passato allo script.
* Destinazione di calcolo per l'esecuzione.
* Ambiente per l'esecuzione.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Montare i file in destinazioni di calcolo remote

Se si dispone di dati non strutturati, creare un [oggetto FileDataset](/python/api/azureml-core/azureml.data.filedataset) e montare o scaricare i file di dati per renderli disponibili alla destinazione di calcolo remota per il training. Informazioni su quando usare il [montaggio e il download](#mount-vs-download) per gli esperimenti di training remoto. 

Nell'esempio seguente, 

* Crea un oggetto FileDataset di input, `mnist_ds` , per i dati di training.
* Specifica dove scrivere i risultati del training e per alzare di livello tali risultati come FileDataset.
* Monta il set di dati di input nella destinazione di calcolo.

> [!Note]
> Se si usa un'immagine di base Docker personalizzata, è necessario installare fuse tramite come dipendenza per il funzionamento del montaggio del set `apt-get install -y fuse` di dati. Informazioni su come [compilare un'immagine di compilazione personalizzata.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

Per l'esempio di notebook, vedere [Come configurare un'esecuzione di training con input e output dei dati.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)

### <a name="create-a-filedataset"></a>Creare un oggetto FileDataset

Nell'esempio seguente viene creato un oggetto FileDataset non registrato dagli `mnist_data` URL Web. Questo FileDataset è i dati di input per l'esecuzione del training.

Altre informazioni su [come creare set di dati](how-to-create-register-datasets.md) da altre origini.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>Dove scrivere l'output di training

È possibile specificare dove scrivere i risultati del training con un [oggetto OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). 

Gli oggetti OutputFileDatasetConfig consentono di: 

* Montare o caricare l'output di un'esecuzione nell'archiviazione cloud specificata.
* Salvare l'output come FileDataset nei tipi di archiviazione supportati seguenti:
    * BLOB di Azure
    * Condivisione file di Azure
    * Azure Data Lake Storage generazioni 1 e 2
* Tenere traccia della derivazione dei dati tra le esecuzioni di training.

Il codice seguente specifica che i risultati del training devono essere salvati come FileDataset nella cartella `outputdataset` nell'archivio dati BLOB predefinito, `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Configurare l'esecuzione del training

È consigliabile passare il set di dati come argomento durante il montaggio tramite `arguments` il parametro del `ScriptRunConfig` costruttore. In questo modo, si ottiene il percorso dei dati (punto di montaggio) nello script di training tramite argomenti. In questo modo, è possibile usare lo stesso script di training per il debug locale e il training remoto in qualsiasi piattaforma cloud.

Nell'esempio seguente viene creato un oggetto ScriptRunConfig che passa fileDataset tramite `arguments` . Dopo aver inviato l'esecuzione, i file di dati a cui fa riferimento il set di dati vengono montati nella destinazione di calcolo e i risultati del training vengono salvati nella cartella specificata `mnist_ds` `outputdataset` nell'archivio dati predefinito.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Script di training semplice

Lo script seguente viene inviato tramite ScriptRunConfig. Legge il set di dati come input e `mnist_ds ` scrive il file nella cartella `outputdataset` nell'archivio dati BLOB predefinito, `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Montaggio e download

Il montaggio o il download di file di qualsiasi formato sono supportati per i set di dati creati da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e Database di Azure per PostgreSQL. 

Quando si **monta un** set di dati, si collegano i file a cui fa riferimento il set di dati a una directory (punto di montaggio) e lo si rende disponibile nella destinazione di calcolo. Il montaggio è supportato per le risorse di calcolo basate su Linux, Azure Machine Learning calcolo, macchine virtuali e HDInsight. 

Quando si **scarica un** set di dati, tutti i file a cui fa riferimento il set di dati verranno scaricati nella destinazione di calcolo. Il download è supportato per tutti i tipi di calcolo. 

Se lo script elabora tutti i file a cui fa riferimento il set di dati e il disco di calcolo può adattarsi al set di dati completo, è consigliabile scaricare per evitare il sovraccarico dei dati di streaming dai servizi di archiviazione. Se le dimensioni dei dati superano le dimensioni del disco di calcolo, il download non è possibile. Per questo scenario, è consigliabile eseguire il montaggio perché al momento dell'elaborazione vengono caricati solo i file di dati usati dallo script.

Il codice seguente viene `dataset` montato nella directory temp all'indirizzo `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Ottenere set di dati negli script di Machine Learning

I set di dati registrati sono accessibili sia in locale che in remoto nei cluster di calcolo, ad esempio Azure Machine Learning calcolo. Per accedere al set di dati registrato tra esperimenti, usare il codice seguente per accedere all'area di lavoro e ottenere il set di dati usato nell'esecuzione inviata in precedenza. Per impostazione predefinita, il metodo nella classe restituisce la versione più recente del set di dati [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) `Dataset` registrato nell'area di lavoro.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Accedere al codice sorgente durante il training

Archiviazione BLOB di Azure offre velocità effettive più elevate rispetto a una condivisione file di Azure e la scalabilità per avviare in parallelo un numero elevato di processi. Per questo motivo, è consigliabile configurare le esecuzioni in modo da usare l'archiviazione BLOB per il trasferimento di file di codice sorgente.

L'esempio di codice seguente specifica nella configurazione di esecuzione quale archivio dati BLOB usare per i trasferimenti del codice sorgente.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Esempi di notebook

+ Per altri esempi e concetti relativi ai set di dati, vedere i [notebook del set di dati](https://aka.ms/dataset-tutorial).
+ Vedere come [parametrizzare i set di dati nelle pipeline di Machine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)Learning.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Inizializzazione del set di dati** non riuscita: si è timeout l'attesa del punto di montaggio: 
  * Se non si dispone [](../virtual-network/network-security-groups-overview.md) di regole del gruppo di sicurezza di rete in uscita e si usa , aggiornare e le relative dipendenze in modo che siano la più recente per la versione secondaria specifica oppure, se si usa in un'esecuzione, ricreare l'ambiente in modo che possa avere la patch più recente con la `azureml-sdk>=1.12.0` `azureml-dataset-runtime` correzione. 
  * Se si usa `azureml-sdk<1.12.0` , eseguire l'aggiornamento alla versione più recente.
  * Se si dispone di regole del gruppo di sicurezza di rete in uscita, assicurarsi che sia presente una regola in uscita che consente tutto il traffico per il tag del servizio `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Archiviazione di AzureFile in overload

Se viene visualizzato un `Unable to upload project files to working directory in AzureFile because the storage is overloaded` errore, applicare le soluzioni alternative seguenti.

Se si usa la condivisione file per altri carichi di lavoro, ad esempio il trasferimento dei dati, è consigliabile usare i BLOB in modo che la condivisione file sia gratuita per l'invio di esecuzioni. È anche possibile dividere il carico di lavoro tra due aree di lavoro diverse.

### <a name="passing-data-as-input"></a>Passaggio di dati come input

*  **TypeError: FileNotFound:** nessun file o directory di questo tipo: questo errore si verifica se il percorso del file specificato non è il percorso in cui si trova il file. È necessario assicurarsi che il modo in cui si fa riferimento al file sia coerente con la posizione in cui è stato montato il set di dati nella destinazione di calcolo. Per garantire uno stato deterministico, è consigliabile usare il percorso astratto quando si monta un set di dati in una destinazione di calcolo. Nel codice seguente, ad esempio, il set di dati viene montato nella radice del file system della destinazione di calcolo, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se non si include la barra iniziale ,'/', è necessario antesare la directory di lavoro, ad esempio nella destinazione di calcolo, per indicare dove si vuole montare il set `/mnt/batch/.../tmp/dataset` di dati.


## <a name="next-steps"></a>Passaggi successivi

* [Training automatico di modelli di Machine Learning](how-to-auto-train-remote.md) con TabularDataset.

* [Training dei modelli di classificazione delle](https://aka.ms/filedataset-samplenotebook) immagini con FileDataset.

* [Eseguire il training con i set di dati usando le pipeline](./how-to-create-machine-learning-pipelines.md).
