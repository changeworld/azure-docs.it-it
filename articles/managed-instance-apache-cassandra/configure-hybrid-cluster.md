---
title: 'Avvio rapido: Configurare un cluster ibrido con Azure Istanza gestita per Apache Cassandra'
description: Questa guida introduttiva illustra come configurare un cluster ibrido con Azure Istanza gestita per Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 9f3ad2a5d5b275ff611653855eff73bd36afda9f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379418"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Guida introduttiva: Configurare un cluster ibrido con Azure Istanza gestita per Apache Cassandra (anteprima)

Azure Istanza gestita per Apache Cassandra offre operazioni automatizzate di distribuzione e ridimensionamento per data center Apache Cassandra open source gestiti. Questo servizio consente di accelerare scenari ibridi e ridurre la manutenzione continuativa.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida introduttiva illustra come usare i comandi dell'interfaccia della riga di comando di Azure per configurare un cluster ibrido. Se si dispone di data center esistenti in un ambiente locale o self-hosted, è possibile usare Azure Istanza gestita per Apache Cassandra per aggiungere altri data center al cluster e gestirli.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.12.1 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

* [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) con connettività all'ambiente self-hosted o locale. Per altre informazioni sulla connessione di ambienti locali ad Azure, vedere l'articolo Connettere una [rete locale ad Azure.](/azure/architecture/reference-architectures/hybrid-networking/)

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Configurare un cluster ibrido

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare alla risorsa Rete virtuale.

1. Aprire la **scheda Subnet** e creare una nuova subnet. Per altre informazioni sui campi nel modulo **Aggiungi subnet,** vedere l'articolo [Rete](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) virtuale:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Aggiungere una nuova subnet alla rete virtuale." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > La distribuzione di un'istanza di Azure Istanza gestita per Apache Cassandra richiede l'accesso a Internet. La distribuzione non riesce negli ambienti in cui l'accesso a Internet è limitato. Assicurarsi di non bloccare l'accesso all'interno della rete virtuale ai servizi fondamentali di Azure seguenti, necessari per il corretto funzionamento di Cassandra gestito:
    > - Archiviazione di Azure
    > - Azure Key Vault
    > - Set di scalabilità delle macchine virtuali di Azure
    > - Monitoraggio di Azure
    > - Azure Active Directory
    > - Sicurezza di Azure

1. A questo punto verranno applicate alcune autorizzazioni speciali alla rete virtuale e alla subnet richieste da Cassandra Istanza gestita tramite l'interfaccia della riga di comando di Azure. Usare il `az role assignment create` comando sostituendo , e con i valori `<subscription ID>` `<resource group name>` `<VNet name>` appropriati:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > I valori e nel comando precedente sono rispettivamente il principio di servizio fisso e `assignee` `role` gli identificatori di ruolo.

1. Verranno quindi configurate le risorse per il cluster ibrido. Poiché si dispone già di un cluster, il nome del cluster in questo caso sarà solo una risorsa logica per identificare il nome del cluster esistente. Assicurarsi di usare il nome del cluster esistente durante la definizione delle `clusterName` variabili e nello script `clusterNameOverride` seguente. Sono necessari anche i nodi di seeding, i certificati client pubblici (se è stata configurata una chiave pubblica/privata nell'endpoint cassandra) e i certificati gossip del cluster esistente.

   > [!NOTE]
   > Il valore della variabile che verrà fornita di seguito è esattamente lo stesso di quello specificato `delegatedManagementSubnetId` `--scope` nel comando precedente:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > È necessario sapere dove vengono conservati i certificati pubblici e/o di gossip esistenti. In caso di incertezze, è possibile eseguire `keytool -list -keystore <keystore-path> -rfc -storepass <password>` per stampare i certificati. 

1. Dopo aver creato la risorsa cluster, eseguire il comando seguente per ottenere i dettagli di installazione del cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Il comando precedente restituisce informazioni sull'ambiente dell'istanza gestita. Saranno necessari i certificati gossip in modo da poterli installare nei nodi del data center esistente. Lo screenshot seguente mostra l'output del comando precedente e il formato dei certificati:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Ottenere i dettagli del certificato dal cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Creare quindi un nuovo data center nel cluster ibrido. Assicurarsi di sostituire i valori delle variabili con i dettagli del cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Ora che il nuovo data center è stato creato, eseguire il comando show datacenter per visualizzarne i dettagli:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Il comando precedente restituisce i nodi di seed del nuovo data center. Aggiungere i nodi di impostazione del nuovo data center alla configurazione del data center esistente all'interno del file *cassandra.yaml.* Installare i certificati dell'istanza gestita raccolti in precedenza:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Ottenere i dettagli del data center." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Se si vogliono aggiungere altri data center, è possibile ripetere i passaggi precedenti, ma sono necessari solo i nodi di seed. 

1. Usare infine la query CQL seguente per aggiornare la strategia di replica in ogni keyspace in modo da includere tutti i data center nel cluster:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   È anche necessario aggiornare le tabelle delle password:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante l'applicazione delle autorizzazioni alla rete virtuale, ad esempio Impossibile trovare l'utente o l'entità servizio nel database a grafo per *'e5007d2c-4b13-4a74-9b6a-605d99f03501',* è possibile applicare la stessa autorizzazione manualmente dal portale di Azure. Per applicare le autorizzazioni dal portale, passare al riquadro Controllo di accesso **(IAM)** della rete virtuale esistente e aggiungere un'assegnazione di ruolo per "Azure Cosmos DB" al ruolo "Amministratore di rete". Se vengono visualizzate due voci quando si cerca "Azure Cosmos DB", aggiungere entrambe le voci come illustrato nell'immagine seguente: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Applicare le autorizzazioni" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> La Azure Cosmos DB ruolo viene usata solo a scopo di distribuzione. Istanza gestita di Azure per Apache Cassandra non ha dipendenze back-end da Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continuerà a usare questo cluster di istanza gestita, eliminarlo seguendo questa procedura:

1. Nel menu a sinistra di portale di Azure selezionare **Gruppi di risorse.**
1. Selezionare nell'elenco il gruppo di risorse creato in questa guida di avvio rapido.
1. Nel riquadro Panoramica del **gruppo di** risorse selezionare Elimina gruppo **di risorse.**
3. Nella finestra successiva immettere il nome del gruppo di risorse da eliminare e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un cluster ibrido usando l'interfaccia della riga di comando di Azure e Azure Istanza gestita per Apache Cassandra. È ora possibile iniziare a usare il cluster.

> [!div class="nextstepaction"]
> [Gestire azure Istanza gestita per le risorse di Apache Cassandra usando l'interfaccia della riga di comando di Azure](manage-resources-cli.md)
