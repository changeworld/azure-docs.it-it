---
title: Join in Apache Hive genera un errore OutOfMemory-Azure HDInsight
description: Gestione degli errori OutOfMemory "limite sovraccarico GC superato errore"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935960"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenario: i join in Apache Hive generano un errore OutOfMemory in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti interattivi di query nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il comportamento predefinito per i join Apache Hive consiste nel caricare l'intero contenuto di una tabella in memoria, in modo da poter eseguire un join senza dover eseguire un passaggio di mapping/riduzione. Se la tabella hive è troppo grande per adattarsi alla memoria, la query potrebbe non riuscire.

## <a name="cause"></a>Causa

Quando si eseguono join in hive di dimensioni sufficienti, viene visualizzato l'errore seguente:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Soluzione

Impedire a hive di caricare le tabelle in memoria nei join, eseguendo invece un passaggio di mapping/riduzione, impostando il valore di configurazione hive seguente:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Passaggi successivi

Se l'impostazione di questo valore non ha risolto il problema, visitare uno dei seguenti...

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).