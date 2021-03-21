---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni su componenti e versioni di Apache Hadoop in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 53ca2ac73fdec9d3b39ffc04cbb24aca707a72eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490450"
---
# <a name="azure-hdinsight-versions"></a>Versioni di Azure HDInsight

HDInsight aggrega Apache Hadoop componenti dell'ambiente e la piattaforma HDInsight in un pacchetto distribuito in un cluster. Per altri dettagli, vedere funzionamento del [controllo delle versioni di HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight

Questa tabella elenca le versioni di HDInsight disponibili nell'portale di Azure e altri metodi di distribuzione come PowerShell, CLI e .NET SDK.

| Versione HDInsight | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24 settembre 2018 | | |Sì |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 aprile 2017      | * 30 giugno 2021 |30 giugno 2021 |Sì |

* L'intervallo di tempo di supporto per determinati tipi di cluster HDInsight 3,6 verrà esteso. Vedere [versioni del componente HDInsight 3,6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Opzioni di supporto per le versioni di HDInsight

HDInsight offre supporto standard definito come periodo di tempo in cui una versione di HDInsight è supportata dal servizio supporto tecnico clienti Microsoft.

Per **scadenza del supporto** si intende che Microsoft non fornisce più supporto per la versione specifica di HDInsight. Inoltre, questa versione non è più disponibile tramite il portale di Azure per la creazione di cluster.

**Ritiro** significa che i cluster esistenti di una versione di HDInsight continuano a essere eseguiti così come sono. Non è possibile creare in alcun modo nuovi cluster di questa versione, neanche tramite l'interfaccia della riga di comando e gli SDK. Le altre funzionalità del piano di controllo, ad esempio la scalabilità manuale e la scalabilità automatica, non funzionano dopo la data di ritiro. Il supporto non è disponibile per le versioni ritirate.

## <a name="versioning-considerations"></a>Considerazioni sul controllo delle versioni
- Una volta distribuito un cluster con un'immagine, il cluster non viene aggiornato automaticamente alla versione più recente dell'immagine. Quando si creano nuovi cluster, viene distribuita la versione più recente dell'immagine.
- I clienti devono testare e verificare che le applicazioni vengano eseguite correttamente quando si usa la nuova versione di HDInsight.
- HDInsight si riserva il diritto di modificare la versione predefinita senza preavviso. Se si dispone di una dipendenza della versione, specificare la versione di HDInsight durante la creazione dei cluster.
- HDInsight può ritirare una versione del componente OSS prima di rimuovere la versione di HDInsight.

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)