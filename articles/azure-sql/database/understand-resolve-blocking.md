---
title: Comprendere e risolvere i problemi di blocco di SQL Azure
titleSuffix: Azure SQL Database
description: Panoramica degli argomenti specifici del database SQL di Azure sul blocco e sulla risoluzione dei problemi.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: e176c0399b191c7a511ea1d26388219b2cef1df8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107147"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Comprendere e risolvere i problemi di blocco del database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Obiettivo

L'articolo descrive il blocco nei database SQL di Azure e illustra come risolvere i problemi e risolvere il blocco. 

In questo articolo il termine connessione si riferisce a una singola sessione di accesso del database. Ogni connessione viene visualizzata come ID di sessione (SPID) o session_id in molti DMV. Ognuno di questi SPID viene spesso definito processo, anche se non si tratta di un contesto di processo separato nel senso usuale. Ogni SPID è invece costituito dalle risorse server e dalle strutture di dati necessarie per soddisfare le richieste di una singola connessione da un determinato client. Una singola applicazione client può avere una o più connessioni. Dal punto di vista del database SQL di Azure, non esiste alcuna differenza tra più connessioni da una singola applicazione client in un singolo computer client e più connessioni da più applicazioni client o più computer client; sono atomiche. Una connessione può bloccare un'altra connessione, indipendentemente dal client di origine.

