---
title: 'Guida introduttiva: distribuire un cluster Apache Spark gestito con Azure Databricks'
description: Questa Guida introduttiva illustra come distribuire un cluster di Apache Spark gestiti con Azure Databricks tramite il portale di Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748709"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Guida introduttiva: distribuire un cluster Apache Spark gestito (anteprima) con Azure Databricks

Azure Istanza gestita per Apache Cassandra fornisce operazioni automatiche di distribuzione e scalabilità per i Data Center di Apache Cassandra Open Source gestiti, accelerando gli scenari ibridi e riducendo la manutenzione continuativa.

> [!IMPORTANT]
> Azure Istanza gestita per Apache Cassandra è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa Guida introduttiva illustra come usare la portale di Azure per creare un cluster Apache Spark completamente gestito all'interno della rete virtuale di Azure del Istanza gestita di Azure per il cluster Apache Cassandra. Si creerà il cluster Spark in Azure Databricks. In un secondo momento è possibile creare o aggiungere notebook al cluster, leggere dati da origini dati diverse e analizzare le informazioni dettagliate.

È anche possibile ottenere altre informazioni con istruzioni dettagliate sulla [distribuzione di Azure Databricks nella rete virtuale di Azure (inserimento di reti virtuali)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Creare un cluster di Azure Databricks

Seguire questa procedura per creare un cluster Azure Databricks in una rete virtuale con Azure Istanza gestita per Apache Cassandra:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel NAV di sinistra individuare i **gruppi di risorse** e passare al gruppo di risorse che contiene la rete virtuale in cui è distribuita l'istanza gestita.

1. Aprire la risorsa **rete virtuale** e prendere nota dello **spazio degli indirizzi**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Ottenere lo spazio degli indirizzi della rete virtuale." border="true":::

1. Dal gruppo di risorse selezionare **Aggiungi** e cercare **Azure Databricks** nel campo di ricerca:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Cercare Azure Databricks." border="true":::

1. Selezionare **Crea** per creare un account Azure Databricks:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Creare un account Azure Databricks." border="true":::

1. Inserire i valori seguenti:

   * **Nome area di lavoro** : specificare un nome per l'area di lavoro di databricks.
   * **Region** : assicurarsi di selezionare la stessa area della rete virtuale.
   * Piano **tariffario** : scegliere tra standard, Premium o versione di valutazione. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Immettere il nome dell'area di lavoro, l'area e il piano tariffario per l'account databricks." border="true":::

1. Selezionare quindi la scheda **rete** e specificare i dettagli seguenti:

   * **Distribuire Azure Databricks area di lavoro nella rete virtuale (VNet)** -selezionare **Sì**.
   * **Rete virtuale** : dall'elenco a discesa scegliere la rete virtuale in cui risiede l'istanza gestita.
   * **Nome della subnet pubblica** : immettere un nome per la subnet pubblica.
   * **Intervallo CIDR della subnet pubblica** : immettere un intervallo di indirizzi IP per la subnet pubblica.
   * **Nome subnet privata** : immettere un nome per la subnet privata.
   * **Intervallo CIDR della subnet privata** : immettere un intervallo di indirizzi IP per la subnet privata.

   Per evitare conflitti di intervallo, assicurarsi di selezionare intervalli più elevati. Se necessario, utilizzare una [calcolatrice della subnet visiva](https://www.fryguy.net/wp-content/tools/subnets.html) per dividere gli intervalli:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Usare il calcolatore subnet della rete virtuale." border="true":::

   La schermata seguente mostra i dettagli di esempio nel riquadro rete:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Specificare i nomi delle subnet pubblica e privata." border="true":::

1. Selezionare **Verifica e crea** e quindi **Crea** per distribuire l'area di lavoro.

1. **Avviare l'area di lavoro** dopo che è stata creata.

1. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **New Cluster** (Nuovo cluster).

1. Nel riquadro **nuovo cluster** accettare i valori predefiniti per tutti i campi diversi dai campi seguenti:

   * **Nome cluster** : immettere un nome per il cluster.
   * **Versione Databricks Runtime** : selezionare Scala 2,11 o versione precedente supportata dal connettore Cassandra.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Selezionare la versione del runtime di databricks e il cluster Spark." border="true":::

1. Espandere **Opzioni avanzate** e aggiungere la configurazione seguente. Assicurarsi di sostituire gli indirizzi IP e le credenziali del nodo:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. Dalla scheda **librerie** installare la versione più recente di Spark Connector per Cassandra (*Spark-Cassandra-Connector*) e riavviare il cluster:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Installare il connettore Cassandra." border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questo cluster di istanze gestite, eliminarlo con i passaggi seguenti:

1. Dal menu a sinistra di portale di Azure selezionare **gruppi di risorse**.
1. Selezionare nell'elenco il gruppo di risorse creato in questa guida di avvio rapido.
1. Nel riquadro **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
3. Nella finestra successiva immettere il nome del gruppo di risorse da eliminare e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come creare un cluster Apache Spark completamente gestito all'interno della rete virtuale di Azure Istanza gestita per il cluster Apache Cassandra. Successivamente, è possibile apprendere come gestire le risorse del cluster e del Data Center: 

> [!div class="nextstepaction"]
> [Gestire Azure Istanza gestita per le risorse Apache Cassandra usando l'interfaccia della riga di comando di Azure](manage-resources-cli.md)

