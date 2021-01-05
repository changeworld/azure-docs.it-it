---
title: Distribuire esperimenti ML con MLflow
titleSuffix: Azure Machine Learning
description: Configurare MLflow con Azure Machine Learning per distribuire i modelli ML come un servizio Web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1159a6cfda6b877f04573c85fa437ce3bff81af1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97761746"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>Distribuire modelli MLflow con Azure Machine Learning (anteprima)

In questo articolo si apprenderà come distribuire il modello di MLflow come servizio Web di Azure Machine Learning, in modo da poter sfruttare e applicare le funzionalità di gestione dei modelli di Azure Machine Learning e di rilevamento della deriva ai modelli di produzione.

Azure Machine Learning offre configurazioni di distribuzione per:
* Istanza di contenitore di Azure (ACI) che rappresenta la scelta ideale per una rapida distribuzione di sviluppo e test.
* Azure Kubernetes Service (AKS), consigliato per le distribuzioni di produzione scalabili.

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita degli esperimenti di machine learning. L'integrazione con Azure Machine Learning consente di estendere questa gestione oltre la fase di training del modello, alla fase di distribuzione del modello di produzione.

>[!NOTE]
> Come libreria open source, MLflow cambia di frequente. Di conseguenza, le funzionalità rese disponibili tramite l'integrazione Azure Machine Learning e MLflow devono essere considerate come anteprima e non sono completamente supportate da Microsoft.

Il diagramma seguente illustra che con l'API di distribuzione MLflow è possibile distribuire il modello MLflow esistente come servizio Web Azure Machine Learning, nonostante i relativi Framework, PyTorch, Tensorflow, Scikit-learn, ONNX e così via, e gestire i modelli di produzione nell'area di lavoro.

![ distribuire modelli mlflow con Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano distribuire il modello MLflow in un endpoint del servizio Web Azure Machine Learning. Gli amministratori interessati al monitoraggio dell'uso delle risorse e degli eventi da Azure Machine Learning, come ad esempio quote, esecuzioni di training o distribuzioni del modello completate, possono consultare la sezione [Monitoraggio di Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

* [Configurare l'URI di rilevamento MLflow per la connessione Azure Machine Learning](how-to-use-mlflow.md).
* Installare il pacchetto `azureml-mlflow`. 
    * Questo pacchetto introduce automaticamente il `azureml-core` [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), che fornisce la connettività per MLflow per accedere all'area di lavoro.

## <a name="deploy-to-aci"></a>Distribuire in ACI

Per distribuire il modello MLflow in un servizio Web Azure Machine Learning, è necessario configurare il modello con l' [URI di rilevamento MLflow per la connessione con Azure Machine Learning](how-to-use-mlflow.md). 

Configurare la configurazione della distribuzione con il metodo [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . È anche possibile aggiungere tag e descrizioni per tenere traccia del servizio Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Quindi, registrare e distribuire il modello in un unico passaggio con il metodo di [distribuzione](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) di MLflow per Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per distribuire il modello MLflow in un servizio Web Azure Machine Learning, è necessario configurare il modello con l' [URI di rilevamento MLflow per la connessione con Azure Machine Learning](how-to-use-mlflow.md). 

Per eseguire la distribuzione in AKS, creare prima un cluster AKS. Creare un cluster AKS usando il metodo [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . La creazione di un nuovo cluster può richiedere 20-25 minuti.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configurare la configurazione della distribuzione con il metodo [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . È anche possibile aggiungere tag e descrizioni per tenere traccia del servizio Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Quindi, registrare e distribuire il modello in un unico passaggio con il metodo di [distribuzione](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) di MLflow per Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

La distribuzione del servizio può richiedere diversi minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare il servizio Web distribuito, usare `service.delete()` per eliminarlo dal notebook.  Per ulteriori informazioni, vedere la documentazione relativa a [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Notebook di esempio

I [notebook MLflow con Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) dimostrano e ampliano le nozioni presentate in questo articolo.

> [!NOTE]
> Un repository gestito dalla community di esempi che usano mlflow è disponibile all'indirizzo https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i modelli](concept-model-management-and-deployment.md).
* Monitorare i modelli di produzione per la [deriva dei dati](./how-to-enable-data-collection.md).
* [Tenere traccia dei Azure Databricks eseguiti con MLflow](how-to-use-mlflow-azure-databricks.md).
