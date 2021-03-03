---
title: Migrazione del carico di lavoro hive al nuovo account in archiviazione di Azure
description: Migrazione del carico di lavoro hive al nuovo account in archiviazione di Azure
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746102"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migrazione del carico di lavoro hive al nuovo account in archiviazione di Azure

Informazioni su come usare le azioni script per copiare le tabelle hive tra gli account di archiviazione in HDInsight. Questa operazione può essere utile quando si esegue la migrazione a [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Per copiare manualmente una singola tabella hive in HDInsight 4,0, vedere [hive Export/Import](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Prerequisiti

* Un nuovo cluster HDInsight con le configurazioni seguenti:
  * Il file System predefinito si trova nell'account di archiviazione di destinazione. Vedere [usare archiviazione di Azure con i cluster HDInsight di Azure](../hdinsight-hadoop-use-blob-storage.md).
  * La versione del cluster deve corrispondere a quella del cluster di origine.
  * Usa un nuovo database di metastore Hive esterno. Vedere [usare gli archivi di metadati esterni](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Un account di archiviazione accessibile ai cluster originali e nuovi. Vedere [aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md) e ai [tipi e alle funzionalità](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) di archiviazione per i tipi di archiviazione secondari consentiti.

    Ecco alcune opzioni:
  * Aggiungere l'account di archiviazione di destinazione al cluster originale.
  * Aggiungere l'account di archiviazione originale al nuovo cluster.
  * Aggiungere un account di archiviazione intermediario ai cluster originali e nuovi.

## <a name="how-it-works"></a>Funzionamento

Si eseguirà un'azione script per esportare le tabelle hive dal cluster originale a una directory HDFS specificata. Vedere [azione script in un cluster in esecuzione](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Quindi, eseguiamo un'altra azione script sul nuovo cluster per importare le tabelle hive dalla directory HDFS.

Lo script creerà nuovamente le tabelle nel file System predefinito del nuovo cluster. Le tabelle native copieranno anche i dati nella risorsa di archiviazione. Le tabelle non native vengono copiate solo per definizione. Per informazioni dettagliate sulle tabelle non native, vedere [gestori di archiviazione hive](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) .

Il percorso delle tabelle esterne che non si trova nella directory warehouse di hive verrà mantenuto. Le altre tabelle vengono copiate nel percorso hive predefinito del cluster di destinazione. Vedere Proprietà hive `hive.metastore.warehouse.external.dir` e `hive.metastore.warehouse.dir` .

Gli script non manterranno le autorizzazioni file personalizzate nel cluster di destinazione.

> [!NOTE]
>
> Questa guida supporta la copia di oggetti di metadati correlati a database, tabelle e partizioni hive. È necessario ricreare manualmente altri oggetti di metadati.
>
> * Per `Views` , hive supporta il `SHOW VIEWS` comando a partire da hive 2.2.0 in HDInsight 4,0. Usare `SHOW CREATE TABLE` per la definizione della vista. Per le versioni precedenti di hive, eseguire una query sul database SQL Metastore per visualizzare le visualizzazioni.
> * Per `Materialized Views` , usare i comandi `SHOW MATERIALIZED VIEWS` , `DESCRIBE FORMATTED` e `CREATE MATERIALIZED VIEW` . Per informazioni dettagliate, vedere [viste materializzate](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) .
> * Per `Constraints` , supportato a partire da hive 2.1.0 in HDInsight 4,0, usare `DESCRIBE EXTENDED` per elencare i vincoli per una tabella e usare `ALTER TABLE` per aggiungere vincoli. Per informazioni dettagliate, vedere [vincoli ALTER TABLE](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) .

## <a name="copy-hive-tables"></a>Copia tabelle hive

1. Applicare l'azione di script "Esporta" nel cluster originale con i campi seguenti.

    Questa operazione genererà ed eseguirà script intermediari di hive. Verranno salvati nell'oggetto specificato `<hdfs-export-path>` .

    Facoltativamente, utilizzare `--run-script=false` per personalizzarle prima di eseguire manualmente.

    |Proprietà | Valore |
    |---|---|
    |URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Tipo/i di nodo|Head|
    |Parametri|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Al termine dell'esportazione, applicare l'azione di script "Import" nel nuovo cluster con i campi seguenti.

    |Proprietà | Valore |
    |---|---|
    |URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Tipo/i di nodo|Head|
    |Parametri|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Verifica

Scaricare ed eseguire lo script come utente root [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) nel nodo primario di ogni cluster e confrontare il contenuto del file di output `/tmp/hive_contents.out` . Vedere [connettersi a HDInsight (Apache Hadoop) tramite SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Pulizia dell'utilizzo aggiuntivo dello spazio di archiviazione

Dopo aver completato e verificato la migrazione dell'archiviazione, è possibile eliminare i dati nel percorso di esportazione HDFS specificato.

L'opzione consiste nell'usare il comando HDFS `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Opzione: ridurre l'utilizzo di spazio di archiviazione aggiuntivo

L'azione Esporta script probabilmente raddoppia l'utilizzo dell'archiviazione a causa di hive. Tuttavia, è possibile limitare l'utilizzo dello spazio di archiviazione aggiuntivo eseguendo la migrazione manuale, un database o una tabella alla volta.

1. Specificare `--run-script=false` per ignorare l'esecuzione dello script hive generato. Gli script di esportazione e importazione hive verranno comunque salvati nel percorso di esportazione.

2. Eseguire frammenti di codice per gli script di esportazione e importazione hive database per database o tabella per tabella, pulendo manualmente il percorso di esportazione dopo ogni tabella o database migrato.

## <a name="next-steps"></a>Passaggi successivi

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Usare archivi di metadati esterni](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Tipi e funzionalità di archiviazione](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Azione script in un cluster in esecuzione](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
