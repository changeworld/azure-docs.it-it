---
title: Il comando di importazione/esportazione di Sqoop non riesce per alcuni utenti nei cluster ESP-Azure HDInsight
description: "Il comando di importazione/esportazione di Apache Sqoop ha esito negativo con l'errore \"importazione non riuscita: Java. io. IOException: la proprietà nella directory di staging/User/yourusername/.staging non è come previsto\" per alcuni utenti nel cluster ESP di Azure HDInsight"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387357"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Scenario: il comando di importazione/esportazione di Sqoop non riesce per i nomi utente con più di 20 caratteri nei cluster ESP di Azure HDInsight

Questo articolo descrive un problema noto e una soluzione alternativa quando si usano i cluster abilitati per Azure HDInsight ESP (Enterprise Security Pack) con l'account di archiviazione ADLS Gen2 (ABFS).

## <a name="issue"></a>Problema

Quando si esegue il comando di importazione/esportazione Sqoop, l'errore si verifica con l'errore riportato di seguito per alcuni utenti:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

Nell'esempio precedente, `/user/yourlongdomainuserna/.staging` Visualizza il nome utente di 20 caratteri troncato per il nome utente `yourlongdomainusername` .

## <a name="cause"></a>Causa

La lunghezza del nome utente supera i 20 caratteri. 

Per ulteriori informazioni, fare riferimento al [modo in cui gli oggetti e le credenziali vengono sincronizzati in un Azure Active Directory Domain Services dominio gestito](../active-directory-domain-services/synchronization.md) .

## <a name="workaround"></a>Soluzione alternativa

Usare un nome utente minore o uguale a 20 caratteri.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
