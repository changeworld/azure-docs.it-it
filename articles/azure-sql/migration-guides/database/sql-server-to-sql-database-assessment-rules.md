---
title: Regole di valutazione per SQL Server alla migrazione del database SQL di Azure
description: Regole di valutazione per identificare i problemi relativi all'istanza di SQL Server di origine che devono essere risolti prima di eseguire la migrazione al database SQL di Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: c407b62b5f0308fbb6d9ff7223a3554ca09592db
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027211"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Regole di valutazione per SQL Server alla migrazione del database SQL di Azure
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Gli strumenti di migrazione convalidano l'istanza di SQL Server di origine eseguendo una serie di regole di valutazione per identificare i problemi che devono essere risolti prima di eseguire la migrazione del database di SQL Server al database SQL di Azure. 

Questo articolo fornisce un elenco delle regole usate per valutare la fattibilità della migrazione del database di SQL Server al database SQL di Azure. 


## <a name="bulk-insert"></a>Inserimento bulk<a id="BulkInsert"></a>

**Title: BULK INSERT con l'origine dati BLOB non di Azure non è supportata nel database SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
Il database SQL di Azure non può accedere alle condivisioni file o alle cartelle di Windows. Vedere la sezione "oggetti interessati" per gli usi specifici delle istruzioni BULK INSERT che non fanno riferimento a un BLOB di Azure. Gli oggetti con ' BULK INSERT ' in cui l'origine non è un archivio BLOB di Azure non funzioneranno dopo la migrazione al database SQL di Azure. 


**Raccomandazione**   
Quando si esegue la migrazione al database SQL di Azure, sarà necessario convertire BULK INSERT le istruzioni che usano file locali o condivisioni file per usare i file dell'archiviazione BLOB di Azure. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

## <a name="compute-clause"></a>Clausola COMPUTE<a id="ComputeClause"></a>

**Title: la clausola COMPUTE non è più disponibile ed è stata rimossa.**   
**Categoria**: avviso   

**Descrizione**   
La clausola COMPUTE genera i totali che vengono visualizzati come colonne di riepilogo aggiuntive alla fine del set di risultati. Tuttavia, questa clausola non è più supportata nel database SQL di Azure. 


**Raccomandazione**   
Il modulo T-SQL deve essere riscritto usando l'operatore ROLLUP. Il codice seguente illustra in che modo è possibile sostituire il calcolo con ROLLUP: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="change-data-capture-cdc"></a>Change Data Capture (CDC)<a id="CDC"></a>

**Titolo: Change Data Capture (CDC) non è supportato nel database SQL di Azure**   
**Categoria**: problema   


**Descrizione**   
Change Data Capture (CDC) non è supportato nel database SQL di Azure. Valutare se è possibile usare invece Rilevamento modifiche.  In alternativa, eseguire la migrazione a Istanza gestita SQL di Azure o SQL Server in macchine virtuali di Azure. 


**Raccomandazione**   
Change Data Capture (CDC) non è supportato nel database SQL di Azure. Valutare se è possibile usare invece Rilevamento modifiche o provare a eseguire la migrazione ad Azure SQL Istanza gestita.

