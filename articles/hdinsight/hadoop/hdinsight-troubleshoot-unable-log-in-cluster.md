---
title: Non è possibile accedere al cluster Azure HDInsight
description: Risolvere i problemi perché non è possibile accedere a Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944313"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: non è possibile accedere al cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile accedere al cluster HDInsight di Azure.

## <a name="cause"></a>Causa

I motivi possono variare. Tenere presente che quando si accede al cluster o ai dashboard dell'app, usare l'account di accesso del cluster o le credenziali HTTP. Se ci si connette in modalità remota, usare le credenziali Secure Shell (SSH) o Desktop remoto.

## <a name="resolution"></a>Soluzione

Per risolvere i problemi comuni, provare le procedure seguenti.

* Provare ad aprire il dashboard del cluster in modalità anonima in una nuova scheda del browser.

* Se non è possibile richiamare le credenziali SSH, è possibile [reimpostare le credenziali all'interno dell'interfaccia utente di Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]