---
title: Regole di valutazione per la migrazione da SQL Server a SQL Istanza gestita
description: Regole di valutazione per identificare i problemi relativi all'istanza di SQL Server di origine che devono essere risolti prima di eseguire la migrazione al Istanza gestita SQL di Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054728"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Regole di valutazione per la migrazione da SQL Server a SQL Istanza gestita
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Gli strumenti di migrazione convalidano l'istanza di SQL Server di origine eseguendo una serie di regole di valutazione per identificare i problemi che devono essere risolti prima di eseguire la migrazione del database di SQL Server in Istanza gestita SQL di Azure. 

Questo articolo fornisce un elenco delle regole usate per valutare la fattibilità della migrazione del database di SQL Server al Istanza gestita SQL di Azure. 

## <a name="analysiscommand-job"></a>Processo AnalysisCommand<a id="AnalysisCommandJob"></a>

**Title: il passaggio di processo AnalysisCommand non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: avviso   


**Descrizione**   
Si tratta di un passaggio di processo che esegue un comando Analysis Services. Il passaggio di processo AnalysisCommand non è supportato in Istanza gestita SQL di Azure.

**Raccomandazione**     
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i processi utilizzando il passaggio di processo del comando di Analysis Services e valutare se è possibile rimuovere il passaggio del processo o l'oggetto interessato. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [SQL Server Agent differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Processo AnalysisQuery<a id="AnalysisQueryJob"></a>

**Title: il passaggio di processo AnalysisQuery non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Si tratta di un passaggio di processo che esegue una query Analysis Services. Il passaggio di processo AnalysisQuery non è supportato in Istanza gestita SQL di Azure.


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i processi utilizzando il passaggio di processo query di Analysis Services e valutare se è possibile rimuovere il passaggio del processo o l'oggetto interessato. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [SQL Server Agent differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Assembly da file<a id="AssemblyFromFile"></a>

**Titolo:' CREATE ASSEMBLY ' è ALTER ASSEMBLY ' con un parametro file non sono supportati in Azure SQL Istanza gestita.**   
**Categoria**: avviso   

**Descrizione**   
Il Istanza gestita SQL di Azure non può accedere alle condivisioni file o alle cartelle di Windows. Vedere la sezione "oggetti interessati" per gli usi specifici delle istruzioni BULK INSERT che non fanno riferimento a un BLOB di Azure. Gli oggetti con ' BULK INSERT ' in cui l'origine non è un archivio BLOB di Azure non funzioneranno dopo la migrazione a SQL Istanza gestita di Azure.


**Raccomandazione**   
È necessario convertire BULK INSERT istruzioni che usano file locali o condivisioni file per usare i file di archiviazione BLOB di Azure, quando si esegue la migrazione ad Azure SQL Istanza gestita. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

