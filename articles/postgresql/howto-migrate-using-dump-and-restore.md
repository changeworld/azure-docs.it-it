---
title: Dump e ripristino-database di Azure per PostgreSQL-server singolo
description: È possibile estrarre un database PostgreSQL in un file dump. Quindi, è possibile eseguire il ripristino da un file creato da pg_dump nel server singolo database di Azure per PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450056"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Eseguire la migrazione del database PostgreSQL tramite dump e Restore
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

È possibile usare [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) per estrarre un database PostgreSQL in un file dump. Usare quindi [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) per ripristinare il database PostgreSQL da un file di archivio creato da `pg_dump` .

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:
- Un [database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md), incluse le regole del firewall per consentire l'accesso.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilità della riga di comando installate.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Creare un file dump contenente i dati da caricare

Per eseguire il backup di un database PostgreSQL in locale o in una macchina virtuale, eseguire questo comando:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Se, ad esempio, si dispone di un server locale e di un database denominato **TestDB** , eseguire:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Ripristinare i dati nel database di destinazione

Dopo aver creato il database di destinazione, è possibile utilizzare il `pg_restore` comando e il  `--dbname` parametro per ripristinare i dati nel database di destinazione dal file di dump.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Se si include il `--no-owner` parametro, tutti gli oggetti creati durante il ripristino saranno di proprietà dell'utente specificato con `--username` . Per ulteriori informazioni, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Nei server di database di Azure per PostgreSQL le connessioni TLS/SSL sono attivate per impostazione predefinita. Se il server PostgreSQL richiede connessioni TLS/SSL, ma non le ha, impostare una variabile di ambiente `PGSSLMODE=require` in modo che lo strumento di pg_restore si connetta con TLS. Senza TLS, l'errore potrebbe leggere: "irreversibile: è richiesta la connessione SSL. Specificare le opzioni SSL e riprovare ". Nella riga di comando di Windows eseguire il comando `SET PGSSLMODE=require` prima di eseguire il `pg_restore` comando. In Linux o bash eseguire il comando `export PGSSLMODE=require` prima di eseguire il `pg_restore` comando. 
>

In questo esempio, ripristinare i dati dal file di dump **TestDB. dump** nel database **mypgsqldb** nel server di destinazione **mydemoserver.postgres.database.Azure.com**.

Ecco un esempio di come usare questo per un `pg_restore` singolo server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Di seguito è riportato un esempio di come usare questo `pg_restore` per un server flessibile:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Ottimizzare il processo di migrazione

Un modo per eseguire la migrazione del database PostgreSQL esistente al database di Azure per PostgreSQL consiste nel eseguire il backup del database nell'origine e ripristinarlo in Azure. Per ridurre al minimo il tempo necessario per completare la migrazione, è consigliabile usare i parametri seguenti con i comandi di backup e ripristino.

> [!NOTE]
> Per informazioni dettagliate sulla sintassi, vedere [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Per il backup

Eseguire il backup con l' `-Fc` opzione, in modo che sia possibile eseguire il ripristino in parallelo per velocizzare l'operazione. Ad esempio:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>Per il ripristino

- Spostare il file di backup in una macchina virtuale di Azure nella stessa area del database di Azure per il server PostgreSQL a cui si sta eseguendo la migrazione. Eseguire `pg_restore` da tale macchina virtuale per ridurre la latenza di rete. Creare la macchina virtuale con [rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) abilitata.

- Aprire il file dump per verificare che le istruzioni create index siano successive all'inserimento dei dati. In caso contrario, spostare le istruzioni CREATE INDEX dopo aver inserito i dati. Questa operazione dovrebbe essere già eseguita per impostazione predefinita, ma è consigliabile confermare.

- Ripristinare con le opzioni `-Fc` e `-j` (con un numero) per parallelizzare il ripristino. Il numero specificato è il numero di core nel server di destinazione. È anche possibile impostare il doppio del numero di core del server di destinazione per verificarne l'effetto.

    Ecco un esempio di come usare questo per un `pg_restore` singolo server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Di seguito è riportato un esempio di come usare questo `pg_restore` per un server flessibile:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- È anche possibile modificare il file dump aggiungendo il comando `set synchronous_commit = off;` all'inizio e il comando `set synchronous_commit = on;` alla fine. Se non lo si accende alla fine, prima che le app modifichino i dati, potrebbe verificarsi una perdita di dati successiva.

- Nel server di Database di Azure per PostgreSQL di destinazione è consigliabile eseguire le seguenti operazioni prima del ripristino:
    
  - Disattiva rilevamento prestazioni query. Queste statistiche non sono necessarie durante la migrazione. È possibile eseguire questa operazione impostando `pg_stat_statements.track` , `pg_qs.query_capture_mode` e `pgms_wait_sampling.query_capture_mode` su `NONE` .

  - Usare uno SKU di memoria e di calcolo elevato, ad esempio 32 vCore con ottimizzazione per la memoria, per velocizzare la migrazione. Al termine del ripristino, è possibile eseguire facilmente la scalabilità verso il basso allo SKU preferito. Maggiore è lo SKU, maggiore è il parallelismo che è possibile ottenere aumentando il `-j` parametro corrispondente nel `pg_restore` comando.

  - Un maggior numero di IOPS nel server di destinazione può migliorare le prestazioni di ripristino. È possibile effettuare il provisioning di più operazioni di I/O al secondo aumentando le dimensioni delle risorse di archiviazione del server. Questa impostazione non è reversibile, ma è opportuno valutare se un numero maggiore di IOPS potrebbe trarre vantaggio dal carico di lavoro effettivo in futuro.

Ricordarsi di testare e validare questi comandi in un ambiente di test prima di usarli in produzione.

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire la migrazione di un database PostgreSQL usando l'esportazione e l'importazione, vedere [eseguire la migrazione del database PostgreSQL con l'esportazione e l'importazione](howto-migrate-using-export-and-import.md).
- Per altre informazioni sulla migrazione dei database in Database di Azure per PostgreSQL, vedere [Database Migration Guide](https://aka.ms/datamigration) (Guida alla migrazione di database).


