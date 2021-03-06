---
title: "Esercitazione: Creare un cluster abilitato Apache Kafka proxy REST in HDInsight usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come eseguire Apache Kafka usando un proxy REST Kafka Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786640"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Esercitazione: Creare un cluster abilitato Apache Kafka proxy REST in HDInsight usando l'interfaccia della riga di comando di Azure

Questa esercitazione illustra come creare un cluster abilitato Apache Kafka [proxy REST](./rest-proxy.md) in Azure HDInsight tramite l'interfaccia della riga di comando di Azure. Azure HDInsight è un servizio di analisi open source, gestito e ad ampio spettro per le aziende. Apache Kafka è una piattaforma di streaming open source distribuita. Viene spesso usata come broker di messaggi perché offre funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione. Il proxy REST Kafka consente di interagire con il cluster Kafka tramite [un'API REST](/rest/api/hdinsight-kafka-rest-proxy/) su HTTP. L'interfaccia della riga di comando di Azure è l'esperienza di riga di comando multipiattaforma Microsoft per la gestione delle risorse di Azure.

Possono accedere all'API Apache Kafka solo risorse interne alla stessa rete virtuale. È possibile accedere al cluster direttamente tramite SSH. Per connettere altri servizi, reti o macchine virtuali ad Apache Kafka, è necessario prima di tutto creare una rete virtuale e quindi creare le risorse all'interno della rete. Per altre informazioni, vedere [Connettersi a Apache Kafka usando una rete virtuale.](./apache-kafka-connect-vpn-gateway.md)

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Prerequisiti per il proxy REST Kafka
> * Creare un cluster di Apache Kafka usando l'interfaccia della riga di comando di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un'applicazione registrata con Azure AD. Le applicazioni client scritte per interagire con il proxy REST Kafka useranno l'ID e il segreto di questa applicazione per l'autenticazione con Azure. Per altre informazioni, vedere [Registrare un'applicazione con Microsoft Identity Platform.](../../active-directory/develop/quickstart-register-app.md)

* Un Azure AD di sicurezza con l'applicazione registrata come membro. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni sono autorizzate a interagire con il proxy REST. Per ulteriori informazioni sulla creazione di gruppi di Azure AD, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Interfaccia della riga di comando di Azure. Assicurarsi di avere almeno la versione 2.0.79. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Creare un cluster Apache Kafka

1. Accedere alla sottoscrizione di Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Impostare le variabili di ambiente. L'uso delle variabili in questa esercitazione si basa su Bash. Per altri ambienti saranno necessarie piccole modifiche.

    |Variabile | Descrizione |
    |---|---|
    |resourceGroupName|Sostituire RESOURCEGROUPNAME con il nome del nuovo gruppo di risorse.|
    |posizione|Sostituire LOCATION con un'area in cui verrà creato il cluster. Per un elenco di percorsi validi, usare il `az account list-locations` comando|
    |clusterName|Sostituire CLUSTERNAME con un nome univoco globale per il nuovo cluster.|
    |storageAccount|Sostituire STORAGEACCOUNTNAME con un nome per il nuovo account di archiviazione.|
    |httpPassword|Sostituire PASSWORD con una password per l'account di accesso del cluster, **admin**.|
    |sshPassword|Sostituire PASSWORD con una password per il nome utente della shell sicura, **sshuser**.|
    |securityGroupName|Sostituire SECURITYGROUPNAME con il nome del gruppo di sicurezza AAD client per il proxy Rest Kafka. La variabile verrà passata al `--kafka-client-group-name` parametro per `az-hdinsight-create` .|
    |securityGroupID|Sostituire SECURITYGROUPID con l'ID del gruppo di sicurezza AAD client per il proxy Rest Kafka. La variabile verrà passata al `--kafka-client-group-id` parametro per `az-hdinsight-create` .|
    |storageContainer|Contenitore di archiviazione che verrà utilizzato dal cluster, lasciare così come è per questa esercitazione. Questa variabile verrà impostata con il nome del cluster.|
    |workernodeCount|Numero di nodi di lavoro nel cluster, lasciare così come sono per questa esercitazione. Per garantire la disponibilità elevata, Kafka richiede almeno 3 nodi di lavoro|
    |clusterType|Tipo di cluster HDInsight, lasciare così come è per questa esercitazione.|
    |clusterVersion|Versione del cluster HDInsight, lasciare così come è per questa esercitazione. Kafka Rest Proxy richiede una versione minima del cluster 4.0.|
    |componentVersion|Versione kafka, lasciare così come è per questa esercitazione. Il proxy REST Kafka richiede una versione minima del componente 2.1.|

    Aggiornare le variabili con i valori desiderati. Immettere quindi i comandi dell'interfaccia della riga di comando per impostare le variabili di ambiente.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Creare il gruppo di risorse](/cli/azure/group#az_group_create) immettendo il comando seguente:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Creare un Archiviazione di Azure account](/cli/azure/storage/account#az_storage_account_create) immettendo il comando seguente:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Estrarre la chiave primaria](/cli/azure/storage/account/keys#az_storage_account_keys_list) dall'account Archiviazione di Azure e archiviarla in una variabile immettendo il comando seguente:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Creare un Archiviazione di Azure contenitore](/cli/azure/storage/container#az_storage_container_create) immettendo il comando seguente:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Creare il cluster HDInsight](/cli/azure/hdinsight#az_hdinsight_create). Prima di immettere il comando, prendere nota dei parametri seguenti:

    1. Parametri obbligatori per i cluster Kafka:

        |Parametro | Descrizione|
        |---|---|
        |--type|Il valore deve essere **Kafka.**|
        |--workernode-data-disks-per-node|Numero di dischi dati da usare per ogni nodo di lavoro. HDInsight Kafka è supportato solo con i dischi dati. In questa esercitazione viene utilizzato il valore **2**.|

    1. Parametri obbligatori per il proxy REST Kafka:

        |Parametro | Descrizione|
        |---|---|
        |--kafka-management-node-size|Dimensioni del nodo. Questa esercitazione usa il valore **Standard_D4_v2**.|
        |--kafka-client-group-id|ID del gruppo di sicurezza AAD client per il proxy Rest Kafka. Il valore viene passato dalla variabile **$securityGroupID**.|
        |--kafka-client-group-name|Nome del gruppo di sicurezza AAD client per il proxy Rest Kafka. Il valore viene passato dalla variabile **$securityGroupName**.|
        |--version|La versione del cluster HDInsight deve essere almeno 4.0. Il valore viene passato dalla variabile **$clusterVersion**.|
        |--component-version|La versione di Kafka deve essere almeno 2.1. Il valore viene passato dalla variabile **$componentVersion**.|
    
        Se si vuole creare il cluster senza proxy REST, eliminare `--kafka-management-node-size` , e dal comando `--kafka-client-group-id` `--kafka-client-group-name` `az hdinsight create` .

    1. Se si dispone di una rete virtuale esistente, aggiungere i `--vnet-name` parametri e e i relativi `--subnet` valori.

    Immettere il comando seguente per creare il cluster:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Il completamento del processo di creazione del cluster potrebbe richiedere alcuni minuti. in genere circa 15.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'articolo, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Immettere tutti o alcuni dei comandi seguenti per rimuovere le risorse:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato correttamente un cluster abilitato Apache Kafka proxy REST in Azure HDInsight usando l'interfaccia della riga di comando di Azure, usare il codice Python per interagire con il proxy REST:

> [!div class="nextstepaction"]
> [Creare un'applicazione di esempio](./rest-proxy.md#client-application-sample)