Altre informazioni: [differenze CLR in istanza gestita SQL di Azure ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Inserimento bulk<a id="BulkInsert"></a>

**Title: BULK INSERT con l'origine dati BLOB non di Azure non è supportata in Istanza gestita SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
Il Istanza gestita SQL di Azure non può accedere alle condivisioni file o alle cartelle di Windows. Vedere la sezione "oggetti interessati" per gli usi specifici delle istruzioni BULK INSERT che non fanno riferimento a un BLOB di Azure. Gli oggetti con ' BULK INSERT ' in cui l'origine non è un archivio BLOB di Azure non funzioneranno dopo la migrazione a SQL Istanza gestita di Azure.


**Raccomandazione**   
È necessario convertire BULK INSERT istruzioni che usano file locali o condivisioni file per usare i file di archiviazione BLOB di Azure, quando si esegue la migrazione ad Azure SQL Istanza gestita.

Altre informazioni: [differenze BULK INSERT e OPENROWSET in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Sicurezza CLR<a id="ClrStrictSecurity"></a>

**Title: gli assembly CLR contrassegnati come SAFE o EXTERNAL_ACCESS sono considerati non sicuri**   
**Categoria**: problema   

**Descrizione**   
La modalità di sicurezza CLR Strict viene applicata in Azure SQL Istanza gestita. Questa modalità è abilitata per impostazione predefinita e introduce modifiche di rilievo per i database contenenti assembly CLR definiti dall'utente contrassegnati come SAFE o EXTERNAL_ACCESS.


**Raccomandazione**   
CLR usa la Sicurezza dall'accesso di codice (CAS, Code Access Security) in .NET Framework, non più supportata come limite di sicurezza. A partire da SQL Server 2017 (14. x) motore di database, `sp_configure` è stata introdotta un'opzione denominata CLR Strict Security per migliorare la sicurezza degli assembly CLR. CLR Strict Security è abilitato per impostazione predefinita e considera gli assembly CLR SAFE e EXTERNAL_ACCESS come se fossero contrassegnati come unsafe. Quando CLR Strict Security è disabilitato, un assembly CLR creato con PERMISSION_SET = SAFE può essere in grado di accedere alle risorse di sistema esterne, chiamare codice non gestito e acquisire privilegi sysadmin. Dopo l'abilitazione di strict security, tutti gli assembly non firmati non verranno caricati. Inoltre, se un database dispone di assembly SAFE o EXTERNAL_ACCESS, le istruzioni di ripristino o di associazione del DATABASE possono essere completate, ma gli assembly potrebbero non essere caricati. Per caricare gli assembly, è necessario modificare o eliminare e ricreare ogni assembly in modo che sia firmato con un certificato o una chiave asimmetrica con un account di accesso corrispondente con l'autorizzazione UNSAFE ASSEMBLY nel server.

Ulteriori informazioni: [CLR Strict Security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Clausola COMPUTE<a id="ComputeClause"></a>

**Title: la clausola COMPUTE non è più disponibile ed è stata rimossa.**   
**Categoria**: avviso   

**Descrizione**   
La clausola COMPUTE genera i totali che vengono visualizzati come colonne di riepilogo aggiuntive alla fine del set di risultati. Tuttavia, questa clausola non è più supportata in Azure SQL Istanza gestita.



**Raccomandazione**   
Il modulo T-SQL deve essere riscritto usando l'operatore ROLLUP. Il codice seguente illustra in che modo è possibile sostituire il calcolo con ROLLUP: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="cryptographic-provider"></a>Provider di crittografia<a id="CryptographicProvider"></a>

**Title: è stato trovato l'uso di CREATE CRYPTOGRAPHIC PROVIDER o ALTER CRYPTOGRAPHIC PROVIDER, che non è supportato in Azure SQL Istanza gestita.**   
**Categoria**: problema   

**Descrizione**   
Il Istanza gestita SQL di Azure non supporta le istruzioni del PROVIDER del servizio di crittografia perché non può accedere ai file. Vedere la sezione oggetti interessati per gli utilizzi specifici delle istruzioni del PROVIDER di crittografia. Gli oggetti con ' CREATE CRYPTOGRAPHIC PROVIDER ' o ' ALTER CRYPTOGRAPHIC PROVIDER ' non funzioneranno correttamente dopo la migrazione ad Azure SQL Istanza gestita.


**Raccomandazione**   
Esaminare gli oggetti con ' CREATE CRYPTOGRAPHIC PROVIDER ' o ' ALTER CRYPTOGRAPHIC PROVIDER '. In tutti gli oggetti di questo tipo sono necessari rimuovere gli utilizzi di queste funzionalità. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

Altre informazioni: [differenze del provider di crittografia in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Compatibilità del database<a id="DbCompatLevelLowerThan100"></a>

**Title: il livello di compatibilità del database inferiore a 100 non è supportato**   
**Categoria**: avviso   

**Descrizione**   
Il livello di compatibilità del database è uno strumento prezioso per facilitare la modernizzazione del database, consentendo l'aggiornamento del SQL Server motore di database, mantenendo al tempo stesso la connessione dello stato funzionale delle applicazioni mantenendo lo stesso livello di compatibilità del database pre-aggiornamento. Istanza gestita SQL di Azure non supporta i livelli di compatibilità inferiori a 100. Quando il database con livello di compatibilità inferiore a 100 viene ripristinato nel Istanza gestita SQL di Azure, il livello di compatibilità viene aggiornato a 100. 


**Consiglio**... Valutare se la funzionalità dell'applicazione è intatta quando il livello di compatibilità del database viene aggiornato a 100 in Azure SQL Istanza gestita. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [livelli di compatibilità supportati in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Alias entità di database<a id="DatabasePrincipalAlias"></a>

**Titolo: SYS. DATABASE_PRINCIPAL_ALIASES è sospeso ed è stato rimosso.**   
**Categoria**: problema   

**Descrizione**   
SYS. DATABASE_PRINCIPAL_ALIASES non è più disponibile ed è stata rimossa in Istanza gestita SQL di Azure.


**Raccomandazione**   
Usare ruoli anziché alias.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="disable_def_cnst_chk-option"></a>Opzione DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Title: l'opzione SET DISABLE_DEF_CNST_CHK è sospesa ed è stata rimossa.**   
**Categoria**: problema   

**Descrizione**   
L'opzione SET DISABLE_DEF_CNST_CHK è stata sospesa ed è stata rimossa in Azure SQL Istanza gestita.


Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="fastfirstrow-hint"></a>Hint FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Title: l'hint per la query FASTFIRSTROW non è più disponibile ed è stato rimosso.**   
**Categoria**: avviso   

**Descrizione**   
L'hint per la query FASTFIRSTROW non è più disponibile ed è stato rimosso in Azure SQL Istanza gestita.


**Raccomandazione**   
Al posto dell'opzione di utilizzo hint per la query FASTFIRSTROW (FAST n).

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Title: FILESTREAM e FileTable non sono supportati in Azure SQL Istanza gestita.**   
**Categoria**: problema   

**Descrizione**   
La funzionalità FILESTREAM, che consente di archiviare dati non strutturati, ad esempio documenti di testo, immagini e video in file system NTFS, non è supportata in Istanza gestita SQL di Azure. **Non è possibile eseguire la migrazione di questo database perché il backup contenente i filegroup FILESTREAM non può essere ripristinato in Istanza gestita SQL di Azure.**


**Raccomandazione**   
Caricare i file non strutturati nell'archiviazione BLOB di Azure e archiviare i metadati correlati a questi file (nome, tipo, percorso URL, chiave di archiviazione e così via) in Azure SQL Istanza gestita. Potrebbe essere necessario riprogettare l'applicazione per abilitare i BLOB di streaming da e verso Azure SQL Istanza gestita. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [streaming di BLOB da e verso SQL Azure Blog](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>MS DTC eterogeneo<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Title: l'avvio della transazione DISTRIBUIta con un server remoto non SQL Server non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
La transazione distribuita avviata da Transact SQL BEGIN DISTRIBUted TRANSACTION e gestita da Microsoft Distributed Transaction Coordinator (MS DTC) non è supportata in Azure SQL Istanza gestita se il server remoto non è SQL Server. 


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per vedere tutti gli oggetti che usano la transazione BEGIN DISTRUBUTED. Provare a migrare i database dei partecipanti in Azure SQL Istanza gestita in cui sono supportate le transazioni distribuite tra più istanze (attualmente in anteprima). In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [transazioni tra più server per Azure SQL istanza gestita ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>MS DTC omogeneo<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Title: l'avvio della transazione DISTRIBUIta è supportato tra più server per Azure SQL Istanza gestita.**   
**Categoria**: problema   

**Descrizione**   
La transazione distribuita avviata da Transact SQL BEGIN DISTRIBUted TRANSACTION e gestita da Microsoft Distributed Transaction Coordinator (MS DTC) è supportata su più server per Istanza gestita SQL di Azure.


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per vedere tutti gli oggetti che usano la transazione BEGIN DISTRUBUTED. Provare a migrare i database dei partecipanti in Azure SQL Istanza gestita in cui sono supportate le transazioni distribuite tra più istanze (attualmente in anteprima). In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

Altre informazioni: [transazioni tra più server per Azure SQL istanza gestita](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Server collegato (provider non SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Title: il server collegato con provider non SQL Server non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
I server collegati consentono al motore di database SQL Server di eseguire comandi su OLE DB origini dati esterne all'istanza di SQL Server. Il server collegato con provider non SQL Server non è supportato in Istanza gestita SQL di Azure. 


**Raccomandazione**   
Il Istanza gestita SQL di Azure non supporta la funzionalità del server collegato se il provider del server remoto è non SQL Server come Oracle, Sybase e così via. 

Per eliminare la necessità di server collegati, è consigliabile eseguire le azioni seguenti: 
- Identificare i database dipendenti da server remoti non SQL e provare a spostarli nel database di cui si esegue la migrazione. 
- Eseguire la migrazione dei database dipendenti a destinazioni supportate, ad esempio SQL Istanza gestita, database SQL, Azure sinapsi SQL e SQL Server istanze. 
- Prendere in considerazione la creazione di un server collegato tra Istanza gestita SQL di Azure e SQL Server nella macchina virtuale di Azure (VM SQL).  Quindi, dalla VM SQL creare un server collegato a Oracle, Sybase e così via. Questo approccio implica due hop, ma può essere utilizzato come soluzione temporanea.  
- In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [differenze tra server collegati in Azure SQL istanza gestita](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Processo di merge<a id="MergeJob"></a>

**Title: il passaggio del processo di merge non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Si tratta di un passaggio di processo che attiva il agente di merge di replica. Agente merge repliche è un eseguibile dell'utilità che consente di applicare lo snapshot iniziale incluso nelle tabelle di database ai Sottoscrittori. Consente inoltre di unire le modifiche ai dati incrementali apportate nel server di pubblicazione dopo la creazione dello snapshot iniziale e di riconciliare i conflitti in base alle regole configurate oppure utilizzando un sistema di risoluzione personalizzato. Il passaggio del processo di merge non è supportato in Istanza gestita SQL di Azure.


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i processi che utilizzano il passaggio del processo di merge e valutare se è possibile rimuovere il passaggio del processo o l'oggetto interessato. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

Altre informazioni: [SQL Server Agent differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Dimensioni database MI<a id="MIDatabaseSize<"></a>

**Title: il Istanza gestita SQL di Azure non supporta dimensioni del database maggiori di 8 TB.**   
**Categoria**: problema   

**Descrizione**   
Le dimensioni del database sono maggiori del valore massimo di archiviazione riservata dell'istanza. **Non è possibile selezionare questo database per la migrazione perché le dimensioni superano il limite consentito.**


**Raccomandazione**   
Valutare se è possibile archiviare i dati compressi o partizionati in più database. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [caratteristiche di generazione hardware di Azure SQL istanza gestita ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Dimensioni istanza MI<a id="MIInstanceSize<"></a>

**Title: le dimensioni massime di archiviazione dell'istanza nel Istanza gestita SQL di Azure non possono essere maggiori di 8 TB.**   
**Categoria**: avviso   

**Descrizione**   
La dimensione di tutti i database è maggiore del numero massimo di istanze di archiviazione riservate.  


**Raccomandazione**   
Si consiglia di eseguire la migrazione dei database a diverse istanze gestite di SQL Azure o di SQL Server in una macchina virtuale di Azure se tutti i database devono esistere nella stessa istanza. 

Altre informazioni: [caratteristiche di generazione hardware di Azure SQL istanza gestita ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Più file di log<a id="MultipleLogFiles<"></a>

**Title: Istanza gestita SQL di Azure non supporta più file di log.**   
**Categoria**: problema   

**Descrizione**   
SQL Server consente a un database di accedere a più file. Questo database include più file di log non supportati in Azure SQL Istanza gestita. * * Non è possibile eseguire la migrazione di questo database perché non è possibile ripristinare il backup nel Istanza gestita SQL di Azure. 
**

**Raccomandazione**   
Azure SQL Istanza gestita supporta solo un singolo log per ogni database. Prima di eseguire la migrazione del database ad Azure, è necessario eliminare tutti i file di log: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Altre informazioni: [Opzioni di database non supportate in Azure SQL istanza gestita  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Colonna successiva<a id="NextColumn"></a>

**Title: le tabelle e le colonne denominate NEXT provocheranno un errore in Azure SQL Istanza gestita.**   
**Categoria**: problema   

**Descrizione**   
Sono state rilevate tabelle o colonne denominate NEXT. Le sequenze, introdotte in Microsoft SQL Server, usano la funzione NEXT VALUE FOR ANSI standard. Se una tabella o colonna viene denominata NEXT, le viene associato un alias VALUE e la parola chiave AS dello standard ANSI viene omessa, è possibile che nell'istruzione risultante si verifichi un errore.


**Raccomandazione**   
Riscrivere le istruzioni in modo da includere la parola chiave AS dello standard ANSI quando si crea l'alias di una tabella o una colonna. Ad esempio, quando una colonna viene denominata NEXT e tale colonna viene aliasata come valore, la query Seleziona valore successivo da TABLE genererà un errore e dovrà essere riscritta come SELECT NEXT AS VALUE FROM TABLE. Analogamente, quando una tabella viene denominata NEXT e a tale tabella viene associato un alias VALUE, la query SELECT col1 FROM NEXT VALUE determinerà un errore e dovrà essere riscritta come SELECT col1 FROM NEXT AS VALUE.



## <a name="non-ansi-style-left-outer-join"></a>left outer join di stile non ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: left outer join di stile non ANSI non è più disponibile ed è stata rimossa.**   
**Categoria**: avviso   

**Descrizione**   
Left outer join di stile non ANSI non è più disponibile ed è stata rimossa in Istanza gestita SQL di Azure. 


**Raccomandazione**   
Utilizzare la sintassi di join ANSI.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="non-ansi-style-right-outer-join"></a>right outer join di stile non ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: right outer join di stile non ANSI non è più disponibile ed è stata rimossa.**   
**Categoria**: avviso   

**Descrizione**   
Right outer join di stile non ANSI non è più disponibile ed è stata rimossa in Istanza gestita SQL di Azure. 



Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

**Raccomandazione**   
Utilizzare la sintassi di join ANSI.

## <a name="databases-exceed-100"></a>I database superano 100 <a id="NumDbExceeds100"></a>

**Title: Azure SQL Istanza gestita supporta un massimo di 100 database per ogni istanza.**   
**Categoria**: avviso   

**Descrizione**   
Il numero massimo di database supportati in Azure SQL Istanza gestita è 100, a meno che non sia stato raggiunto il limite delle dimensioni di archiviazione dell'istanza.



**Raccomandazione**   
Si consiglia di eseguire la migrazione dei database a diverse istanze gestite di SQL Azure o di SQL Server in una macchina virtuale di Azure se tutti i database devono esistere nella stessa istanza. 

Altre informazioni: [limiti delle risorse di Azure SQL istanza gestita ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (origine dati non BLOB)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: la funzionalità OpenRowSet usata nell'operazione bulk con l'origine dati di archiviazione BLOB non di Azure non è supportata in Azure SQL Istanza gestita.**   
**Categoria**: problema   

**Descrizione**   
OPENROWSET supporta le operazioni bulk tramite un provider BULK incorporato che consente la lettura e la restituzione di dati da un file come set di righe. L'origine dati OPENROWSET con archiviazione BLOB non di Azure non è supportata in Azure SQL Istanza gestita. 



**Raccomandazione**   
La funzione OPENROWSET può essere usata per eseguire query solo su istanze di SQL Server (gestite, locali o in macchine virtuali). Come provider sono supportati solo i valori SQLNCLI, SQLNCLI11 e SQLOLEDB. Pertanto, l'azione consigliata è quella di identificare i database dipendenti dai server remoti non SQL e provare a spostarli nel database di cui viene eseguita la migrazione. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

Altre informazioni: [differenze BULK INSERT e OPENROWSET in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (provider non SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Title: OpenRowSet con provider non SQL non è supportato in Azure SQL Istanza gestita.**   
**Categoria**: problema   

**Descrizione**   
Si tratta di un metodo alternativo per l'accesso alle tabelle di un server collegato e corrisponde a un metodo ad hoc eseguito una sola volta per la connessione e l'accesso ai dati remoti tramite OLE DB. OpenRowSet con provider non SQL non è supportato in Istanza gestita SQL di Azure. 



**Raccomandazione**   
La funzione OPENROWSET può essere usata per eseguire query solo su istanze di SQL Server (gestite, locali o in macchine virtuali). Come provider sono supportati solo i valori SQLNCLI, SQLNCLI11 e SQLOLEDB. Pertanto, l'azione consigliata è quella di identificare i database dipendenti dai server remoti non SQL e provare a spostarli nel database di cui viene eseguita la migrazione.

Altre informazioni: [differenze BULK INSERT e OPENROWSET in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Processo di PowerShell<a id="PowerShellJob"></a>

**Title: il passaggio del processo di PowerShell non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Si tratta di un passaggio di processo che esegue uno script di PowerShell. Il passaggio del processo di PowerShell non è supportato in Istanza gestita SQL di Azure. 



**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i processi usando il passaggio del processo di PowerShell e valutare se è possibile rimuovere il passaggio del processo o l'oggetto interessato.  Valutare se è possibile usare automazione di Azure. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure

Altre informazioni: [SQL Server Agent differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Processo di lettura coda<a id="QueueReaderJob"></a>

**Title: il passaggio di processo lettura coda non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: avviso   

**Descrizione**   
Si tratta di un passaggio di processo che attiva il agente di lettura coda di replica. Il agente di lettura coda di replica è un eseguibile che consente di leggere i messaggi archiviati in una coda di Microsoft SQL Server o in una coda di messaggi Microsoft, quindi di applicare tali messaggi al server di pubblicazione. L'agente di lettura coda viene utilizzato con le pubblicazioni snapshot e transazionali che consentono l'aggiornamento in coda. Il passaggio di processo lettura coda non è supportato in Istanza gestita SQL di Azure.


**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i processi che utilizzano il passaggio di processo lettura coda e valutare se è possibile rimuovere il passaggio del processo o l'oggetto interessato. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [SQL Server Agent differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: le chiamate RAISERROR di tipo legacy devono essere sostituite con gli equivalenti moderni.**   
**Categoria**: avviso   

**Descrizione**   
Le chiamate RAISERROR come l'esempio seguente vengono denominate come legacy, perché non includono le virgole e la parentesi. RAISERROR 50001' questo è un test '. Questo metodo di chiamata di RAISERROR non è più disponibile e viene rimosso nella Istanza gestita SQL di Azure.



**Raccomandazione**   
Riscrivere l'istruzione usando la sintassi RAISERROR corrente oppure valutare se l'approccio moderno di `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` è fattibile.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Title: la funzionalità Service Broker è parzialmente supportata in Istanza gestita SQL di Azure.**   
**Categoria**: problema   

**Descrizione**   
SQL Server Service Broker fornisce il supporto nativo per le applicazioni di messaggistica e accodamento nel motore di database SQL Server. Per questo database è abilitata la Service Broker tra istanze, che non è supportata in Istanza gestita SQL di Azure. 


**Raccomandazione**   
Istanza gestita SQL di Azure non supporta Service Broker tra istanze, ad esempio se l'indirizzo non è locale. Prima di eseguire la migrazione del database ad Azure, è necessario disabilitare Service Broker usando il comando seguente: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Inoltre, potrebbe essere necessario rimuovere o arrestare l'endpoint Service Broker per impedire l'arrivo dei messaggi nell'istanza di SQL. Una volta eseguita la migrazione del database in Azure, è possibile esaminare le funzionalità del bus di servizio di Azure per implementare un sistema di messaggistica generico basato sul cloud anziché Service Broker. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure. 

Altre informazioni: [Service Broker differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Title: SQL Mail non è più disponibile.**   
**Categoria**: avviso   


**Descrizione**   
SQL Mail è stato interrotto e rimosso in Azure SQL Istanza gestita.



**Raccomandazione**   
Usare la posta elettronica database.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: sono state rilevate istruzioni che fanno riferimento a stored procedure di sistema rimosse che non sono disponibili in Azure SQL Istanza gestita.**   
**Categoria**: avviso   

**Descrizione**   
Non è possibile usare il sistema e le stored procedure estese seguenti in Azure SQL istanza gestita- `sp_dboption` ,, `sp_addserver` `sp_dropalias` , `sp_activedirectory_obj` , `sp_activedirectory_scp` e `sp_activedirectory_start` . 




**Raccomandazione**   
Rimuovere i riferimenti a procedure di sistema non supportate che sono state rimosse in Azure SQL Istanza gestita.

Altre informazioni: [funzionalità di motore di database](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) non più utilizzate in SQL Server

## <a name="transact-sql-job"></a>Processo Transact-SQL<a id="TransactSqlJob"></a>

**Title: il passaggio di processo TSQL include comandi non supportati in Azure SQL Istanza gestita**   
**Categoria**: avviso   


**Descrizione**   
Si tratta di un passaggio di processo che esegue gli script TSQL all'ora pianificata. Il passaggio di processo TSQL include comandi non supportati che non sono supportati in Azure SQL Istanza gestita.



**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i processi che includono comandi non supportati in Azure SQL Istanza gestita e valutare se è possibile rimuovere il passaggio del processo o l'oggetto interessato. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [SQL Server Agent differenze in Azure SQL istanza gestita ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Flag di traccia<a id="TraceFlags"></a>

**Title: i flag di traccia non supportati in Azure SQL Istanza gestita sono stati trovati**   
**Categoria**: avviso   


**Descrizione**   
Istanza gestita SQL di Azure supporta solo un numero limitato di flag di traccia globali. I flag di traccia della sessione non sono supportati.



**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti i flag di traccia non supportati in Azure SQL Istanza gestita e valutare se possono essere rimossi. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [flag di traccia](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Autenticazione di Windows<a id="WindowsAuthentication"></a>

**Titolo: gli utenti del database mappati con l'autenticazione di Windows (sicurezza integrata) non sono supportati in Azure SQL Istanza gestita**   
**Categoria**: avviso   


**Descrizione**   
Istanza gestita SQL di Azure supporta due tipi di autenticazione: 
- Autenticazione SQL, che usa nome utente e password
- L'autenticazione di Azure Active Directory, che usa identità gestite da Azure Active Directory ed è supportata per i domini gestiti e integrati. 

Gli utenti del database mappati con l'autenticazione di Windows (sicurezza integrata) non sono supportati in Azure SQL Istanza gestita. 


**Raccomandazione**   
Federazione del Active Directory locale con Azure Active Directory. L'identità Windows può quindi essere sostituita con le identità Azure Active Directory equivalenti. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [funzionalità di sicurezza di SQL istanza gestita](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell non è supportato in Istanza gestita SQL di Azure.**   
**Categoria**: problema   


**Descrizione**   
Xp_cmdshell che genera una shell dei comandi di Windows e passa una stringa per l'esecuzione non è supportata nel Istanza gestita SQL di Azure. 



**Raccomandazione**   
Esaminare la sezione oggetti interessati in Azure Migrate per visualizzare tutti gli oggetti che usano xp_cmdshell e valutare se è possibile rimuovere il riferimento a xp_cmdshell o l'oggetto interessato. Prendere in considerazione l'esplorazione di automazione di Azure che offre servizi di configurazione e automazione basati sul cloud. In alternativa, eseguire la migrazione a SQL Server in una macchina virtuale di Azure.

Altre informazioni: [differenze tra stored procedure in Azure SQL istanza gestita](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Passaggi successivi

Per avviare la migrazione del SQL Server al Istanza gestita SQL di Azure, vedere la [Guida alla migrazione da SQL Server a sql istanza gestita](sql-server-to-managed-instance-guide.md).

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni su Azure SQL Istanza gestita vedere:
   - [Livelli di servizio in Azure SQL Istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Differenze tra SQL Server e Azure SQL Istanza gestita](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).