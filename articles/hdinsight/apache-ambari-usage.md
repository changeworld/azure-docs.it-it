---
title: Utilizzo di Apache Ambari in Azure HDInsight
description: Discussione sull'uso di Apache Ambari in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2021
ms.openlocfilehash: 6233c42d7fbf8dc7821d26f77171c44485fb8d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946940"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Utilizzo di Apache Ambari in Azure HDInsight

HDInsight usa Apache Ambari per la distribuzione e la gestione dei cluster. Gli agenti Ambari vengono eseguiti in ogni nodo (nodo Head, nodo del ruolo di lavoro, Zookeeper e nodo perimetrale se esistente). Il server Ambari viene eseguito solo in nodo Head (hn0 o HN1). Viene eseguita una sola istanza del server Ambari alla volta. Questa operazione è controllata dal controller di failover di HDInsight. Quando uno dei nodi head è inattivo per il riavvio o la manutenzione, le altre nodo head diventeranno attive e il server Ambari nel secondo nodo head verrà avviato.

Tutte le configurazioni del cluster devono essere eseguite tramite l' [interfaccia utente di Ambari](./hdinsight-hadoop-manage-ambari.md). eventuali modifiche locali verranno sovrascritte al riavvio del nodo.

## <a name="failover-controller-services"></a>Servizi del controller di failover

Il controller di failover di HDInsight è anche responsabile dell'aggiornamento dell'indirizzo IP dell'host nodo Head, che fa riferimento al nodo head attivo corrente. Tutti gli agenti Ambari sono configurati per segnalare lo stato e l'heartbeat all'host nodo head. Il controller di failover è un set di servizi in esecuzione in ogni nodo del cluster, se non sono in esecuzione, il failover di nodo head potrebbe non funzionare correttamente e si otterrà HTTP 502 quando si tenta di accedere al server di Ambari.

Per verificare quale nodo Head è attivo, è possibile eseguire ssh in uno dei nodi del cluster, quindi eseguire `ping headnodehost` e confrontare l'IP con quello delle due nodi head.

Se i servizi del controller di failover non sono in esecuzione, il failover di nodo head potrebbe non essere eseguito correttamente, il che potrebbe non essere in esecuzione nel server Ambari. Per verificare se i servizi del controller di failover sono in esecuzione, eseguire:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Log

Nel nodo head attivo è possibile controllare i log del server Ambari all'indirizzo:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

In tutti i nodi del cluster è possibile controllare i log dell'agente Ambari all'indirizzo:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sequenze di avvio del servizio

Questa è la sequenza di avvio del servizio al momento dell'avvio:

1. HDInsight-Agent avvia i servizi del controller di failover.
1. I servizi del controller di failover avviano l'agente Ambari in ogni nodo e server Ambari in nodo head attivo.

## <a name="ambari-database"></a>Database Ambari

HDInsight crea un database nel database SQL sotto la cappa per fungere da database per il server Ambari. Il [livello di servizio predefinito è S0](../azure-sql/database/elastic-pool-scale.md).

Per qualsiasi cluster con numero di nodi di lavoro maggiore di 16 quando si crea il cluster, S2 è il livello di servizio del database.

## <a name="takeaway-points"></a>Punti da asporto

Non eseguire mai manualmente i servizi Ambari-server o Ambari-Agent, a meno che non si stia tentando di riavviare il servizio per risolvere un problema. Per forzare un failover, è possibile riavviare il nodo head attivo.

Non modificare mai manualmente i file di configurazione in qualsiasi nodo del cluster, quindi consentire all'interfaccia utente di Ambari di eseguire il processo.

## <a name="property-values-in-esp-clusters"></a>Valori delle proprietà nei cluster ESP

In HDInsight 4,0 Enterprise Security Package cluster usare le pipe `|` anziché le virgole come delimitatori di variabile. Di seguito è riportato un esempio:

```
Property Key: hive.security.authorization.sqlstd.confwhitelist.append
Property Value: environment|env|dl_data_dt
```

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
