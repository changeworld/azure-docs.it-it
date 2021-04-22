---
title: Aggiornare i servizi Web
titleSuffix: Azure Machine Learning
description: Informazioni su come aggiornare un servizio Web già distribuito in Azure Machine Learning. È possibile aggiornare le impostazioni, ad esempio il modello, l'ambiente e lo script di ingresso.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: 098dc4be33c82faff1b85a5b7224ecaf1be64944
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875468"
---
# <a name="update-a-deployed-web-service"></a>Aggiornare un servizio Web distribuito

Questo articolo illustra come aggiornare un servizio Web distribuito con Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che sia già stato distribuito un servizio Web con Azure Machine Learning. Per informazioni su come distribuire un servizio Web, [seguire questa procedura.](how-to-deploy-and-where.md)

## <a name="update-web-service"></a>Aggiornare un servizio Web

Per aggiornare un servizio Web, usare il `update` metodo . È possibile aggiornare il servizio Web per l'uso di un nuovo modello, di un nuovo script di immissione o di nuove dipendenze che possono essere specificate in una configurazione di inferenza. Per altre informazioni, vedere la documentazione relativa a [Webservice.update.](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-)

Vedere [AKS Service Update Method (Metodo di aggiornamento del servizio AKS).](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Vedere [Metodo di aggiornamento del servizio ACI.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Quando si crea una nuova versione di un modello, è necessario aggiornare manualmente ogni servizio che si vuole usare.
>
> Non è possibile usare l'SDK per aggiornare un servizio Web pubblicato da Azure Machine Learning progettazione.

**Uso dell'SDK**

Il codice seguente illustra come usare l'SDK per aggiornare il modello, l'ambiente e lo script di ingresso per un servizio Web:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**Uso dell'interfaccia della riga di comando**

È anche possibile aggiornare un servizio Web usando l'interfaccia della riga di comando di Machine Learning. L'esempio seguente illustra la registrazione di un nuovo modello e l'aggiornamento di un servizio Web per l'uso del nuovo modello:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In questo esempio viene usato un documento JSON per passare le informazioni sul modello dal comando di registrazione al comando update.
>
> Per aggiornare il servizio per l'uso di un nuovo ambiente o script di ingresso, creare un file di configurazione [dell'inferenza](./reference-azure-machine-learning-cli.md#inference-configuration-schema) e specificarlo con il `ic` parametro .

Per altre informazioni, vedere la documentazione [di az ml service update.](/cli/azure/ml/service#az_ml_service_update)

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creare applicazioni client per l'utilizzo dei servizi Web](how-to-consume-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger di eventi per le distribuzioni di modelli](how-to-use-event-grid.md)
