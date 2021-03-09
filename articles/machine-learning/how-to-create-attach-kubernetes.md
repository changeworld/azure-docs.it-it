---
title: Creare e alleghi il servizio Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un nuovo cluster del servizio Kubernetes di Azure tramite Azure Machine Learning o su come aggiungere un cluster AKS esistente all'area di lavoro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: 197dfa6a33e3a91a4d5717746629a667c5518b05
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506923"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Creare e alleghi un cluster di servizi Kubernetes di Azure

Azure Machine Learning possibile distribuire modelli di apprendimento automatico sottoposti a training nel servizio Azure Kubernetes. Tuttavia, è necessario __creare__ prima di tutto un cluster Azure Kubernetes Service (AKS) dall'area di lavoro di Azure ml o __alleghi__ un cluster AKS esistente. Questo articolo fornisce informazioni sulla creazione e sul fissaggio di un cluster.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Se si prevede di usare una rete virtuale di Azure per proteggere le comunicazioni tra l'area di lavoro di Azure ML e il cluster AKS, vedere l'articolo relativo all' [isolamento della rete durante il training & inferenza](./how-to-network-security-overview.md) .

## <a name="limitations"></a>Limitazioni

- Se è necessario un **Load Balancer standard (SLB)** distribuito nel cluster anziché una Load Balancer di base (BLB), creare un cluster nel portale AKS/CLI/SDK e quindi **collegarlo** all'area di lavoro AML.

- Se si dispone di un criterio di Azure che limita la creazione di indirizzi IP pubblici, la creazione del cluster AKS avrà esito negativo. AKS richiede un indirizzo IP pubblico per il [traffico in uscita](../aks/limit-egress-traffic.md). L'articolo sul traffico in uscita fornisce anche indicazioni per bloccare il traffico in uscita dal cluster tramite l'indirizzo IP pubblico, ad eccezione di alcuni nomi di dominio completi. Esistono due modi per abilitare un IP pubblico:
    - Il cluster può usare l'indirizzo IP pubblico creato per impostazione predefinita con BLB o SLB oppure
    - Il cluster può essere creato senza un indirizzo IP pubblico e quindi un indirizzo IP pubblico viene configurato con un firewall con una route definita dall'utente. Per altre informazioni, vedere [personalizzare l'uscita del cluster con una route definita dall'utente](../aks/egress-outboundtype.md).
    
    Il piano di controllo AML non comunica con questo indirizzo IP pubblico. Comunica con il piano di controllo AKS per le distribuzioni. 

- Se si **connette** un cluster AKS per cui è [abilitato un intervallo di indirizzi IP autorizzati per accedere al server API](../aks/api-server-authorized-ip-ranges.md), abilitare gli intervalli IP del piano di controllo AML per il cluster AKS. Il piano di controllo AML viene distribuito tra le aree abbinate e distribuisce i pod di inferenza nel cluster AKS. Senza l'accesso al server API, non è possibile distribuire i pod di inferenza. Usare gli [intervalli IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) per entrambe le [aree abbinate](../best-practices-availability-paired-regions.md) quando si abilitano gli intervalli IP in un cluster AKS.

    Gli intervalli IP autorizzati funzionano solo con Load Balancer Standard.

- Quando si **collega** un cluster AKS, questo deve trovarsi nella stessa sottoscrizione di Azure dell'area di lavoro Azure Machine Learning.

- Se si vuole usare un cluster del servizio contenitore di Azure privato (usando il collegamento privato di Azure), è necessario creare prima il cluster e quindi **collegarlo** all'area di lavoro. Per altre informazioni, vedere [creare un cluster di servizi Kubernetes di Azure privato](../aks/private-clusters.md).

- Il nome di calcolo per il cluster AKS deve essere univoco all'interno dell'area di lavoro di Azure ML.
    - Il nome è obbligatorio e deve avere una lunghezza compresa tra 3 e 24 caratteri.
    - I caratteri validi sono lettere maiuscole e minuscole, cifre e il carattere.
    - Il nome deve iniziare con una lettera.
    - Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso.
   
 - Se si desidera distribuire modelli a nodi **GPU** o a nodi **FPGA** (o a qualsiasi SKU specifico), è necessario creare un cluster con lo SKU specifico. Non è disponibile alcun supporto per la creazione di un pool di nodi secondari in un cluster esistente e la distribuzione di modelli nel pool di nodi secondari.
 
