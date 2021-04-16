---
title: Procedure consigliate per il caricamento dei dati per pool SQL dedicati
description: Raccomandazioni e ottimizzazioni delle prestazioni per il caricamento di dati tramite pool SQL dedicati in Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 294b2a5188f0dfd8cb29f21bdbb29236b1740231
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565866"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Procedure consigliate per il caricamento di dati tramite pool SQL dedicati in Azure Synapse Analytics

Questo articolo illustra le raccomandazioni e le ottimizzazioni delle prestazioni per il caricamento dei dati usando un pool SQL dedicato.

## <a name="preparing-data-in-azure-storage"></a>Preparazione dei dati in Archiviazione di Azure

Per ridurre al minimo la latenza, spostare il livello di archiviazione e il pool SQL dedicato.

In caso di esportazione di dati in un formato di file ORC, quando sono presenti colonne di testo di grandi dimensioni potrebbero verificarsi errori di memoria insufficiente di Java. Per risolvere questo problema, esportare solo un subset di colonne.

Tutti i formati di file hanno caratteristiche di prestazioni diverse. Per ottenere la velocità di caricamento massima, usare file di testo delimitati compressi. La differenza di prestazioni tra UTF-8 e UTF-16 è minima.

Suddividere i file compressi di grandi dimensioni in file compressi di dimensioni inferiori.

## <a name="running-loads-with-enough-compute"></a>Esecuzione di carichi con risorse di calcolo sufficienti

Per ottenere la velocità di caricamento massima, eseguire un solo processo di caricamento alla volta. Se ciò non è fattibile, eseguire un numero minimo di caricamenti contemporaneamente. Se si prevede un processo di caricamento di grandi dimensioni, è consigliabile aumentare le dimensioni del pool SQL dedicato prima del caricamento.

Per eseguire i caricamenti con risorse di calcolo appropriate, creare utenti designati addetti al caricamento. Classificare ogni utente di caricamento in un gruppo di carico di lavoro specifico. Per eseguire un carico, accedere come uno degli utenti in fase di caricamento e quindi eseguire il carico. Il carico viene eseguito con il gruppo di carico di lavoro dell'utente.  

### <a name="example-of-creating-a-loading-user"></a>Esempio di creazione di un utente addetto al caricamento

