---
title: Apache Hive le connessioni ad Apache Zookeeper-Azure HDInsight
description: Visualizzazione Apache Hive inaccessibile a causa di problemi di Apache Zookeeper in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939303"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scenario: Apache Hive non riesce a stabilire una connessione ad Apache Zookeeper in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

La visualizzazione hive non è accessibile e i log in `/var/log/hive` mostrano un errore simile al seguente:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Causa

È possibile che hive potrebbe non riuscire a stabilire una connessione a Zookeeper, impedendo l'avvio della visualizzazione hive.

## <a name="resolution"></a>Soluzione

1. Verificare che il servizio Zookeeper sia integro.

1. Controllare se il servizio Zookeeper ha una voce elemento znode per hive Server2. Il valore sarà mancante o non corretto.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Per ristabilire la connettività, riavviare i nodi Zookeeper e riavviare HiveServer2.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]