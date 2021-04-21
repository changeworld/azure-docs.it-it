---
title: Training di modelli di Machine Learning scikit-learn
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning scalabilità orizzontale di un processo di training scikit-learn usando risorse di calcolo cloud elastiche.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef64d94ed3e860895bcc81a1429008205a1a8acb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817108"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Training di modelli scikit-learn su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training scikit-learn con Azure Machine Learning.

Gli script di esempio in questo articolo vengono usati per classificare immagini di fiori iris per creare un modello di Machine Learning basato sul set di dati [iris di](https://archive.ics.uci.edu/ml/datasets/iris)scikit-learn.

Se si esegue il training di un modello scikit-learn di Machine Learning da zero o si porta un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare orizzontalmente i processi di training open source usando risorse di calcolo cloud elastiche. È possibile compilare, distribuire, controllare la versione e monitorare i modelli a livello di produzione con Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:
 - Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

    - Completare [l'Esercitazione: Configurare l'ambiente](tutorial-1st-experiment-sdk-setup.md)  e l'area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella di training degli esempi nel server notebook trovare un notebook completato ed espanso passando alla cartella **how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn.**

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, definendo l'ambiente di training e preparando lo script di training.

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L Azure Machine Learning area di](concept-workspace.md) lavoro è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli artefatti dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) oggetto .

Creare un oggetto area di lavoro `config.json` dal file creato nella sezione dei [prerequisiti](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparare gli script

In questa esercitazione lo script di training **train_iris.py** è già disponibile [qui.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) In pratica, è possibile usare qualsiasi script di training personalizzato così come è ed eseguirlo con Azure ML senza dover modificare il codice.

Note:
- Lo script di training fornito illustra come registrare alcune metriche nell'esecuzione di Azure ML usando `Run` l'oggetto all'interno dello script.
- Lo script di training fornito usa dati di esempio della  `iris = datasets.load_iris()` funzione .  Per usare e accedere ai propri dati, vedere Come eseguire il training con [i set](how-to-train-with-datasets.md) di dati per rendere disponibili i dati durante il training.

### <a name="define-your-environment"></a>Definire l'ambiente

Per definire l'ambiente di Azure [ML](concept-environments.md) che incapsula le dipendenze dello script di training, è possibile definire un ambiente personalizzato oppure usare e un ambiente curato di Azure ML.

#### <a name="use-a-curated-environment"></a>Usare un ambiente curato
Facoltativamente, Azure ML offre ambienti predefiniti e curati se non si vuole definire il proprio ambiente. Per altre informazioni, vedere [qui](resource-curated-environments.md).
Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato

È anche possibile creare un proprio ambiente personalizzato. Definire le dipendenze conda in un file YAML. In questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Creare un ambiente Azure ML da questa specifica dell'ambiente Conda. L'ambiente verrà inserito in un pacchetto in un contenitore Docker in fase di esecuzione.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Per altre informazioni sulla creazione e sull'uso di ambienti, vedere Creare e usare ambienti [software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare uno ScriptRunConfig
Creare un oggetto ScriptRunConfig per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo.
Tutti gli argomenti dello script di training verranno passati tramite la riga di comando, se specificati nel `arguments` parametro .

Il codice seguente configurerà un oggetto ScriptRunConfig per l'invio del processo per l'esecuzione nel computer locale.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Se si vuole invece eseguire il processo in un cluster remoto, è possibile specificare la destinazione di calcolo desiderata per il `compute_target` parametro di ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Inviare l'esecuzione
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si vuole caricare, usare un [file con](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) estensione ignore o non includerli nella directory di origine. Accedere invece ai dati usando un set di dati [di](how-to-train-with-datasets.md)Azure ML.

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Durante l'esecuzione, l'esecuzione viene eseguita nelle fasi seguenti:

- **Preparazione:** viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece si specifica un ambiente curato, verrà usata l'immagine memorizzata nella cache che contiene l'ambiente curato.

- **Ridimensionamento:** il cluster tenta di aumentare le dimensioni se il cluster batch per intelligenza artificiale richiede più nodi per eseguire l'esecuzione rispetto a quelli attualmente disponibili.

- **In** esecuzione: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene `script` eseguito . Gli output di stdout e **della cartella ./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione:** la **cartella ./outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script di training, train_iris.py, per salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente. Specificando i parametri , e , la distribuzione del modello senza codice `model_framework` `model_framework_version` diventa `resource_configuration` disponibile. La distribuzione di modelli senza codice consente di distribuire direttamente il modello come servizio Web dal modello registrato e l'oggetto definisce la risorsa di calcolo [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) per il servizio Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Distribuzione

Il modello appena registrato può essere distribuito esattamente come qualsiasi altro modello registrato in Azure ML. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente [alla](how-to-deploy-and-where.md#choose-a-compute-target) creazione di una destinazione di calcolo per la distribuzione, poiché è già presente un modello registrato.

### <a name="preview-no-code-model-deployment"></a>(Anteprima) Distribuzione di modelli senza codice

Invece della route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per scikit-learn. La distribuzione di modelli senza codice è supportata per tutti i tipi di modello scikit-learn predefiniti. Registrando il modello come illustrato in precedenza con i parametri , e , è possibile usare semplicemente la `model_framework` `model_framework_version` funzione `resource_configuration` [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statica per distribuire il modello.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTA: queste dipendenze sono incluse nel contenitore di inferenza scikit-learn predefinito.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

La procedura [completa illustra la](how-to-deploy-and-where.md) distribuzione in Azure Machine Learning in modo più approfondito.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato esecuto il training e la registrazione di un modello scikit-learn e sono stati appresi i dettagli sulle opzioni di distribuzione. Vedere questi altri articoli per altre informazioni su Azure Machine Learning.

* [Tracciare le metriche di esecuzione durante il training](how-to-log-view-metrics.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
