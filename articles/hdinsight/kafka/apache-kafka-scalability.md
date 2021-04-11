---
title: Aumento della scalabilità in Apache Kafka - Azure HDInsight
description: Informazioni su come configurare i dischi gestiti per i cluster di Apache Kafka in Azure HDInsight per aumentare la scalabilità.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 9aa11be42aca59458fea0462a90b6aeb70df893d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863140"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurare l'archiviazione e la scalabilità per Apache Kafka in HDInsight

Questo articolo spiega come configurare il numero di dischi gestiti usati da [Apache Kafka](https://kafka.apache.org/) in HDInsight.

Kafka in HDInsight usa il disco locale delle macchine virtuali nel cluster HDInsight. Dal momento che in Kafka i processi I/O sono intensivi, viene usata la funzionalità [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) per assicurare una velocità effettiva elevata e fornire maggiore spazio di archiviazione per ogni nodo. Se si usano le tradizionali unità disco rigido virtuali (VHD) per Kafka, ogni nodo è limitato a 1 TB. Con i dischi gestiti, è possibile usare più dischi per ottenere 16 TB per ogni nodo del cluster.

Il diagramma seguente offre un confronto tra Kafka in HDInsight prima dei dischi gestiti e Kafka in HDInsight con i dischi gestiti:

:::image type="content" source="./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png" alt-text="Kafka con architettura Managed Disks" border="false":::

## <a name="configure-managed-disks-azure-portal"></a>Configurare i dischi gestiti: portale di Azure

1. Seguire i passaggi riportati in [Creare un cluster HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md) per comprendere le operazioni principali per creare un cluster tramite il portale. Non completare il processo di creazione del portale.

2. Nella sezione **Configuration & pricing** usare il campo __Number of nodes__ per configurare il numero di dischi.

    > [!NOTE]  
    > Il tipo di disco gestito può essere __Standard__ (HDD) o __Premium__ (SSD). I dischi Premium sono usati con le macchine virtuali serie DS e GS. Tutti gli altri tipi di macchine virtuali usano dischi Standard.

    :::image type="content" source="./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png" alt-text="sezione dimensioni cluster con i dischi per nodo del ruolo di lavoro evidenziato" border="true":::

## <a name="configure-managed-disks-resource-manager-template"></a>Configurare i dischi gestiti: modello di Resource Manager

Per controllare il numero di dischi usati dai nodi del ruolo di lavoro in un cluster Kafka, usare la sezione seguente del modello:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

È possibile trovare un modello completo che illustra come configurare Managed disks in [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso della gestione di Apache Kafka in HDInsight, vedere i documenti seguenti:

* [Usare MirrorMaker per creare una replica di Apache Kafka in HDInsight](apache-kafka-mirroring.md)
* [Usare Apache Storm con Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Usare Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Connettersi ad Apache Kafka tramite una rete virtuale di Azure](apache-kafka-connect-vpn-gateway.md)

* [Blog di HDInsight sui dischi gestiti con Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
