---
title: Proteggere gli ambienti di inferenza con le reti virtuali
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata per proteggere l'Azure Machine Learning di inferenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 610ab82bfc4665fbb30aa3d3bc0448fa9338689c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872552"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Proteggere un Azure Machine Learning di inferenza con le reti virtuali

Questo articolo illustra come proteggere gli ambienti di inferenza con una rete virtuale in Azure Machine Learning.

Questo articolo è la quattro di una serie in cinque parti che illustra come proteggere un flusso Azure Machine Learning flusso di lavoro. È consigliabile leggere la prima [parte:](how-to-network-security-overview.md) Panoramica della rete virtuale per comprendere prima l'architettura complessiva. 

Vedere gli altri articoli di questa serie:

[1. Panoramica della rete virtuale Proteggere](how-to-network-security-overview.md)  >  [l'area di lavoro](how-to-secure-workspace-vnet.md)  >  [3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  **4. Proteggere l'ambiente di inferenza**  >  [5. Abilitare le funzionalità di Studio](how-to-enable-studio-virtual-network.md)

Questo articolo illustra come proteggere le risorse di inferenza seguenti in una rete virtuale:
> [!div class="checklist"]
> - Cluster servizio Azure Kubernetes predefinito
> - Cluster del servizio AKS privato
> - Cluster del servizio AKS con collegamento privato
> - Istanze di Azure Container

## <a name="prerequisites"></a>Prerequisiti

+ Leggere [l'articolo Panoramica della sicurezza di](how-to-network-security-overview.md) rete per comprendere gli scenari di rete virtuale comuni e l'architettura complessiva della rete virtuale.

+ Una rete virtuale e una subnet esistenti da usare con le risorse di calcolo.

+ Per distribuire le risorse in una rete virtuale o in una subnet, l'account utente deve disporre delle autorizzazioni per le azioni seguenti nel controllo degli accessi in base al ruolo di Azure:

    - "Microsoft.Network/virtualNetworks/join/action" nella risorsa di rete virtuale.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" nella risorsa subnet.

    Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con la rete, vedere Ruoli [predefiniti di rete](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

Per usare un cluster del servizio AKS in una rete virtuale, è necessario soddisfare i requisiti di rete seguenti:

> [!div class="checklist"]
> * Seguire i prerequisiti in [Configurare la rete avanzata in servizio Azure Kubernetes (AKS).](../aks/configure-azure-cni.md#prerequisites)
> * L'istanza del servizio Diaks e la rete virtuale devono essere nella stessa area. Se si protegge uno o più account Archiviazione di Azure usati dall'area di lavoro in una rete virtuale, questi devono essere nella stessa rete virtuale dell'istanza del servizio Web Diaks.

Per aggiungere il servizio AKS in una rete virtuale all'area di lavoro, seguire questa procedura:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/), quindi selezionare la sottoscrizione e l'area di lavoro.

1. Selezionare __Calcolo__ a sinistra.

1. Selezionare __Inference clusters__ (Cluster di inferenza) dal centro, quindi selezionare __+__ .

1. Nella finestra di dialogo __New Inference Cluster__ (Nuovo cluster di inferenza) selezionare __Avanzata__ in __Configurazione di rete__.

1. Per configurare questa risorsa di calcolo per l'uso di una rete virtuale, eseguire le operazioni seguenti:

    1. Nell'elenco a discesa __Gruppo di risorse__ selezionare il gruppo di risorse che contiene la rete virtuale.
    1. Selezionare la rete virtuale che contiene la subnet nell'elenco a discesa __Rete virtuale__.
    1. Selezionare la subnet nell'elenco a discesa __Subnet__.
    1. Immettere l'intervallo di indirizzi del servizio Kubernetes nella casella __Intervallo di indirizzi del servizio Kubernetes__. Questo intervallo di indirizzi usa un indirizzo IP in notazione CIDR (Classless Inter-Domain Routing) per definire gli indirizzi IP disponibili per il cluster. Non deve sovrapporsi a nessun intervallo IP della subnet, ad esempio 10.0.0.0/16.
    1. Nella casella __Indirizzo IP del servizio DNS di Kubernetes__ immettere l'indirizzo IP del servizio DNS di Kubernetes. Questo indirizzo IP viene assegnato al servizio DNS di Kubernetes. Deve essere compreso nell'intervallo di indirizzi del servizio Kubernetes, ad esempio 10.0.0.10.
    1. Nella casella __Indirizzo del bridge Docker__ immettere l'indirizzo del Bridge Docker. Questo indirizzo IP viene assegnato al bridge Docker. Non deve essere compreso in nessun intervallo IP della subnet o nell'intervallo di indirizzi del servizio Kubernetes, ad esempio 172.17.0.1/16.

   ![Azure Machine Learning: impostazioni della rete virtuale dell'ambiente di calcolo di Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Quando si distribuisce un modello come servizio Web nel servizio DinK, viene creato un endpoint di assegnazione dei punteggi per gestire le richieste di inferenza. Assicurarsi che nel gruppo NSG che controlla la rete virtuale sia abilitata una regola di sicurezza in ingresso per l'indirizzo IP dell'endpoint di assegnazione dei punteggi, se si vuole chiamarlo dall'esterno della rete virtuale.

    Per trovare l'indirizzo IP dell'endpoint di assegnazione dei punteggi, esaminare l'URI di assegnazione dei punteggi per il servizio distribuito. Per informazioni sulla visualizzazione dell'URI di assegnazione dei punteggi, vedere Utilizzare un [modello distribuito come servizio Web.](how-to-consume-web-service.md#connection-information)

   > [!IMPORTANT]
   > Mantenere le regole in uscita predefinite per il gruppo di sicurezza di rete. Per altre informazioni, vedere le regole di sicurezza predefinite in [Gruppi di sicurezza](../virtual-network/network-security-groups-overview.md#default-security-rules).

   [![Aggiungere una regola di sicurezza in ingresso](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > L'indirizzo IP visualizzato nell'immagine per l'endpoint di assegnazione dei punteggi sarà diverso per le distribuzioni. Anche se lo stesso indirizzo IP è condiviso da tutte le distribuzioni in un cluster del servizio Web Disassociato di , ogni cluster del servizio Web Disatteso avrà un indirizzo IP diverso.

È anche possibile usare Azure Machine Learning SDK per aggiungere il servizio Azure Kubernetes in una rete virtuale. Se è già presente un cluster del servizio Azure Kubernetes in una rete virtuale, collegarlo all'area di lavoro come descritto nell'articolo relativo alla [distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md). Il codice seguente crea una nuova istanza del servizio Azure Kubernetes nella subnet `default` di una rete virtuale denominata `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Al termine del processo di creazione, è possibile eseguire l'inferenza o il calcolo del modello in un cluster del servizio Azure Kubernetes in una rete virtuale. Per altre informazioni, vedere [Come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-and-where.md).

Per altre informazioni sull'uso del Role-Based di accesso con Kubernetes, vedere Usare il controllo degli accessi in base al ruolo di Azure per [l'autorizzazione di Kubernetes.](../aks/manage-azure-rbac.md)

## <a name="network-contributor-role"></a>Ruolo Collaboratore Rete

> [!IMPORTANT]
> Se si crea o si collega un cluster del servizio AKS fornendo una rete virtuale creata in precedenza, è necessario concedere all'entità servizio (SP) o all'identità gestita per il cluster del servizio AKS il ruolo Collaboratore Rete al gruppo di risorse che contiene la rete virtuale. 
>
> Per aggiungere l'identità come collaboratore alla rete, seguire questa procedura:

1. Per trovare l'entità servizio o l'ID identità gestita per il servizio Azure AzureKs, usare i comandi dell'interfaccia della riga di comando di Azure seguenti. Sostituire `<aks-cluster-name>` con il nome del cluster. Sostituire con il nome del gruppo di risorse che contiene il cluster del servizio Web `<resource-group-name>` _Diaks:_

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Se questo comando restituisce un valore , usare il comando seguente per identificare `msi` l'ID entità per l'identità gestita:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Per trovare l'ID del gruppo di risorse che contiene la rete virtuale, usare il comando seguente. Sostituire `<resource-group-name>` con il nome del gruppo di risorse che contiene la rete _virtuale:_

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Per aggiungere l'entità servizio o l'identità gestita come collaboratore alla rete, usare il comando seguente. Sostituire `<SP-or-managed-identity>` con l'ID restituito per l'entità servizio o l'identità gestita. Sostituire `<resource-group-id>` con l'ID restituito per il gruppo di risorse che contiene la rete virtuale:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Per altre informazioni sull'uso del bilanciamento del carico interno con il servizio Azure Kubernetes, vedere l'articolo sull'[uso del bilanciamento del carico interno con il servizio Azure Kubernetes](../aks/internal-lb.md).

## <a name="secure-vnet-traffic"></a>Proteggere il traffico della rete virtuale

Esistono due approcci per isolare il traffico da e verso il cluster del servizio Web di Servizio Web di Microsoft Alla rete virtuale:

* __Cluster del servizio AKS privato:__ questo approccio usa collegamento privato di Azure per proteggere le comunicazioni con il cluster per le operazioni di distribuzione/gestione.
* __Servizio di bilanciamento del carico__ del servizio AzureKs interno: questo approccio configura l'endpoint per le distribuzioni nel servizio AzureKs per l'uso di un indirizzo IP privato all'interno della rete virtuale.

> [!WARNING]
> Il servizio di bilanciamento del carico interno non funziona con un cluster del servizio Azure Kubenet che usa kubenet. Se si vuole usare contemporaneamente un servizio di bilanciamento del carico interno e un cluster del servizio AzureKs privato, configurare il cluster del servizio AzureKs privato con Azure Container Networking Interface (CNI). Per altre informazioni, vedere [Configurare Azure CNI di rete in servizio Azure Kubernetes](../aks/configure-azure-cni.md).

### <a name="private-aks-cluster"></a>Cluster del servizio AKS privato

Per impostazione predefinita, i cluster del servizio Gateway Gateway hanno un piano di controllo, o server API, con indirizzi IP pubblici. È possibile configurare il servizio AKS per l'uso di un piano di controllo privato creando un cluster del servizio AKS privato. Per altre informazioni, vedere [Creare un cluster servizio Azure Kubernetes privato](../aks/private-clusters.md).

Dopo aver creato il cluster del servizio AKS privato, [collegare il cluster alla](how-to-create-attach-kubernetes.md) rete virtuale da usare con Azure Machine Learning.

> [!IMPORTANT]
> Prima di usare un cluster del servizio AKS abilitato per il collegamento privato con Azure Machine Learning, è necessario aprire un evento imprevisto del supporto per abilitare questa funzionalità. Per altre informazioni, vedere [Gestire e aumentare le quote](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

### <a name="internal-aks-load-balancer"></a>Servizio di bilanciamento del carico del servizio Azure Azure Interno

Per impostazione predefinita, le distribuzioni del servizio AzureKs usano un [servizio di bilanciamento del carico pubblico.](../aks/load-balancer-standard.md) In questa sezione si apprenderà come configurare il servizio AzureKs per l'uso di un servizio di bilanciamento del carico interno. Viene usato un servizio di bilanciamento del carico interno (o privato) in cui solo gli indirizzi IP privati sono consentiti come front-end. I servizi di bilanciamento del carico interni vengono usati per bilanciare il carico del traffico all'interno di una rete virtuale

Un servizio di bilanciamento del carico privato viene abilitato configurando il servizio AzureKs per l'uso di _un servizio di bilanciamento del carico interno._ 

#### <a name="enable-private-load-balancer"></a>Abilitare il servizio di bilanciamento del carico privato

> [!IMPORTANT]
> Non è possibile abilitare l'indirizzo IP privato quando si crea servizio Azure Kubernetes cluster in studio di Azure Machine Learning. È possibile crearne uno con un servizio di bilanciamento del carico interno quando si usa Python SDK o l'estensione dell'interfaccia della riga di comando di Azure per Machine Learning.

Gli esempi seguenti illustrano come creare un nuovo cluster del servizio AzureKs con un servizio di bilanciamento del carico __interno/IP__ privato usando l'SDK e l'interfaccia della riga di comando:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> Usando l'interfaccia della riga di comando, è possibile creare solo un cluster del servizio AzureKs con un servizio di bilanciamento del carico interno. Non è disponibile alcun comando az ml per aggiornare un cluster esistente per usare un servizio di bilanciamento del carico interno.

Per altre informazioni, vedere le informazioni di riferimento [su az ml computetarget create aks.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks)

---

Quando __si collega un cluster esistente all'area__ di lavoro, è necessario attendere fino a dopo l'operazione di collegamento per configurare il servizio di bilanciamento del carico. Per informazioni sul collegamento di un cluster, vedere [Attach an existing AKS cluster](how-to-create-attach-kubernetes.md).

Dopo aver collegato il cluster esistente, è possibile aggiornare il cluster per l'uso di un servizio di bilanciamento del carico interno/ip privato:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Abilitare Istanze di Azure Container (ACI)

Le Istanze di Azure Container vengono create dinamicamente quando si distribuisce un modello. Per abilitare Azure Machine Learning per la creazione di Istanze di Azure Container all'interno della rete virtuale, è necessario abilitare la __delega subnet__ per la subnet usata dalla distribuzione.

> [!WARNING]
> Quando si Istanze di Azure Container in una rete virtuale, la rete virtuale deve essere:
> * Nello stesso gruppo di risorse dell'area Azure Machine Learning lavoro.
> * Se l'area di lavoro ha un __endpoint__ privato, la rete virtuale usata per Istanze di Azure Container deve essere uguale a quella usata dall'endpoint privato dell'area di lavoro.
>
> Quando si Istanze di Azure Container all'interno della rete virtuale, il Registro Azure Container (ACR) per l'area di lavoro non può essere nella rete virtuale.

Per usare Istanze di Azure Container all'area di lavoro, seguire questa procedura:

1. Per abilitare la delega subnet nella rete virtuale, usare le informazioni contenute nell'articolo relativo ad [aggiunta e rimozione di una delega subnet](../virtual-network/manage-subnet-delegation.md). È possibile abilitare la delega quando si crea una rete virtuale o aggiungerla a una rete esistente.

    > [!IMPORTANT]
    > Quando si abilita la delega, usare `Microsoft.ContainerInstance/containerGroups` come valore di __Delega subnet a un servizio__.

2. Distribuire il modello usando [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), usare i parametri `vnet_name` e `subnet_name`. Impostare questi parametri sul nome e sulla subnet della rete virtuale in cui è stata abilitata la delega.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Limitare la connettività in uscita dalla rete virtuale

Se non si vogliono usare le regole in uscita predefinite e si vuole limitare l'accesso in uscita della rete virtuale, è necessario consentire l'accesso a Registro Azure Container. Ad esempio, assicurarsi che i gruppi di sicurezza di rete (NSG) contengano una regola che consente l'accesso al tag di servizio __AzureContainerRegistry.RegionName,__ dove '{RegionName} è il nome di un'area di Azure.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è la quattro di una serie di reti virtuali in cinque parti. Vedere gli altri articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica della rete virtuale](how-to-network-security-overview.md)
* [Parte 2: Proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Parte 3: Proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 5: Abilitare la funzionalità di Studio](how-to-enable-studio-virtual-network.md)

Vedere anche l'articolo sull'uso [di DNS personalizzato per](how-to-custom-dns.md) la risoluzione dei nomi.