Altre informazioni: [Abilita rilevamento modifiche SQL di Azure](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>Assembly CLR<a id="ClrAssemblies"></a>

**Title: gli assembly CLR SQL non sono supportati nel database SQL di Azure**   
**Categoria**: problema   


**Descrizione**   
Il database SQL di Azure non supporta gli assembly CLR SQL. 


**Raccomandazione**   
Attualmente, non è possibile ottenere questo risultato nel database SQL di Azure. Le soluzioni alternative consigliate richiedono il codice dell'applicazione e le modifiche al database per usare solo gli assembly supportati dal database SQL di Azure. In alternativa, Esegui la migrazione a Istanza gestita SQL di Azure o SQL Server nella macchina virtuale di Azure

Altre informazioni: [differenze di Transact-SQL non supportate nel database SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Provider di crittografia<a id="CryptographicProvider"></a>

**Title: è stato trovato l'uso di CREATE CRYPTOGRAPHIC PROVIDER o ALTER CRYPTOGRAPHIC PROVIDER, che non è supportato nel database SQL di Azure**   
**Categoria**: problema   

**Descrizione**   
Il database SQL di Azure non supporta le istruzioni del PROVIDER del servizio di crittografia perché non può accedere ai file. Vedere la sezione oggetti interessati per gli utilizzi specifici delle istruzioni del PROVIDER di crittografia. Gli oggetti con `CREATE CRYPTOGRAPHIC PROVIDER` o `ALTER CRYPTOGRAPHIC PROVIDER` non funzioneranno correttamente dopo la migrazione al database SQL di Azure.  


**Raccomandazione**   
Esaminare gli oggetti con `CREATE CRYPTOGRAPHIC PROVIDER` o `ALTER CRYPTOGRAPHIC PROVIDER` . In tutti gli oggetti di questo tipo sono necessari rimuovere gli utilizzi di queste funzionalità. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

## <a name="cross-database-references"></a>Riferimenti tra database<a id="CrossDataseReferences"></a>

**Title: le query tra database non sono supportate nel database SQL di Azure**   
**Categoria**: problema   

**Descrizione**   
I database in questo server usano query tra database, che non sono supportate nel database SQL di Azure. 


**Raccomandazione**   
Il database SQL di Azure non supporta le query tra database. Sono consigliate le azioni seguenti: 
- Esegui la migrazione dei database dipendenti nel database SQL di Azure e usa la funzionalità query di database elastico (attualmente in anteprima) per eseguire query nei database SQL di Azure. 
- Spostare i set di dati dipendenti da altri database nel database di cui viene eseguita la migrazione. 
- Eseguire la migrazione ad Azure SQL Istanza gestita.
- Eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

Altre informazioni: [controllo query database elastico del database SQL di Azure (anteprima)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Compatibilità del database<a id="DbCompatLevelLowerThan100"></a>

**Title: il database SQL di Azure non supporta i livelli di compatibilità inferiori a 100.**   
**Categoria**: avviso   

**Descrizione**   
Il livello di compatibilità del database è uno strumento prezioso per facilitare la modernizzazione del database, consentendo l'aggiornamento del SQL Server motore di database, mantenendo al tempo stesso la connessione dello stato funzionale delle applicazioni mantenendo lo stesso livello di compatibilità del database pre-aggiornamento. Il database SQL di Azure non supporta i livelli di compatibilità inferiori a 100. 


**Raccomandazione**   
Valutare se la funzionalità dell'applicazione è intatta quando il livello di compatibilità del database viene aggiornato a 100 in Azure SQL Istanza gestita. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

## <a name="database-mail"></a>Posta elettronica database<a id="DatabaseMail"></a>

**Title: Posta elettronica database non è supportato nel database SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Questo server usa la funzionalità Posta elettronica database, che non è supportata nel database SQL di Azure.


**Raccomandazione**   
Si consiglia di eseguire la migrazione a Istanza gestita SQL di Azure che supporta Posta elettronica database.  In alternativa, è consigliabile usare funzioni di Azure e SendGrid per ottenere la funzionalità di posta elettronica nel database SQL di Azure.

Altre informazioni: [inviare messaggi di posta elettronica dal database SQL di Azure usando lo script di funzioni di Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Alias entità di database<a id="DatabasePrincipalAlias"></a>

**Titolo: SYS. DATABASE_PRINCIPAL_ALIASES è sospeso ed è stato rimosso.**   
**Categoria**: problema   

**Descrizione**   
SYS. DATABASE_PRINCIPAL_ALIASES non è più disponibile ed è stata rimossa nel database SQL di Azure.  


**Raccomandazione**   
Usare ruoli anziché alias.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server


## <a name="disable_def_cnst_chk-option"></a>Opzione DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Title: l'opzione SET DISABLE_DEF_CNST_CHK è sospesa ed è stata rimossa.**   
**Categoria**: problema   

**Descrizione**   
L'opzione SET DISABLE_DEF_CNST_CHK è stata sospesa ed è stata rimossa nel database SQL di Azure.  


Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="fastfirstrow-hint"></a>Hint FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Title: l'hint per la query FASTFIRSTROW non è più disponibile ed è stato rimosso.**   
**Categoria**: avviso   

**Descrizione**   
L'hint per la query FASTFIRSTROW non è più disponibile ed è stato rimosso nel database SQL di Azure.  


**Raccomandazione**   
Al posto dell'opzione di utilizzo hint per la query FASTFIRSTROW (FAST n).

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Title: FILESTREAM non è supportato nel database SQL di Azure**   
**Categoria**: problema   

**Descrizione**   
La funzionalità FILESTREAM, che consente di archiviare dati non strutturati, ad esempio documenti di testo, immagini e video in file system NTFS, non è supportata nel database SQL di Azure. 


**Raccomandazione**   
Caricare i file non strutturati nell'archiviazione BLOB di Azure e archiviare i metadati correlati a questi file (nome, tipo, percorso URL, chiave di archiviazione e così via) nel database SQL di Azure. Potrebbe essere necessario riprogettare l'applicazione per abilitare i BLOB di streaming da e verso il database SQL di Azure. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [streaming di BLOB da e verso Azure SQL Blog](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Server collegato<a id="LinkedServer"></a>

**Title: la funzionalità del server collegato non è supportata nel database SQL di Azure**   
**Categoria**: problema   

**Descrizione**   
I server collegati consentono al motore di database SQL Server di eseguire comandi su OLE DB origini dati esterne all'istanza di SQL Server. 


**Raccomandazione**   
Il database SQL di Azure non supporta la funzionalità del server collegato. Per eliminare la necessità di server collegati, è consigliabile eseguire le azioni seguenti: 
- Identificare i set di dati dipendenti da server SQL remoti e provare a spostarli nel database di cui viene eseguita la migrazione. 
- Esegui la migrazione dei database dipendenti in Azure e usa la funzionalità di query del database elastico (anteprima) per eseguire query tra database nel database SQL di Azure. 

Altre informazioni: [controllare la query elastica del database SQL di Azure (anteprima)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Title: l'avvio della transazione DISTRIBUIta non è supportato nel database SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
La transazione distribuita avviata da Transact SQL BEGIN DISTRIBUted TRANSACTION e gestita da Microsoft Distributed Transaction Coordinator (MS DTC) non è supportata nel database SQL di Azure.  


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per vedere tutti gli oggetti che usano la transazione BEGIN DISTRUBUTED. Provare a migrare i database dei partecipanti in Azure SQL Istanza gestita in cui sono supportate le transazioni distribuite tra più istanze (attualmente in anteprima). In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [transazioni tra più server per Azure SQL istanza gestita ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (in blocco)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: OpenRowSet usata nell'operazione bulk con l'origine dati di archiviazione BLOB non di Azure non è supportata nel database SQL di Azure.**   
**Categoria**: problema   

**Descrizione** di OPENROWSET supporta le operazioni bulk tramite un provider BULK incorporato che consente la lettura e la restituzione di dati da un file come set di righe. L'origine dati OPENROWSET con archiviazione BLOB non di Azure non è supportata nel database SQL di Azure.


**Raccomandazione**   
Il database SQL di Azure non può accedere alle condivisioni file e alle cartelle di Windows, quindi i file devono essere importati dall'archivio BLOB di Azure. Pertanto, nella funzione OPENROWSET è supportata solo l'origine dati di tipo BLOB. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

Ulteriori informazioni: [risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (provider)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Title: OpenRowSet con SQL o il provider non SQL non è supportato nel database SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
OpenRowSet con SQL o provider non SQL è un'alternativa all'accesso alle tabelle in un server collegato ed è un metodo ad hoc monouso per la connessione e l'accesso ai dati remoti tramite OLE DB. OpenRowSet con SQL o il provider non SQL non è supportato nel database SQL di Azure.


**Raccomandazione**   
Il database SQL di Azure supporta OPENROWSET solo per l'importazione dall'archiviazione BLOB di Azure. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

Ulteriori informazioni: [risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>left outer join non ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: left outer join di stile non ANSI non è più disponibile ed è stata rimossa.**   
**Categoria**: avviso   

**Descrizione**   
Left outer join di stile non ANSI non è più disponibile ed è stato rimosso nel database SQL di Azure. 


**Raccomandazione**   
Utilizzare la sintassi di join ANSI.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server


## <a name="non-ansi-right-outer-join"></a>right outer join non ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: right outer join di stile non ANSI non è più disponibile ed è stata rimossa.**   
**Categoria**: avviso   

**Descrizione**   
Right outer join di stile non ANSI non è più disponibile ed è stato rimosso nel database SQL di Azure. 


**Raccomandazione**   
Utilizzare la sintassi di join ANSI.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="next-column"></a>Colonna successiva<a id="NextColumn"></a>

**Title: le tabelle e le colonne denominate NEXT determineranno un errore nel database SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
Sono state rilevate tabelle o colonne denominate NEXT. Le sequenze, introdotte in Microsoft SQL Server, usano la funzione NEXT VALUE FOR ANSI standard. Se una tabella o colonna viene denominata NEXT, le viene associato un alias VALUE e la parola chiave AS dello standard ANSI viene omessa, è possibile che nell'istruzione risultante si verifichi un errore.  


**Raccomandazione**   
Riscrivere le istruzioni in modo da includere la parola chiave AS dello standard ANSI quando si crea l'alias di una tabella o una colonna. Ad esempio, quando una colonna viene denominata NEXT e tale colonna viene aliasata come valore, la query `SELECT NEXT VALUE FROM TABLE` genererà un errore e dovrà essere riscritta come Select Next As value from table. Analogamente, quando una tabella viene denominata NEXT e a tale tabella viene associato un alias VALUE, la query `SELECT Col1 FROM NEXT VALUE` causerà un errore e dovrà essere riscritta come `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: le chiamate RAISERROR di tipo legacy devono essere sostituite con gli equivalenti moderni.**   
**Categoria**: avviso   

**Descrizione**   
Le chiamate RAISERROR come l'esempio seguente vengono denominate come legacy, perché non includono le virgole e la parentesi. `RAISERROR 50001 'this is a test'`. Questo metodo di chiamata a RAISERROR non è più disponibile e rimosso nel database SQL di Azure. 


**Raccomandazione**   
Riscrivere l'istruzione usando la sintassi RAISERROR corrente oppure valutare se l'approccio moderno di `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` è fattibile.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="server-audits"></a>Controlli server<a id="ServerAudits"></a>

**Title: usare le funzionalità di controllo del database SQL di Azure per sostituire i controlli server**   
**Categoria**: avviso   

**Descrizione**   
I controlli server non sono supportati nel database SQL di Azure.


**Raccomandazione**   
Prendere in considerazione le funzionalità di controllo del database SQL di Azure per sostituire i controlli del server.  Azure SQL supporta il controllo e le funzionalità sono più ricche di SQL Server. Il database SQL di Azure è in grado di controllare varie azioni ed eventi del database, tra cui: accesso ai dati, modifiche dello schema (DDL), modifiche dei dati (DML), account, ruoli e autorizzazioni (DCL, eccezioni di sicurezza. Il controllo del database SQL di Azure aumenta la capacità di un'organizzazione di ottenere informazioni approfondite sugli eventi e sulle modifiche che si verificano all'interno del database, inclusi gli aggiornamenti e le query sui dati. In alternativa, eseguire la migrazione a Istanza gestita SQL di Azure o SQL Server nella macchina virtuale di Azure.

Altre informazioni: [controllo per il database SQL di Azure ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Credenziali del server<a id="ServerCredentials"></a>

**Title: le credenziali con ambito server non sono supportate nel database SQL di Azure**   
**Categoria**: avviso   

**Descrizione**   
Una credenziale è un record contenente le informazioni di autenticazione (credenziali) necessarie per connettersi a una risorsa all'esterno SQL Server. Il database SQL di Azure supporta le credenziali del database, ma non quelle create nell'ambito SQL Server.   


**Raccomandazione**   
Il database SQL di Azure supporta le credenziali con ambito database. Converte le credenziali con ambito server in credenziali con ambito database. In alternativa, Esegui la migrazione a Istanza gestita SQL di Azure o SQL Server nella macchina virtuale di Azure

Altre informazioni: [creazione di credenziali con ambito database](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Broker di servizio<a id="ServiceBroker"></a>

**Title: la funzionalità Service Broker non è supportata nel database SQL di Azure**   
**Categoria**: problema   

**Descrizione**   
SQL Server Service Broker fornisce il supporto nativo per le applicazioni di messaggistica e accodamento nel motore di database SQL Server. Service Broker funzionalità non è supportata nel database SQL di Azure.


**Raccomandazione**   
Service Broker funzionalità non è supportata nel database SQL di Azure. Si consiglia di eseguire la migrazione a Istanza gestita SQL di Azure che supporta Service Broker all'interno della stessa istanza. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

## <a name="server-scoped-triggers"></a>Trigger con ambito server<a id="ServerScopedTriggers"></a>

**Title: il trigger con ambito server non è supportato nel database SQL di Azure**   
**Categoria**: avviso   

**Descrizione**   
Un trigger è un tipo speciale di stored procedure eseguito in risposta a una determinata azione su una tabella come l'inserimento, l'eliminazione o l'aggiornamento dei dati. I trigger con ambito server non sono supportati nel database SQL di Azure. Il database SQL di Azure non supporta le opzioni seguenti per i trigger: per l'accesso, la crittografia, con ACCODAmento e non per la replica, l'opzione nome esterno (non è disponibile alcun supporto per i metodi esterni), l'opzione tutti i SERVER (trigger DDL), il trigger per un evento LOGON (trigger LOGON), il database SQL di Azure non supporta i trigger CLR.


**Raccomandazione**   
Usare invece il trigger a livello di database. In alternativa, Esegui la migrazione a Istanza gestita SQL di Azure o SQL Server nella macchina virtuale di Azure

Ulteriori informazioni: [risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>Processi di SQL Agent<a id="AgentJobs"></a>

**Title: i processi di SQL Server Agent non sono disponibili nel database SQL di Azure**   
**Categoria**: avviso   

**Descrizione**   
SQL Server Agent è un servizio di Microsoft Windows che esegue attività amministrative pianificate, denominate processi in SQL Server. I processi di SQL Server Agent non sono disponibili nel database SQL di Azure. 


**Indicazione** Usare i processi elastici (anteprima), che sostituiscono i processi di SQL Server Agent nel database SQL di Azure. Processi di database elastico per il database SQL di Azure consentono di eseguire in modo affidabile gli script T-SQL che si estendono su più database, ritentando automaticamente e fornendo le garanzie di completamento finali. In alternativa, è consigliabile eseguire la migrazione ad Azure SQL Istanza gestita o SQL Server in macchine virtuali di Azure.

Altre informazioni: [Introduzione a processi di database elastico (anteprima) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>Dimensioni database SQL<a id="SQLDBDatabaseSize"></a>

**Title: il database SQL di Azure non supporta dimensioni del database superiori a 100 TB.**   
**Categoria**: problema   

**Descrizione**   
Le dimensioni del database sono maggiori delle dimensioni massime supportate di 100 TB. 


**Raccomandazione**   
Valutare se i dati possono essere archiviati o compressi o partizionati in più database. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

Altre informazioni: [limiti delle risorse di vCore](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Title: SQL Mail non è più disponibile.**   
**Categoria**: avviso   

**Descrizione**   
SQL Mail è stato interrotto e rimosso nel database SQL di Azure.


**Raccomandazione**   
Prendere in considerazione la migrazione ad Azure SQL Istanza gestita o SQL Server in macchine virtuali di Azure e usare Posta elettronica database.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: sono state rilevate istruzioni che fanno riferimento a stored procedure di sistema rimosse che non sono disponibili nel database SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Nel database SQL di Azure non è possibile usare il sistema e le stored procedure estese seguenti: `sp_dboption` , `sp_addserver` , `sp_dropalias` , `sp_activedirectory_obj` , `sp_activedirectory_scp` , `sp_activedirectory_start` .


**Raccomandazione**    
Rimuovere i riferimenti alle procedure di sistema non supportate che sono state rimosse nel database SQL di Azure.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="trace-flags"></a>Flag di traccia<a id="TraceFlags"></a>

**Title: il database SQL di Azure non supporta i flag di traccia**   
**Categoria**: avviso   

**Descrizione**   
I flag di traccia vengono utilizzati per impostare temporaneamente funzionalità del server specifiche o per disattivare un particolare comportamento. I flag di traccia vengono spesso utilizzati per diagnosticare problemi di prestazioni o per eseguire il debug di stored procedure o sistemi complessi. Il database SQL di Azure non supporta i flag di traccia. 


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i flag di traccia non supportati nel database SQL di Azure e valutare se possono essere rimossi. In alternativa, eseguire la migrazione ad Azure SQL Istanza gestita che supporta un numero limitato di flag di traccia globali o SQL Server nella macchina virtuale di Azure.

Ulteriori informazioni: [risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Autenticazione di Windows<a id="WindowsAuthentication"></a>

**Title: gli utenti del database di cui viene eseguito il mapping con l'autenticazione di Windows (sicurezza integrata) non sono supportati nel database SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Il database SQL di Azure supporta due tipi di autenticazione 
- Autenticazione SQL: Usa nome utente e password 
- Azure Active Directory Authentication: usa le identità gestite da Azure Active Directory ed è supportata per i domini gestiti e integrati. 

Gli utenti del database mappati con l'autenticazione di Windows (sicurezza integrata) non sono supportati nel database SQL di Azure. 



**Raccomandazione**   
Federazione del Active Directory locale con Azure Active Directory. L'identità Windows può quindi essere sostituita con le identità Azure Active Directory equivalenti. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [funzionalità di sicurezza del database SQL](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell non è supportato nel database SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
xp_cmdshell che genera una shell dei comandi di Windows e passa una stringa per l'esecuzione non è supportata nel database SQL di Azure. 


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti gli oggetti che usano xp_cmdshell e valutare se è possibile rimuovere il riferimento a xp_cmdshell o l'oggetto interessato. Prendere in considerazione anche l'esplorazione di automazione di Azure che offre servizi di configurazione e automazione basati sul cloud. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

Per avviare la migrazione del SQL Server al database SQL di Azure, vedere la [Guida alla migrazione da SQL Server a database SQL](sql-server-to-sql-database-guide.md).

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul database SQL, vedere:
   - [Panoramica del database SQL di Azure](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
