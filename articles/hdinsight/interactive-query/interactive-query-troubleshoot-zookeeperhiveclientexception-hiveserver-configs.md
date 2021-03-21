---
title: Errore dell'interprete di Apache Hive Zeppelin-Azure HDInsight
description: L'interprete JDBC per Apache Zeppelin hive sta puntando all'URL errato in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930753"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenario: l'interprete di Apache Hive Zeppelin restituisce un errore Zookeeper in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

In un cluster Apache Hive LLAP, l'interprete Zeppelin fornisce il messaggio di errore seguente quando si tenta di eseguire una query:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Causa

L'interprete JDBC di Zeppelin hive sta puntando all'URL errato.

## <a name="resolution"></a>Soluzione

1. Passare al riepilogo del componente hive e copiare "URL JDBC hive" negli Appunti.

1. Passare a `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`.

1. Modificare le impostazioni JDBC: aggiornare il valore hive. URL con l'URL JDBC di hive copiato nel passaggio 1

1. Salvare, quindi riprovare a eseguire la query

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]