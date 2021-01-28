---
title: Avvisi directory di Apache Ambari in Azure HDInsight
description: Discussione e analisi delle possibili cause e delle soluzioni per gli avvisi directory di Apache Ambari in HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943279"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Scenario: avvisi directory di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Si ricevono errori da Apache Ambari simili a:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Causa

Le directory indicate dall'avviso Ambari non sono presenti nei nodi del ruolo di lavoro interessati.

## <a name="resolution"></a>Soluzione

Creare manualmente directory mancanti nei nodi di lavoro interessati.

1. Eseguire SSH sul nodo del ruolo di lavoro pertinente.

1. Ottenere l'utente root: `sudo su` .

1. Creare le directory necessarie in modo ricorsivo.

1. Modificare il proprietario e il gruppo per le directory.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Dall'interfaccia utente di Apache Ambari disabilitare e quindi abilitare Alert.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]