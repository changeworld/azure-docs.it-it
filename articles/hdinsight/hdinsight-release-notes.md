---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065498"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

Per sottoscrivere le note sulla versione, Guarda le versioni di [questo repository GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Data di rilascio: 03/24/2021

Questa versione è valida sia per HDInsight 3,6 che per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="spark-30-preview"></a>Anteprima di Spark 3,0
HDInsight ha aggiunto il supporto [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) a HDInsight 4,0 come funzionalità di anteprima. 

### <a name="kafka-24-preview"></a>Kafka 2,4 Preview
HDInsight ha aggiunto il supporto [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) a HDInsight 4,0 come funzionalità di anteprima.

### <a name="eav4-series-support"></a>Supporto della serie Eav4
HDInsight ha aggiunto il supporto della serie Eav4 in questa versione. Scopri di più sulla [serie Dav4 qui](../virtual-machines/eav4-easv4-series.md). La serie è stata resa disponibile nelle aree geografiche seguenti: 

* Australia orientale
* Brasile meridionale
* Stati Uniti centrali
* Asia orientale
* Stati Uniti orientali
* Giappone orientale
* Asia sud-orientale
* Regno Unito meridionale
* Europa occidentale
* Stati Uniti occidentali 2

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. Il servizio esegue gradualmente la migrazione ai [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/overview.md). L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione in questa versione.

## <a name="behavior-changes"></a>Modifiche del comportamento
### <a name="default-cluster-version-is-changed-to-40"></a>La versione predefinita del cluster è stata modificata in 4,0
La versione predefinita del cluster HDInsight è stata modificata da 3,6 a 4,0. Per ulteriori informazioni sulle versioni disponibili, vedere [versioni disponibili](./hdinsight-component-versioning.md). Altre informazioni sulle novità in [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Le dimensioni predefinite delle macchine virtuali del cluster vengono modificate in serie EV3 
Le dimensioni predefinite delle macchine virtuali del cluster vengono modificate dalla serie D alla serie EV3. Questa modifica si applica ai nodi head e ai nodi del ruolo di lavoro. Per evitare che questa modifica influisca sui flussi di lavoro testati, specificare le dimensioni delle macchine virtuali che si vuole usare nel modello ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Risorsa dell'interfaccia di rete non visibile per i cluster in esecuzione nei set di scalabilità di macchine virtuali di Azure
HDInsight sta migrando gradualmente ai set di scalabilità di macchine virtuali di Azure. Le interfacce di rete per le macchine virtuali non sono più visibili ai clienti per i cluster che usano i set di scalabilità di macchine virtuali di Azure.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="os-version-upgrade"></a>Aggiornamento versione sistema operativo
HDInsight aggiornerà la versione del sistema operativo da Ubuntu 16,04 a 18,04. L'aggiornamento viene completato prima del 2021 aprile.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Supporto Basic per HDInsight 3,6 a partire dal 1 ° luglio 2021
A partire dal 1 ° luglio 2021, Microsoft offrirà [supporto Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) per determinati tipi di cluster HDInsight 3,6. Il piano di supporto Basic sarà disponibile fino al 3 aprile 2022. Verrà registrato automaticamente in supporto Basic a partire dal 1 ° luglio 2021. Non è richiesto alcun intervento da te per acconsentire esplicitamente. Vedere [la documentazione](hdinsight-36-component-versioning.md) per cui sono inclusi i tipi di cluster in supporto Basic. 

Non è consigliabile compilare nuove soluzioni in HDInsight 3,6, bloccare le modifiche negli ambienti 3,6 esistenti. Si consiglia [di eseguire la migrazione dei cluster a HDInsight 4,0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Altre informazioni sulle [novità in HDInsight 4,0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Aggiunta del supporto per Spark 3.0.0 e Kafka 2.4.1 come anteprima. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](./hdinsight-component-versioning.md).

## <a name="recommanded-features"></a>Funzionalità riordinate
### <a name="service-tags"></a>Tag di servizio
I tag di servizio semplificano la limitazione dell'accesso di rete ai servizi di Azure per macchine virtuali di Azure e reti virtuali di Azure. I tag del servizio nelle regole del gruppo di sicurezza di rete (NSG) consentono o rifiutano il traffico verso un servizio di Azure specifico. La regola può essere impostata globalmente o per area di Azure. Azure fornisce la manutenzione degli indirizzi IP sottostanti ogni tag. I tag del servizio HDInsight per i gruppi di sicurezza di rete (gruppi) sono gruppi di indirizzi IP per i servizi di gestione e integrità. Questi gruppi consentono di ridurre al minimo la complessità per la creazione delle regole di sicurezza. I clienti di HDInsight possono abilitare il tag del servizio tramite portale di Azure, PowerShell e l'API REST. Per altre informazioni, vedere [Tag del servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](./hdinsight-service-tags.md).