> [!NOTE]
> **Questo contenuto è incentrato sul database SQL di Azure.** Il database SQL di Azure è basato sulla versione stabile più recente del motore di database di Microsoft SQL Server, quindi la maggior parte del contenuto è simile anche se le opzioni e gli strumenti per la risoluzione dei problemi potrebbero essere diversi. Per ulteriori informazioni sul blocco in SQL Server, vedere [comprendere e risolvere SQL Server problemi di blocco](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Informazioni sul blocco 
 
Il blocco è una caratteristica inevitabile e di progettazione di qualsiasi sistema di gestione di database relazionali (RDBMS) con concorrenza basata su blocchi. Come indicato in precedenza, in SQL Server, il blocco si verifica quando una sessione include un blocco su una risorsa specifica e un secondo SPID tenta di acquisire un tipo di blocco in conflitto sulla stessa risorsa. In genere, l'intervallo di tempo per il quale il primo SPID blocca la risorsa è ridotto. Quando la sessione proprietaria rilascia il blocco, la seconda connessione è quindi gratuita per acquisire il proprio blocco sulla risorsa e continuare l'elaborazione. Si tratta di un comportamento normale che può verificarsi molte volte nel corso di un giorno senza effetti evidenti sulle prestazioni del sistema.

La durata e il contesto di transazione di una query determinano per quanto tempo i blocchi vengono mantenuti e, quindi, il loro effetto su altre query. Se la query non viene eseguita all'interno di una transazione e non viene utilizzato alcun hint di blocco, i blocchi per le istruzioni SELECT verranno conservati solo su una risorsa nel momento in cui vengono effettivamente letti, non durante la query. Per le istruzioni INSERT, UPDATE e DELETE, i blocchi vengono mantenuti durante la query, sia per la coerenza dei dati sia per consentire il rollback della query, se necessario.

Per le query eseguite all'interno di una transazione, la durata per cui vengono mantenuti i blocchi è determinata dal tipo di query, dal livello di isolamento della transazione e dall'eventuale utilizzo di hint di blocco nella query. Per una descrizione del blocco, degli hint di blocco e dei livelli di isolamento delle transazioni, vedere gli articoli seguenti:

* [Blocchi nel motore di database](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Personalizzazione dell'utilizzo di blocchi e del controllo delle versioni delle righe](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Modalità di blocco](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Compatibilità blocchi](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transazioni](/sql/t-sql/language-elements/transactions-transact-sql)

Quando il blocco e il blocco vengono mantenuti fino al punto in cui si verifica un impatto negativo sulle prestazioni del sistema, il problema è dovuto a uno dei motivi seguenti:

* Uno SPID utilizza blocchi su un set di risorse per un periodo di tempo prolungato prima di rilasciarli. Questo tipo di blocco si risolve nel tempo, ma può causare un calo delle prestazioni.

* Uno SPID utilizza blocchi su un set di risorse e non li rilascia mai. Questo tipo di blocco non risolve se stesso e impedisce l'accesso alle risorse interessate per un periodo illimitato.

Nel primo scenario, la situazione può essere molto fluida, in quanto gli SPID diversi causano il blocco su risorse diverse nel tempo, creando una destinazione di trasferimento. Queste situazioni sono difficili da risolvere usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) per circoscrivere il problema a singole query. Al contrario, la seconda situazione produce uno stato coerente che può essere più facile da diagnosticare.

## <a name="applications-and-blocking"></a>Applicazioni e blocco

Potrebbe esserci una tendenza a concentrarsi sull'ottimizzazione sul lato server e sui problemi della piattaforma quando si verifica un problema di blocco. Tuttavia, l'attenzione a pagamento solo per il database potrebbe non comportare una risoluzione e può assorbire più tempo ed energia a cui si rivolge per esaminare l'applicazione client e le query inviate. Indipendentemente dal livello di visibilità esposto dall'applicazione in merito alle chiamate al database, un problema di blocco richiede spesso sia l'ispezione delle istruzioni SQL esatte inviate dall'applicazione sia il comportamento esatto dell'applicazione in relazione all'annullamento della query, alla gestione della connessione, al recupero di tutte le righe di risultati e così via. Se lo strumento di sviluppo non consente il controllo esplicito sulla gestione della connessione, l'annullamento di query, il timeout delle query, il recupero dei risultati e così via, i problemi di blocco potrebbero non essere risolvibili. Questo potenziale dovrebbe essere esaminato attentamente prima di selezionare uno strumento di sviluppo di applicazioni per il database SQL di Azure, in particolare per gli ambienti OLTP sensibili alle prestazioni. 

Prestare attenzione alle prestazioni del database durante la fase di progettazione e costruzione del database e dell'applicazione. In particolare, è necessario valutare il consumo di risorse, il livello di isolamento e la lunghezza del percorso delle transazioni per ogni query. Ogni query e transazione dovrebbe essere il più semplice possibile. È necessario esercitare una valida disciplina di gestione della connessione, senza di essa, l'applicazione può sembrare accettabile per le prestazioni a un numero ridotto di utenti, ma le prestazioni potrebbero peggiorare significativamente con la scalabilità verso l'alto del numero di utenti. 

Con la corretta progettazione di applicazioni e query, il database SQL di Azure è in grado di supportare molte migliaia di utenti simultanei in un singolo server, con un minimo blocco.

> [!Note]
> Per altre indicazioni sullo sviluppo di applicazioni, vedere [risoluzione dei problemi di connettività e altri errori con il database SQL di Azure e Azure sql istanza gestita e la](troubleshoot-common-errors-issues.md) [gestione degli errori temporanei](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Risoluzione dei problemi di blocco

Indipendentemente dalla situazione di blocco in cui ci troviamo, la metodologia per la risoluzione dei problemi di blocco è la stessa. Queste separazioni logiche determineranno il resto della composizione di questo articolo. Il concetto consiste nel trovare il blocco Head e identificare il comportamento della query e il motivo del blocco. Una volta identificata la query problematica (ovvero che cosa mantiene i blocchi per il periodo prolungato), il passaggio successivo consiste nell'analizzare e determinare il motivo per cui si verifica il blocco. Dopo aver compreso il motivo, è possibile apportare modifiche riprogettando la query e la transazione.

Passaggi per la risoluzione dei problemi:

1. Identificare la sessione di blocco principale (blocco Head)

2. Trovare la query e la transazione che causa il blocco (cosa trattiene i blocchi per un periodo prolungato)

3. Analizzare/comprendere il motivo per cui si verifica il blocco prolungato

4. Risolvere il problema di blocco riprogettando query e transazioni

Verrà ora illustrato come individuare la sessione di blocco principale con un'acquisizione dati appropriata.

## <a name="gather-blocking-information"></a>Raccogliere informazioni di blocco

Per contrastare la difficoltà di risoluzione dei problemi di blocco, un amministratore di database può usare gli script SQL che monitorano costantemente lo stato di blocco e blocco nel database SQL di Azure. Per raccogliere questi dati, esistono essenzialmente due metodi. 

Il primo consiste nell'eseguire una query sugli oggetti a gestione dinamica (DMOs) e archiviare i risultati per il confronto nel tempo. Alcuni oggetti a cui si fa riferimento in questo articolo sono viste a gestione dinamica (DMV) e alcuni sono funzioni a gestione dinamica (DMF). Il secondo metodo consiste nell'usare XEvent per acquisire ciò che è in esecuzione. 


## <a name="gather-information-from-dmvs"></a>Raccogliere informazioni da DMV

Il riferimento a DMV per risolvere i problemi di blocco ha lo scopo di identificare lo SPID (ID sessione) all'inizio della catena di blocco e l'istruzione SQL. Individuare gli SPID vittima bloccati. Se uno SPID è bloccato da un altro SPID, esaminare lo SPID che possiede la risorsa (SPID di blocco). Viene bloccato anche lo SPID del proprietario? È possibile passare alla catena per trovare il blocco Head, quindi esaminare il motivo per cui è in grado di mantenere il blocco.

Ricordarsi di eseguire ognuno di questi script nel database SQL di Azure di destinazione.

* I comandi sp_who e sp_who2 sono comandi precedenti per visualizzare tutte le sessioni correnti. Il sys.dm_exec_sessions DMV restituisce più dati in un set di risultati più semplice da interrogare e filtrare. Sono disponibili sys.dm_exec_sessions alla base di altre query. 

* Se è già stata identificata una determinata sessione, è possibile usare `DBCC INPUTBUFFER(<session_id>)` per trovare l'ultima istruzione inviata da una sessione. I risultati simili possono essere restituiti con la funzione di gestione dinamica sys.dm_exec_input_buffer (DMF), in un set di risultati più facile da eseguire in query e filtrare, fornendo le session_id e il request_id. Ad esempio, per restituire la query più recente inviata da session_id 66 e request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Fare riferimento alla sys.dm_exec_requests e fare riferimento alla colonna blocking_session_id. Quando blocking_session_id = 0, una sessione non è bloccata. Mentre sys.dm_exec_requests elenca solo le richieste attualmente in esecuzione, qualsiasi connessione (attiva o non) verrà elencata in sys.dm_exec_sessions. Si basa su questo comune join tra sys.dm_exec_requests e sys.dm_exec_sessions nella query successiva.

* Eseguire questa query di esempio per trovare le query in esecuzione attivamente e il testo del buffer di input o il testo del batch SQL corrente usando il [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) o [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV. Se i dati restituiti dal `text` campo di sys.dm_exec_sql_text sono null, la query non è attualmente in esecuzione. In tal caso, il `event_info` campo di sys.dm_exec_input_buffer conterrà l'ultima stringa di comando passata al motore di SQL. Questa query può essere usata anche per identificare le sessioni che bloccano altre sessioni, incluso un elenco di session_ids bloccate per ogni session_id. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Eseguire questa query di esempio più elaborata, fornita da supporto tecnico Microsoft, per identificare la parte iniziale di una catena di blocco di più sessioni, incluso il testo della query delle sessioni implicate in una catena di blocco.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Per intercettare le transazioni con esecuzione prolungata o non di cui è stato eseguito il commit, utilizzare un altro set di DMV per la visualizzazione delle transazioni aperte correnti, tra cui [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)e sys.dm_exec_sql_text. Esistono diverse DMV associate alle transazioni di rilevamento. vedere più [DMV sulle transazioni](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) qui. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Riferimento [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) che si trova a livello di thread/attività di SQL. Restituisce informazioni sul tipo di attesa SQL che la richiesta sta attualmente riscontrando. Come sys.dm_exec_requests, solo le richieste attive vengono restituite da sys.dm_os_waiting_tasks. 

> [!Note]
> Per molte altre informazioni sui tipi di attesa, incluse le statistiche di attesa aggregate nel tempo, vedere la DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Questa DMV restituisce le statistiche di attesa aggregate solo per il database corrente.

* Utilizzare la DMV [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) per ottenere informazioni più dettagliate sui blocchi posizionati dalle query. Questa DMV può restituire grandi quantità di dati in un SQL Server di produzione ed è utile per la diagnosi dei blocchi attualmente conservati. 

A causa del INNER JOIN in sys.dm_os_waiting_tasks, la query seguente consente di limitare l'output di sys.dm_tran_locks solo alle richieste attualmente bloccate, al relativo stato di attesa e ai relativi blocchi:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Con DMV, l'archiviazione dei risultati della query nel tempo fornirà punti dati che consentono di esaminare il blocco in un intervallo di tempo specificato per identificare le tendenze o il blocco permanente. 

## <a name="gather-information-from-extended-events"></a>Raccogliere informazioni da eventi estesi

Oltre alle informazioni precedenti, è spesso necessario acquisire una traccia delle attività nel server per analizzare accuratamente un problema di blocco nel database SQL di Azure. Se, ad esempio, una sessione esegue più istruzioni all'interno di una transazione, verrà rappresentata solo l'ultima istruzione inviata. Tuttavia, è possibile che una delle istruzioni precedenti sia il motivo per cui i blocchi sono ancora in attesa. Una traccia consente di visualizzare tutti i comandi eseguiti da una sessione all'interno della transazione corrente.

Esistono due modi per acquisire le tracce in SQL Server; Eventi estesi (XEvent) e tracce del profiler. Tuttavia, [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) è deprecata tecnologia di traccia non supportata per il database SQL di Azure. [Gli eventi estesi](/sql/relational-databases/extended-events/extended-events) sono la tecnologia di analisi più recente che consente una maggiore versatilità e un minore effetto sul sistema osservato e la relativa interfaccia è integrata in SQL Server Management Studio (SSMS). 

Vedere il documento che illustra come usare la [procedura guidata nuova sessione eventi estesi](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) in SSMS. Per i database SQL di Azure, tuttavia, SSMS fornisce una sottocartella degli eventi estesi in ogni database in Esplora oggetti. Utilizzare una procedura guidata per la sessione eventi estesi per acquisire questi eventi utili: 

-   Errori di categoria:
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   Avvisi Categoria: 
    -   Missing_join_predicate

-   Esecuzione Categoria:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Lock
    -   Lock_deadlock

-   Sessione
    -   Existing_connection
    -   Accedi
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identificare e risolvere scenari di blocco comuni

Esaminando le informazioni precedenti, è possibile determinare la causa della maggior parte dei problemi di blocco. Il resto di questo articolo descrive come usare queste informazioni per identificare e risolvere alcuni scenari di blocco comuni. In questa discussione si presuppone che siano stati utilizzati gli script di blocco (a cui si fa riferimento in precedenza) per acquisire informazioni sugli SPID di blocco e che l'attività dell'applicazione sia stata acquisita utilizzando una sessione XEvent.

## <a name="analyze-blocking-data"></a>Analizzare i dati di blocco 

* Esaminare l'output del sys.dm_exec_requests DMV e sys.dm_exec_sessions per determinare le intestazioni delle catene di blocco, usando blocking_these e session_id. Questo consentirà di identificare più chiaramente quali richieste sono bloccate e bloccate. Esaminare le sessioni bloccate e bloccate. Esiste una comune o una radice alla catena di blocco? Probabilmente condividono una tabella comune e una o più sessioni interessano una catena di blocco sta eseguendo un'operazione di scrittura. 

* Esaminare l'output del sys.dm_exec_requests DMV e sys.dm_exec_sessions per informazioni sugli SPID all'inizio della catena di blocco. Cercare i campi seguenti: 

    -    `sys.dm_exec_requests.status`  
    In questa colonna viene visualizzato lo stato di una determinata richiesta. In genere, uno stato di sospensione indica che lo SPID ha completato l'esecuzione ed è in attesa dell'invio di un'altra query o batch da parte dell'applicazione. Uno stato eseguibile o in esecuzione indica che lo SPID sta attualmente elaborando una query. Nella tabella seguente vengono fornite brevi spiegazioni dei diversi valori di stato.

    | Stato | Significato |
    |:-|:-|
    | Sfondo | Lo SPID sta eseguendo un'attività in background, ad esempio il rilevamento dei deadlock, il writer di log o il checkpoint. |
    | Sospeso | Il SPID non è attualmente in esecuzione. Questo indica in genere che lo SPID è in attesa di un comando dell'applicazione. |
    | In esecuzione | Lo SPID è attualmente in esecuzione in un'utilità di pianificazione. |
    | Eseguibile | Lo SPID si trova nella coda eseguibile di un'utilità di pianificazione e in attesa di ottenere l'ora dell'utilità di pianificazione. |
    | Suspended | Lo SPID è in attesa di una risorsa, ad esempio un blocco o un latch. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Questo campo indica il numero di transazioni aperte in questa sessione. Se questo valore è maggiore di 0, lo SPID si trova all'interno di una transazione aperta e potrebbe contenere blocchi acquisiti da qualsiasi istruzione all'interno della transazione.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Analogamente, questo campo indica il numero di transazioni aperte in questa richiesta. Se questo valore è maggiore di 0, lo SPID si trova all'interno di una transazione aperta e potrebbe contenere blocchi acquisiti da qualsiasi istruzione all'interno della transazione.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` e `last_wait_type`  
    Se  `sys.dm_exec_requests.wait_type`   è null, la richiesta non è attualmente in attesa di alcun elemento e il  `last_wait_type`   valore indica l'ultimo  `wait_type`   rilevato dalla richiesta. Per ulteriori informazioni su  `sys.dm_os_wait_stats` e una descrizione dei tipi di attesa più comuni, vedere [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). Il  `wait_time`   valore può essere utilizzato per determinare se la richiesta sta procedendo. Quando una query sulla tabella sys.dm_exec_requests restituisce un valore nella  `wait_time`   colonna minore del  `wait_time`   valore di una query precedente di sys.dm_exec_requests, indica che il blocco precedente è stato acquisito e rilasciato ed è ora in attesa di un nuovo blocco (presupponendo un valore diverso da zero `wait_time` ). Questo può essere verificato confrontando `wait_resource`   tra sys.dm_exec_requests output, che visualizza la risorsa per la quale la richiesta è in attesa.

    -   `sys.dm_exec_requests.wait_resource` Questo campo indica la risorsa in attesa di una richiesta bloccata. La tabella seguente elenca i  `wait_resource`   formati comuni e il relativo significato:

    | Risorsa | Formato | Esempio | Spiegazione | 
    |:-|:-|:-|:-|
    | Tabella | DatabaseID: ObjectID: IndexID | SCHEDA: 5:261575970:1 | In questo caso, il database con ID 5 è il database di esempio pubs e l'ID oggetto 261575970 è la tabella titles e 1 è l'indice cluster. |
    | Pagina | DatabaseID: FileID: PageID | PAGINA: 5:1:104 | In questo caso, il database con ID 5 è pubs, il file con ID 1 è il file di dati primario e la pagina 104 è una pagina appartenente alla tabella titles. Per identificare la object_id a cui appartiene la pagina, usare la funzione a gestione dinamica [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), passando DatabaseID, fileid, pageid da `wait_resource` . | 
    | Chiave | DatabaseID: Hobt_id (valore hash per la chiave di indice) | CHIAVE: 5:72057594044284928 (3300a4f361aa) | In questo caso, il database con ID 5 è pubs, Hobt_ID 72057594044284928 corrisponde a index_id 2 per object_id 261575970 (tabella titles). Utilizzare la vista del catalogo sys. partitions per associare il hobt_id a una index_id e object_id specifici. Non è possibile decodificare l'hash della chiave di indice a un valore di chiave specifico. |
    | Riga | DatabaseID: FileID: PageID: slot (riga) | RID: 5:1:104:3 | In questo caso, il database con ID 5 è pubs, il file con ID 1 è il file di dati primario, la pagina 104 è una pagina che appartiene alla tabella titles e slot 3 indica la posizione della riga nella pagina. |
    | Compilazione  | DatabaseID: FileID: PageID: slot (riga) | RID: 5:1:104:3 | In questo caso, il database con ID 5 è pubs, il file con ID 1 è il file di dati primario, la pagina 104 è una pagina che appartiene alla tabella titles e slot 3 indica la posizione della riga nella pagina. |

    -    `sys.dm_tran_active_transactions` La DMV [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) contiene i dati sulle transazioni aperte che possono essere aggiunti ad altri DMV per un quadro completo delle transazioni in attesa del commit o del rollback. Usare la query seguente per restituire informazioni sulle transazioni aperte, unite ad altre DMV, tra cui [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Considerare lo stato corrente di una transazione, `transaction_begin_time` , e altri dati di situazione per valutare se potrebbe essere un'origine del blocco.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Altre colonne

        Le colonne rimanenti in [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) e [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) possono fornire informazioni approfondite anche sulla radice di un problema. L'utilità varia a seconda delle circostanze del problema. Ad esempio, è possibile determinare se il problema si verifica solo da determinati client (hostname), su determinate librerie di rete (net_library), quando l'ultimo batch inviato da uno SPID era `last_request_start_time` in sys.dm_exec_sessions, per quanto tempo una richiesta era in esecuzione utilizzando `start_time` in sys.dm_exec_requests e così via.


## <a name="common-blocking-scenarios"></a>Scenari di blocco comuni

La tabella seguente associa i sintomi comuni alle possibili cause.  

Le `wait_type` `open_transaction_count` colonne, e si `status` riferiscono alle informazioni restituite da [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), altre colonne possono essere restituite da [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). Il "risolve?" column indica se il blocco verrà risolto autonomamente o se la sessione deve essere terminata tramite il `KILL` comando. Per ulteriori informazioni, vedere [Kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Scenario | Waittype | Open_Tran | Stato | Risolve? | Altri sintomi |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | eseguibile | Sì, al termine della query. | In sys.dm_exec_sessions le colonne **Reads**, **cpu_time** e/o **memory_usage** aumenteranno nel tempo. La durata della query sarà elevata al termine dell'operazione. |
| 2 | NULL | \>0 | in sospensione | No, ma è possibile terminare lo SPID. | Un segnale di attenzione può essere visualizzato nella sessione eventi estesi per questo SPID, a indicare che si è verificato un timeout della query o l'annullamento. |
| 3 | NULL | \>= 0 | eseguibile | No. Non verrà risolto finché il client non recupererà tutte le righe o chiuderà la connessione. È possibile terminare lo SPID, ma potrebbe essere necessario attendere fino a 30 secondi. | Se open_transaction_count = 0 e lo SPID conserva i blocchi mentre il livello di isolamento della transazione è predefinito (READ COMMMITTED), si tratta di una causa probabile. |  
| 4 | Varia | \>= 0 | eseguibile | No. Non verrà risolto finché il client non annulla le query o non chiude le connessioni. Gli SPID possono essere terminati, ma possono essere importati fino a 30 secondi. | La colonna **hostname** in sys.dm_exec_sessions per lo SPID all'inizio di una catena di blocco sarà uguale a uno degli SPID che sta bloccando. |  
| 5 | NULL | \>0 | rollback | Sì. | È possibile che venga visualizzato un segnale di attenzione nella sessione degli eventi estesi per questo SPID, a indicare che si è verificato un timeout della query o un'operazione di annullamento oppure è stata eseguita semplicemente un'istruzione rollback. |  
| 6 | NULL | \>0 | in sospensione | Infine. Quando Windows NT determina che la sessione non è più attiva, la connessione al database SQL di Azure verrà interruppe. | Il `last_request_start_time` valore nel sys.dm_exec_sessions è molto precedente all'ora corrente. |

## <a name="detailed-blocking-scenarios"></a>Scenari di blocco dettagliati

1.  Blocco causato da una query in esecuzione normale con tempi di esecuzione lunghi

    **Soluzione: la** soluzione a questo tipo di problema di blocco consiste nell'individuare i modi per ottimizzare la query. In realtà, questa classe di problemi di blocco potrebbe essere solo un problema di prestazioni e richiedere l'inseguimento. Per informazioni sulla risoluzione dei problemi relativi a una query con esecuzione prolungata specifica, vedere [come risolvere i problemi relativi a query con esecuzione prolungata su SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Per altre informazioni, vedere [Monitorare e ottimizzare le prestazioni](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    I report provenienti dal [query Store](/sql/relational-databases/performance/best-practice-with-the-query-store) in SSMS rappresentano inoltre uno strumento estremamente consigliato e utile per l'identificazione delle query più costose, dei piani di esecuzione non ottimali. Vedere anche la sezione relativa alle [prestazioni intelligenti](intelligent-insights-overview.md) del portale di Azure per il database SQL di Azure, incluso [informazioni dettagliate prestazioni query](query-performance-insight-use.md).

    Se si dispone di una query con esecuzione prolungata che blocca altri utenti e non può essere ottimizzata, provare a trasferirla da un ambiente OLTP a un sistema di Reporting dedicato, una [replica di sola lettura sincrona del database](read-scale-out.md).

1.  Blocco causato da uno SPID in sospensione con una transazione di cui non è stato eseguito il commit

    Questo tipo di blocco può essere spesso identificato da uno SPID che è in sospensione o in attesa di un comando, ma il cui livello di annidamento delle transazioni ( `@@TRANCOUNT` , `open_transaction_count` da sys.dm_exec_requests) è maggiore di zero. Questo problema può verificarsi se si verifica un timeout per la query o se si esegue un annullamento senza emettere anche il numero necessario di istruzioni ROLLBACK e/o COMMIT. Quando uno SPID riceve un timeout per la query o un annullamento, termina la query e il batch correnti, ma non esegue automaticamente il rollback o il commit della transazione. L'applicazione è responsabile di questo, poiché il database SQL di Azure non è in grado di presupporre che sia necessario eseguire il rollback di un'intera transazione a causa dell'annullamento di una singola query. Il timeout o l'annullamento della query verrà visualizzato come un evento del segnale di attenzione per lo SPID nella sessione dell'evento esteso.

    Per dimostrare una transazione esplicita di cui non è stato eseguito il commit, eseguire la query seguente:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Eseguire quindi la query nella stessa finestra:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    L'output della seconda query indica che il livello di nidificazione della transazione è uno. Tutti i blocchi acquisiti nella transazione rimangono mantenuti fino a quando non è stato eseguito il commit o il rollback della transazione. Se le applicazioni aprono ed eseguino il commit delle transazioni in modo esplicito, una comunicazione o un altro errore potrebbe lasciare la sessione e la relativa transazione in uno stato aperto. 

    Usare lo script riportato in precedenza in questo articolo in base sys.dm_tran_active_transactions per identificare le transazioni attualmente non salvate nell'istanza.

    **Soluzioni**:

     -   Inoltre, questa classe di problemi di blocco potrebbe essere un problema di prestazioni e richiedere l'esecuzione di tali problemi. Se il tempo di esecuzione della query può essere ridotto, il timeout o l'annullamento della query non verrà eseguito. È importante che l'applicazione sia in grado di gestire gli scenari di timeout o annullamento qualora si verifichino, ma è possibile anche trarre vantaggio dall'analisi delle prestazioni della query. 
     
     -    Le applicazioni devono gestire correttamente i livelli di annidamento delle transazioni oppure possono causare un problema di blocco dopo l'annullamento della query in questo modo. Considerare quanto segue:  

            *    Nel gestore degli errori dell'applicazione client, eseguire `IF @@TRANCOUNT > 0 ROLLBACK TRAN` dopo qualsiasi errore, anche se l'applicazione client non ritiene che una transazione sia aperta. Il controllo delle transazioni aperte è obbligatorio perché un stored procedure chiamato durante il batch potrebbe avere avviato una transazione senza la conoscenza dell'applicazione client. Determinate condizioni, ad esempio l'annullamento della query, impediscono l'esecuzione di una procedura oltre l'istruzione corrente, quindi anche se la procedura dispone della logica per verificare `IF @@ERROR <> 0` e interrompere la transazione, questo codice di rollback non verrà eseguito in tali casi.  
            *    Se il pool di connessioni viene utilizzato in un'applicazione che apre la connessione ed esegue un numero ridotto di query prima di rilasciare nuovamente la connessione al pool, ad esempio un'applicazione basata sul Web, la disabilitazione temporanea del pool di connessioni può contribuire a risolvere il problema fino a quando l'applicazione client non viene modificata per gestire gli errori in modo appropriato. Disabilitando il pool di connessioni, il rilascio della connessione provocherà una disconnessione fisica della connessione del database SQL di Azure, con conseguente rollback delle transazioni aperte da parte del server.  
            *    Utilizzare `SET XACT_ABORT ON` per la connessione o in tutte le stored procedure che avviano le transazioni e che non vengono ripulite in seguito a un errore. In caso di errore di run-time, questa impostazione interrompe tutte le transazioni aperte e restituisce il controllo al client. Per ulteriori informazioni, vedere [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).

    > [!NOTE]
    > La connessione non viene reimpostata finché non viene riutilizzata dal pool di connessioni, pertanto è possibile che un utente possa aprire una transazione e quindi rilasciare la connessione al pool di connessioni, ma potrebbe non essere riutilizzata per alcuni secondi, durante i quali la transazione rimarrà aperta. Se la connessione non viene riutilizzata, la transazione verrà interrotta al timeout della connessione e verrà rimossa dal pool di connessioni. Pertanto, è ottimale affinché l'applicazione client interrompa le transazioni nel proprio gestore errori o utilizzi `SET XACT_ABORT ON` per evitare questo potenziale ritardo.

    > [!CAUTION]
    > Le `SET XACT_ABORT ON` istruzioni T-SQL che seguono un'istruzione che provoca un errore non verranno eseguite. Questo potrebbe influire sul flusso previsto del codice esistente.

1.  Blocco causato da uno SPID la cui applicazione client corrispondente non ha recuperato tutte le righe di risultati fino al completamento

    Dopo l'invio di una query al server, tutte le applicazioni devono recuperare immediatamente tutte le righe dei risultati fino al completamento. Se un'applicazione non recupera tutte le righe di risultati, i blocchi possono essere lasciati nelle tabelle, bloccando altri utenti. Se si utilizza un'applicazione che invia in modo trasparente istruzioni SQL al server, l'applicazione deve recuperare tutte le righe di risultati. In caso contrario (e se non è possibile configurarlo), potrebbe non essere possibile risolvere il problema di blocco. Per evitare il problema, è possibile limitare le applicazioni che si sono comportate in modo non corretto a una creazione di report o a un database di supporto decisionale, separate dal database OLTP principale.
    
    > [!NOTE]
    > Vedere [linee guida per la logica di ripetizione dei tentativi](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) per le applicazioni che si connettono al database SQL 
    
    **Soluzione**: l'applicazione deve essere riscritta per recuperare tutte le righe del risultato fino al completamento. Questa operazione non esclude l'utilizzo di [offset e FETCH nella clausola ORDER BY](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) di una query per eseguire il paging lato server.

1.  Blocco causato da una sessione in stato di rollback

    Verrà eseguito il rollback di una query di modifica dei dati terminata o annullata all'esterno di una transazione definita dall'utente. Questo può verificarsi anche come effetto collaterale della disconnessione della sessione di rete client o quando viene selezionata una richiesta come vittima del deadlock. Questo può essere spesso identificato osservando l'output di sys.dm_exec_requests, che può indicare il **comando** rollback e la **colonna percent_complete** può mostrare lo stato di avanzamento. 

    Grazie alla [funzionalità di ripristino del database accelerato](../accelerated-database-recovery.md) introdotta in 2019, i rollback lunghi dovrebbero essere rari.
    
    **Soluzione**: attendere che lo SPID completi il rollback delle modifiche apportate. 

    Per evitare questa situazione, non eseguire operazioni di scrittura batch di grandi dimensioni o operazioni di creazione o manutenzione di indici durante l'orario occupato nei sistemi OLTP. Se possibile, eseguire operazioni di questo tipo durante i periodi di minore attività.

1.  Blocco causato da una connessione orfana

    Se l'applicazione client intercetta gli errori o la workstation client viene riavviata, è possibile che la sessione di rete sul server non venga annullata immediatamente in alcune condizioni. Dal punto di vista del database SQL di Azure, il client sembra ancora essere presente e tutti i blocchi acquisiti possono comunque essere conservati. Per ulteriori informazioni, vedere [come risolvere i problemi relativi alle connessioni orfane in SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Soluzione**: se l'applicazione client si è disconnessa senza pulire adeguatamente le risorse, è possibile terminare lo SPID usando il `KILL` comando. Il `KILL` comando accetta come input il valore SPID. Ad esempio, per terminare con SPID 99, eseguire il comando seguente:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Vedi anche

* [Monitoraggio e ottimizzazione delle prestazioni del database SQL di Azure e di Istanza gestita di SQL di Azure](./monitor-tune-overview.md)
* [Monitoraggio delle prestazioni con Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Guida per il controllo delle versioni delle righe e il blocco della transazione](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Guida introduttiva: Eventi estesi in SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights con intelligenza artificiale per eseguire il monitoraggio e risolvere i problemi delle prestazioni del database](intelligent-insights-overview.md)

## <a name="learn-more"></a>Altre informazioni

* [Database SQL di Azure: miglioramento dell'ottimizzazione delle prestazioni con ottimizzazione automatica](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Migliorare le prestazioni del database SQL di Azure con l'ottimizzazione automatica](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Offrire prestazioni coerenti con Azure SQL](/learn/modules/azure-sql-performance/)
* [Risoluzione dei problemi di connettività e di altri errori con il database SQL di Azure e Azure SQL Istanza gestita](troubleshoot-common-errors-issues.md)
* [Gestione degli errori temporanei](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
* [Configurare il grado massimo di parallelismo (MAXDOP) nel database SQL di Azure](configure-max-degree-of-parallelism.md)
