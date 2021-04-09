---
title: RpcTimeoutException per Apache Spark parsimonioso-Azure HDInsight
description: Vengono visualizzati 502 errori durante l'elaborazione di set di dati di grandi dimensioni con Apache Spark server di risparmio
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932698"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException per il server di Apache Spark parsimonia in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

L'applicazione Spark ha esito negativo con un' `org.apache.spark.rpc.RpcTimeoutException` eccezione e un messaggio: `Futures timed out` , come nell'esempio seguente:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError``overhead limit exceeded`gli errori e possono anche essere visualizzati in `sparkthriftdriver.log` come nell'esempio seguente:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Questi errori sono causati da risorse di memoria insufficienti durante l'elaborazione dei dati. Se il processo di Garbage Collection Java viene avviato, l'applicazione Spark potrebbe smettere di rispondere. Le query inizieranno a timeout e arresteranno l'elaborazione. L' `Futures timed out` errore indica un cluster sottoposto a gravi stress.

## <a name="resolution"></a>Soluzione

Aumentare le dimensioni del cluster aggiungendo più nodi di lavoro o aumentando la capacità di memoria dei nodi del cluster esistenti. È anche possibile modificare la pipeline di dati per ridurre la quantità di dati elaborati in una sola volta.

`spark.network.timeout`Controlla il timeout per tutte le connessioni di rete. L'aumento del timeout di rete potrebbe consentire più tempo per il completamento di alcune operazioni critiche, ma il problema non verrà risolto completamente.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]