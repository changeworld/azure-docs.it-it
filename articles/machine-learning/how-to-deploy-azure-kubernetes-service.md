---
title: Distribuire modelli di Machine Learning nel servizio Kubernetes
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli Azure Machine Learning come servizio Web usando servizio Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 6030462fc7c9678200aa14fa852a82d35f8703b6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877823"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuire un modello in un cluster servizio Azure Kubernetes distribuzione

Informazioni su come usare Azure Machine Learning distribuire un modello come servizio Web in servizio Azure Kubernetes (AKS). servizio Azure Kubernetes è adatta per le distribuzioni di produzione su larga scala. Usare il servizio Azure Kubernetes se sono necessarie una o più delle funzionalità seguenti:

- __Tempi di risposta rapidi__
- __Scalabilità automatica__ del servizio distribuito
- __Logging__
- __Raccolta di dati del modello__
- __autenticazione__
- __Terminazione TLS__
- __Opzioni di__ accelerazione hardware come GPU e dispositivi FPGA (Field Programmable Gate Array)

Quando si esegue la distribuzione in servizio Azure Kubernetes, si esegue la distribuzione in un cluster del servizio Web Diaks __connesso all'area di lavoro.__ Per informazioni sulla connessione di un cluster del servizio Web Disassoniato all'area di lavoro, vedere Creare e collegare [un cluster servizio Azure Kubernetes servizio.](how-to-create-attach-kubernetes.md)

> [!IMPORTANT]
> È consigliabile eseguire il debug in locale prima della distribuzione nel servizio Web. Per altre informazioni, vedere Eseguire il [debug in locale](./how-to-troubleshoot-deployment-local.md)
>
> È anche possibile fare riferimento ad Azure Machine Learning - [Eseguire la distribuzione a un notebook locale](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)

- Un modello di Machine Learning registrato nell'area di lavoro. Se non si ha un modello registrato, vedere [Come e dove distribuire i modelli.](how-to-deploy-and-where.md)

- [L'estensione dell'interfaccia della riga Machine Learning azure per il](reference-azure-machine-learning-cli.md)servizio , Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)o l'estensione [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

