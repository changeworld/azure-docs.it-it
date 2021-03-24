---
title: Ottimizzare i cluster con Apache Ambari in Azure HDInsight
description: Usare l'interfaccia utente Web di Apache Ambari per configurare e ottimizzare i cluster HDInsight di Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 2146ccb0c4d7f263c3e1a69db9b172649fcd25ea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863497"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Ottimizzare i cluster con Apache Ambari in Azure HDInsight

HDInsight fornisce i cluster Apache Hadoop per le applicazioni di elaborazione dei dati su larga scala. La gestione, il monitoraggio e l'ottimizzazione di questi complessi cluster multinodo possono risultare difficili. Apache Ambari è un'interfaccia Web per la gestione e il monitoraggio di cluster HDInsight Linux.

Per un'introduzione all'uso dell'interfaccia utente Web Ambari, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Accedere ad Ambari all'indirizzo `https://CLUSTERNAME.azurehdidnsight.net` con le credenziali del cluster. La schermata iniziale visualizza un dashboard generale.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png" alt-text="Visualizzazione del dashboard utente di Apache Ambari":::

L'interfaccia utente Web di Ambari viene utilizzata per gestire host, servizi, avvisi, configurazioni e visualizzazioni. Non è possibile usare Ambari per creare un cluster HDInsight o aggiornare i servizi. Non è inoltre in grado di gestire stack e versioni, rimuovere le autorizzazioni o ricommissionare host o aggiungere servizi al cluster.

## <a name="manage-your-clusters-configuration"></a>Gestire la configurazione del cluster

Le impostazioni di configurazione consentono di ottimizzare un determinato servizio. Per modificare le impostazioni di configurazione di un servizio, selezionare il servizio dalla barra laterale dei **Servizi** (a sinistra). Passare quindi alla scheda **configs (configurazioni** ) nella pagina dei dettagli del servizio.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png" alt-text="Barra laterale dei servizi Apache Ambari":::

## <a name="modify-namenode-java-heap-size"></a>Modificare le dimensioni dell'heap Java NameNode

Le dimensioni dell'heap Java NameNode dipendono da molti fattori, ad esempio il carico del cluster. Inoltre, il numero di file e il numero di blocchi. Le dimensioni predefinite, pari a 1 GB, vanno bene per la maggior parte dei cluster, anche se alcuni carichi di lavoro richiedono più o meno memoria.

Per modificare le dimensioni dell'heap Java NameNode:

1. Selezionare **HDFS** dalla barra laterale Services (Servizi) e passare alla scheda **Configs** (Configurazioni).

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png" alt-text="Configurazione di Apache Ambari HDFS":::

1. Trovare l'impostazione **NameNode Java heap size** (Dimensioni dell'heap Java NameNode). È anche possibile usare la casella di testo **filter** (filtro) per digitare e trovare una determinata impostazione. Selezionare l'icona **pen** (penna) accanto al nome dell'impostazione.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png" alt-text="Dimensioni heap Java Ambari NameNode Apache":::

1. Digitare il nuovo valore nella casella di testo, quindi premere **INVIO** per salvare la modifica.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png" alt-text="Ambari modificare NameNode heap Java size1":::

1. Le dimensioni dell'heap Java NameNode sono state modificate in 1 GB da 2 GB.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png" alt-text="Modificato NameNode heap Java size2":::

1. Salvare le modifiche facendo clic sul pulsante verde **Save** (Salva) nella parte superiore della schermata di configurazione.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png" alt-text="' Apache Ambari Save Configurations '":::

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con l'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Ottimizzare Apache HBase](./optimize-hbase-ambari.md)
* [Ottimizzare Apache Hive](./optimize-hive-ambari.md)
* [Ottimizzare Apache Pig](./optimize-pig-ambari.md)
