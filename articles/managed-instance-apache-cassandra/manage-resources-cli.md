---
title: Gestire azure Istanza gestita per le risorse di Apache Cassandra usando l'interfaccia della riga di comando di Azure
description: Informazioni sui comandi comuni per automatizzare la gestione di Azure Istanza gestita per Apache Cassandra tramite l'interfaccia della riga di comando di Azure.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875450"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Gestire azure Istanza gestita per le risorse di Apache Cassandra usando l'interfaccia della riga di comando di Azure (anteprima)

Questo articolo descrive i comandi comuni per automatizzare la gestione dei cluster azure Istanza gestita per Apache Cassandra tramite l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.17.1 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.
>
> Non è Istanza gestita possibile rinominare le risorse di Azure Istanza gestita per Apache Cassandra, in quanto ciò viola il funzionamento Azure Resource Manager con gli URI delle risorse.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Azure Istanza gestita per cluster Apache Cassandra

Le sezioni seguenti illustrano come gestire Azure Istanza gestita per i cluster Apache Cassandra, tra cui:

* [Creare un cluster di istanza gestita](#create-cluster)
* [Eliminare un cluster di istanza gestita](#delete-cluster)
* [Ottenere i dettagli del cluster](#get-cluster-details)
* [Ottenere lo stato del nodo del cluster](#get-cluster-status)
* [Elencare i cluster per gruppo di risorse](#list-clusters-resource-group)
* [Elencare i cluster in base all'ID sottoscrizione](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Creare un cluster di istanza gestita

Creare un cluster Istanza gestita Azure per Apache Cassandra usando il [comando az managed-cassandra cluster create:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Eliminare un cluster di istanza gestita

Eliminare un cluster usando il [comando az managed-cassandra cluster delete:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Ottenere i dettagli del cluster

Ottenere i dettagli del cluster usando [il comando az managed-cassandra cluster show:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Ottenere lo stato del nodo del cluster

Ottenere i dettagli del cluster usando il [comando az managed-cassandra cluster node-status:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status)

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Elencare i cluster in base al gruppo di risorse

Elencare i cluster in base al gruppo di risorse usando [il comando az managed-cassandra cluster list:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list)

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Elencare i cluster in base all'ID sottoscrizione

Elencare i cluster in base all'ID sottoscrizione usando [il comando az managed-cassandra cluster list:](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true)

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Data center dell'istanza gestita

Le sezioni seguenti illustrano come gestire azure Istanza gestita per data center Apache Cassandra, tra cui:

* [Creare un data center](#create-datacenter)
* [Eliminare un data center](#delete-datacenter)
* [Ottenere i dettagli del data center](#get-datacenter-details)
* [Aggiornare o ridimensionare un data center](#update-datacenter)
* [Ottenere data center in un cluster](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Creare un data center

Creare un data center usando il [comando az managed-cassandra datacenter create:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Eliminare un data center

Eliminare un data center usando il [comando az managed-cassandra datacenter delete:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Ottenere i dettagli del data center

Ottenere i dettagli del data center usando [il comando az managed-cassandra datacenter show:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Aggiornare o ridimensionare un data center

Aggiornare o ridimensionare un data center (per ridimensionare il valore nodeCount) usando il [comando az managed-cassandra datacenter update:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Ottenere i data center in un cluster

Ottenere i data center in un cluster usando il [comando az managed-cassandra datacenter list:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un cluster dell'istanza gestita dal portale di Azure](create-cluster-portal.md)
* [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)
