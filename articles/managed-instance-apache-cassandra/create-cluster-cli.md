---
title: "Guida introduttiva: usare l'interfaccia della riga di comando per creare un cluster Azure Istanza gestita"
description: Usare questa Guida introduttiva per creare un cluster Azure Istanza gestita per Apache Cassandra usando l'interfaccia della riga di comando di Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 86fa7e2e45dacb86b6601b699dca46b1b909fd08
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424700"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Guida introduttiva: creare un cluster Azure Istanza gestita per Apache Cassandra usando l'interfaccia della riga di comando di Azure (anteprima)

Azure Istanza gestita per Apache Cassandra fornisce operazioni automatiche di distribuzione e scalabilità per i Data Center di Apache Cassandra Open Source gestiti. Questo servizio ti aiuta ad accelerare gli scenari ibridi e a ridurre la manutenzione continuativa.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa Guida introduttiva illustra come usare i comandi dell'interfaccia della riga di comando di Azure per creare un cluster con Azure Istanza gestita per Apache Cassandra. Mostra anche la creazione di un Data Center e la scalabilità dei nodi all'interno del Data Center.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.12.1 o successiva. Se si utilizza Azure Cloud Shell, è già installata la versione più recente.

* [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) con connettività all'ambiente self-hosted o on-premise. Per altre informazioni sulla connessione di ambienti locali ad Azure, vedere l'articolo [connettere una rete locale ad Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) .

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Creare un cluster di istanze gestite

1. Accedere al [portale di Azure](https://portal.azure.com/)

1. Impostare l'ID sottoscrizione nell'interfaccia della riga di comando di Azure:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Successivamente, creare una rete virtuale con una subnet dedicata nel gruppo di risorse:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Applicare alcune autorizzazioni speciali per la rete virtuale e la subnet, necessarie per l'istanza gestita. Usare il `az role assignment create` comando, sostituendo `<subscription ID>` , `<resource group name>` , `<VNet name>` e `<subnet name>` con i valori appropriati:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > I `assignee` `role` valori e nel comando precedente sono rispettivamente un principio di servizio fisso e gli identificatori di ruolo.

1. Successivamente, creare il cluster nella rete virtuale appena creata. Eseguire il comando seguente e assicurarsi di usare il `Resource ID` valore recuperato nel comando precedente come valore della `delegatedManagementSubnetId` variabile:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Infine, creare un Data Center per il cluster con tre nodi:

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. Una volta creato il Data Center, se si desidera aumentare o ridurre i nodi del Data Center, eseguire il comando seguente. Modificare il valore del `node-count` parametro impostando il valore desiderato:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Connettersi al cluster

Azure Istanza gestita per Apache Cassandra non crea nodi con indirizzi IP pubblici. Per connettersi al cluster Cassandra appena creato, è necessario creare un'altra risorsa all'interno della rete virtuale. Questa risorsa può essere un'applicazione o una macchina virtuale con lo strumento di query open source di Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) installato. È possibile usare un [modello di gestione risorse](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) per distribuire una macchina virtuale Ubuntu. Dopo la distribuzione, usare SSH per connettersi al computer e installare CQLSH come illustrato nei comandi seguenti:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile usare il `az group delete` comando per rimuovere il gruppo di risorse, l'istanza gestita e tutte le risorse correlate:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come creare un cluster di Istanza gestita di Azure per Apache Cassandra usando l'interfaccia della riga di comando di Azure. È ora possibile iniziare a usare il cluster:

> [!div class="nextstepaction"]
> [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)