- Quando si crea o si connette un cluster, è possibile scegliere se creare il cluster per __sviluppo/test__ o __produzione__. Se si vuole creare un cluster AKS per __lo sviluppo__, __la convalida__ e il __test__ anziché la produzione, impostare lo __scopo del cluster__ su __dev-test__. Se non si specifica lo scopo del cluster, viene creato un cluster di __produzione__ . 

    > [!IMPORTANT]
    > Un cluster di __sviluppo e test__ non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore.

- Quando si crea o si collega un cluster, se il cluster verrà usato per la __produzione__, deve contenere almeno 12 __CPU virtuali__. Il numero di CPU virtuali può essere calcolato moltiplicando il __numero di nodi__ nel cluster per il __numero di core__ fornito dalle dimensioni della macchina virtuale selezionate. Se ad esempio si usano le dimensioni della macchina virtuale "Standard_D3_v2" con 4 core virtuali, è necessario selezionare 3 o una versione successiva come numero di nodi.

    Per un cluster di __sviluppo e test__ , si riordinano almeno 2 CPU virtuali.

- Il Azure Machine Learning SDK non fornisce il supporto per il ridimensionamento di un cluster AKS. Per ridimensionare i nodi del cluster, usare l'interfaccia utente per il cluster AKS in Azure Machine Learning Studio. È possibile modificare solo il numero di nodi, non le dimensioni della macchina virtuale del cluster. Per altre informazioni sul ridimensionamento dei nodi in un cluster AKS, vedere gli articoli seguenti:

    - [Ridimensionare manualmente il numero di nodi in un cluster AKS](../aks/scale-cluster.md)
    - [Configurare la scalabilità automatica del cluster in AKS](../aks/cluster-autoscaler.md)

- __Non aggiornare direttamente il cluster usando una configurazione YAML__. Sebbene i servizi Kubernetes di Azure supportino gli aggiornamenti tramite la configurazione YAML, le distribuzioni Azure Machine Learning sostituiranno le modifiche. Gli unici due campi YAML che non verranno sovrascritti sono i __limiti della richiesta__ e la __CPU e la memoria__.

## <a name="azure-kubernetes-service-version"></a>Versione del servizio Azure Kubernetes

Il servizio Azure Kubernetes consente di creare un cluster con diverse versioni di Kubernetes. Per altre informazioni sulle versioni disponibili, vedere [versioni di Kubernetes supportate nel servizio Kubernetes di Azure](../aks/supported-kubernetes-versions.md).

Quando si **Crea** un cluster del servizio Azure Kubernetes usando uno dei metodi seguenti, *non è possibile scegliere la versione* del cluster creata:

* Azure Machine Learning Studio o la sezione Azure Machine Learning della portale di Azure.
* Estensione Machine Learning per l'interfaccia della riga di comando di Azure.
* Azure Machine Learning SDK.

Questi metodi di creazione di un cluster AKS usano la versione __predefinita__ del cluster. *La versione predefinita cambia nel tempo* Man mano che le nuove versioni di Kubernetes diventano disponibili.

Quando si **connette** un cluster AKS esistente, sono supportate tutte le versioni di AKS attualmente supportate.

> [!NOTE]
> In alcuni casi è possibile che si disponga di un cluster obsoleto che non è più supportato. In questo caso, l'operazione di associazione restituirà un errore ed elenca le versioni attualmente supportate.
>
> È possibile alleghi le versioni di **Anteprima** . La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Il supporto per l'utilizzo di versioni di anteprima può essere limitato. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Versioni disponibili e predefinite

