---
title: Eseguire la migrazione di carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0
description: Informazioni su come eseguire la migrazione di carichi di lavoro Apache Hive in HDInsight 3,6 in HDInsight 4,0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566071"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Eseguire la migrazione di carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0

HDInsight 4,0 presenta diversi vantaggi rispetto a HDInsight 3,6. Ecco una [Panoramica delle novità di HDInsight 4,0](../hdinsight-version-release.md).

Questo articolo illustra i passaggi per eseguire la migrazione dei carichi di lavoro hive da HDInsight 3,6 a 4,0, tra cui

* metastore Hive l'aggiornamento dello schema e della copia
* Migrazione sicura per la compatibilità ACID
* Conservazione dei criteri di sicurezza hive

I cluster HDInsight nuovi e obsoleti devono avere accesso agli stessi account di archiviazione.

La migrazione delle tabelle hive in un nuovo account di archiviazione deve essere eseguita come passaggio separato. Vedere [migrazione hive tra gli account di archiviazione](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento

### <a name="1-prepare-the-data"></a>1. preparare i dati

* HDInsight 3,6, per impostazione predefinita, non supporta le tabelle ACID. Se sono presenti tabelle ACID, tuttavia, eseguire la compattazione "MAJOR". Per informazioni dettagliate sulla compattazione, vedere il [manuale della lingua di hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

* Se si usa [Azure Data Lake storage Gen1](../overview-data-lake-storage-gen1.md), i percorsi delle tabelle hive dipendono probabilmente dalle configurazioni HDFS del cluster. Eseguire la seguente azione script per rendere le posizioni portabili in altri cluster. Vedere [azione script in un cluster in esecuzione](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Proprietà | Valore |
    |---|---|
    |URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Tipo/i di nodo|Head|
    |Parametri||

### <a name="2-copy-the-sql-database"></a>2. copiare il database SQL

* Se il cluster usa una metastore Hive predefinita, seguire questa [Guida](./hive-default-metastore-export-import.md) per esportare i metadati in un Metastore esterno. Quindi, creare una copia del Metastore esterno per l'aggiornamento.

* Se il cluster usa un metastore Hive esterno, crearne una copia. Le opzioni includono l'esportazione, l' [importazione](../../azure-sql/database/database-export.md) e il [ripristino temporizzato](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. aggiornare lo schema del Metastore

Questo passaggio usa il [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) da HDInsight 4,0 per aggiornare lo schema del Metastore.

> [!Warning]
> Questo passaggio non è reversibile. Eseguire questa operazione solo su una copia del Metastore.

1. Creare un cluster HDInsight 4,0 temporaneo per accedere all'hive 4,0 `schematool` . Per questo passaggio, è possibile usare la [Metastore hive predefinita](../hdinsight-use-external-metadata-stores.md#default-metastore) .

1. Dal cluster HDInsight 4,0 eseguire `schematool` per aggiornare il Metastore di destinazione HDInsight 3,6:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Questa utilità usa il client `beeline` per eseguire gli script SQL in `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > La sintassi SQL in questi script non è necessariamente compatibile con altri strumenti client. Ad esempio, [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) e l' [editor di query nel portale di Azure](../../azure-sql/database/connect-query-portal.md) richiedono la parola chiave `GO` dopo ogni comando.
    >
    > Se uno script ha esito negativo a causa della capacità delle risorse o dei timeout delle transazioni, aumentare il livello di prestazioni del database SQL.

1. Verificare la versione finale con query `select schema_version from dbo.version` .

    L'output deve corrispondere a quello del comando bash seguente dal cluster HDInsight 4,0.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Eliminare il cluster HDInsight 4,0 temporaneo.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. distribuire un nuovo cluster HDInsight 4,0

Creare un nuovo cluster HDInsight 4,0, [selezionando il Metastore hive aggiornato](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) e gli stessi account di archiviazione.

* Per il nuovo cluster non è necessario avere lo stesso file System predefinito.

* Se il Metastore contiene tabelle che risiedono in più account di archiviazione, è necessario aggiungere gli account di archiviazione al nuovo cluster per accedere a tali tabelle. Vedere [aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md).

* Se i processi hive hanno esito negativo a causa dell'inaccessibilità dell'archiviazione, verificare che il percorso della tabella si trovi in un account di archiviazione aggiunto al cluster.

    Usare il comando hive seguente per identificare la posizione della tabella:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. convertire le tabelle per la conformità ACID

Le tabelle gestite devono essere conformi a ACID in HDInsight 4,0. Eseguire `strictmanagedmigration` in HDInsight 4,0 per convertire tutte le tabelle non acid gestite in tabelle esterne con la proprietà `'external.table.purge'='true'` . Eseguire dall'nodo head:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Sicurezza hive tra le versioni di HDInsight

HDInsight si integra facoltativamente con Azure Active Directory usando HDInsight Enterprise Security Package (ESP). ESP USA Kerberos e Apache Ranger per gestire le autorizzazioni di risorse specifiche all'interno del cluster. È possibile eseguire la migrazione dei criteri Ranger distribuiti in hive in HDInsight 3,6 in HDInsight 4,0 con i passaggi seguenti:

1. Passare al pannello Service Manager Ranger nel cluster HDInsight 3,6.
2. Passare al criterio denominato **hive** ed esportare il criterio in un file JSON.
3. Assicurarsi che tutti gli utenti a cui viene fatto riferimento nel file JSON dei criteri esportati esistano nel nuovo cluster. Se a un utente viene fatto riferimento nel codice JSON dei criteri ma non esiste nel nuovo cluster, aggiungere l'utente al nuovo cluster o rimuovere il riferimento dal criterio.
4. Passare al pannello **Service Manager Ranger** nel cluster HDInsight 4,0.
5. Passare al criterio denominato **hive** e importare il codice JSON dei criteri Ranger dal passaggio 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Modifiche di hive in HDInsight 4,0 che possono richiedere modifiche all'applicazione

* Per la condivisione del Metastore tra Spark e hive per le tabelle ACID, vedere [configurazione aggiuntiva usando il connettore di hive warehouse](./apache-hive-warehouse-connector.md) .

* HDInsight 4,0 usa l' [autorizzazione basata sull'archiviazione](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Se si modificano le autorizzazioni per i file o si creano cartelle come utenti diversi da hive, probabilmente si verificano errori hive in base alle autorizzazioni di archiviazione. Per risolvere il problema, concedere l' `rw-` accesso all'utente. Vedere la [Guida alle autorizzazioni di HDFS](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` viene sostituito con `Beeline` .

Per altre modifiche, vedere l' [annuncio HDInsight 4,0](../hdinsight-version-release.md) .

## <a name="further-reading"></a>Ulteriori informazioni

* [Annuncio HDInsight 4,0](../hdinsight-version-release.md)
* [Approfondimento su HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabelle ACID hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)