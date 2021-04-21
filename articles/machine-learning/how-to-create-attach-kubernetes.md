---
title: Creare e collegare servizio Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un nuovo cluster di servizio Azure Kubernetes tramite Azure Machine Learning o su come collegare un cluster del servizio Web Disassoce esistente all'area di lavoro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/08/2021
ms.openlocfilehash: 1c9434d137114560b5585b081961497412dfbf69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770256"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Creare e collegare un cluster servizio Azure Kubernetes rete

Azure Machine Learning possibile distribuire modelli di Machine Learning con training in servizio Azure Kubernetes. Tuttavia, è prima necessario creare __un__ cluster servizio Azure Kubernetes (AKS) dall'area di lavoro di Azure ML o collegare __un__ cluster del servizio Azure Machine Learning esistente. Questo articolo fornisce informazioni sulla creazione e il collegamento di un cluster.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)

- [L'estensione dell'interfaccia della riga Machine Learning di Azure per il](reference-azure-machine-learning-cli.md)servizio , Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)o l'estensione [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

- Se si prevede di usare una rete virtuale di Azure per proteggere la comunicazione tra l'area di lavoro di Azure ML e il cluster del servizio Azure Machine Learning, leggere l'articolo Isolamento della rete durante il training & [inferenza.](./how-to-network-security-overview.md)

## <a name="limitations"></a>Limitazioni

- Se è necessario distribuire un **Load Balancer Standard (SLB)** nel cluster invece di basic Load Balancer (BLB), creare un cluster nel  portale del servizio AzureKs/interfaccia della riga di comando/SDK e quindi collegarlo all'area di lavoro AML.

- Se si dispone di un Criteri di Azure che limita la creazione di indirizzi IP pubblici, la creazione del cluster del servizio Web Diaks avrà esito negativo. Il servizio AKS richiede un indirizzo IP pubblico per [il traffico in uscita.](../aks/limit-egress-traffic.md) L'articolo traffico in uscita fornisce anche indicazioni per bloccare il traffico in uscita dal cluster attraverso l'indirizzo IP pubblico, ad eccezione di alcuni nomi di dominio completi. Esistono due modi per abilitare un indirizzo IP pubblico:
    - Il cluster può usare l'indirizzo IP pubblico creato per impostazione predefinita con BLB o SLB oppure
    - Il cluster può essere creato senza un indirizzo IP pubblico e quindi un indirizzo IP pubblico viene configurato con un firewall con una route definita dall'utente. Per altre informazioni, vedere [Personalizzare l'uscita del cluster con una route definita dall'utente.](../aks/egress-outboundtype.md)
    
    Il piano di controllo di AML non parla con questo indirizzo IP pubblico. Viene parlato con il piano di controllo del servizio Diaks per le distribuzioni. 

- Se si **collega un** cluster del servizio AKS, con un intervallo IP autorizzato abilitato per accedere al [server API,](../aks/api-server-authorized-ip-ranges.md)abilitare gli intervalli IP del piano di controllo AML per il cluster del servizio Gateway Gateway. Il piano di controllo AML viene distribuito tra aree abbinate e distribuisce pod di inferenza nel cluster del servizio AKS. Senza l'accesso al server API, i pod di inferenza non possono essere distribuiti. Usare gli [intervalli IP per](https://www.microsoft.com/download/confirmation.aspx?id=56519) entrambe le [aree abbinate](../best-practices-availability-paired-regions.md) quando si abilitano gli intervalli IP in un cluster del servizio AKS.

    Gli intervalli IP autorizzati funzionano solo con Load Balancer Standard.

- Quando **si collega un** cluster del servizio AzureKs, deve essere nella stessa sottoscrizione di Azure dell'area Azure Machine Learning lavoro.

- Se si vuole usare un cluster del servizio Web del servizio Web privato (usando collegamento privato di Azure), è necessario creare prima il cluster e quindi **collegarlo** all'area di lavoro. Per altre informazioni, vedere [Creare un cluster servizio Azure Kubernetes privato](../aks/private-clusters.md).

- Il nome di calcolo per il cluster del servizio AzureKs DEVE essere univoco all'interno dell'area di lavoro di Azure ML. Può includere lettere, cifre e trattini. Deve iniziare con una lettera, terminare con una lettera o una cifra e avere una lunghezza compresa tra 3 e 24 caratteri.
 
 - Se si vogliono distribuire modelli in nodi **GPU** o **FPGA** (o in uno SKU specifico), è necessario creare un cluster con lo SKU specifico. Non è disponibile alcun supporto per la creazione di un pool di nodi secondari in un cluster esistente e la distribuzione di modelli nel pool di nodi secondari.
 