Per trovare le versioni di AKS disponibili e predefinite, usare l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) [AZ AKS get-versions](/cli/azure/aks#az_aks_get_versions). Ad esempio, il comando seguente restituisce le versioni disponibili nell'area Stati Uniti occidentali:

```azurecli-interactive
az aks get-versions -l westus -o table
```

L'output di questo comando è simile al testo seguente:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Per trovare la versione predefinita usata durante la **creazione** di un cluster tramite Azure Machine Learning, è possibile usare il `--query` parametro per selezionare la versione predefinita:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

L'output di questo comando è simile al testo seguente:

```text
Result
--------
1.16.13
```

Per controllare a livello di **codice le versioni disponibili**, usare l'API REST degli agenti di [orchestrazione dell'elenco client del servizio contenitore](/rest/api/container-service/container%20service%20client/listorchestrators) . Per trovare le versioni disponibili, esaminare le voci in cui `orchestratorType` è `Kubernetes` . Le `orchestrationVersion` voci associate contengono le versioni disponibili che possono essere **collegate** all'area di lavoro.

Per trovare la versione predefinita utilizzata durante la **creazione** di un cluster tramite Azure Machine Learning, trovare la voce in cui `orchestratorType` è `Kubernetes` e `default` è `true` . Il `orchestratorVersion` valore associato è la versione predefinita. Il frammento di codice JSON seguente mostra una voce di esempio:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Creare un nuovo cluster del servizio Azure Kubernetes

**Tempo stimato**: circa 10 minuti.

La creazione o il fissaggio di un cluster AKS è un processo di tipo One-Time per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster la volta successiva che è necessario distribuire. È possibile collegare più cluster AKS all'area di lavoro.

L'esempio seguente illustra come creare un nuovo cluster AKS usando l'SDK e l'interfaccia della riga di comando:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Per ulteriori informazioni, vedere il riferimento [AZ ml computetarget create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sulla creazione di un cluster AKS nel portale, vedere [creare destinazioni di calcolo in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Alleghi un cluster AKS esistente

**Tempo stimato:** Circa 5 minuti.

Se si dispone già del cluster AKS nella sottoscrizione di Azure, è possibile usarlo con l'area di lavoro.

> [!TIP]
> Il cluster AKS esistente può trovarsi in un'area di Azure diversa dall'area di lavoro Azure Machine Learning.


> [!WARNING]
> Non creare più allegati simultanei nello stesso cluster AKS dall'area di lavoro. Ad esempio, se si connette un cluster AKS a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.
>
> Se si vuole ricollegare un cluster AKS, ad esempio per modificare TLS o altre impostazioni di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente usando [AksCompute. Detach ()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--).

Per altre informazioni sulla creazione di un cluster AKS con l'interfaccia della riga di comando di Azure o il portale, vedere gli articoli seguenti:

