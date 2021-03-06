---
title: Usare Apache Pig
titleSuffix: Azure HDInsight
description: Informazioni su come usare Pig con Apache Hadoop in HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 4cbb7b96610a56f3b6049038bb5c9c6bc0870b57
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871364"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Usare Apache Pig con Apache Hadoop in HDInsight

Informazioni su come usare [Apache Pig](https://pig.apache.org/) con HDInsight.

Apache Pig è una piattaforma che consente la creazione di programmi per Apache Hadoop usando un linguaggio procedurale denominato *Pig Latin*. Pig è un'alternativa a Java per la creazione di soluzioni *MapReduce* ed è incluso in Azure HDInsight. Usare la tabella seguente per individuare i vari modi in cui Pig può essere usato con HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Perché usare Apache Pig

Quando si elaborano dati usando MapReduce in Hadoop, uno dei problemi principali è riuscire a implementare la logica di elaborazione usando solo una mappa e una funzione di riduzione. In caso di esigenze di elaborazione complesse, è spesso necessario interrompere l'elaborazione in più operazioni di MapReduce concatenate insieme per ottenere il risultato desiderato.

Pig consente di definire l'elaborazione come una serie di trasformazioni a cui vengono sottoposti i dati fino a raggiungere l'output desiderato.

Il linguaggio Pig Latin consente di descrivere il flusso dati dall'input non elaborato fino all'output desiderato, attraverso una o più trasformazioni. I programmi in Pig Latin seguono questo modello generale:

* **Load**: legge i dati da modificare dal file System.

* **Transform**: modificare i dati.

* **Dump o Store**: consente di visualizzare i dati sullo schermo o di archiviarli per l'elaborazione.

### <a name="user-defined-functions"></a>Funzioni definite dall'utente

Pig Latin supporta anche funzioni definite dall'utente (UDF), che consentono di richiamare componenti esterni in grado di implementare logica difficile da modellare in Pig Latin.

Per altre informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) e il [manuale di riferimento di Pig Latin 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Dati di esempio

HDInsight offre diversi set di dati di esempio, archiviati nelle directory `/example/data` e `/HdiSamples`. Queste directory si trovano nella risorsa di archiviazione predefinita per il cluster. L'esempio di Pig in questo documento usa il file *log4j* da `/example/data/sample.log`.

Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

Nell'esempio precedente, il livello log è ERROR.

> [!NOTE]  
> È anche possibile generare un file log4j usando lo strumento di registrazione [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) e quindi caricandolo nel BLOB. Per istruzioni, vedere [Caricamento di dati in HDInsight](hdinsight-upload-data.md) . Per altre informazioni sul modo in cui HDInsight usa l'archiviazione BLOB di Azure, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Processo di esempio

Il processo Pig Latin seguente carica il file `sample.log` dalla risorsa di archiviazione predefinita per il cluster HDInsight. Esegue quindi una serie di trasformazioni che generano un conteggio delle occorrenze di ciascun livello di log presente nei dati di input. I risultati vengono scritti in STDOUT.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

L'immagine seguente illustra un riepilogo degli effetti di ogni trasformazione sui dati.

:::image type="content" source="./media/use-pig/hdi-data-transformation.gif" alt-text="Rappresentazione grafica delle trasformazioni" border="false":::

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Eseguire il processo Pig Latin

HDInsight è in grado di eseguire processi Pig Latin in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

## <a name="pig-and-sql-server-integration-services"></a>Pig e SQL Server Integration Services

È possibile usare SQL Server Integration Services (SSIS) per eseguire un processo Pig. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i processi Pig in HDInsight.

* [Attività Pig di Azure HDInsight][pigtask]

* [Gestione connessione della sottoscrizione di Azure][connectionmanager]

Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Passaggi successivi

Dopo avere appreso come usare Pig con HDInsight, vedere i collegamenti seguenti per scoprire altri modi di usare Azure HDInsight.

* [Caricare dati in HDInsight](hdinsight-upload-data.md)
* [Usare Apache Hive con HDInsight](./hadoop/hdinsight-use-hive.md)
* [Usare Apache Sqoop con HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [Usare processi MapReduce con HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