- I __frammenti__ di codice Python in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws` : impostare l'area di lavoro.
    * `model` : impostare sul modello registrato.
    * `inference_config` : impostare sulla configurazione di inferenza per il modello.

    Per altre informazioni sull'impostazione di queste variabili, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I __frammenti__ di codice dell'interfaccia della riga di comando in questo articolo presuppongono che sia stato creato un `inferenceconfig.json` documento. Per altre informazioni sulla creazione di questo documento, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Un servizio Azure Kubernetes cluster connesso all'area di lavoro. Per altre informazioni, vedere [Creare e collegare un cluster servizio Azure Kubernetes cluster](how-to-create-attach-kubernetes.md).

    - Se si vogliono distribuire modelli in nodi GPU o FPGA (o in uno SKU specifico), è necessario creare un cluster con lo SKU specifico. Non è disponibile alcun supporto per la creazione di un pool di nodi secondari in un cluster esistente e la distribuzione di modelli nel pool di nodi secondari.

## <a name="understand-the-deployment-processes"></a>Comprendere i processi di distribuzione

La parola "distribuzione" viene usata sia in Kubernetes che Azure Machine Learning. "Distribuzione" ha significati diversi in questi due contesti. In Kubernetes un è `Deployment` un'entità concreta, specificata con un file YAML dichiarativo. Kubernetes ha un ciclo di vita definito e relazioni concrete con altre entità `Deployment` di Kubernetes, ad esempio `Pods` e `ReplicaSets` . Per altre informazioni su Kubernetes, vedere la documentazione e i video in [Che cos'è Kubernetes?](https://aka.ms/k8slearning).

In Azure Machine Learning, la "distribuzione" viene usata nel senso più generale di rendere disponibili e pulire le risorse del progetto. I passaggi che Azure Machine Learning parte della distribuzione sono:

1. Compressione dei file nella cartella del progetto, ignorando quelli specificati in .amlignore o .gitignore
1. Aumento delle dimensioni del cluster di calcolo (correlato a Kubernetes)
1. Compilazione o download del dockerfile nel nodo di calcolo (correlato a Kubernetes)
    1. Il sistema calcola un hash di: 
        - Immagine di base 
        - Passaggi docker personalizzati (vedere [Distribuire un modello usando un'immagine di base Docker personalizzata)](./how-to-deploy-custom-docker-image.md)
        - La definizione YAML conda (vedere [Creare & usare ambienti software in Azure Machine Learning](./how-to-use-environments.md))
    1. Il sistema usa questo hash come chiave in una ricerca dell'area di Registro Azure Container (ACR)
    1. Se non viene trovato, cerca una corrispondenza nel Controllo di accesso globale
    1. Se non viene trovato, il sistema compila una nuova immagine (che verrà memorizzata nella cache e inserita nel registro di controllo di accesso dell'area di lavoro)
1. Download del file di progetto compresso nell'archiviazione temporanea nel nodo di calcolo
1. Decomprimere il file di progetto
1. Nodo di calcolo in esecuzione `python <entry script> <arguments>`
1. Salvataggio di log, file di modello e altri file scritti `./outputs` nell'account di archiviazione associato all'area di lavoro
1. Riduzione delle risorse di calcolo, inclusa la rimozione dell'archiviazione temporanea (correlata a Kubernetes)

### <a name="azure-ml-router"></a>Router di Azure ML

Il componente front-end (azureml-fe) che instrada le richieste di inferenza in ingresso ai servizi distribuiti viene ridimensionato automaticamente in base alle esigenze. Il ridimensionamento di azureml-fe si basa sullo scopo e sulle dimensioni del cluster del servizio AzureKs (numero di nodi). Lo scopo del cluster e i nodi vengono configurati quando si crea o si collega un cluster del servizio [Web Diaks.](how-to-create-attach-kubernetes.md) Esiste un servizio azureml-fe per cluster, che può essere in esecuzione in più pod.

> [!IMPORTANT]
> Quando si usa un cluster configurato come __dev-test,__ l'utilità di scalabilità autonoma è **disabilitata.**

Azureml-fe consente di aumentare le dimensioni (verticalmente) per usare più core e di aumentare (orizzontalmente) l'uso di più pod. Quando si decide di aumentare le dimensioni, viene usato il tempo necessario per instradare le richieste di inferenza in ingresso. Se questo tempo supera la soglia, si verifica un ridimensionamento. Se il tempo necessario per instradare le richieste in ingresso continua a superare la soglia, si verifica una scalabilità orizzontale.

In caso di riduzione e riduzione, viene usato l'utilizzo della CPU. Se viene raggiunta la soglia di utilizzo della CPU, il front-end verrà prima ridimensionato. Se l'utilizzo della CPU scende alla soglia di scalabilità verticale, viene eseguita un'operazione di ridimensionamento. La scalabilità verticale e orizzontale si verificherà solo se sono disponibili risorse cluster sufficienti.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Informazioni sui requisiti di connettività per il cluster di inferenza del servizio Azure Kubernetes

Quando Azure Machine Learning crea o collega un cluster del servizio AKS, viene distribuito un cluster del servizio Web Del servizio Web con uno dei due modelli di rete seguenti:When Azure Machine Learning creates or attaches an AKS cluster, AKS cluster is deployed with one of the following two network models:
* Funzionalità di rete kubenet: le risorse di rete vengono in genere create e configurate quando viene distribuito il cluster del servizio Azure Kubernetes.
* Funzionalità di rete Azure Container Networking Interface (Azure CNI): il cluster del servizio Azure Kubernetes viene connesso alle configurazioni e alle risorse di rete virtuale esistenti.

Per la prima modalità di rete, la rete viene creata e configurata correttamente per servizio Azure Machine Learning. Per la seconda modalità di rete, poiché il cluster è connesso alla rete virtuale esistente, in particolare quando viene usato dns personalizzato per la rete virtuale esistente, il cliente deve prestare maggiore attenzione ai requisiti di connettività per il cluster di inferenza del servizio AKS e garantire la risoluzione DNS e la connettività in uscita per l'inferenza del servizio AKS.

Il diagramma seguente acquisisce tutti i requisiti di connettività per l'inferenza del servizio AKS. Le frecce nere rappresentano la comunicazione effettiva e le frecce blu rappresentano i nomi di dominio che il DNS controllato dal cliente deve risolvere.

 ![Requisiti di connettività per l'inferenza del servizio AKS](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Requisiti complessivi per la risoluzione DNS
La risoluzione DNS all'interno della rete virtuale esistente è sotto il controllo del cliente. Le voci DNS seguenti devono essere risolvibili:
* Il server API del servizio Web Diaks sotto forma \<cluster\> di .hcp. \<region\> azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* L'Registro Azure Container del cliente (ARC) sotto forma di \<ACR name\> .azurecr.io
* Archiviazione di Azure account nel formato \<account\> .table.core.windows.net \<account\> e .blob.core.windows.net
* (Facoltativo) Per l'autenticazione di AAD: api.azureml.ms
* Nome di dominio dell'endpoint di assegnazione dei punteggi, generato automaticamente da Azure ML o nome di dominio personalizzato. Il nome di dominio generato automaticamente sarà simile al seguente: \<leaf-domain-label \+ auto-generated suffix\> \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Requisiti di connettività in ordine cronologico: dalla creazione del cluster alla distribuzione del modello

Durante il processo di creazione o collegamento del servizio Azure Machine Learning, il router di Azure ML (azureml-fe) viene distribuito nel cluster del servizio AzureKs. Per distribuire il router di Azure ML, il nodo del servizio Azure Machine Learning deve essere in grado di:
* Risolvere il DNS per il server DELL'API del servizio Web Disatteso
* Risolvere il DNS per MCR per scaricare le immagini Docker per il router di Azure Machine Learning
* Scaricare immagini da MCR, in cui è necessaria la connettività in uscita

Subito dopo la distribuzione di azureml-fe, verrà eseguito un tentativo di avvio, che richiede di:
* Risolvere il DNS per il server DELL'API del servizio Web Disatteso
* Eseguire una query sul server API del servizio Contenitore di Microsoft Service Pack per individuare altre istanze di se stesso (si tratta di un servizio multi-pod)
* Connettersi ad altre istanze di se stesso

Dopo l'avvio di azureml-fe, è necessaria connettività aggiuntiva per il corretto funzionamento:
* Connettersi a Archiviazione di Azure per scaricare la configurazione dinamica
* Risolvere il DNS per il server di autenticazione api.azureml.ms AAD e comunicare con esso quando il servizio distribuito usa l'autenticazione AAD.
* Eseguire query sul server API del servizio Web Diaks per individuare i modelli distribuiti
* Comunicare con i modelli POD distribuiti

In fase di distribuzione del modello, per una corretta distribuzione del modello, il nodo del servizio Web Disatteso deve essere in grado di: 
* Risolvere il DNS per il record di controllo di errore del cliente
* Scaricare immagini dal record di controllo di acr del cliente
* Risolvere il DNS per i BLOB di Azure in cui è archiviato il modello
* Scaricare modelli da BLOB di Azure

Dopo la distribuzione del modello e l'avvio del servizio, azureml-fe lo individua automaticamente usando l'API servizio AzureKs e sarà pronto per instradare la richiesta. Deve essere in grado di comunicare con i POD del modello.
>[!Note]
>Se il modello distribuito richiede connettività ,ad esempio l'esecuzione di query su database esterni o altri servizi REST, il download di un BLOB e così via, è necessario che sia la risoluzione DNS che la comunicazione in uscita per questi servizi siano abilitate.

## <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per distribuire un modello in servizio Azure Kubernetes, creare una configurazione __di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È anche necessaria una configurazione __di inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per altre informazioni sulla creazione della configurazione di inferenza, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

> [!NOTE]
> Il numero di modelli da distribuire è limitato a 1.000 modelli per ogni distribuzione (per contenitore).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Per altre informazioni sulle classi, i metodi e i parametri usati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la distribuzione tramite l'interfaccia della riga di comando, usare il comando seguente. Sostituire `myaks` con il nome della destinazione di calcolo del servizio AKS. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome per assegnare questo servizio:

```azurecli-interactive
az ml model deploy --ct myaks -m mymodel:1 -n myservice --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Per altre informazioni, vedere le informazioni di riferimento sulla distribuzione del modello [az ml.](/cli/azure/ml/model#az_ml_model_deploy)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Per informazioni sull'uso di VS Code, vedere [Distribuire nel servizio Web Diaks tramite l'estensione VS Code .](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> La distribuzione tramite VS Code richiede la creazione o il collegamento anticipato del cluster del servizio Web Diaks all'area di lavoro.

---

### <a name="autoscaling"></a>Scalabilità automatica

Il componente che gestisce la scalabilità automatica per le distribuzioni del modello di Azure Machine Learning è azureml-fe, un router di richiesta intelligente. Poiché tutte le richieste di inferenza passano attraverso di essa, sono presenti i dati necessari per ridimensionare automaticamente i modelli distribuiti.

> [!IMPORTANT]
> * **Non abilitare Kubernetes Horizontal Pod Autoscaler (HPA)** per le distribuzioni di modelli . In questo modo i due componenti di ridimensionamento automatico potrebbero essere in competizione tra loro. Azureml-fe è progettato per ridimensionare automaticamente i modelli distribuiti da Azure ML, in cui HPA deve indovinare o approssimare l'utilizzo del modello da una metrica generica come l'utilizzo della CPU o una configurazione di metrica personalizzata.
> 
> * **Azureml-fe non ridimensiona il numero** di nodi in un cluster del servizio AzureKs, perché ciò potrebbe causare aumenti imprevisti dei costi. Al contrario, **ridimensiona il numero di repliche per il modello entro** i limiti del cluster fisico. Se è necessario ridimensionare il numero di nodi all'interno del cluster, è possibile ridimensionare manualmente il cluster o configurare il ridimensionamento automatico del cluster del [servizio](../aks/cluster-autoscaler.md)Diaks.

La scalabilità automatica può essere controllata impostando , e per il servizio Web `autoscale_target_utilization` `autoscale_min_replicas` `autoscale_max_replicas` AKS. L'esempio seguente illustra come abilitare la scalabilità automatica:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Le decisioni relative alla scalabilità verticale/verticale si basano sull'utilizzo delle repliche dei contenitori correnti. Il numero di repliche occupate (elaborazione di una richiesta) diviso per il numero totale di repliche correnti è l'utilizzo corrente. Se questo numero supera `autoscale_target_utilization` , vengono create più repliche. Se è inferiore, le repliche vengono ridotte. Per impostazione predefinita, l'utilizzo di destinazione è 70%.

Le decisioni relative all'aggiunta di repliche sono eager e veloci (circa 1 secondo). Le decisioni relative alla rimozione delle repliche sono conservativi (circa 1 minuto).

È possibile calcolare le repliche necessarie usando il codice seguente:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Per altre informazioni sull'impostazione `autoscale_target_utilization` di , e , vedere le informazioni di riferimento sul modulo `autoscale_max_replicas` `autoscale_min_replicas` [AksWebservice.](/python/api/azureml-core/azureml.core.webservice.akswebservice)

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuire modelli nel servizio AKS usando l'implementazione controllata (anteprima)

Analizzare e alzare di livello le versioni del modello in modo controllato usando gli endpoint. È possibile distribuire fino a sei versioni dietro un singolo endpoint. Gli endpoint offrono le funzionalità seguenti:

* Configurare la __percentuale di assegnazione del punteggio al traffico inviato a ogni endpoint.__ Ad esempio, indirizzare il 20% del traffico all'endpoint 'test' e l'80% a 'production'.

    > [!NOTE]
    > Se non si tratta del 100% del traffico, qualsiasi percentuale rimanente viene instradata alla __versione dell'endpoint__ predefinita. Ad esempio, se si configura la versione dell'endpoint 'test' per ottenere il 10% del traffico e 'prod' per il 30%, il restante 60% viene inviato alla versione dell'endpoint predefinita.
    >
    > La prima versione dell'endpoint creata viene configurata automaticamente come predefinita. È possibile modificare questa impostazione durante la `is_default=True` creazione o l'aggiornamento di una versione dell'endpoint.
     
* Contrassegnare una versione dell'endpoint come __controllo__ o __trattamento.__ Ad esempio, la versione corrente dell'endpoint di produzione potrebbe essere il controllo , mentre i potenziali nuovi modelli vengono distribuiti come versioni di trattamento. Dopo la valutazione delle prestazioni delle versioni di trattamento, se uno supera il controllo corrente, potrebbe essere promosso al nuovo controllo/produzione.

    > [!NOTE]
    > È possibile avere un __solo__ controllo. È possibile avere più trattamento.

È possibile abilitare Le informazioni dettagliate sulle app per visualizzare le metriche operative degli endpoint e delle versioni distribuite.

### <a name="create-an-endpoint"></a>Creare un endpoint
Quando si è pronti per distribuire i modelli, creare un endpoint di assegnazione dei punteggi e distribuire la prima versione. L'esempio seguente illustra come distribuire e creare l'endpoint usando l'SDK. La prima distribuzione verrà definita come versione predefinita, il che significa che il percentile del traffico non specificato in tutte le versioni passa alla versione predefinita.  

> [!TIP]
> Nell'esempio seguente la configurazione imposta la versione iniziale dell'endpoint per gestire il 20% del traffico. Poiché si tratta del primo endpoint, è anche la versione predefinita. E poiché non sono disponibili altre versioni per l'altro 80% del traffico, viene instradato anche all'impostazione predefinita. Fino a quando non vengono distribuite altre versioni che accettano una percentuale di traffico, questa riceve in modo efficace il 100% del traffico.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aggiornare e aggiungere versioni a un endpoint

Aggiungere un'altra versione all'endpoint e configurare il percentile del traffico di assegnazione dei punteggi per la versione. Esistono due tipi di versioni, un controllo e una versione di trattamento. Possono essere disponibili più versioni di trattamento per facilitare il confronto con una singola versione del controllo.

> [!TIP]
> La seconda versione, creata dal frammento di codice seguente, accetta il 10% del traffico. La prima versione è configurata per il 20%, quindi solo il 30% del traffico è configurato per versioni specifiche. Il rimanente 70% viene inviato alla prima versione dell'endpoint, perché è anche la versione predefinita.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Aggiornare le versioni esistenti o eliminarle in un endpoint. È possibile modificare il tipo predefinito della versione, il tipo di controllo e il percentile del traffico. Nell'esempio seguente, la seconda versione aumenta il traffico al 40% ed è ora l'impostazione predefinita.

> [!TIP]
> Dopo il frammento di codice seguente, la seconda versione è ora predefinita. È ora configurato per il 40%, mentre la versione originale è ancora configurata per il 20%. Ciò significa che il 40% del traffico non viene in considerazione dalle configurazioni della versione. Il traffico rimanente verrà instradato alla seconda versione, perché è ora l'impostazione predefinita. Riceve in modo efficace l'80% del traffico.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

Quando si esegue la distribuzione servizio Azure Kubernetes, __l'autenticazione basata su__ chiave è abilitata per impostazione predefinita. È anche possibile abilitare __l'autenticazione basata su token.__ L'autenticazione basata su token richiede ai client di usare un account Azure Active Directory per richiedere un token di autenticazione, usato per effettuare richieste al servizio distribuito.

Per __disabilitare__ l'autenticazione, impostare `auth_enabled=False` il parametro durante la creazione della configurazione di distribuzione. L'esempio seguente disabilita l'autenticazione tramite l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Per informazioni sull'autenticazione da un'applicazione client, vedere utilizzare un [modello Azure Machine Learning distribuito come servizio Web.](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Se l'autenticazione con chiave è abilitata, è possibile usare il metodo per recuperare una chiave di autenticazione primaria `get_keys` e secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Autenticazione con token

Per abilitare l'autenticazione basata su token, impostare `token_auth_enabled=True` il parametro durante la creazione o l'aggiornamento di una distribuzione. L'esempio seguente abilita l'autenticazione tramite l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se l'autenticazione del token è abilitata, è possibile usare il `get_token` metodo per recuperare un token JWT e l'ora di scadenza del token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Sarà necessario richiedere un nuovo token dopo l'ora del `refresh_by` token.
>
> Microsoft consiglia di creare l'area di Azure Machine Learning nella stessa area del cluster servizio Azure Kubernetes locale. Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web anche se il cluster si trova in un'area diversa rispetto all'area di lavoro. In questo modo l'autenticazione basata su token non sarà disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile. Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.
>
> Per recuperare un token, è necessario usare Azure Machine Learning SDK o [il comando az ml service get-access-token.](/cli/azure/ml/service#az_ml_service_get_access_token)


### <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità

Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. È consigliabile Centro sicurezza di Azure analizzare le risorse e seguire le raccomandazioni. Per altre informazioni, vedere [Integrazione di Azure Kubernetes Services con il Centro sicurezza.](../security-center/defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Passaggi successivi

* [Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes](../aks/manage-azure-rbac.md)
* [Proteggere l'ambiente di inferenza con Rete virtuale di Azure](how-to-secure-inferencing-vnet.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
