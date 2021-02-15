---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 1a0b1a0400ae3d43817921e8a336421aee35ccd6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378146"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

Per sottoscrivere le note sulla versione, Guarda le versioni di [questo repository GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Data di rilascio: 02/05/2021

Questa versione è valida sia per HDInsight 3,6 che per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="dav4-series-support"></a>Supporto della serie Dav4
HDInsight ha aggiunto il supporto della serie Dav4 in questa versione. Scopri di più sulla [serie Dav4 qui](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>GA proxy di Kafka 
Il proxy REST Kafka consente di interagire con il cluster Kafka tramite un'API REST su HTTPS. Il proxy Rest Kafka è disponibile a livello generale a partire da questa versione. Altre informazioni sul [proxy Rest Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy)sono disponibili qui.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. Il servizio esegue gradualmente la migrazione ai [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/overview.md). L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
### <a name="disabled-vm-sizes"></a>Dimensioni VM disabilitate
A partire dal 9 2021 gennaio, HDInsight bloccherà tutti i clienti che creano cluster con standand_A8, standand_A9, standand_A10 e standand_A11 le dimensioni delle macchine virtuali. I cluster esistenti vengono eseguiti così come sono. Provare a migrare a HDInsight 4,0 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="behavior-changes"></a>Modifiche del comportamento
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Dimensioni predefinite della macchina virtuale del cluster modifiche alla serie EV3 
Le dimensioni predefinite delle macchine virtuali del cluster verranno modificate dalla serie D alla serie EV3. Questa modifica si applica ai nodi head e ai nodi del ruolo di lavoro. Per evitare che questa modifica influisca sui flussi di lavoro testati, specificare le dimensioni delle macchine virtuali che si vuole usare nel modello ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Risorsa dell'interfaccia di rete non visibile per i cluster in esecuzione nei set di scalabilità di macchine virtuali di Azure
HDInsight sta migrando gradualmente ai set di scalabilità di macchine virtuali di Azure. Le interfacce di rete per le macchine virtuali non sono più visibili ai clienti per i cluster che usano i set di scalabilità di macchine virtuali di Azure.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Modifica di rilievo per .NET per Apache Spark 1.0.0
Con la versione più recente, in HDInsight è stata introdotta la prima versione ufficiale v 1.0.0 della libreria [".NET for Apache Spark"](https://github.com/dotnet/spark) . Fornisce la completezza dell'API dataframe per Spark 2.4. x e Spark 3.0. x insieme a un host di [altre funzionalità](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md). Verranno apportate modifiche di rilievo a questa versione principale. per informazioni sui passaggi necessari per aggiornare il codice e le pipeline, vedere [la guida alla migrazione di .NET per Apache Spark](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) . Per altre informazioni, vedere la [Guida di .NET per Apache Spark v 1.0 in Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).


## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="default-cluster-version-will-be-changed-to-40"></a>La versione predefinita del cluster verrà modificata in 4,0
A partire dal 2021 febbraio, la versione predefinita del cluster HDInsight verrà modificata da 3,6 a 4,0. Per ulteriori informazioni sulle versioni disponibili, vedere [versioni disponibili](./hdinsight-component-versioning.md#available-versions). Altre informazioni sulle novità in [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Aggiornamento versione sistema operativo
HDInsight sta aggiornando la versione del sistema operativo da Ubuntu 16,04 a 18,04. L'aggiornamento viene completato prima del 2021 aprile.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6-fine del supporto del 30 2021 giugno
HDInsight 3,6 sarà la fine del supporto. A partire dal 30 2021 giugno, i clienti non possono creare nuovi cluster HDInsight 3,6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Provare a migrare a HDInsight 4,0 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](./hdinsight-component-versioning.md).
