---
title: 'Guida introduttiva: creare un cluster Azure Istanza gestita per Apache Cassandra dalla portale di Azure'
description: Questa Guida introduttiva illustra come creare un cluster Azure Istanza gestita per Apache Cassandra usando il portale di Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: d94bedad1ba7a2c6d814021b733404ccc58148ed
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424683"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Guida introduttiva: creare un cluster Azure Istanza gestita per Apache Cassandra dall'portale di Azure (anteprima)
 
Azure Istanza gestita per Apache Cassandra fornisce operazioni automatiche di distribuzione e scalabilità per i Data Center di Apache Cassandra Open Source gestiti, accelerando gli scenari ibridi e riducendo la manutenzione continuativa.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa Guida introduttiva illustra come usare la portale di Azure per creare un cluster Azure Istanza gestita per Apache Cassandra.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Creare un cluster di istanze gestite

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Dalla barra di ricerca cercare **istanza gestita per Apache Cassandra** e selezionare il risultato.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Cercare Istanza gestita per Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Selezionare il pulsante **crea istanza gestita per il cluster Apache Cassandra** .

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Creare il cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Dal riquadro **crea istanza gestita per Apache Cassandra** immettere i dettagli seguenti:

   * **Sottoscrizione** : dall'elenco a discesa selezionare la sottoscrizione di Azure.
   * **Gruppo di risorse**: specificare se si vuole creare un nuovo gruppo di risorse o utilizzarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere l'articolo Panoramica del [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) .
   * **Nome cluster** : immettere un nome per il cluster.
   * **Location** : percorso in cui verrà distribuito il cluster.
   * **SKU** : tipo di SKU per il cluster.
   * **No. dei nodi**: numero di nodi in un cluster. Questi nodi fungono da repliche per i dati.
   * **Password amministratore Cassandra iniziale** : password usata per creare il cluster.
   * **Conferma password amministratore Cassandra** : immettere nuovamente la password.

    > [!NOTE]
    > Durante l'anteprima pubblica, è possibile creare il cluster di istanze gestite nelle aree *Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Europa settentrionale, Europa occidentale, Asia orientale meridionale e Australia orientale* .

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Compilare il modulo Crea cluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Selezionare quindi la scheda **rete** .

1. Nel riquadro **rete** scegliere il nome e la **subnet** della **rete virtuale** . È possibile selezionare una rete virtuale esistente o crearne una nuova.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Configurare i dettagli di rete." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Se nel passaggio precedente è stato creato un nuovo VNet, andare al passaggio 8. Se è stato selezionato un VNet esistente, prima di creare il cluster, è necessario applicare alcune autorizzazioni speciali per la rete virtuale e la subnet. A tale scopo, utilizzare il `az role assignment create` comando, sostituendo `<subscription ID>` , `<resource group name>` , `<VNet name>` e `<subnet name>` con i valori appropriati:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > I `assignee` `role` valori e nel comando precedente sono rispettivamente un principio di servizio fisso e gli identificatori di ruolo.

1. A questo punto, dopo aver completato la rete, fare clic su **Verifica + crea**  >  **creazione**

    > [!NOTE]
    > Per la creazione del cluster possono essere necessari fino a 15 minuti.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Esaminare il riepilogo per creare il cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Al termine della distribuzione, controllare il gruppo di risorse per visualizzare il cluster di istanze gestite appena creato:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Pagina Panoramica dopo la creazione del cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Per esplorare i nodi del cluster, passare al riquadro rete virtuale usato per creare il cluster e aprire il riquadro **Panoramica** per visualizzarli:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Visualizzare le risorse del cluster." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>Connessione al cluster

Azure Istanza gestita per Apache Cassandra non crea nodi con indirizzi IP pubblici. Pertanto, per connettersi al cluster Cassandra appena creato, sarà necessario creare un'altra risorsa all'interno della VNet. Potrebbe trattarsi di un'applicazione o di una macchina virtuale con lo strumento di query open source di Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) installato. È possibile usare un [modello](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) per distribuire una macchina virtuale Ubuntu. Quando viene distribuito, usare SSH per connettersi al computer e installare CQLSH usando i comandi seguenti:

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

Se non si intende continuare a usare questo cluster di istanze gestite, eliminarlo con i passaggi seguenti:

1. Dal menu a sinistra di portale di Azure selezionare **gruppi di risorse**.
1. Selezionare nell'elenco il gruppo di risorse creato in questa guida di avvio rapido.
1. Nel riquadro **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Nella finestra successiva immettere il nome del gruppo di risorse da eliminare e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato illustrato come creare un Istanza gestita di Azure per un cluster Apache Cassandra usando portale di Azure. È ora possibile iniziare a usare il cluster:

> [!div class="nextstepaction"]
> [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)
