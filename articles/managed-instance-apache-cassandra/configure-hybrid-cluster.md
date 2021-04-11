---
title: Guida introduttiva-configurare un cluster ibrido con Azure Istanza gestita per Apache Cassandra
description: Questa Guida introduttiva illustra come configurare un cluster ibrido con Azure Istanza gestita per Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: b022bff9db87c248881cd18cc21569aaef8f404a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562138"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Guida introduttiva: configurare un cluster ibrido con Azure Istanza gestita per Apache Cassandra (anteprima)

Azure Istanza gestita per Apache Cassandra fornisce operazioni automatiche di distribuzione e scalabilità per i Data Center di Apache Cassandra Open Source gestiti. Questo servizio ti aiuta ad accelerare gli scenari ibridi e a ridurre la manutenzione continuativa.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa Guida introduttiva illustra come usare i comandi dell'interfaccia della riga di comando di Azure per configurare un cluster ibrido. Se si dispone di data center esistenti in un ambiente locale o self-hosted, è possibile usare Azure Istanza gestita per Apache Cassandra per aggiungere altri Data Center a tale cluster e gestirli.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.12.1 o successiva. Se si utilizza Azure Cloud Shell, è già installata la versione più recente.

* [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) con connettività all'ambiente self-hosted o on-premise. Per altre informazioni sulla connessione di ambienti locali ad Azure, vedere l'articolo [connettere una rete locale ad Azure](/azure/architecture/reference-architectures/hybrid-networking/) .

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Configurare un cluster ibrido

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare alla risorsa di rete virtuale.

1. Aprire la scheda **subnet** e creare una nuova subnet. Per ulteriori informazioni sui campi del modulo **Aggiungi subnet** , vedere l'articolo [rete virtuale](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) :

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Aggiungere una nuova subnet alla rete virtuale." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. A questo punto verranno applicate alcune autorizzazioni speciali per la VNet e la subnet che Cassandra Istanza gestita richiede, usando l'interfaccia della riga di comando di Azure. Usare il `az role assignment create` comando, sostituendo `<subscription ID>` , `<resource group name>` , `<VNet name>` e `<subnet name>` con i valori appropriati:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > I `assignee` `role` valori e nel comando precedente sono rispettivamente un principio di servizio fisso e gli identificatori di ruolo.

1. Successivamente, si configureranno le risorse per il cluster ibrido. Poiché si dispone già di un cluster, il nome del cluster sarà solo una risorsa logica per identificare il nome del cluster esistente. Assicurarsi di usare il nome del cluster esistente quando si definiscono le `clusterName` `clusterNameOverride` variabili e nello script seguente. Sono necessari anche i nodi di inizializzazione, i certificati client pubblici (se è stata configurata una chiave pubblica/privata nell'endpoint Cassandra) e i certificati di gossip del cluster esistente.

   > [!NOTE]
   > Il valore della `delegatedManagementSubnetId` variabile che verrà fornita di seguito è esattamente uguale al valore di `--scope` specificato nel comando precedente:

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
    > È necessario essere a conoscenza della posizione in cui vengono conservati i certificati pubblici e/o di gossip esistenti. Se non si è certi, dovrebbe essere possibile eseguire `keytool -list -keystore <keystore-path> -rfc -storepass <password>` per stampare i certificati. 

1. Dopo aver creato la risorsa cluster, eseguire il comando seguente per ottenere i dettagli di configurazione del cluster:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Il comando precedente restituisce informazioni sull'ambiente dell'istanza gestita. Sono necessari i certificati di gossip per poterli installare nei nodi del data center esistente. Lo screenshot seguente mostra l'output del comando precedente e il formato dei certificati:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Ottenere i dettagli del certificato dal cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Successivamente, creare un nuovo Data Center nel cluster ibrido. Assicurarsi di sostituire i valori delle variabili con i dettagli del cluster:

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

1. Ora che il nuovo Data Center è stato creato, eseguire il comando show datacenter per visualizzarne i dettagli:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Il comando precedente restituisce i nodi di inizializzazione del nuovo Data Center. Aggiungere i nuovi nodi di inizializzazione del Data Center alla configurazione del data center esistente nel file *Cassandra. YAML* . E installare i certificati di gossip dell'istanza gestita raccolti in precedenza:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Ottenere i dettagli dei data center." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Se si desidera aggiungere altri Data Center, è possibile ripetere i passaggi precedenti, ma sono necessari solo i nodi di inizializzazione. 

1. Infine, usare la query CQL seguente per aggiornare la strategia di replica in ogni spazio dei dati per includere tutti i Data Center nel cluster:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   È anche necessario aggiornare le tabelle delle password:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante l'applicazione delle autorizzazioni alla rete virtuale, ad esempio *non è possibile trovare l'utente o l'entità servizio nel database Graph per ' e5007d2c-4B13-4a74-9B6A-605d99f03501'*, è possibile applicare la stessa autorizzazione manualmente dalla portale di Azure. Per applicare le autorizzazioni dal portale, passare al riquadro **controllo di accesso (IAM)** della rete virtuale esistente e aggiungere un'assegnazione di ruolo per "Azure Cosmos DB" al ruolo "amministratore di rete". Se vengono visualizzate due voci quando si cerca "Azure Cosmos DB", aggiungere entrambe le voci come illustrato nell'immagine seguente: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Applicare le autorizzazioni" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> L'assegnazione di ruolo Azure Cosmos DB viene utilizzata solo a scopo di distribuzione. Istanza gestita di Azure per Apache Cassandra non ha dipendenze back-end in Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questo cluster di istanze gestite, eliminarlo con i passaggi seguenti:

1. Dal menu a sinistra di portale di Azure selezionare **gruppi di risorse**.
1. Selezionare nell'elenco il gruppo di risorse creato in questa guida di avvio rapido.
1. Nel riquadro **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
3. Nella finestra successiva immettere il nome del gruppo di risorse da eliminare e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come creare un cluster ibrido usando l'interfaccia della riga di comando di Azure e Azure Istanza gestita per Apache Cassandra. È ora possibile iniziare a usare il cluster.

> [!div class="nextstepaction"]
> [Gestire Azure Istanza gestita per le risorse Apache Cassandra usando l'interfaccia della riga di comando di Azure](manage-resources-cli.md)
