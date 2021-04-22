---
title: "Avvio rapido: Usare l'interfaccia della riga di comando per creare Istanza gestita azure per il cluster Apache Cassandra"
description: Usare questa guida introduttiva per creare un cluster Azure Istanza gestita per Apache Cassandra usando l'interfaccia della riga di comando di Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 56fe69ad7f56d62c9f61738448ea0276fee47063
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862526"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Guida introduttiva: Creare un cluster Azure Istanza gestita per Apache Cassandra usando l'interfaccia della riga di comando di Azure (anteprima)

Azure Istanza gestita per Apache Cassandra offre operazioni automatizzate di distribuzione e ridimensionamento per data center Apache Cassandra open source gestiti. Questo servizio consente di accelerare scenari ibridi e ridurre la manutenzione continuativa.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida introduttiva illustra come usare i comandi dell'interfaccia della riga di comando di Azure per creare un cluster con Azure Istanza gestita per Apache Cassandra. Illustra anche come creare un data center e ridimensionare i nodi all'interno del data center.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) con connettività all'ambiente self-hosted o locale. Per altre informazioni sulla connessione di ambienti locali ad Azure, vedere l'articolo Connettere una [rete locale ad Azure.](/azure/architecture/reference-architectures/hybrid-networking/)

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.17.1 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Creare un cluster di istanza gestita

1. Accedere al [portale di Azure](https://portal.azure.com/)

1. Impostare l'ID sottoscrizione nell'interfaccia della riga di comando di Azure:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Creare quindi una rete virtuale con una subnet dedicata nel gruppo di risorse:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```
    > [!NOTE]
    > La distribuzione di un'istanza di Azure Istanza gestita per Apache Cassandra richiede l'accesso a Internet. La distribuzione non riesce negli ambienti in cui l'accesso a Internet è limitato. Assicurarsi di non bloccare l'accesso all'interno della rete virtuale ai servizi di Azure fondamentali seguenti, necessari per il corretto funzionamento di Cassandra gestito:
    > - Archiviazione di Azure
    > - Azure Key Vault
    > - Set di scalabilità delle macchine virtuali di Azure
    > - Monitoraggio di Azure
    > - Azure Active Directory
    > - Sicurezza di Azure

1. Applicare alcune autorizzazioni speciali alla rete virtuale, richieste dall'istanza gestita. Usare il `az role assignment create` comando , sostituendo , e con i valori `<subscription ID>` `<resource group name>` `<VNet name>` appropriati:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > I `assignee` valori e nel comando precedente sono valori `role` fissi. Immettere questi valori esattamente come indicato nel comando. Se non si esegue questa operazione, si verificano errori durante la creazione del cluster. Se si verificano errori durante l'esecuzione di questo comando, è possibile che non si abbia l'autorizzazione per eseguirlo. Contattare l'amministratore per ottenere le autorizzazioni.

1. Creare quindi il cluster nella rete virtuale appena creata usando il [comando az managed-cassandra cluster create.](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) Eseguire il comando seguente con il valore `delegatedManagementSubnetId` della variabile :

   > [!NOTE]
   > Il valore della variabile che verrà fornita di seguito è esattamente lo stesso di quello specificato `delegatedManagementSubnetId` `--scope` nel comando precedente:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Infine, creare un data center per il cluster con tre nodi usando il [comando az managed-cassandra datacenter create:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create)

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Dopo aver creato il data center, se si vuole aumentare o ridimensionare i nodi nel data center, eseguire il [comando az managed-cassandra datacenter update.](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) Modificare il valore del `node-count` parametro nel valore desiderato:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Connettersi al cluster

Azure Istanza gestita per Apache Cassandra non crea nodi con indirizzi IP pubblici. Per connettersi al cluster Cassandra appena creato, è necessario creare un'altra risorsa all'interno della rete virtuale. Questa risorsa può essere un'applicazione o una macchina virtuale in cui è installato lo strumento di query open source di Apache [CQLSH.](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) È possibile usare un [modello Resource Manager per](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) distribuire una macchina virtuale Ubuntu. Dopo la distribuzione, usare SSH per connettersi al computer e installare CQLSH, come illustrato nei comandi seguenti:

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante l'applicazione delle autorizzazioni alla rete virtuale, ad esempio Impossibile trovare l'utente o l'entità servizio nel database graph per *'e5007d2c-4b13-4a74-9b6a-605d99f03501',* è possibile applicare manualmente la stessa autorizzazione dal portale di Azure. Per applicare le autorizzazioni dal portale, passare al riquadro Controllo di accesso **(IAM)** della rete virtuale esistente e aggiungere un'assegnazione di ruolo per "Azure Cosmos DB" al ruolo "Amministratore di rete". Se vengono visualizzate due voci quando si cerca "Azure Cosmos DB", aggiungere entrambe le voci come illustrato nell'immagine seguente: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Applicare le autorizzazioni" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> La Azure Cosmos DB ruolo viene usata solo a scopo di distribuzione. Istanza gestita di Azure per Apache Cassandra non ha dipendenze back-end da Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare il comando per rimuovere il gruppo di `az group delete` risorse, l'istanza gestita e tutte le risorse correlate:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un cluster Azure Istanza gestita per Apache Cassandra usando l'interfaccia della riga di comando di Azure. È ora possibile iniziare a usare il cluster:

> [!div class="nextstepaction"]
> [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)