Questo esempio crea un utente di caricamento classificato in un gruppo di carico di lavoro specifico. Il primo passaggio consiste nel **connettersi al master** e creare un account di accesso.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Connettersi al pool SQL dedicato e creare un utente. Il codice seguente presuppone che l'utente sia connesso al database denominato mySampleDataWarehouse. Illustra come creare un utente denominato loader e concede all'utente le autorizzazioni per creare tabelle e caricarlo usando [l'istruzione COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Quindi classifica l'utente nel gruppo di carico di lavoro DataLoads con il numero massimo di risorse. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Si tratta di un esempio estremio di allocazione del 100% delle risorse del pool SQL a un singolo carico. In questo modo, la concorrenza massima sarà di 1. Tenere presente che deve essere usato solo per il carico iniziale in cui sarà necessario creare gruppi di carico di lavoro aggiuntivi con le proprie configurazioni per eseguire l'analisi delle risorse tra i carichi di lavoro. 

Per eseguire un carico con risorse per il gruppo di carico di lavoro di caricamento, accedere come caricatore ed eseguire il carico.

## <a name="allowing-multiple-users-to-load-polybase"></a>Consentire il caricamento di più utenti (PolyBase)

Spesso è necessario che più utenti carichino i dati in un pool SQL dedicato. Il caricamento [con CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (PolyBase) richiede le autorizzazioni CONTROL del database.  L'autorizzazione CONTROL fornisce il controllo degli accessi a tutti gli schemi.

È consigliabile che non tutti gli utenti che eseguono caricamenti abbiano il controllo degli accessi a tutti gli schemi. Per limitare le autorizzazioni, usare l'istruzione DENY CONTROL.

Si supponga ad esempio che esistano gli schemi di database schema_A per il reparto A e schema_B per il reparto B e che gli utenti di database user_A e user_B siano gli utenti addetti al caricamento PolyBase rispettivamente nel reparto A e nel reparto B. A entrambi gli utenti sono state concesse le autorizzazioni di database CONTROL. Gli autori di schema A e B usano a questo punto DENY per bloccare i rispettivi schemi:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B sono ora bloccati dallo schema dell'altro dept.

## <a name="loading-to-a-staging-table"></a>Caricamento in una tabella di staging

Per ottenere la velocità di caricamento più rapida per lo spostamento dei dati in una tabella del pool SQL dedicata, caricare i dati in una tabella di staging.  Definire la tabella di staging come heap e usare round robin per l'opzione di distribuzione.

Si consideri che il caricamento è in genere un processo in due passaggi in cui si caricano prima in una tabella di staging e quindi si inseriscono i dati in una tabella del pool SQL dedicata di produzione. Se la tabella di produzione usa una distribuzione hash, il tempo totale necessario per il caricamento e l'inserimento può essere ridotto se si definisce una tabella di staging con la distribuzione hash.

Il caricamento nella tabella di staging richiede più tempo, ma il secondo passaggio di inserimento delle righe nella tabella di produzione non comporta lo spostamento dei dati tra le distribuzioni.

## <a name="loading-to-a-columnstore-index"></a>Caricamento in un indice columnstore

Gli indici columnstore richiedono una grande quantità di memoria per la compressione dei dati in rowgroup di qualità elevata. Per una compressione e un'efficienza dell'indice ottimali, l'indice columnstore deve comprimere il valore massimo di 1.048.576 righe in ogni rowgroup.

In caso di utilizzo elevato di memoria, l'indice columnstore potrebbe non riuscire a raggiungere i tassi di compressione massimi. Questo scenario, a sua volta, ha effetto sulle prestazioni delle query. Per un approfondimento, vedere l'articolo relativo alle [ottimizzazioni della memoria columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Per garantire all'utente addetto al caricamento una quantità di memoria sufficiente per raggiungere i massimi tassi di compressione, usare utenti addetti al caricamento che siano membri di una classe di risorse di medie o grandi dimensioni.
- Caricare un numero di righe sufficiente a riempire completamente i nuovi rowgroup. Durante un caricamento bulk, ogni gruppo di 1.048.576 righe viene compresso direttamente nel columnstore come rowgroup completo. In caso di caricamenti con meno di 102.400 righe, le righe vengono inviate nell'archivio differenziale, in cui vengono mantenute in un indice albero B.

> [!NOTE]
> Se si caricano troppe righe, queste potrebbero essere tutte indirizzate all'archivio differenziale e non vengono compresse immediatamente nel formato columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumentare le dimensioni del batch quando si usa l'API SqLBulkCopy o bcp

Il caricamento con l'istruzione COPY fornirà la velocità effettiva più elevata con pool SQL dedicati. Se non è possibile usare COPY per caricare ed è necessario usare [l'API SqLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), è consigliabile aumentare le dimensioni del batch per una migliore velocità effettiva.

> [!TIP]
> Una dimensione del batch compresa tra 100 K e 1M è la linea di base consigliata per determinare la capacità ottimale delle dimensioni del batch.

## <a name="handling-loading-failures"></a>Gestione degli errori di caricamento

Un caricamento con una tabella esterna può avere esito negativo con l'errore *"Query interrotta. È stata raggiunta la soglia massima di rifiuti durante la lettura da un'origine esterna"*. Questo messaggio indica che i dati esterni contengono record dirty.

Un record di dati viene considerato dirty se soddisfa una delle condizioni seguenti:

- I tipi di dati e il numero di colonne non corrispondono alle definizioni di colonna della tabella esterna.
- I dati non sono conformi al formato di file esterno specificato.

Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato di file esterno siano corrette e che i dati esterni siano conformi a queste definizioni.

Se un subset di record di dati esterni è dirty, è possibile scegliere di rifiutare questi record per le query usando le opzioni di rifiuto in [CREATE EXTERNAL TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="inserting-data-into-a-production-table"></a>Inserimento di dati in una tabella di produzione

Per una singola operazione di caricamento in una tabella di piccole dimensioni con un'[istruzione INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) o anche un ricaricamento periodico di una ricerca, è possibile ottenere prestazioni soddisfacenti con un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Gli inserimenti singleton non offrono tuttavia la stessa efficienza di un caricamento bulk.

Se nel corso di una giornata si eseguono migliaia di singoli inserimenti o più, inviare in batch gli inserimenti per poterne eseguire il caricamento bulk.  Sviluppare i processi per aggiungere i singoli inserimenti in un file e quindi creare un altro processo che carica periodicamente il file.

## <a name="creating-statistics-after-the-load"></a>Creazione di statistiche dopo il caricamento

Per migliorare le prestazioni delle query, è importante creare statistiche su tutte le colonne di tutte le tabelle dopo il primo caricamento o modifiche sostanziali ai dati. La creazione di statistiche può essere eseguita manualmente oppure è possibile abilitare [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Per una spiegazione dettagliata delle statistiche, vedere [Statistiche](sql-data-warehouse-tables-statistics.md). L'esempio seguente illustra come creare manualmente statistiche in cinque colonne della Customer_Speed tabella.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Ruotare le chiavi di archiviazione (PolyBase)

Ai fini della sicurezza è consigliabile modificare regolarmente la chiave di accesso all'archivio BLOB. Sono disponibili due chiavi di archiviazione per l'account di archiviazione BLOB ed è quindi possibile passare da una chiave all'altra.

Per ruotare le chiavi dell'account di archiviazione di Azure:

Per ogni account di archiviazione la cui chiave ha subito modifiche, eseguire [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Esempio:

Creazione della chiave originale

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotazione della chiave dalla chiave 1 alla chiave 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Non sono necessarie altre modifiche alle origini dati esterne.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'istruzione COPY o PolyBase durante la progettazione di un processo ELT (Extract, Load, and Transform), vedere [Progettare ELT per](design-elt-data-loading.md)Azure Synapse Analytics .
- Per un'esercitazione sul caricamento, [usare l'istruzione COPY per caricare dati dall'archivio BLOB](./load-data-from-azure-blob-storage-using-copy.md)di Azure Synapse SQL .
- Per monitorare il caricamento dei dati, vedere [Monitoraggio del carico di lavoro mediante DMV](sql-data-warehouse-manage-monitor.md).