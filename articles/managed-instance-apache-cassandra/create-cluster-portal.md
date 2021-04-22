---
title: 'Guida introduttiva: Creare Istanza gestita azure per il cluster Apache Cassandra dal portale di Azure'
description: Questa guida introduttiva illustra come creare un cluster azure Istanza gestita per Apache Cassandra usando il portale di Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 315b505e9de853fbe4663eacdfe929149dbaa458
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873146"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Avvio rapido: Creare un cluster azure Istanza gestita per Apache Cassandra dal portale di Azure (anteprima)
 
Azure Istanza gestita per Apache Cassandra offre operazioni di distribuzione e ridimensionamento automatizzate per data center Apache Cassandra open source gestiti, accelerando gli scenari ibridi e riducendo la manutenzione continua.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida introduttiva illustra come usare il portale di Azure per creare un cluster azure Istanza gestita per Apache Cassandra.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Creare un cluster di istanza gestita

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nella barra di ricerca cercare Istanza gestita **Apache Cassandra** e selezionare il risultato.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Cercare Istanza gestita per Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Selezionare **crea Istanza gestita per il cluster Apache Cassandra.**

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Creare il cluster." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Nel riquadro **Crea Istanza gestita per Apache Cassandra** immettere i dettagli seguenti:

   * **Sottoscrizione:** nell'elenco a discesa selezionare la sottoscrizione di Azure.
   * **Gruppo di risorse:** specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere l'articolo Panoramica del gruppo di risorse di [Azure.](../azure-resource-manager/management/overview.md)
   * **Nome cluster:** immettere un nome per il cluster.
   * **Località:** posizione in cui verrà distribuito il cluster.
   * **SKU:** tipo di SKU per il cluster.
   * **No. di nodi:** numero di nodi in un cluster. Questi nodi fungono da repliche per i dati.
   * **Password amministratore cassandra iniziale:** password usata per creare il cluster.
   * **Confermare la password dell'amministratore di Cassandra:** reimrla.

    > [!NOTE]
    > Durante l'anteprima pubblica, è possibile creare il cluster dell'istanza gestita nelle aree Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali *2,* Stati Uniti centrali, Stati Uniti centro-meridionali, Europa settentrionale, Europa occidentale, Asia orientale meridionale e Australia orientale.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Compilare il modulo crea cluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Selezionare quindi la **scheda** Rete.

1. Nel riquadro **Rete** scegliere il nome **della rete virtuale e** la **subnet**. È possibile selezionare una rete virtuale esistente o crearne una nuova.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Configurare i dettagli di rete." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > La distribuzione di un'istanza di Azure Istanza gestita per Apache Cassandra richiede l'accesso a Internet. La distribuzione non riesce negli ambienti in cui l'accesso a Internet è limitato. Assicurarsi di non bloccare l'accesso all'interno della rete virtuale ai servizi di Azure fondamentali seguenti, necessari per il corretto funzionamento di Cassandra gestito:
    > - Archiviazione di Azure
    > - Azure Key Vault
    > - Set di scalabilità delle macchine virtuali di Azure
    > - Monitoraggio di Azure
    > - Azure Active Directory
    > - Sicurezza di Azure

1. Se è stata creata una nuova rete virtuale nel passaggio precedente, andare al passaggio 8. Se è stata selezionata una rete virtuale esistente, prima di creare il cluster è necessario applicare alcune autorizzazioni speciali alla rete virtuale e alla subnet. A tale scopo, usare il `az role assignment create` comando , sostituendo , e con i valori `<subscription ID>` `<resource group name>` `<VNet name>` appropriati:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > I `assignee` valori e nel comando precedente sono valori `role` fissi. Immettere questi valori esattamente come indicato nel comando. Se non si esegue questa operazione, si verificano errori durante la creazione del cluster. Se si verificano errori durante l'esecuzione di questo comando, è possibile che non si abbia le autorizzazioni per eseguirlo. Contattare l'amministratore per ottenere le autorizzazioni.

1. Ora che la rete è terminata, fare clic **su Rivedi e crea**  >  **crea**

    > [!NOTE]
    > La creazione del cluster può richiedere fino a 15 minuti.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Esaminare il riepilogo per creare il cluster." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Al termine della distribuzione, controllare il gruppo di risorse per visualizzare il cluster dell'istanza gestita appena creato:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Pagina di panoramica dopo la creazione del cluster." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Per esplorare i nodi del cluster, passare al riquadro Rete virtuale usato per creare il cluster e aprire il riquadro **Panoramica** per visualizzarli:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Visualizzare le risorse del cluster." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Connessione al cluster

Azure Istanza gestita per Apache Cassandra non crea nodi con indirizzi IP pubblici, quindi per connettersi al cluster Cassandra appena creato, è necessario creare un'altra risorsa all'interno della rete virtuale. Potrebbe trattarsi di un'applicazione o di una macchina virtuale con lo strumento di query open source di Apache [installato CQLSH.](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) È possibile usare un [modello](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) per distribuire una macchina virtuale Ubuntu. Quando viene distribuito, usare SSH per connettersi al computer e installare CQLSH usando i comandi seguenti:

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
> L Azure Cosmos DB ascarico del ruolo viene usato solo a scopo di distribuzione. Istanza gestita di Azure per Apache Cassandra non ha dipendenze back-end Azure Cosmos DB.   

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continuerà a usare questo cluster di istanza gestita, eliminarlo con la procedura seguente:

1. Nel menu a sinistra di portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare nell'elenco il gruppo di risorse creato in questa guida di avvio rapido.
1. Nel riquadro Panoramica del gruppo **di** risorse selezionare **Elimina gruppo di risorse**.
1. Nella finestra successiva immettere il nome del gruppo di risorse da eliminare e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un cluster azure Istanza gestita per Apache Cassandra usando portale di Azure. È ora possibile iniziare a usare il cluster:

> [!div class="nextstepaction"]
> [Distribuire un cluster Apache Spark gestito con Azure Databricks](deploy-cluster-databricks.md)
