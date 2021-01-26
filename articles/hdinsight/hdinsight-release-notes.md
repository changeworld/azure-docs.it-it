---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 5c414a11085a6a37dee6be522dcf513e8990e5e2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786352"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

Per sottoscrivere le note sulla versione, Guarda le versioni di [questo repository GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Data di rilascio: 11/18/2020

Questa versione è valida sia per HDInsight 3,6 che per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Rotazione automatica della chiave per la crittografia della chiave gestita dal cliente inattiva
A partire da questa versione, i clienti possono usare gli URL delle chiavi di crittografia senza versione di Azure KeyValut per la crittografia della chiave gestita dal cliente. HDInsight ruoterà automaticamente le chiavi in scadenza o sostituite con le nuove versioni. Altre informazioni sono disponibili [qui](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Possibilità di selezionare diverse dimensioni delle macchine virtuali Zookeeper per i servizi Spark, Hadoop e ML
HDInsight in precedenza non supporta la personalizzazione delle dimensioni del nodo Zookeeper per i tipi di cluster Spark, Hadoop e ML Services. Il valore predefinito è A2_v2 dimensioni della macchina virtuale/a2, che vengono fornite gratuitamente. Da questa versione è possibile selezionare le dimensioni della macchina virtuale Zookeeper più appropriate per il proprio scenario. Verranno addebitati i nodi Zookeeper con dimensioni della macchina virtuale diverse da A2_v2/a2. Le macchine virtuali A2_v2 e a2 sono ancora disponibili gratuitamente.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. A partire da questa versione, il servizio eseguirà gradualmente la migrazione ai [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/overview.md). L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Deprecazione del cluster di servizi HDInsight 3,6 ML
Il tipo di cluster di servizi ML di HDInsight 3,6 sarà la fine del supporto entro il 31 2020 dicembre. I clienti non saranno in grado di creare nuovi cluster di 3,6 ML Services dopo il 31 2020 dicembre. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Verificare la scadenza del supporto per le versioni di HDInsight e i tipi di cluster [qui](./hdinsight-component-versioning.md#available-versions).

### <a name="disabled-vm-sizes"></a>Dimensioni VM disabilitate
A partire dal 16 2020 novembre, HDInsight bloccherà i nuovi clienti creando cluster con standand_A8, standand_A9, standand_A10 e standand_A11 le dimensioni delle macchine virtuali. I clienti esistenti che hanno usato queste dimensioni di VM negli ultimi tre mesi non saranno interessati. A partire dal 9 2021 gennaio, HDInsight bloccherà tutti i clienti che creano cluster con standand_A8, standand_A9, standand_A10 e standand_A11 le dimensioni delle macchine virtuali. I cluster esistenti vengono eseguiti così come sono. Provare a migrare a HDInsight 4,0 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="behavior-changes"></a>Modifiche del comportamento
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>Aggiungere il controllo delle regole NSG prima dell'operazione di ridimensionamento
HDInsight ha aggiunto gruppi di sicurezza di rete (gruppi) e il controllo delle route definite dall'utente (UDR) con operazione di ridimensionamento. La stessa convalida viene eseguita per la scalabilità del cluster oltre alla creazione del cluster. Questa convalida consente di evitare errori imprevedibili. Se la convalida non viene superata, il ridimensionamento non riesce. Per altre informazioni su come configurare gruppi e UdR correttamente, vedere [indirizzi IP di gestione di HDInsight](./hdinsight-management-ip-addresses.md).

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>Le dimensioni predefinite della macchina virtuale del cluster verranno modificate in EV3 Family
A partire dalla versione successiva (intorno alla fine di gennaio), le dimensioni predefinite delle macchine virtuali del cluster verranno modificate dalla famiglia D alla famiglia EV3. Questa modifica si applica ai nodi head e ai nodi del ruolo di lavoro. Per evitare questa modifica, specificare le dimensioni delle macchine virtuali che si vuole usare nel modello ARM.

### <a name="default-cluster-version-will-be-changed-to-40"></a>La versione predefinita del cluster verrà modificata in 4,0
A partire dal 2021 febbraio, la versione predefinita del cluster HDInsight verrà modificata da 3,6 a 4,0. Per ulteriori informazioni sulle versioni disponibili, vedere [versioni disponibili](./hdinsight-component-versioning.md#available-versions). Altre informazioni sulle novità di [HDInsight 4,0](./hdinsight-version-release.md)

### <a name="os-version-upgrade"></a>Aggiornamento versione sistema operativo
HDInsight sta aggiornando la versione del sistema operativo da 16,04 a 18,04. L'aggiornamento viene completato prima del 2021 aprile.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6-fine del supporto del 30 2021 giugno
HDInsight 3,6 sarà la fine del supporto. A partire dal 30 2021 giugno, i clienti non possono creare nuovi cluster HDInsight 3,6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Provare a migrare a HDInsight 4,0 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](./hdinsight-component-versioning.md).

## <a name="known-issues"></a>Problemi noti
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>Impedire il riavvio periodico delle macchine virtuali del cluster HDInsight

A partire dalla metà del 2020 novembre, è possibile che si sia notato che le macchine virtuali del cluster HDInsight vengono riavviate a intervalli regolari. Il problema potrebbe essere causato da:

1.  ClamAV è abilitato nel cluster. Il nuovo pacchetto azsec-ClamAV utilizza una grande quantità di memoria che attiva il riavvio del nodo. 
2.  Un processo CRON è pianificato ogni giorno che monitora le modifiche all'elenco di autorità di certificazione (CAs) usate dai servizi di Azure. Quando è disponibile un nuovo certificato CA, lo script aggiunge il certificato all'archivio di attendibilità JDK e pianifica un riavvio.

HDInsight distribuisce correzioni e applica patch per tutti i cluster in esecuzione per entrambi i problemi. Per applicare immediatamente la correzione ed evitare il riavvio imprevisto di macchine virtuali, è possibile eseguire le azioni script seguenti in tutti i nodi del cluster come azione di script permanente. HDInsight invierà un'altra notifica al termine della correzione e dell'applicazione di patch.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```