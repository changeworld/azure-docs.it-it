---
title: Come distribuire modelli in Istanze di Azure Container
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli Azure Machine Learning come servizio Web usando Istanze di Azure Container.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 845a146d9e3f920f3313a80f1bb8c845cb781f37
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875540"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Distribuire un modello a Istanze di Azure Container

Informazioni su come usare Azure Machine Learning distribuire un modello come servizio Web in Istanze di Azure Container (ACI). Usare Istanze di Azure Container se si verifica una delle condizioni seguenti:

- È necessario distribuire e convalidare rapidamente il modello. Non è necessario creare contenitori ACI in anticipo. Vengono creati come parte del processo di distribuzione.
- Si sta eseguendo il test di un modello in fase di sviluppo. 

Per informazioni sulla disponibilità di quote e aree per l'istanza di ACI, vedere Quote e disponibilità dell'area [per Istanze di Azure Container](../container-instances/container-instances-quotas.md) articolo.

> [!IMPORTANT]
> È consigliabile eseguire il debug in locale prima della distribuzione nel servizio Web. Per altre informazioni, vedere Eseguire il [debug in locale](./how-to-troubleshoot-deployment-local.md)
>
> È anche possibile fare riferimento ad Azure Machine Learning - [Eseguire la distribuzione a un notebook locale](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)

- Modello di Machine Learning registrato nell'area di lavoro. Se non si ha un modello registrato, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- [L'estensione dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando di Azure Machine Learning, [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)o [l'Azure Machine Learning Visual Studio Code.](tutorial-setup-vscode-extension.md)

- I __frammenti__ di codice Python in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws` - Impostare l'area di lavoro.
    * `model` - Impostare sul modello registrato.
    * `inference_config` : impostare sulla configurazione di inferenza per il modello.

    Per altre informazioni sull'impostazione di queste variabili, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I __frammenti__ di codice dell'interfaccia della riga di comando in questo articolo presuppongono che sia stato creato un `inferenceconfig.json` documento. Per altre informazioni sulla creazione di questo documento, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="limitations"></a>Limitazioni

* Quando si Istanze di Azure Container in una rete virtuale, la rete virtuale deve essere nello stesso gruppo di risorse dell'area Azure Machine Learning lavoro.
* Quando si Istanze di Azure Container all'interno della rete virtuale, anche il Registro Azure Container (ACR) per l'area di lavoro non può essere presente nella rete virtuale.

Per altre informazioni, vedere [Come proteggere l'inferenza con le reti virtuali.](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)

## <a name="deploy-to-aci"></a>Distribuire in ACI

Per distribuire un modello in Istanze di Azure Container, creare una __configurazione di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È anche necessaria una __configurazione di inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per altre informazioni sulla creazione della configurazione dell'inferenza, vedere [Come e dove distribuire i modelli.](how-to-deploy-and-where.md)

> [!NOTE]
> * ACI è adatto solo per i modelli di piccole dimensioni di dimensioni inferiore a 1 GB. 
> * È consigliabile usare il servizio AKS a nodo singolo per lo sviluppo e il test di modelli di dimensioni maggiori.
> * Il numero di modelli da distribuire è limitato a 1.000 modelli per distribuzione (per contenitore). 

### <a name="using-the-sdk"></a>Uso dell'SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per altre informazioni sulle classi, i metodi e i parametri usati in questo esempio, vedere i documenti di riferimento seguenti:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Per eseguire la distribuzione tramite l'interfaccia della riga di comando, usare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome per assegnare il servizio:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Per altre informazioni, vedere le informazioni di [riferimento su az ml model deploy.](/cli/azure/ml/model#az_ml_model_deploy) 

## <a name="using-vs-code"></a>Uso di VS Code

Vedere [Distribuire i modelli con VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Non è necessario creare un contenitore ACI per eseguire il test in anticipo. I contenitori ACI vengono creati in base alle esigenze.

> [!IMPORTANT]
> L'ID dell'area di lavoro con hash viene aggiunto a tutte le risorse ACI sottostanti create, tutti i nomi ACI della stessa area di lavoro avranno lo stesso suffisso. Il nome servizio Azure Machine Learning sarà comunque lo stesso "service_name" fornito dal cliente e tutte le API dell'SDK Azure Machine Learning non necessitano di alcuna modifica. Non vengono date garanzie sui nomi delle risorse sottostanti create.

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi relativi alla distribuzione](how-to-troubleshoot-deployment.md)
* [Aggiornare il servizio Web](how-to-deploy-update-web-service.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)