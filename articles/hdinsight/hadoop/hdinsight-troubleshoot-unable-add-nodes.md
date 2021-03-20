---
title: Non è possibile aggiungere nodi al cluster Azure HDInsight
description: Risolvere i problemi perché non è possibile aggiungere nodi a Apache Hadoop cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944330"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: non è possibile aggiungere nodi al cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile aggiungere nodi al cluster HDInsight di Azure.

## <a name="cause"></a>Causa

I motivi possono variare.

## <a name="resolution"></a>Soluzione

Usando la funzionalità [dimensioni cluster](../hdinsight-scaling-best-practices.md) , calcolare il numero di core aggiuntivi necessari per il cluster. Questo valore si basa sul numero totale di core nei nuovi nodi del ruolo di lavoro. Quindi, provare uno o più dei passaggi seguenti:

* Verificare se sono presenti core disponibili nella posizione del cluster.

* Osservare il numero di core disponibili in altre località. Provare a ricreare il cluster in un'altra località con un numero di core disponibili sufficiente.

* Per aumentare la quota di core per una località specifica, aprire un ticket di supporto per un aumento della quota di core per HDInsight.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]