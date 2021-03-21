---
title: Messaggio di errore non visualizzato nella visualizzazione Apache Hive-Azure HDInsight
description: La query ha esito negativo in Apache Hive visualizzazione senza dettagli sul cluster HDInsight di Azure.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931002"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenario: il messaggio di errore della query non viene visualizzato nella visualizzazione Apache Hive in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il messaggio di errore della query di visualizzazione Apache Hive sarà simile al seguente, senza ulteriori informazioni:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Causa

A volte il messaggio di errore di un errore di query può essere troppo grande per essere visualizzato nella pagina principale della vista hive.

## <a name="resolution"></a>Soluzione

Controllare la scheda notifiche nell'angolo superiore destro della Hive_view per visualizzare il messaggio di errore e di StackTrace completo.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]