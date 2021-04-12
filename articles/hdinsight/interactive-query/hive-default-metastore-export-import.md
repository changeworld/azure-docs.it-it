---
title: Eseguire la migrazione del metastore Hive predefinito al Metastore esterno in Azure HDInsight
description: Eseguire la migrazione del metastore Hive predefinito al Metastore esterno in Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566116"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migrare il database predefinito metastore Hive al database di Metastore esterno

Questo articolo illustra come eseguire la migrazione di metadati da un database di [Metastore predefinito](../hdinsight-use-external-metadata-stores.md#default-metastore) per hive a un database SQL esterno in HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Perché eseguire la migrazione al database del Metastore esterno

* Il database Metastore predefinito è limitato allo SKU Basic e non è in grado di gestire i carichi di lavoro in scala di produzione.

* Il database del Metastore esterno consente ai clienti di ridimensionare orizzontalmente le risorse di calcolo hive aggiungendo nuovi cluster HDInsight che condividono lo stesso database di Metastore.

* Per la migrazione da HDInsight 3,6 a 4,0, è obbligatorio migrare i metadati nel database del Metastore esterno prima di aggiornare la versione dello schema hive. Vedere [migrazione dei carichi di lavoro da HDInsight 3,6 a HDInsight 4,0](./apache-hive-migrate-workloads.md).

Poiché il database del Metastore predefinito ha una capacità di calcolo limitata, si consiglia un basso utilizzo di altri processi nel cluster durante la migrazione dei metadati.

I database di origine e di destinazione devono usare la stessa versione di HDInsight e gli stessi account di archiviazione. Se si aggiornano le versioni di HDInsight da 3,6 a 4,0, eseguire prima i passaggi descritti in questo articolo. Quindi, seguire la procedura di aggiornamento ufficiale [qui](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Prerequisiti

Se si usa [Azure Data Lake storage Gen1](../overview-data-lake-storage-gen1.md), i percorsi delle tabelle hive dipendono probabilmente dalle configurazioni HDFS del cluster per Azure Data Lake storage Gen1. Eseguire la seguente azione script per rendere le posizioni portabili in altri cluster. Vedere [azione script in un cluster in esecuzione](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

L'azione è simile alla sostituzione dei collegamenti simbolici con i percorsi completi.

|Proprietà | Valore |
|---|---|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Tipo/i di nodo|Head|
|Parametri|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Eseguire la migrazione con l'esportazione/importazione con SqlPackage

HDInsight An cluster creato solo dopo 2020-10-15 supporta l'esportazione/importazione SQL per il database del Metastore predefinito hive usando `sqlpackage` .

1. Installare [SqlPackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) nel cluster.

2. Esportare il database del Metastore predefinito nel file BACPAC eseguendo il comando seguente.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Salvare il file BACPAC. Di seguito è riportata un'opzione.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importare il file BACPAC in un nuovo database con i passaggi elencati di [seguito](../../azure-sql/database/database-import.md).

5. Il nuovo database è pronto per essere [configurato come DB del Metastore esterno in un nuovo cluster HDInsight](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Eseguire la migrazione usando uno script hive

I cluster creati prima di 2020-10-15 non supportano l'esportazione/importazione del database dei Metastore predefinito.

Per tali cluster, seguire la Guida [copiare le tabelle hive tra gli account di archiviazione](./hive-migration-across-storage-accounts.md), usando un secondo cluster con un database di [Metastore hive esterno](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Il secondo cluster può usare lo stesso account di archiviazione, ma deve usare un nuovo file System predefinito.

### <a name="option-to-shallow-copy"></a>Opzione per la copia superficiale
Il consumo di spazio di archiviazione sarebbe doppio quando le tabelle vengono copiate "approfondite" utilizzando la guida precedente. È necessario pulire manualmente i dati nel contenitore di archiviazione di origine.
Possiamo, invece, copiare le tabelle se sono non transazionali. Per impostazione predefinita, tutte le tabelle hive in HDInsight 3,6 sono non transazionali, ma solo le tabelle esterne sono non transazionali in HDInsight 4,0. Le tabelle transazionali devono essere copiate in modo approfondito. Attenersi alla seguente procedura per copiare le tabelle non transazionali in modo superficiale:

1. Eseguire lo script [hive-DDLS.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) nel nodo head primario del cluster di origine per generare il linguaggio DDL per ogni tabella hive.
2. Il linguaggio DDL viene scritto in uno script hive locale denominato `/tmp/hdi_hive_ddls.hql` . Eseguire questa operazione nel cluster di destinazione che usa un database di metastore Hive esterno.

## <a name="verify-that-all-hive-tables-are-imported"></a>Verificare che tutte le tabelle hive vengano importate

Il comando seguente usa una query SQL sul database del Metastore per stampare tutte le tabelle hive e i relativi percorsi di dati. Confrontare gli output tra i cluster nuovi e quelli obsoleti per verificare che nel nuovo database Metastore non siano presenti tabelle mancanti.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Ulteriori informazioni

* [Migrare i carichi di lavoro da HDInsight 3,6 a 4,0](./apache-hive-migrate-workloads.md)
* [Migrazione di carichi di lavoro hive tra account di archiviazione](./hive-migration-across-storage-accounts.md)
* [Connettersi a HDInsight](../hadoop/connect-install-beeline.md)
* [Risolvere gli errori di autorizzazione creare una tabella](./interactive-query-troubleshoot-permission-error-create-table.md)