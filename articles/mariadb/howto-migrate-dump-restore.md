---
title: Eseguire la migrazione con dump e ripristino-database di Azure per MariaDB
description: Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MariaDB usando strumenti come mysqldump, MySQL Workbench e phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628221"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Eseguire la migrazione del database di MariaDB a un database di Azure per MariaDB usando dump e Restore

Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MariaDB:
- Eseguire il dump e il ripristino usando uno strumento da riga di comando (tramite mysqldump) 
- Dump e ripristino con phpMyAdmin

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare la migrazione del database, eseguire le operazioni seguenti:
- Creare un [database di Azure per il server MariaDB-portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Installare l'utilità da riga di comando [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) .
- Scaricare e installare [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) o un altro strumento MySQL di terze parti per l'esecuzione di comandi dump e Restore.

## <a name="use-common-tools"></a>Usare strumenti comuni
Usare utilità e strumenti comuni, come MySQL Workbench o mysqldump, per connettersi in modalità remota e ripristinare i dati nel database di Azure per MariaDB. Usare questi strumenti nel computer client con una connessione Internet per connettersi al database di Azure per MariaDB. Usare una connessione crittografata con SSL come procedura consigliata per la sicurezza. Per altre informazioni, vedere [configurare la connettività SSL nel database di Azure per MariaDB](concepts-ssl-connection-security.md). Quando si esegue la migrazione dei dati nel database di Azure per MariaDB, non è necessario spostare i file di dump in una posizione cloud speciale. 

## <a name="common-uses-for-dump-and-restore"></a>Usi comuni per il dump e ripristino
È possibile usare le utilità MySQL, ad esempio mysqldump e mysqlpump, per scaricare e caricare i database in un database di Azure per il server MariaDB in diversi scenari comuni. 

- Usare i dump del database quando si esegue la migrazione di un intero database. Questa raccomandazione si verifica quando si trasferisce una grande quantità di dati o quando si desidera ridurre al minimo l'interruzione del servizio per siti o applicazioni Live. 
-  Assicurarsi che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MariaDB. Il database di Azure per MariaDB supporta solo il motore di archiviazione InnoDB e non altri motori di archiviazione. Se le tabelle sono configurate con altri motori di archiviazione, convertirle nel formato del motore InnoDB prima di eseguirne la migrazione nel database di Azure per MariaDB.
   
   Se, ad esempio, si dispone di un'app WordPress o di un'app Web che usa tabelle di ISAM, è necessario innanzitutto convertire tali tabelle eseguendone la migrazione nel formato InnoDB prima di ripristinarle nel database di Azure per MariaDB. Utilizzare la clausola `ENGINE=InnoDB` per impostare il motore da utilizzare per la creazione di una nuova tabella, quindi trasferire i dati nella tabella compatibile prima di ripristinarli. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Per evitare problemi di compatibilità quando si esegue il dump dei database, assicurarsi di usare la stessa versione di MariaDB nei sistemi di origine e di destinazione. Ad esempio, se il server MariaDB esistente è la versione 10,2, è necessario eseguire la migrazione al database di Azure per MariaDB configurato per eseguire la versione 10,2. Il `mysql_upgrade` comando non funziona in un database di Azure per il server MariaDB e non è supportato. Se è necessario eseguire l'aggiornamento tra le versioni di MariaDB, prima di tutto eseguire il dump o esportare il database della versione precedente in una versione successiva di MariaDB nel proprio ambiente. È quindi possibile eseguire `mysql_upgrade` prima di provare a eseguire la migrazione nel database di Azure per MariaDB.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni
Per ottimizzare le prestazioni quando si esegue il dump di database di grandi dimensioni, tenere presenti le considerazioni seguenti:
-   Usare l' `exclude-triggers` opzione in mysqldump. Escludere i trigger dai file di dump per evitare che i comandi trigger vengano attivati durante il ripristino dei dati. 
-   Utilizzare l' `single-transaction` opzione per impostare la modalità di isolamento della transazione su Repeatable Read e inviare un'istruzione SQL Start Transaction al server prima di eseguire il dump dei dati. Il dump di molte tabelle all'interno di una singola transazione comporta l'utilizzo di un'archiviazione aggiuntiva durante il ripristino. L' `single-transaction` opzione e l' `lock-tables` opzione si escludono a vicenda. Ciò è dovuto al fatto che le tabelle di blocco causano il commit implicito di tutte le transazioni in sospeso. Per eseguire il dump di tabelle di grandi dimensioni, combinare l'opzione `single-transaction` con l'opzione `quick`. 
-   Usare la sintassi a più righe `extended-insert` che include vari elenchi VALUES. Questo approccio comporta un file di dump più piccolo e accelera gli inserimenti quando il file viene ricaricato.
-  Usare l' `order-by-primary` opzione in mysqldump quando si esegue il dump dei database, in modo che i dati vengano scritti in ordine di chiave primaria.
-   Usare l' `disable-keys` opzione in mysqldump quando si esegue il dump dei dati, per disabilitare i vincoli di chiave esterna prima del caricamento. La disabilitazione di controlli di chiave esterna consente di migliorare le prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Usare le tabelle partizionate quando appropriato.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo, che potrebbe causare il raggiungimento di un limite di risorse e monitorare le risorse usando le metriche disponibili nell'portale di Azure. 
-   Usare l' `defer-table-indexes` opzione in mysqlpump quando si esegue il dump dei database, in modo che la creazione dell'indice avvenga dopo il caricamento dei dati della tabella.
-   Copiare i file di backup in un archivio BLOB di Azure ed eseguire il ripristino da questa posizione. Questo approccio dovrebbe essere molto più veloce rispetto all'esecuzione del ripristino in Internet.

## <a name="create-a-backup-file"></a>Creare un file di backup

Per eseguire il backup di un database MariaDB esistente nel server locale o in una macchina virtuale, eseguire il comando seguente usando mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

I parametri da specificare sono:
- *\<uname>*: Nome utente del database 
- *\<pass>*: Password per il database. si noti che non è presente spazio tra-p e la password. 
- *\<dbname>*: Nome del database 
- *\<backupfile.sql>*: Nome file per il backup del database 
- *\<--opt>*: L'opzione mysqldump 

Ad esempio, per eseguire il backup di un database denominato *TestDB* nel server MariaDB con il nome utente *testuser* e senza password in un file testdb_backup. SQL, usare il comando seguente. Il comando esegue il backup del database `testdb` in un file denominato `testdb_backup.sql`, che contiene tutte le istruzioni SQL necessarie per ricreare il database. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Per selezionare tabelle specifiche di cui eseguire il backup nel database, elencare i nomi di tabella, separati da spazi. Ad esempio, per eseguire il backup solo delle tabelle Tabella1 e Table2 da *TestDB*, seguire questo esempio: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Per eseguire il backup di più di un database contemporaneamente, utilizzare l'opzione--database ed elencare i nomi di database, separati da spazi. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Creare un database sul server di destinazione
Creare un database vuoto nel database di Azure per il server MariaDB di destinazione in cui si vuole eseguire la migrazione dei dati. Usare uno strumento come MySQL Workbench per creare il database. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per connettersi, trovare le informazioni di connessione nel riquadro **Panoramica** del database di Azure per MariaDB.

![Screenshot del riquadro della panoramica per un database di Azure per il server MariaDB nel portale di Azure.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

In MySQL Workbench aggiungere le informazioni di connessione.

![Screenshot del riquadro connessioni MySQL in MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Ripristinare il database di MariaDB
Dopo aver creato il database di destinazione, è possibile usare il comando MySQL o MySQL Workbench per ripristinare i dati nel database appena creato dal file dump.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

In questo esempio i dati vengono ripristinati nel database appena creato nel database di Azure di destinazione per il server MariaDB.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Esportare il database MariaDB con phpMyAdmin

Per esportare, è possibile usare lo strumento comune phpMyAdmin, che potrebbe essere già installato localmente nell'ambiente in uso. Per esportare il database MariaDB, eseguire le operazioni seguenti:
1. Aprire phpMyAdmin.
1. Nel riquadro sinistro selezionare il database e quindi selezionare il collegamento **Esporta** . Viene visualizzata una nuova pagina per eseguire il dump del database.
1. Nell'area **Esporta** selezionare il collegamento **Seleziona tutto** per scegliere le tabelle nel database. 
1. Nell'area **opzioni SQL** selezionare le opzioni appropriate. 
1. Selezionare l'opzione **Salva come file** e l'opzione di compressione corrispondente, quindi selezionare **Vai**. Al prompt, salvare il file localmente.

## <a name="import-your-database-by-using-phpmyadmin"></a>Importare il database con phpMyAdmin

Il processo di importazione è simile al processo di esportazione. Eseguire le operazioni seguenti:
1. Aprire phpMyAdmin. 
1. Nella pagina di installazione di phpMyAdmin selezionare **Aggiungi** per aggiungere il database di Azure per il server MariaDB. 
1. Immettere i dettagli della connessione e le informazioni di accesso.
1. Creare un database denominato in modo appropriato e selezionarlo nel riquadro sinistro. Per riscrivere il database esistente, selezionare il nome del database, selezionare tutte le caselle di controllo accanto ai nomi delle tabelle e selezionare **Drop** (Elimina) per eliminare le tabelle esistenti. 
1. Selezionare il collegamento **SQL** per visualizzare la pagina in cui è possibile immettere i comandi SQL o caricare il file SQL. 
1. Selezionare il pulsante **Sfoglia** per trovare il file di database. 
1. Selezionare il pulsante **Go (Vai** ) per esportare il backup, eseguire i comandi SQL e ricreare il database.

## <a name="next-steps"></a>Passaggi successivi
- [Connettere le applicazioni al database di Azure per MariaDB](./howto-connection-string.md).
