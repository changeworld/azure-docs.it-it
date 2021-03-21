---
title: Introduzione al controllo delle versioni-Azure HDInsight
description: Informazioni sul funzionamento del controllo delle versioni in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493867"
---
# <a name="how-versioning-works-in-hdinsight"></a>Funzionamento del controllo delle versioni in HDInsight

Il servizio HDInsight ha due componenti principali: un provider di risorse e Apache Hadoop componenti distribuiti in un cluster. 

## <a name="hdinsight-resource-provider"></a>Provider di risorse HDInsight

Le risorse in Azure vengono rese disponibili da un provider di risorse. Il provider di risorse HDInsight è responsabile della creazione, della gestione e dell'eliminazione di cluster.

## <a name="hdinsight-images"></a>Immagini HDInsight

HDInsight usa le immagini per riunire i componenti software open source (OSS) che possono essere distribuiti in un cluster. Queste immagini contengono il sistema operativo Ubuntu di base e i componenti principali, ad esempio Spark, Hadoop, Kafka, HBase o hive.

## <a name="versioning-in-hdinsight"></a>Controllo delle versioni in HDInsight

Microsoft aggiorna periodicamente le immagini e il provider di risorse HDInsight per includere nuovi miglioramenti e funzionalità.

È possibile creare una nuova versione di HDInsight quando si verificano una o più delle condizioni seguenti:

- Modifiche o aggiornamenti principali della funzionalità del provider di risorse HDInsight
- Versioni principali dei componenti OSS
- Principali modifiche al sistema operativo Ubuntu

Una nuova versione dell'immagine viene creata quando si verificano una o più delle condizioni seguenti:

- Versioni principali o secondarie e aggiornamenti dei componenti OSS
- Patch o correzioni per un componente nell'immagine

## <a name="next-steps"></a>Passaggi successivi

- [Componenti e versioni Apache in HDInsight](./hdinsight-component-versioning.md)
