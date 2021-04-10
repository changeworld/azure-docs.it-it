---
title: NoClassDefFoundError-Apache Spark con Apache Kafka dati in Azure HDInsight
description: Apache Spark processo di streaming che legge i dati da un cluster Apache Kafka ha esito negativo con un NoClassDefFoundError in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946383"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Spark processo di streaming che legge Apache Kafka i dati ha esito negativo con NoClassDefFoundError in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Il cluster Apache Spark esegue un processo di Spark streaming che legge i dati da un cluster Apache Kafka. Il processo di Spark streaming non riesce se la compressione del flusso Kafka è attivata. In questo caso, l'applicazione Spark streaming Yarn application_1525986016285_0193 non riuscita a causa di un errore:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Causa

Questo errore può essere causato dalla specifica di una versione del `spark-streaming-kafka` file jar diversa dalla versione del cluster Kafka in esecuzione.

Se ad esempio si esegue una versione del cluster Kafka 0.10.1, il comando seguente genererà un errore:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Soluzione

Usare il comando Spark-Submit con l' `–packages` opzione e assicurarsi che la versione del file jar di Spark-streaming-Kafka corrisponda alla versione del cluster Kafka in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]