* [Creare un cluster del servizio Azure Kubernetes (interfaccia della riga di comando)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az-aks-create)
* [Creare un cluster AKS (portale)](../aks/kubernetes-walkthrough-portal.md)
* [Creare un cluster AKS (modello ARM nei modelli di avvio rapido di Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

L'esempio seguente illustra come aggiungere un cluster AKS esistente all'area di lavoro:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute. Connetti](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per alleghi un cluster esistente usando l'interfaccia della riga di comando, è necessario ottenere l'ID risorsa del cluster esistente. Per ottenere questo valore, utilizzare il comando seguente. Sostituire `myexistingcluster` con il nome del cluster AKS. Sostituire `myresourcegroup` con il gruppo di risorse che contiene il cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Il comando restituisce un valore simile al testo seguente:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Per alleghi il cluster esistente all'area di lavoro, usare il comando seguente. Sostituire `aksresourceid` con il valore restituito dal comando precedente. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Per ulteriori informazioni, vedere la Guida di riferimento al comando [AZ ml computetarget per l'associazione AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sul fissaggio di un cluster AKS nel portale, vedere [creare destinazioni di calcolo in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Creare o associare un cluster AKS con la terminazione TLS
Quando si [Crea o si associa un cluster AKS](how-to-create-attach-kubernetes.md), è possibile abilitare la terminazione TLS con gli oggetti di configurazione **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** e **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Entrambi i metodi restituiscono un oggetto di configurazione con un metodo **enable_ssl** ed è possibile usare **enable_ssl** metodo per abilitare TLS.

Nell'esempio seguente viene illustrato come abilitare la terminazione TLS con la generazione e la configurazione di certificati TLS automatici utilizzando il certificato Microsoft sotto le quinte.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
Nell'esempio seguente viene illustrato come abilitare la terminazione TLS con il certificato personalizzato e il nome di dominio personalizzato. Con il dominio personalizzato e il certificato, è necessario aggiornare il record DNS in modo che punti all'indirizzo IP dell'endpoint di assegnazione dei punteggi. vedere [aggiornare il DNS](how-to-secure-web-service.md#update-your-dns)

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Per altre informazioni su come proteggere la distribuzione del modello nel cluster AKS, vedere [usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Creare o associare un cluster AKS per usare Load Balancer interni con IP privato
Quando si crea o si connette un cluster AKS, è possibile configurare il cluster per l'uso di un Load Balancer interno. Con un Load Balancer interno, gli endpoint di assegnazione dei punteggi per le distribuzioni in AKS utilizzeranno un indirizzo IP privato all'interno della rete virtuale. I frammenti di codice seguenti illustrano come configurare un Load Balancer interno per un cluster AKS.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning non supporta la terminazione TLS con Load Balancer interni. Il Load Balancer interno ha un indirizzo IP privato e tale indirizzo IP privato potrebbe trovarsi in un'altra rete e il certificato può essere ricusato. 

>[!NOTE]
> Per altre informazioni su come proteggere l'ambiente di inferenza, vedere [proteggere un ambiente di Azure Machine Learning inferenza](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Scollegare un cluster AKS

Per scollegare un cluster dall'area di lavoro, usare uno dei metodi seguenti:

> [!WARNING]
> L'uso di Azure Machine Learning Studio, SDK o l'estensione dell'interfaccia della riga di comando di Azure per l'apprendimento automatico per scollegare un cluster AKS non **Elimina il cluster AKS**. Per eliminare il cluster, vedere [usare l'interfaccia della riga di comando di Azure con AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per scollegare il cluster esistente nell'area di lavoro, usare il comando seguente. Sostituire `myaks` con il nome a cui è collegato il cluster AKS per l'area di lavoro. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

In Azure Machine Learning Studio selezionare __calcolo__, __inferenza cluster__ e il cluster che si vuole rimuovere. Usare il collegamento __Scollega__ per scollegare il cluster.

---

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="update-the-cluster"></a>Aggiornare il cluster

È necessario applicare manualmente gli aggiornamenti ai componenti Azure Machine Learning installati in un cluster del servizio Azure Kubernetes. 

È possibile applicare questi aggiornamenti scollegando il cluster dall'area di lavoro Azure Machine Learning, quindi riconnettendo il cluster all'area di lavoro. Se TLS è abilitato nel cluster, sarà necessario fornire il certificato TLS/SSL e la chiave privata quando si riconnette il cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se non si dispone più del certificato TLS/SSL e della chiave privata oppure si usa un certificato generato da Azure Machine Learning, è possibile recuperare i file prima di scollegare il cluster connettendosi al cluster usando `kubectl` e recuperando il segreto `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes archivia i segreti nel formato con codifica base 64. Prima di fornire tali componenti, è necessario decodificare in base 64 i `cert.pem` `key.pem` componenti e dei segreti `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Errori WebService

È possibile eseguire il debug di molti errori WebService in AKS connettendosi al cluster usando `kubectl` . È possibile ottenere `kubeconfig.json` per un cluster AKS eseguendo

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Passaggi successivi

* [Usa RBAC di Azure per l'autorizzazione Kubernetes](../aks/manage-azure-rbac.md)
* [Come e dove distribuire un modello](how-to-deploy-and-where.md)
* [Distribuire un modello in un cluster del servizio Kubernetes di Azure](how-to-deploy-azure-kubernetes-service.md)