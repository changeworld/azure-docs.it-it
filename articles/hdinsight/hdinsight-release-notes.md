---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 838eb517697c0625139058a19c7def764e869ed5
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588174"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

Per sottoscrivere le note sulla versione, guardare le versioni in [questo repository GitHub.](https://github.com/hdinsight/release-notes/releases)

## <a name="release-date-03242021"></a>Data di rilascio: 24/03/2021

Questa versione si applica sia a HDInsight 3.6 che a HDInsight 4.0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se le modifiche seguenti non vengono mostrate, attendere che il rilascio sia in tempo reale nella propria area geografica tra diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="spark-30-preview"></a>Anteprima di Spark 3.0
HDInsight ha aggiunto il supporto di [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) a HDInsight 4.0 come funzionalità di anteprima. 

### <a name="kafka-24-preview"></a>Anteprima di Kafka 2.4
HDInsight ha aggiunto [il supporto kafka 2.4.1](http://kafka.apache.org/24/documentation.html) a HDInsight 4.0 come funzionalità di anteprima.

### <a name="eav4-series-support"></a>Supporto della serie Eav4
HDInsight ha aggiunto il supporto della serie Eav4 in questa versione. Per altre informazioni sulla [serie Dav4, vedere](../virtual-machines/eav4-easv4-series.md)qui . La serie è stata resa disponibile nelle aree seguenti: 

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
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. Il servizio esegue gradualmente la migrazione ai set [di scalabilità di macchine virtuali di Azure.](../virtual-machine-scale-sets/overview.md) L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione in questa versione.

## <a name="behavior-changes"></a>Modifiche del comportamento
### <a name="default-cluster-version-is-changed-to-40"></a>La versione predefinita del cluster viene modificata in 4.0
La versione predefinita del cluster HDInsight è stata modificata da 3.6 a 4.0. Per altre informazioni sulle versioni disponibili, vedere [versioni disponibili.](./hdinsight-component-versioning.md) Altre informazioni sulle novità di [HDInsight 4.0.](./hdinsight-version-release.md)

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Le dimensioni predefinite delle macchine virtuali del cluster vengono modificate in serie Ev3 
Le dimensioni predefinite delle macchine virtuali del cluster vengono modificate da serie D a serie Ev3. Questa modifica si applica ai nodi head e ai nodi di lavoro. Per evitare questa modifica che influisce sui flussi di lavoro testati, specificare le dimensioni delle macchine virtuali da usare nel modello di Gestione risorse di Microsoft Windows.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Risorsa dell'interfaccia di rete non visibile per i cluster in esecuzione nei set di scalabilità di macchine virtuali di Azure
HDInsight esegue gradualmente la migrazione ai set di scalabilità di macchine virtuali di Azure. Le interfacce di rete per le macchine virtuali non sono più visibili ai clienti per i cluster che usano set di scalabilità di macchine virtuali di Azure.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="os-version-upgrade"></a>Aggiornamento della versione del sistema operativo
I cluster HDInsight sono attualmente in esecuzione in Ubuntu 16.04 LTS. Come indicato nel ciclo di rilascio di [Ubuntu,](https://ubuntu.com/about/release-cycle)il kernel Ubuntu 16.04 raggiungerà End of Life (EOL) nell'aprile 2021. Si inizierà a eseguire la nuova immagine del cluster HDInsight 4.0 in esecuzione in Ubuntu 18.04 a maggio 2021. I cluster HDInsight 4.0 appena creati verranno eseguiti in Ubuntu 18.04 per impostazione predefinita, una volta disponibili. I cluster esistenti in Ubuntu 16.04 verranno eseguiti così come sono con il supporto completo.

HDInsight 3.6 continuerà a essere eseguito in Ubuntu 16.04. Raggiungerà la fine del supporto standard entro il 30 giugno 2021 e cambierà in supporto Basic a partire dal 1° luglio 2021. Per altre informazioni su date e opzioni di supporto, vedere Azure HDInsight [versioni](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Ubuntu 18.04 non sarà supportato per HDInsight 3.6. Se si desidera usare Ubuntu 18.04, è necessario eseguire la migrazione dei cluster a HDInsight 4.0. 

È necessario eliminare e ricreare i cluster se si desidera spostare i cluster esistenti in Ubuntu 18.04. Pianificare la creazione o la ricreazione del cluster quando sarà disponibile il supporto per Ubuntu 18.04. Si invierà un'altra notifica quando la nuova immagine sarà disponibile in tutte le aree.

È consigliabile testare in anticipo le azioni script e le applicazioni personalizzate distribuite nei nodi perimetrali in una macchina virtuale Ubuntu 18.04. È possibile creare una semplice macchina virtuale Ubuntu Linux in [18.04-LTS,](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)quindi creare e usare una coppia di chiavi [SSH (Secure Shell)](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys#ssh-into-your-vm) nella macchina virtuale per eseguire e testare le azioni script e le applicazioni personalizzate distribuite nei nodi perimetrali.

### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsgiht-40"></a>Disabilitare Stardard_A5 dimensioni della macchina virtuale come nodo head per HDInsgiht 4.0
Il nodo head del cluster HDInsight è responsabile dell'inizializzazione e della gestione del cluster. Standard_A5 dimensioni della macchina virtuale presenta problemi di affidabilità come nodo head per HDInsight 4.0. A partire dalla prossima versione di maggio 2021, i clienti non potranno creare nuovi cluster con dimensioni Standard_A5 macchina virtuale come nodo head. È possibile usare altre macchine virtuali a 2 core, ad esempio E2_v3 o E2s_v3. I cluster esistenti verranno eseguiti così come sono. Una macchina virtuale con 4 core è altamente consigliata per il nodo head per garantire la disponibilità elevata e l'affidabilità dei cluster HDInsight di produzione.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>supporto Basic per HDInsight 3.6 a partire dal 1° luglio 2021
A partire dal 1° luglio 2021, Microsoft offrirà supporto Basic per determinati tipi di cluster HDInsight 3.6. [](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) Il supporto Basic sarà disponibile fino al 3 aprile 2022. A partire dal 1° luglio 2021 si verrà supporto Basic a partire dal 1° luglio 2021. Non è necessaria alcuna azione da parte dell'utente per acconsentire esplicitamente. Vedere [la documentazione per](hdinsight-36-component-versioning.md) i tipi di cluster inclusi in supporto Basic. 

Non è consigliabile creare nuove soluzioni in HDInsight 3.6, bloccare le modifiche negli ambienti 3.6 esistenti. È consigliabile eseguire [la migrazione dei cluster a HDInsight 4.0.](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40) Altre informazioni [sulle novità di HDInsight 4.0.](hdinsight-version-release.md#whats-new-in-hdinsight-40)

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Aggiunta del supporto per Spark 3.0.0 e Kafka 2.4.1 come anteprima. Le versioni correnti dei componenti per HDInsight 4.0 e HDInsight 3.6 sono disponibili in [questo documento.](./hdinsight-component-versioning.md)

## <a name="recommanded-features"></a>Funzionalità di cui è stato fatto nuovo clic
### <a name="service-tags"></a>Tag di servizio
I tag di servizio semplificano la limitazione dell'accesso di rete ai servizi di Azure per le macchine virtuali di Azure e le reti virtuali di Azure. I tag di servizio nelle regole del gruppo di sicurezza di rete consentono o negano il traffico verso un servizio di Azure specifico. La regola può essere impostata a livello globale o in base all'area di Azure. Azure offre la manutenzione degli indirizzi IP sottostanti a ogni tag. I tag del servizio HDInsight per i gruppi di sicurezza di rete sono gruppi di indirizzi IP per i servizi di integrità e gestione. Questi gruppi consentono di ridurre al minimo la complessità per la creazione di regole di sicurezza. I clienti HDInsight possono abilitare il tag del servizio portale di Azure, PowerShell e API REST. Per altre informazioni, vedere [Tag del servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](./hdinsight-service-tags.md).
