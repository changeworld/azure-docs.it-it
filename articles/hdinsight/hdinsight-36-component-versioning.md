---
title: Componenti e versioni di Apache Hadoop-Azure HDInsight 3,6
description: Informazioni sui componenti e le versioni di Apache Hadoop in Azure HDInsight 3,6.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727689"
---
# <a name="hdinsight-36-component-versions"></a>Versioni del componente HDInsight 3,6

Questo articolo illustra i componenti e le versioni dell'ambiente Apache Hadoop in Azure HDInsight 3,6.

## <a name="support-for-hdinsight-36"></a>Supporto per HDInsight 3,6

A partire dal 1 ° luglio 2021 Microsoft offrirà supporto Basic per determinati tipi di cluster HDI 3,6.
La tabella seguente elenca l'intervallo di tempo di supporto per i tipi di cluster HDInsight 3,6.

| Tipo di cluster                    | Versione del Framework | Scadenza supporto standard       | Data di scadenza supporto Basic | Data di ritiro |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 30 giugno 2021                     | 3 aprile 2022                | 4 aprile 2022 |
| HDInsight 3,6 Spark             | 2.3               | 30 giugno 2021                     | 3 aprile 2022                | 4 aprile 2022 |
| HDInsight 3,6 Kafka             | 1.1               | 30 giugno 2021                     | 3 aprile 2022                | 4 aprile 2022 |
| HDInsight 3,6 HBase             | 1.1               | 30 giugno 2021                     | 3 aprile 2022                | 4 aprile 2022 |
| HDInsight 3,6-query interattiva | 2.1               | 30 giugno 2021                     | 3 aprile 2022                | 4 aprile 2022 |
| HDInsight 3,6 Storm             | 1.1               | 30 giugno 2021                     | 3 aprile 2022                | 4 aprile 2022 |
| Servizi HDInsight 3,6 ML      | 9.3               | -                                 | -                            | 31 dicembre 2020 |
| HDInsight 3,6 Spark             | 2.2               | -                                 | -                            | 30 giugno 2020 |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | 30 giugno 2020 |
| HDInsight 3,6 Kafka             | 1.0               | -                                 | -                            | 30 giugno 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Componenti Apache disponibili con HDInsight versione 3,6

Le versioni del componente OSS associate a HDInsight 3,6 sono elencate nella tabella seguente.

| Componente              | HDInsight 3.6 (predefinito)     |
|------------------------|-----------------------------|
| Apache Hadoop e YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 in ESP Interactive query) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0+                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
