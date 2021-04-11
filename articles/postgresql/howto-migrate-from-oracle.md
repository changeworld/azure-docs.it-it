---
title: 'Da Oracle a database di Azure per PostgreSQL: Guida alla migrazione'
titleSuffix: Azure Database for PostgreSQL
description: Questa guida illustra come eseguire la migrazione dello schema Oracle al database di Azure per PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968935"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Eseguire la migrazione di Oracle al database di Azure per PostgreSQL

Questa guida illustra come eseguire la migrazione dello schema Oracle al database di Azure per PostgreSQL. 

Per indicazioni dettagliate e complete sulla migrazione, vedere la [Guida alla migrazione risorse](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione dello schema Oracle al database di Azure per PostgreSQL, è necessario: 

- Verificare che l'ambiente di origine sia supportato. 
- Scaricare la versione più recente di [ora2pg](https://ora2pg.darold.net/). 
- Disporre della versione più recente del [modulo DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Panoramica

PostgreSQL è uno dei database open source più avanzati del mondo. Questo articolo descrive come usare lo strumento ora2pg gratuito per eseguire la migrazione di un database Oracle a PostgreSQL. È possibile usare ora2pg per eseguire la migrazione di un database Oracle o di un database MySQL a uno schema compatibile con PostgreSQL. 

Lo strumento ora2pg connette il database Oracle, lo analizza automaticamente ed estrae la struttura o i dati. Ora2pg genera quindi gli script SQL che è possibile caricare nel database PostgreSQL. È possibile usare ora2pg per attività come la Reverse Engineering di un database Oracle, la migrazione di un database aziendale di grandi dimensioni o semplicemente la replica di alcuni dati Oracle in un database PostgreSQL. Lo strumento è facile da utilizzare e non richiede alcuna conoscenza del database Oracle, oltre alla possibilità di fornire i parametri necessari per connettersi al database Oracle.

> [!NOTE]
> Per ulteriori informazioni sull'utilizzo della versione più recente di ora2pg, vedere la [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Architettura di migrazione ora2pg tipica

![Screenshot dell'architettura di migrazione di ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Dopo aver effettuato il provisioning della macchina virtuale e del database di Azure per PostgreSQL, sono necessarie due configurazioni per abilitare la connettività tra di essi: **consentire l'accesso ai servizi di Azure** e **applicare la connessione SSL**: 

- Pannello **sicurezza connessione** > **consentire l'accesso ai servizi di Azure**  >  **in**

- Pannello **sicurezza connessione** > **Impostazioni SSL**  >  **applica la connessione SSL**  >  **disabilitata**

### <a name="recommendations"></a>Consigli

- Per migliorare le prestazioni delle operazioni di valutazione o esportazione nel server Oracle, raccogliere le statistiche:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Esportare i dati utilizzando il `COPY` comando anziché `INSERT` .

- Evitare l'esportazione di tabelle con chiavi esterne (FKs), vincoli e indici. Questi elementi rallentano il processo di importazione dei dati in PostgreSQL.

- Creare viste materializzate usando la *clausola no data*. Aggiornare quindi le visualizzazioni in un secondo momento.

- Se possibile, utilizzare indici univoci nelle viste materializzate. Questi indici possono velocizzare l'aggiornamento quando si utilizza la sintassi `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Premigrazione 

Dopo aver verificato che l'ambiente di origine sia supportato e che siano stati risolti i prerequisiti, si è pronti per avviare la fase di pre-migrazione. Per iniziare: 

1. Eseguire l'inventario dei database di cui è necessario eseguire la migrazione. 
1. Valutare i database per individuare potenziali problemi o blocchi di migrazione.
1. Risolvere tutti gli elementi che sono stati scoperti. 
 
Per le migrazioni eterogeneo come Oracle al database di Azure per PostgreSQL, questa fase comporta anche la compatibilità degli schemi del database di origine con l'ambiente di destinazione.

### <a name="discover"></a>Rilevazione

L'obiettivo della fase di individuazione è quello di identificare le origini dati esistenti e i dettagli sulle funzionalità in uso. Questa fase consente una migliore comprensione e pianificazione della migrazione. Il processo prevede l'analisi della rete per identificare tutte le istanze Oracle dell'organizzazione insieme alla versione e alle funzionalità in uso.

Gli script di Prevalutazione Microsoft per Oracle vengono eseguiti sul database Oracle. Gli script di Prevalutazione eseguono una query sui metadati Oracle. Gli script forniscono:

- Inventario del database, inclusi i conteggi degli oggetti per schema, tipo e stato.
- Stima approssimativa dei dati non elaborati in ogni schema, in base alle statistiche.
- Dimensioni delle tabelle in ogni schema.
- Il numero di righe di codice per pacchetto, funzione, procedura e così via.

Scaricare gli script correlati dal [sito Web ora2pg](https://ora2pg.darold.net/).

### <a name="assess"></a>Valutare

Dopo aver eseguito l'inventario dei database Oracle, si avrà un'idea delle dimensioni del database e delle potenziali problemi. Il passaggio successivo consiste nell'eseguire la valutazione.

La stima del costo di una migrazione da Oracle a PostgreSQL non è semplice. Per valutare il costo della migrazione, ora2pg controlla tutti gli oggetti di database, le funzioni e le stored procedure per gli oggetti e il codice PL/SQL che non possono essere convertiti automaticamente.

Lo strumento ora2pg dispone di una modalità di analisi del contenuto che controlla il database Oracle per generare un report di testo. Il report descrive il contenuto del database Oracle e non è possibile esportarlo.

Per attivare la modalità di *analisi e report* , usare il tipo esportato `SHOW_REPORT` come illustrato nel comando seguente:

```
ora2pg -t SHOW_REPORT
```

Lo strumento ora2pg può convertire codice SQL e PL/SQL dalla sintassi Oracle a PostgreSQL. Quindi, dopo aver analizzato il database, ora2pg può stimare le difficoltà del codice e il tempo necessario per eseguire la migrazione di un database completo.

Per stimare il costo della migrazione in giorni umani, ora2pg consente di usare una direttiva di configurazione denominata `ESTIMATE_COST` . È anche possibile abilitare questa direttiva al prompt dei comandi:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

L'unità di migrazione predefinita rappresenta circa cinque minuti per un esperto PostgreSQL. Se questa migrazione è la prima volta, è possibile aumentare l'unità di migrazione predefinita usando la direttiva di configurazione `COST_UNIT_VALUE` o l' `--cost_unit_value` opzione della riga di comando.

L'ultima riga del report Mostra il codice di migrazione stimato totale in giorni umani. La stima segue il numero di unità di migrazione stimate per ogni oggetto.

Questa unità di migrazione rappresenta circa cinque minuti per un esperto PostgreSQL. Se questa migrazione è la prima volta, è possibile aumentare il valore predefinito usando la direttiva di configurazione `COST_UNIT_VALUE` o l'opzione della riga di comando `--cost_unit_value` . 

Nell'esempio di codice seguente vengono visualizzate alcune variazioni di valutazione: 
* Valutazione tabelle
* Valutazione colonne
* Valutazione dello schema che usa un'unità di costo predefinita di 5 minuti
* Valutazione dello schema che usa un'unità di costo di 10 minuti

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Di seguito è riportato l'output del livello di migrazione della valutazione dello schema B-5:

* Livelli di migrazione:

  * Una migrazione di che può essere eseguita automaticamente
    
  * B-migrazione con riscrittura del codice e costo dei giorni lavorativi fino a 5 giorni
    
  * C-migrazione con riscrittura del codice e costo dei giorni umani in 5 giorni
    
* Livelli tecnici:

   * 1 = Trivial: nessuna funzione archiviata e nessun trigger

   * 2 = facile: nessuna funzione archiviata, ma trigger; Nessuna riscrittura manuale

   * 3 = semplice: funzioni e/o trigger archiviati; Nessuna riscrittura manuale

   * 4 = manuale: nessuna funzione archiviata, ma trigger o viste con la riscrittura del codice

   * 5 = difficile: funzioni e/o trigger archiviati con la riscrittura del codice

La valutazione è costituita da: 
* Lettera (A o B) per specificare se la migrazione richiede la riscrittura manuale.

* Numero compreso tra 1 e 5 per indicare la difficoltà tecnica. 

Un'altra opzione, `-human_days_limit` , specifica il limite di giorni umani. In questo caso, impostare il livello di migrazione su C per indicare che la migrazione richiede una grande quantità di lavoro, la gestione completa del progetto e il supporto per la migrazione. Il valore predefinito è 10 giorni umani. È possibile utilizzare la direttiva `HUMAN_DAYS_LIMIT` di configurazione per modificare il valore predefinito in modo permanente.

Questa valutazione dello schema è stata sviluppata per aiutare gli utenti a decidere quale database migrare per primo e quali team si mobilitano.

### <a name="convert"></a>Conversione

Nelle migrazioni con tempi di inattività minimi, le modifiche apportate all'origine della migrazione. Deriva dalla destinazione in termini di dati e di schema dopo la migrazione unica. Durante la fase di *sincronizzazione dei dati* , assicurarsi che tutte le modifiche nell'origine vengano acquisite e applicate alla destinazione in tempo quasi reale. Dopo aver verificato che tutte le modifiche vengano applicate alla destinazione, è possibile *passare* dall'origine all'ambiente di destinazione.

In questo passaggio della migrazione, il codice Oracle e gli script DDL vengono convertiti o convertiti in PostgreSQL. Lo strumento ora2pg Esporta automaticamente gli oggetti Oracle in un formato PostgreSQL. Alcuni degli oggetti generati non possono essere compilati nel database PostgreSQL senza modifiche manuali.  

Per comprendere quali elementi richiedono un intervento manuale, compilare prima i file generati da ora2pg nel database PostgreSQL. Controllare il log, quindi apportare le modifiche necessarie fino a quando la struttura dello schema non è compatibile con la sintassi PostgreSQL.


#### <a name="create-a-migration-template"></a>Creare un modello di migrazione 

È consigliabile usare il modello di migrazione fornito da ora2pg. Quando si utilizzano le opzioni `--project_base` e `--init_project` , ora2pg crea un modello di progetto con una struttura ad albero del lavoro, un file di configurazione e uno script per esportare tutti gli oggetti dal database Oracle. Per ulteriori informazioni, vedere la [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html).

Usare il comando seguente: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Ecco l'output di esempio: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

La `sources/` directory contiene il codice Oracle. La `schema/` directory contiene il codice trasferito a PostgreSQL. E la `reports/` directory contiene i report HTML e la valutazione dei costi di migrazione.


Dopo la creazione della struttura del progetto, viene creato un file di configurazione generico. Definire la connessione al database Oracle e i parametri di configurazione pertinenti nel file di configurazione. Per ulteriori informazioni sul file di configurazione, vedere la [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Esporta oggetti Oracle

Esportare quindi gli oggetti Oracle come oggetti PostgreSQL eseguendo il file *export_schema. sh*.

```
cd /app/migration/mig_project
./export_schema.sh
```

Eseguire manualmente il comando seguente.

```
SET namespace="/app/migration/mig_project"

ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
%namespace%/config/ora2pg.conf
ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
%namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
%namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
ora2pg -p -t PROCEDURE -o procs.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
ora2pg -t SEQUENCE -o sequences.sql
%namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
%namespace%/config/ora2pg.conf
ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
%namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
%namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
```

Per estrarre i dati, usare il comando seguente.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Compila file

Infine, compilare tutti i file nel database di Azure per il server PostgreSQL. È possibile scegliere di caricare i file DDL generati manualmente oppure utilizzare il secondo script *import_all. sh* per importare tali file in modo interattivo.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Ecco il comando per l'importazione dei dati:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Durante la compilazione dei file, controllare i log e correggere qualsiasi sintassi che ora2pg non è in grado di convertire autonomamente.

Per altre informazioni, vedere [soluzioni alternative per la migrazione da Oracle a database di Azure per PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrate 

Dopo aver completato i prerequisiti necessari e aver completato i passaggi di premigrazione, è possibile avviare la migrazione dello schema e dei dati.

### <a name="migrate-schema-and-data"></a>Eseguire la migrazione dello schema e dei dati

Una volta apportate le correzioni necessarie, una build stabile del database è pronta per la distribuzione. Eseguire i `psql` comandi Import, che puntano ai file che contengono il codice modificato. Questa attività Compila gli oggetti di database nel database PostgreSQL e importa i dati.

In questo passaggio è possibile implementare un livello di parallelismo nell'importazione dei dati.

### <a name="sync-data-and-cut-over"></a>Sincronizzare i dati e ridurre

In una migrazione in linea (minima del tempo di inattività), l'origine della migrazione continua a cambiare. Deriva dalla destinazione in termini di dati e di schema dopo la migrazione unica. 

Durante la fase di *sincronizzazione dei dati* , assicurarsi che tutte le modifiche nell'origine vengano acquisite e applicate alla destinazione in tempo quasi reale. Una volta verificata l'applicazione di tutte le modifiche, è possibile passare dall'origine all'ambiente di destinazione.

Per eseguire una migrazione online, contattare il AskAzureDBforPostgreSQL@service.microsoft.com supporto tecnico.

In una migrazione *Delta/incrementale* che usa ora2pg, per ogni tabella, usare una query che filtra (*taglia*) per data, ora o un altro parametro. Quindi completare la migrazione utilizzando una seconda query che esegue la migrazione dei dati rimanenti.

Nella tabella dati di origine eseguire prima la migrazione di tutti i dati cronologici. Ecco un esempio:

```
select * from table1 where filter_data < 01/01/2019
```

È possibile eseguire una query sulle modifiche dopo la migrazione iniziale eseguendo un comando simile al seguente:

```
select * from table1 where filter_data >= 01/01/2019
```

In questo caso, è consigliabile migliorare la convalida controllando la parità dei dati su entrambi i lati, l'origine e la destinazione.

## <a name="postmigration"></a>Postmigrazione 

Dopo la fase di *migrazione* , completare le attività di post-migrazione per assicurarsi che tutto funzioni in modo semplice ed efficiente.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Il programma di installazione talvolta richiede modifiche alle applicazioni.

### <a name="test"></a>Test

Dopo la migrazione dei dati alla destinazione, eseguire i test sui database per verificare che le applicazioni funzionino correttamente con la destinazione. Verificare che l'origine e la destinazione siano migrate correttamente eseguendo gli script di convalida dei dati manuali nei database di destinazione Oracle e PostgreSQL.

Idealmente, se i database di origine e di destinazione hanno un percorso di rete, ora2pg deve essere usato per la convalida dei dati. È possibile usare l' `TEST` azione per assicurarsi che tutti gli oggetti del database Oracle siano stati creati in PostgreSQL. 

Eseguire questo comando:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Ottimizzazione

La fase di postmigrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza. In questa fase vengono anche affrontati i problemi di prestazioni del carico di lavoro.

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriori informazioni su questo scenario di migrazione, vedere le risorse seguenti. Supportano il coinvolgimento del progetto di migrazione nel mondo reale.

| Risorsa | Descrizione    |
| -------------- | ------------------ |
| [Cookbook per la migrazione da Oracle ad Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Questo documento consente a architetti, consulenti, amministratori di database e ruoli correlati di eseguire rapidamente la migrazione dei carichi di lavoro da Oracle al database di Azure per PostgreSQL usando ora2pg. |
| [Soluzioni alternative per la migrazione da Oracle ad Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Questo documento consente a architetti, consulenti, amministratori di database e ruoli correlati di risolvere rapidamente i problemi durante la migrazione dei carichi di lavoro da Oracle al database di Azure per PostgreSQL. |
| [Passaggi per l'installazione di ora2pg in Windows o Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Questo documento fornisce una guida all'installazione rapida per la migrazione dello schema e dei dati da Oracle al database di Azure per PostgreSQL tramite ora2pg in Windows o Linux. Per ulteriori informazioni, vedere la [documentazione di ora2pg](http://ora2pg.darold.net/documentation.html). |

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma di dati Microsoft Azure.

## <a name="more-support"></a>Ulteriore supporto

Per informazioni sulla migrazione oltre l'ambito degli strumenti di ora2pg, contattare il [ @Ask database di Azure per PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

Per una matrice di servizi e strumenti per la migrazione di database e dati e per attività speciali, vedere [Servizi e strumenti per la migrazione dei dati](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Documentazione: 
- [Documentazione del Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html)
- [Sito Web PostgreSQL](https://www.postgresql.org/)
- [Supporto delle transazioni autonome in PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