- Quando si crea o si collega un cluster, è possibile scegliere se creare il cluster per __sviluppo-test__ o __produzione.__ Se si vuole creare un cluster del servizio AKS per lo __sviluppo,__ la convalida e il __test__ anziché per la produzione, impostare lo scopo __del cluster__ su __dev-test__. Se non si specifica lo scopo del cluster, __viene__ creato un cluster di produzione. 

    > [!IMPORTANT]
    > Un __cluster dev-test__ non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore.

- Quando si crea o si collega un cluster, se il cluster verrà usato per la __produzione,__ deve contenere almeno 12 __CPU virtuali.__ Il numero di CPU virtuali può essere calcolato moltiplicando il numero di __nodi__ nel cluster per il numero __di core__ forniti dalle dimensioni della macchina virtuale selezionate. Ad esempio, se si usa una dimensione di macchina virtuale "Standard_D3_v2", che ha 4 core virtuali, è necessario selezionare 3 o superiore come numero di nodi.

    Per un cluster __di sviluppo e test,__ è necessario eseguire il comando di almeno 2 CPU virtuali.

- L'SDK Azure Machine Learning non offre il supporto per il ridimensionamento di un cluster del servizio Web Diaks. Per ridimensionare i nodi nel cluster, usare l'interfaccia utente per il cluster del servizio Web Diaks nella studio di Azure Machine Learning. È possibile modificare solo il numero di nodi, non le dimensioni della macchina virtuale del cluster. Per altre informazioni sul ridimensionamento dei nodi in un cluster del servizio Web Diaks, vedere gli articoli seguenti:

    - [Ridimensionare manualmente il numero di nodi in un cluster del servizio Web Diaks](../aks/scale-cluster.md)
    - [Configurare il ridimensionamento automatico del cluster nel servizio Web Diaks](../aks/cluster-autoscaler.md)

- __Non aggiornare direttamente il cluster usando una configurazione YAML.__ Anche se il servizio Azure Kubernetes supporta gli aggiornamenti tramite la configurazione YAML, Azure Machine Learning le distribuzioni eseguiranno l'override delle modifiche. Gli unici due campi YAML che non verranno sovrascritti sono i limiti delle richieste __e__ e cpu __e memoria.__

- La creazione di un cluster del servizio Web Del servizio Studio di Azure Machine Learning tramite l'interfaccia utente, l'SDK o l'estensione dell'interfaccia della riga di comando __non è__ idempotente. Il tentativo di creare nuovamente la risorsa restituirà un errore che indica che esiste già un cluster con lo stesso nome.
    
    - Non è idempotente usare un modello di servizio Azure Resource Manager e la risorsa [Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) per creare un cluster del servizio Web Diaks.  Se si tenta di usare di nuovo il modello per aggiornare una risorsa già esistente, verrà visualizzato lo stesso errore.

## <a name="azure-kubernetes-service-version"></a>Versione del servizio Azure Kubernetes

servizio Azure Kubernetes consente di creare un cluster usando un'ampia gamma di versioni di Kubernetes. Per altre informazioni sulle versioni disponibili, vedere [versioni di Kubernetes](../aks/supported-kubernetes-versions.md)supportate in servizio Azure Kubernetes .

Quando **si** crea servizio Azure Kubernetes cluster usando uno dei  metodi seguenti, non è possibile scegliere nella versione del cluster creata:

* studio di Azure Machine Learning o la Azure Machine Learning della portale di Azure.
* Machine Learning'estensione per l'interfaccia della riga di comando di Azure.
* Azure Machine Learning SDK.

Questi metodi di creazione di un cluster del servizio Gateway Gateway usano __la versione__ predefinita del cluster. *La versione predefinita cambia nel tempo man* mano che diventano disponibili nuove versioni di Kubernetes.

Quando **si collega un** cluster del servizio AKS esistente, sono supportate tutte le versioni del servizio AKS attualmente supportate.

> [!NOTE]
> Possono verificarsi casi perimetrali in cui un cluster meno recente non è più supportato. In questo caso, l'operazione di collegamento restituirà un errore ed elenca le versioni attualmente supportate.
>
> È possibile allegare **versioni di anteprima.** La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Il supporto per l'uso delle versioni di anteprima può essere limitato. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Versioni disponibili e predefinite

