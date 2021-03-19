---
title: Eseguire la migrazione da Oracle
titleSuffix: Azure Database for PostgreSQL
description: Questa guida illustra come eseguire la migrazione dello schema Oracle al database di Azure per PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: ec6cf87b3fd326c905b4843dc30ae6ce15379305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609450"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Eseguire la migrazione di Oracle al database di Azure per PostgreSQL

Questa guida illustra come eseguire la migrazione dello schema Oracle al database di Azure per PostgreSQL. 

Per indicazioni dettagliate e complete sulla migrazione, vedere la [Guida alla migrazione risorse](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione dello schema Oracle al database di Azure per PostgreSQL, è necessario: 

- Verificare che l'ambiente di origine sia supportato. 
- Scaricare la versione più recente di [ora2pg](https://ora2pg.darold.net/). 
- Versione più recente del [modulo DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Panoramica

PostgreSQL è uno dei database open source più avanzati del mondo. Questo articolo descrive come usare l'utilità ora2pg gratuita per eseguire la migrazione di un database Oracle a PostgreSQL. È possibile usare ora2pg, uno strumento gratuito, per eseguire la migrazione di un database Oracle o MySQL a uno schema compatibile con PostgreSQL. L'utilità connette il database Oracle, lo analizza automaticamente ed estrae la struttura o i dati. Successivamente, ora2pg genera gli script SQL che è possibile caricare nel database PostgreSQL. ora2pg può essere usato per le attività da reverse engineering un database Oracle, per eseguire un'enorme migrazione di database aziendali o semplicemente per la replica di alcuni dati Oracle in un database PostgreSQL. È facile da usare e non richiede alcuna conoscenza del database Oracle, oltre alla possibilità di fornire i parametri necessari per connettersi al database Oracle.

> [!NOTE]
> Per ulteriori informazioni sull'utilizzo della versione più recente di ora2pg, vedere la [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Architettura di migrazione ora2pg tipica

![Screenshot dell'architettura di migrazione di ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Dopo il provisioning della macchina virtuale e del database di Azure per PostgreSQL, sono necessarie due configurazioni per abilitare la connettività tra di essi: **consentire i servizi di Azure** e applicare la **connessione SSL**, illustrato di seguito:

- Pannello **sicurezza connessione** -> **consentire l'accesso ai servizi di Azure** ->

- Pannello **sicurezza connessione** -> **Impostazioni SSL**  ->  **applicano la connessione SSL** -> disabilitata

### <a name="recommendations"></a>Consigli

- Per migliorare le prestazioni delle operazioni di valutazione o esportazione nel server Oracle, raccogliere le statistiche come segue:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Esportare i dati usando il comando COPY anziché INSERT.

- Evitare di esportare tabelle con FKs, vincoli e indici. in questo modo sarà più lento importare i dati in PostgreSQL.

- Creare viste materializzate usando la **clausola no data** e aggiornarle in un secondo momento.

- Se possibile, implementare indici univoci nelle viste materializzate, in modo da rendere più veloce l'aggiornamento con la sintassi `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>Pre-migrazione 

Dopo aver verificato che l'ambiente di origine è supportato e aver verificato che siano stati risolti i prerequisiti, è possibile avviare la fase di pre-migrazione. Questa parte del processo comporta l'esecuzione di un inventario dei database di cui è necessario eseguire la migrazione, la valutazione di tali database per potenziali problemi o blocchi di migrazione e la risoluzione di eventuali elementi che potrebbero essere stati individuati. Per le migrazioni eterogeneo, ad esempio Oracle, per database di Azure per PostgreSQL, questa fase comporta anche la conversione degli schemi nei database di origine per la compatibilità con l'ambiente di destinazione.

### <a name="discover"></a>Rilevazione

L'obiettivo della fase di individuazione è quello di identificare le origini dati esistenti e i dettagli sulle funzionalità utilizzate per ottenere una migliore comprensione e pianificazione della migrazione. Questo processo comporta l'analisi della rete per identificare tutte le istanze Oracle dell'organizzazione insieme alla versione e alle funzionalità in uso.

Gli script di pre-valutazione di Microsoft Oracle vengono eseguiti sul database Oracle. Gli script di pre-valutazione sono un set di query che raggiunge i metadati Oracle e fornisce gli elementi seguenti:

- Inventario del database, inclusi i conteggi degli oggetti per schema, tipo e stato.

- Stima approssimativa dei dati non elaborati in ogni schema (in base alle statistiche).

- Ridimensionamento delle tabelle in ogni schema.

- Il numero di righe di codice per pacchetto, funzione, procedura e così via.

Scaricare gli script correlati dal [sito Web ora2pg](http://ora2pg.darold.net/).

### <a name="assess"></a>Valutare

Dopo aver completato l'inventario dei database Oracle per ottenere un'idea delle dimensioni del database e di quali sono le questioni, il passaggio successivo consiste nell'eseguire la valutazione.

Non è facile stimare il costo di un processo di migrazione da Oracle a PostgreSQL. Per ottenere una valutazione efficace del costo della migrazione, ora2pg analizzerà tutti gli oggetti di database, tutte le funzioni e le stored procedure per rilevare se esistono ancora alcuni oggetti e codice PL/SQL che non possono essere convertiti automaticamente da ora2pg.

ora2pg dispone di una modalità di analisi del contenuto che controlla il database Oracle per generare un report di testo sul contenuto del database Oracle e non è possibile esportarlo.

Per attivare la modalità di **analisi e report** , usare il tipo esportato `SHOW_REPORT` come illustrato nel comando seguente:

```
ora2pg -t SHOW_REPORT
```

Dopo che il database è stato analizzato, ora2pg, con la capacità di convertire il codice SQL e PL/SQL dalla sintassi Oracle a PostgreSQL, può andare oltre stimando le difficoltà del codice e il tempo necessario per eseguire una migrazione completa del database.

Per stimare il costo della migrazione in giorni lavorativi, ora2pg consente di usare una direttiva di configurazione denominata ESTIMATE_COST, che può essere abilitata anche alla riga di comando:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

L'unità di migrazione predefinita rappresenta circa cinque minuti per un esperto PostgreSQL. Se si tratta della prima migrazione, è possibile ottenerla più in alto con la direttiva di configurazione COST_UNIT_VALUE o l'opzione della riga di comando--cost_unit_value.

L'ultima riga del report Mostra il codice di migrazione stimato totale in giorni lavorativi che segue il numero di unità di migrazione stimate per ogni oggetto.

Questa unità di migrazione rappresenta circa cinque minuti per un esperto PostgreSQL. Se si tratta della prima migrazione, è possibile aumentare il valore predefinito con la direttiva di configurazione COST_UNIT_VALUE o l'opzione della riga di comando--cost_unit_value. Di seguito sono riportate alcune varianti di assessment a) valutazione delle tabelle; b) valutazione dello schema per la valutazione delle colonne c) utilizzando la valutazione dello schema cost_unit (5 min) d) utilizzando 10 minuti come unità di costo.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

L'output della valutazione dello schema è illustrato di seguito:

**Livello di migrazione: B-5**

Livelli di migrazione:

Una migrazione che può essere eseguita automaticamente

B-migrazione con riscrittura del codice e costo dei giorni lavorativi fino a 5 giorni

C-migrazione con riscrittura del codice e costo dei giorni lavorativi superiore a 5 giorni

Livelli tecnici:

1 = Trivial: nessuna funzione archiviata e nessun trigger

2 = facile: nessuna funzione archiviata ma con trigger, nessuna riscrittura manuale

3 = semplice: funzioni e/o trigger archiviati, nessuna riscrittura manuale

4 = manuale: nessuna funzione archiviata ma con trigger o viste con la riscrittura del codice

5 = difficile: funzioni e/o trigger archiviati con la riscrittura del codice

La valutazione è costituita da una lettera (A o B) per specificare se la migrazione necessita o meno di una riscrittura manuale e un numero compreso tra 1 e 5 per indicare il livello di difficoltà tecnico. Si dispone di un'opzione aggiuntiva-human_days_limit per specificare il numero di giorni in cui il livello di migrazione deve essere impostato su C per indicare che è necessaria una grande quantità di lavoro e una gestione completa del progetto con supporto per la migrazione. Il valore predefinito è 10 giorni umani. È possibile utilizzare la direttiva di configurazione HUMAN_DAYS_LIMIT per modificare il valore predefinito in modo permanente.

Questa funzionalità è stata sviluppata per consentire di decidere quale database potrebbe essere migrato per primo e qual è il team che è necessario mobilitare.

### <a name="convert"></a>Conversione

Con le migrazioni con tempi di inattività minimi, l'origine di cui si esegue la migrazione continua a cambiare, allontanandosi dalla destinazione in termini di dati e schema, dopo la migrazione unica. Durante la fase di **sincronizzazione dei dati** è necessario assicurarsi che tutte le modifiche nell'origine vengano acquisite e applicate alla destinazione in tempo quasi reale. Dopo aver verificato che tutte le modifiche nell'origine siano state applicate alla destinazione, è possibile cutover dall'origine all'ambiente di destinazione.

In questo passaggio della migrazione viene eseguita la conversione o la traduzione del codice Oracle + DDL in PostgreSQL. Lo strumento ora2pg Esporta automaticamente gli oggetti Oracle in un formato PostgreSQL. Per gli oggetti generati, alcuni non verranno compilati nel database PostgreSQL senza modifiche manuali.  
Il processo di comprensione degli elementi che richiedono interventi manuali è costituito dalla compilazione dei file generati da ora2pg nel database PostgreSQL, dalla verifica del log e dall'esecuzione delle modifiche necessarie fino a quando tutta la struttura dello schema non è compatibile con la sintassi PostgreSQL.


#### <a name="create-migration-template"></a>Crea modello di migrazione 

Per prima cosa, è consigliabile creare il modello di migrazione fornito con ora2pg. Le due opzioni--project_base e--init_project quando utilizzato indicano a ora2pg di creare un modello di progetto con una struttura ad albero del lavoro, un file di configurazione e uno script per esportare tutti gli oggetti dal database Oracle. Per ulteriori informazioni, vedere la [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html).

   Usare il comando seguente: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Output di esempio: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

La directory/origini contiene il codice Oracle, lo schema o la directory contiene il codice trasferito a PostgreSQL. La directory Reports/contiene i report HTML con la valutazione dei costi di migrazione.


Dopo la creazione della struttura del progetto, viene creato un file di configurazione generico. Definire la connessione al database Oracle, nonché i parametri di configurazione pertinenti nel file config.  Vedere la documentazione di ora2pg per informazioni su ciò che è possibile configurare nel file di configurazione e su come.


#### <a name="export-oracle-objects"></a>Esporta oggetti Oracle

Esportare quindi gli oggetti Oracle come oggetti PostgreSQL eseguendo il file export_schema. sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
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

   Per estrarre i dati, usare il comando seguente:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Compila file

Infine, compilare tutti i file nel database di Azure per il server PostgreSQL. È ora possibile scegliere di caricare i file DDL generati manualmente oppure utilizzare il secondo script import_all. sh per importare tali file in modo interattivo.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Comando di importazione dati:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Durante la compilazione dei file, controllare i log e correggere le sintassi necessarie che ora2pg non è stato in grado di convertire.

Vedere le soluzioni alternative per la migrazione da white paper [Oracle al database di Azure per PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) per il supporto per risolvere i problemi.

## <a name="migrate"></a>Migrate 

Una volta soddisfatti i prerequisiti necessari e aver completato le attività associate alla fase di **pre-migrazione** , si è pronti per eseguire la migrazione dello schema e dei dati.

### <a name="migrate-schema-and-data"></a>Eseguire la migrazione dello schema e dei dati

Una volta applicate le correzioni, una build stabile del database è pronta per la distribuzione.

A questo punto, è necessario eseguire i comandi di importazione *PSQL* , che puntano ai file che contengono il codice modificato per compilare gli oggetti di database nel database PostgreSQL e importare i dati.

In questo passaggio è possibile implementare un certo livello di parallelismo nell'importazione dei dati.

### <a name="data-sync-and-cutover"></a>Sincronizzazione dati e cutover

Con le migrazioni in linea (minimo-tempo di inattività), l'origine di cui si esegue la migrazione continua a cambiare, allontanandosi dalla destinazione in termini di dati e schema, dopo la migrazione unica. Durante la fase di **sincronizzazione dei dati** è necessario assicurarsi che tutte le modifiche nell'origine vengano acquisite e applicate alla destinazione in tempo quasi reale. Dopo aver verificato che tutte le modifiche nell'origine siano state applicate alla destinazione, è possibile cutover dall'origine all'ambiente di destinazione.

A partire dal 2019 marzo, se si vuole eseguire una migrazione in linea, provare a usare Attunity replicate for Microsoft Migrations o StriIm.

Per la migrazione *Delta/incrementale* tramite ora2pg, la tecnica consiste nell'applicare per ogni tabella una query che applica un filtro (taglia) in base a data o ora e così via, e successivamente alla finalizzazione della migrazione applicando una seconda query che eseguirà la migrazione del resto dei dati (avanzi).

Nella tabella dati di origine eseguire prima la migrazione di tutti i dati cronologici. Di seguito è riportato un esempio:

```
select * from table1 where filter_data < 01/01/2019
```

È possibile eseguire una query sulle modifiche apportate dopo la migrazione iniziale eseguendo un comando simile al seguente:

```
select * from table1 where filter_data >= 01/01/2019
```

In questo caso, è consigliabile migliorare la convalida controllando la parità dei dati su entrambi i lati, l'origine e la destinazione.

## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di **migrazione** , è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

Dopo la migrazione dei dati alla destinazione, eseguire i test sui database per verificare che le applicazioni vengano eseguite correttamente rispetto alla destinazione dopo la migrazione.

Per garantire che l'origine e la destinazione siano migrate correttamente, eseguire gli script di convalida dei dati manuali nei database di destinazione Oracle e PostgreSQL.

Idealmente, se i database di origine e di destinazione hanno un percorso di rete, ora2pg deve essere usato per la convalida dei dati. Utilizzando l'azione di TEST è possibile verificare che tutti gli oggetti del database Oracle siano stati creati in PostgreSQL. Eseguire il comando come illustrato:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza, nonché per risolvere i problemi di prestazioni del carico di lavoro.

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione del mondo reale.

| **Collegamento titolo** | **Descrizione**    |
| -------------- | ------------------ |
| [Guida di riferimento dettagliata per la migrazione da Oracle ad Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Questo documento ha lo scopo di fornire a architetti, consulenti, DBA e ruoli correlati una guida per la migrazione rapida dei carichi di lavoro da Oracle al database di Azure per PostgreSQL usando lo strumento ora2pg. |
| [Soluzioni alternative per la migrazione da Oracle ad Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Questo documento ha lo scopo di fornire ad architetti, consulenti, DBA e ruoli correlati una guida per correggere o risolvere i problemi durante la migrazione dei carichi di lavoro da Oracle al database di Azure per PostgreSQL. |
| [Passaggi per l'installazione di ora2pg in Windows o Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Questo documento è destinato a essere usato come guida all'installazione rapida per abilitare la migrazione dei dati dello schema & da Oracle a database di Azure per PostgreSQL usando lo strumento ora2pg in Windows o Linux. I dettagli completi sullo strumento sono disponibili in http://ora2pg.darold.net/documentation.html . |

Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.


### <a name="contact-support"></a>Contattare il supporto tecnico

Se è necessaria assistenza per le migrazioni oltre gli strumenti ora2pg, contattare l'alias di [ @Ask Azure DB per PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) per informazioni su altre opzioni di migrazione.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati (e attività speciali), vedere l'articolo [servizio e strumenti per la migrazione dei dati](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Per altre informazioni, vedere: 
- [Documentazione del Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentazione di ora2pg](https://ora2pg.darold.net/documentation.html)
- [Sito Web PostgreSQL](https://www.postgresql.org/)
- [Supporto delle transazioni autonome in PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Per contenuti video: 
- [Panoramica del percorso di migrazione e degli strumenti e dei servizi consigliati per eseguire la valutazione e la migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).