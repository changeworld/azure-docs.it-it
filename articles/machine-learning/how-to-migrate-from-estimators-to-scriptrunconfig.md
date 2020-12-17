---
title: Eseguire la migrazione da estimatori a ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Guida alla migrazione per la migrazione da estimatori a ScriptRunConfig per la configurazione dei processi di training.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 64c03b1c9fc18a4e78af9914b893599683069ced
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97633014"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrazione da estimatori a ScriptRunConfig

Fino ad ora, sono disponibili diversi metodi per la configurazione di un processo di training in Azure Machine Learning tramite l'SDK, inclusi gli estimatori, ScriptRunConfig e RunConfiguration di livello inferiore.   Per risolvere questa ambiguità e incoerenza, viene semplificato il processo di configurazione dei processi in Azure ML.  È ora possibile usare ScriptRunConfig come opzione consigliata per la configurazione dei processi di training. 

Gli estimatori sono deprecati con la versione 1.19.0 di Python SDK. In genere, è consigliabile evitare di creare in modo esplicito un'istanza di un oggetto RunConfiguration e configurare il processo usando la classe ScriptRunConfig.

Questo articolo illustra le considerazioni più comuni per la migrazione da estimatori a ScriptRunConfig.

> [!IMPORTANT]
> Per eseguire la migrazione a ScriptRunConfig da estimatori, assicurarsi di usare >= 1.15.0 di Python SDK.

## <a name="scriptrunconfig-documentation-and-samples"></a>Documentazione ed esempi di ScriptRunConfig
Azure Machine Learning documentazione ed esempi sono stati aggiornati per l'uso di [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) per la configurazione e l'invio dei processi.

Per informazioni sull'uso di ScriptRunConfig, vedere la documentazione seguente:
* [Configurare e inviare le esecuzioni di training](how-to-set-up-training-targets.md)
* [Configurazione delle esecuzioni di training PyTorch](how-to-train-pytorch.md)
* [Configurazione delle esecuzioni di training TensorFlow](how-to-train-tensorflow.md)
* [Configurazione delle esecuzioni di training di Scikit-learn](how-to-train-scikit-learn.md)

Inoltre, fare riferimento agli esempi seguenti & esercitazioni:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Definizione dell'ambiente di training
Sebbene i vari estimatori di Framework dispongano di ambienti preconfigurati supportati da immagini Docker, le Dockerfile per queste immagini sono private.  Di conseguenza, non si dispone di una maggiore trasparenza per gli ambienti che contengono. Inoltre, gli estimatori accettano le configurazioni correlate all'ambiente come singoli parametri (ad esempio `pip_packages` , `custom_docker_image` ) sui rispettivi costruttori.

Quando si usa ScriptRunConfig, tutte le configurazioni correlate all'ambiente sono incapsulate nell' `Environment` oggetto che viene passato al `environment` parametro del costruttore ScriptRunConfig. Per configurare un processo di training, fornire un ambiente con tutte le dipendenze necessarie per lo script di training. Se non viene fornito alcun ambiente, Azure ML userà una delle immagini di base di Azure ML, in particolare quella definita da `azureml.core.environment.DEFAULT_CPU_IMAGE` , come ambiente predefinito. Esistono un paio di modi per fornire un ambiente:

* [Usare un ambiente](how-to-use-environments.md#use-a-curated-environment) curato. gli ambienti sono disponibili nell'area di lavoro per impostazione predefinita. È disponibile un ambiente curato corrispondente per ognuna delle immagini Docker di Framework/versione preconfigurate che hanno supportato ogni strumento di stima del Framework.
* [Definire un ambiente personalizzato](how-to-use-environments.md)

Di seguito è riportato un esempio di uso dell'ambiente PyTorch 1,6 curato per il training:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Se si desidera specificare le **variabili di ambiente** che vengono impostate per il processo in cui viene eseguito lo script di training, utilizzare l'oggetto ambiente:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Per informazioni sulla configurazione e la gestione di ambienti Azure ML, vedere:
* [Come usare gli ambienti](how-to-use-environments.md)
* [Ambienti dedicati](resource-curated-environments.md)
* [Eseguire il training con un'immagine Docker personalizzata](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Utilizzo dei dati per il training
### <a name="datasets"></a>Set di dati
Se si usa un set di dati di Azure ML per il training, passare il set di dati come argomento allo script usando il `arguments` parametro. In questo modo si otterrà il percorso dei dati (punto di montaggio o percorso di download) nello script di training tramite argomenti.

Nell'esempio seguente viene configurato un processo di training in cui il filedataset, `mnist_ds` , viene montato nel calcolo remoto.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (obsoleto)
Sebbene sia consigliabile usare i set di impostazioni di Azure machine learning nel modo precedente, se per qualsiasi motivo si usano ancora i riferimenti a datareferences, è necessario configurare il processo come indicato di seguito:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Per ulteriori informazioni sull'utilizzo dei dati per il training, vedere:
* [Eseguire il training con i set di impostazioni in Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)

## <a name="distributed-training"></a>Training distribuito
Se è necessario configurare un processo distribuito per il training, è necessario specificare il `distributed_job_config` parametro nel costruttore ScriptRunConfig. Passare un [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true), [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true)o [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) per i processi distribuiti dei rispettivi tipi.

Nell'esempio seguente viene configurato un processo di training di PyTorch per l'utilizzo della formazione distribuita con MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Per altre informazioni, vedere:
* [Training distribuito con PyTorch](how-to-train-pytorch.md#distributed-training)
* [Formazione distribuita con TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Varie
Se per qualsiasi motivo è necessario accedere all'oggetto RunConfiguration sottostante per un ScriptRunConfig, è possibile procedere nel modo seguente:
```
src.run_config
```

## <a name="next-steps"></a>Passaggi successivi

* [Configurare e inviare le esecuzioni di training](how-to-set-up-training-targets.md)