Per trovare le versioni disponibili e predefinite del servizio AzureKs, usare il comando dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) [az aks get-versions](/cli/azure/aks#az_aks_get_versions). Ad esempio, il comando seguente restituisce le versioni disponibili nell'area Stati Uniti occidentali:

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

Per trovare la versione predefinita  usata durante la creazione di un cluster tramite Azure Machine Learning, è possibile usare il parametro `--query` per selezionare la versione predefinita:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

L'output di questo comando è simile al testo seguente:

```text
Result
--------
1.16.13
```

Se si desidera controllare a livello di codice le versioni disponibili, usare l'API REST Container Service Client - List Orchestrators.If you'd like to **programmatically check the available versions**, use the Container Service Client - List [Orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators) REST API. Per trovare le versioni disponibili, esaminare le voci in cui `orchestratorType` è `Kubernetes` . Le voci `orchestrationVersion` associate contengono le versioni disponibili che possono essere collegate **all'area** di lavoro.

Per trovare la versione predefinita usata durante la creazione **di** un cluster tramite Azure Machine Learning, trovare la voce in cui `orchestratorType` è e è `Kubernetes` `default` `true` . Il valore `orchestratorVersion` associato è la versione predefinita. Il frammento di codice JSON seguente mostra una voce di esempio:

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

**Tempo stimato:** circa 10 minuti.

La creazione o il collegamento di un cluster del servizio Web Diaks è un processo una sola volta per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster alla successiva distribuzione. All'area di lavoro possono essere collegati più cluster del servizio AKS.

L'esempio seguente illustra come creare un nuovo cluster del servizio Servizio Web Di seguito viene illustrato come usare l'SDK e l'interfaccia della riga di comando:

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

Per altre informazioni sulle classi, i metodi e i parametri usati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Per altre informazioni, vedere le informazioni di riferimento [su az ml computetarget create aks.](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sulla creazione di un cluster servizio Azure Studio di Azure Machine Learning nel [portale,](how-to-create-attach-compute-studio.md#inference-clusters)vedere Creare destinazioni di calcolo studio di Azure Machine Learning .

---

## <a name="attach-an-existing-aks-cluster"></a>Collegare un cluster del servizio Web Disassoce esistente

**Tempo stimato:** Circa 5 minuti.

Se nella sottoscrizione di Azure è già presente un cluster del servizio AzureKs, è possibile usarlo con l'area di lavoro.

> [!TIP]
> Il cluster del servizio AzureKs esistente può essere in un'area di Azure diversa dall'area Azure Machine Learning lavoro.


> [!WARNING]
> Non creare più allegati simultanei allo stesso cluster del servizio Web Diaks dall'area di lavoro. Ad esempio, collegare un cluster del servizio Web Del servizio Web a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.
>
> Se si vuole riallegare un cluster del servizio Web Del servizio Web Di windows, ad esempio per modificare TLS o un'altra impostazione di configurazione del cluster, è necessario rimuovere prima l'allegato esistente usando [AksCompute.detach().](/python/api/azureml-core/azureml.core.compute.akscompute#detach--)

Per altre informazioni sulla creazione di un cluster del servizio AzureKs usando l'interfaccia della riga di comando o il portale di Azure, vedere gli articoli seguenti:

* [Creare un cluster del servizio Azure Kubernetes (interfaccia della riga di comando)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [Creare un cluster del servizio AzureKs (portale)](../aks/kubernetes-walkthrough-portal.md)
* [Creare un cluster del servizio Azure AzureKs (modello arm nei modelli di avvio rapido di Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

L'esempio seguente illustra come collegare un cluster del servizio Web Del servizio Web esistente all'area di lavoro:The following example demonstrates how to attach an existing AKS cluster to your workspace:

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

Per altre informazioni sulle classi, i metodi e i parametri usati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per collegare un cluster esistente tramite l'interfaccia della riga di comando, è necessario ottenere l'ID risorsa del cluster esistente. Per ottenere questo valore, usare il comando seguente. Sostituire `myexistingcluster` con il nome del cluster del servizio Web Disaks. Sostituire `myresourcegroup` con il gruppo di risorse che contiene il cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Il comando restituisce un valore simile al testo seguente:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Per collegare il cluster esistente all'area di lavoro, usare il comando seguente. Sostituire `aksresourceid` con il valore restituito dal comando precedente. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Per altre informazioni, vedere il riferimento [a az ml computetarget attach aks.](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sul collegamento di un cluster servizio Azure Studio di Azure Machine Learning nel [portale,](how-to-create-attach-compute-studio.md#inference-clusters)vedere Creare destinazioni di calcolo studio di Azure Machine Learning .

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Creare o collegare un cluster del servizio Web Diaks con terminazione TLS
Quando si [crea o si collega](how-to-create-attach-kubernetes.md)un cluster del servizio AksCompute.attach_configuration, è possibile abilitare la terminazione TLS con gli oggetti di configurazione **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** **[e AksCompute.attach_configuration().](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** Entrambi i metodi restituiscono un oggetto di configurazione **con enable_ssl** metodo ed è possibile usare enable_ssl **per** abilitare TLS.

L'esempio seguente illustra come abilitare la terminazione TLS con la generazione e la configurazione automatiche dei certificati TLS usando il certificato Microsoft in secondo piano.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
L'esempio seguente illustra come abilitare la terminazione TLS con un certificato personalizzato e un nome di dominio personalizzato. Con il dominio personalizzato e il certificato, è necessario aggiornare il record DNS in modo che punti all'indirizzo IP dell'endpoint di assegnazione dei punteggi. Vedere [Aggiornare il DNS](how-to-secure-web-service.md#update-your-dns)

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
> Per altre informazioni su come proteggere la distribuzione del modello nel cluster del servizio Web Diaks, vedere Usare TLS per proteggere un servizio Web [tramite Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Creare o collegare un cluster del servizio Web Disassoce per l'uso Load Balancer con l'indirizzo IP privato
Quando si crea o si collega un cluster del servizio Web Diaks, è possibile configurare il cluster per l'uso di un servizio Load Balancer. Con un indirizzo IP Load Balancer, gli endpoint di assegnazione dei punteggi per le distribuzioni nel servizio Web Disatteso useranno un indirizzo IP privato all'interno della rete virtuale. I frammenti di codice seguenti illustrano come configurare un'istanza di Load Balancer per un cluster del servizio Web Diaks.
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
> Azure Machine Learning non supporta la terminazione TLS con regole Load Balancer. Il Load Balancer interno ha un indirizzo IP privato e tale indirizzo IP privato potrebbe essere in un'altra rete e il certificato può essere ricusato. 

>[!NOTE]
> Per altre informazioni su come proteggere l'ambiente di inferenza, vedere Proteggere un [ambiente Azure Machine Learning di inferenza](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Scollegare un cluster del servizio Web Disassociato

Per scollegare un cluster dall'area di lavoro, usare uno dei metodi seguenti:

> [!WARNING]
> L'studio di Azure Machine Learning, l'SDK o l'estensione dell'interfaccia della riga di comando di Azure per Machine Learning per scollegare un cluster del servizio AzureKs non elimina il cluster del servizio **AzureKs.** Per eliminare il cluster, vedere Usare l'interfaccia della riga [di comando di Azure con il servizio AzureKs.](../aks/kubernetes-walkthrough.md#delete-the-cluster)

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per scollegare il cluster esistente nell'area di lavoro, usare il comando seguente. Sostituire `myaks` con il nome con cui il cluster del servizio Web Disatteso è collegato all'area di lavoro. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

In studio di Azure Machine Learning selezionare __Calcolo__, __Cluster di inferenza__ e il cluster da rimuovere. Usare il __collegamento Scollega__ per scollegare il cluster.

---

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="update-the-cluster"></a>Aggiornare il cluster

Gli aggiornamenti Azure Machine Learning componenti installati in un cluster servizio Azure Kubernetes devono essere applicati manualmente. 

È possibile applicare questi aggiornamenti scollegando il cluster dall'area di lavoro Azure Machine Learning e quindi ricollegare il cluster all'area di lavoro. Se TLS è abilitato nel cluster, è necessario fornire il certificato TLS/SSL e la chiave privata quando si ricollegare il cluster. 

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

Se non si hanno più il certificato TLS/SSL e la chiave privata o si usa un certificato generato da Azure Machine Learning, è possibile recuperare i file prima di scollegare il cluster connettendosi al cluster usando e recuperando il `kubectl` segreto `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes archivia i segreti in formato codificato in base 64. È necessario decodificare in base 64 i componenti e dei segreti prima di `cert.pem` `key.pem` fornirli a `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Errori del servizio Web

È possibile eseguire il debug di molti errori del servizio Web nel servizio AKS connettendosi al cluster usando `kubectl` . È possibile ottenere `kubeconfig.json` l'oggetto per un cluster del servizio Web Disatteso eseguendo

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Passaggi successivi

* [Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes](../aks/manage-azure-rbac.md)
* [Come e dove distribuire un modello](how-to-deploy-and-where.md)
* [Distribuire un modello in un cluster servizio Azure Kubernetes distribuzione](how-to-deploy-azure-kubernetes-service.md)