---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni su componenti e versioni di Apache Hadoop in Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726584"
---
# <a name="azure-hdinsight-versions"></a>Versioni di Azure HDInsight

HDInsight aggrega Apache Hadoop componenti dell'ambiente e la piattaforma HDInsight in un pacchetto distribuito in un cluster. Per altri dettagli, vedere funzionamento del [controllo delle versioni di HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight

Questa tabella elenca le versioni di HDInsight disponibili nell'portale di Azure e altri metodi di distribuzione come PowerShell, CLI e .NET SDK.

| Versione HDInsight | Sistema operativo della macchina virtuale | Data di rilascio| Tipo di supporto | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24 settembre 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Sì |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 aprile 2017      | [Base](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Scadenza supporto standard-30 giugno 2021 <br> Scadenza supporto Basic-3 aprile 2022 |4 aprile 2022 |Sì |

* A partire dal 1 ° luglio 2021 Microsoft offrirà supporto Basic per determinati tipi di cluster HDI 3,6. Vedere [versioni del componente HDInsight 3,6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Opzioni di supporto per le versioni di HDInsight

Il supporto è definito come periodo di tempo in cui una versione di HDInsight è supportata dal servizio supporto tecnico clienti Microsoft. HDInsight offre due tipi di supporto: 
- Il **supporto tecnico standard** è un periodo di tempo in cui Microsoft fornisce aggiornamenti e supporto nei cluster HDInsight.  
    È consigliabile creare soluzioni usando la versione più recente supportata in modo completo. 
- **Supporto Basic** è un periodo di tempo in cui Microsoft fornirà assistenza limitata al provider di risorse HDInsight. Le immagini HDInsight e i componenti del software open source (OSS) non verranno serviti.   Solo gli aggiornamenti critici della sicurezza verranno corretti nei cluster HDInsight.  
  Microsoft non promuove la creazione di nuovi cluster o la creazione di soluzioni innovative quando una versione è supporto Basic. Si consiglia di migrare i cluster esistenti alla versione più recente supportata completamente. 

Per **scadenza del supporto** si intende che Microsoft non fornisce più supporto per la versione specifica di HDInsight. E potrebbe non essere più disponibile tramite il portale di Azure per la creazione del cluster.

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
