---
title: Configurare il massimo grado di parallelismo (MAXDOP)
titleSuffix: Azure SQL Database
description: Informazioni sul massimo grado di parallelismo (MAXDOP).
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536909"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Configurare il grado massimo di parallelismo (MAXDOP) nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Questo articolo descrive l'impostazione di configurazione **max degree of parallelism (MAXDOP)** in database SQL di Azure. 

> [!NOTE]
> **Questo contenuto è incentrato sulle database SQL di Azure.** database SQL di Azure si basa sulla versione stabile più recente del motore di database Microsoft SQL Server, gran parte del contenuto è simile anche se le opzioni di configurazione e risoluzione dei problemi sono diverse. Per altre informazioni su MAXDOP in SQL Server, vedere [Configurare l'opzione di](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)configurazione del server max degree of parallelism .

## <a name="overview"></a>Panoramica
  MAXDOP controlla il parallelismo tra query nel motore di database. Valori MAXDOP più elevati in genere comportano un maggior numero di thread paralleli per ogni query e un'esecuzione più rapida delle query. 

  In database SQL di Azure, l'impostazione MAXDOP predefinita per ogni nuovo database singolo e database del pool elastico è 8. Questa impostazione predefinita impedisce l'utilizzo non necessario delle risorse, consentendo al motore di database di eseguire più rapidamente le query usando thread paralleli. In genere non è necessario configurare ulteriormente MAXDOP nei carichi di lavoro database SQL di Azure, anche se può offrire vantaggi come esercizio di ottimizzazione delle prestazioni avanzato.

> [!Note]
>   Nel mese di settembre 2020, in base a anni di telemetria nel servizio database SQL di Azure MAXDOP 8 è stato impostato come valore predefinito per i nuovi [database,](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)come valore ottimale per la più ampia gamma di carichi di lavoro dei clienti. Questa impostazione predefinita ha consentito di evitare problemi di prestazioni a causa di un parallelismo eccessivo. In precedenza, l'impostazione predefinita per i nuovi database era MAXDOP 0. MAXDOP non è stato modificato automaticamente per i database esistenti creati prima di settembre 2020.

  In generale, se il motore di database sceglie di eseguire una query usando il parallelismo, il tempo di esecuzione è più veloce. Tuttavia, il parallelismo in eccesso può utilizzare risorse aggiuntive del processore senza migliorare le prestazioni delle query. Su larga scala, il parallelismo in eccesso può influire negativamente sulle prestazioni delle query per tutte le query in esecuzione nella stessa istanza del motore di database. Tradizionalmente, l'impostazione di un limite superiore per il parallelismo è un esercizio comune di ottimizzazione delle prestazioni nei SQL Server di lavoro.

  La tabella seguente descrive il comportamento del motore di database quando si eseguono query con valori MAXDOP diversi:

| MAXDOP | Comportamento | 
|--|--|
| = 1 | Il motore di database usa un singolo thread seriale per eseguire query. I thread paralleli non vengono usati. | 
| > 1 | Il motore di database [](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) imposta il numero di utilità di pianificazione aggiuntive che devono essere usate dai thread paralleli sul valore MAXDOP o sul numero totale di processori logici, a seconda del valore minore. |
| = 0 | Il motore di database [](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) imposta il numero di utilità di pianificazione aggiuntive che devono essere usate dai thread paralleli sul numero totale di processori logici o 64, a seconda del valore minore. | 
| | |

> [!Note]
> Ogni query viene eseguita con almeno un'utilità di pianificazione e un thread di lavoro in tale utilità di pianificazione.
>
> Una query in esecuzione con parallelismo usa utilità di pianificazione aggiuntive e thread paralleli aggiuntivi. Poiché nella stessa utilità di pianificazione possono essere eseguiti più thread paralleli, il numero totale di thread usati per eseguire una query può essere superiore al valore MAXDOP specificato o al numero totale di processori logici. Per altre informazioni, vedere [Pianificazione di attività parallele.](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)

##  <a name="considerations"></a><a name="Considerations"></a> Considerazioni  

-   In database SQL di Azure è possibile modificare il valore MAXDOP predefinito:
    -   A livello di query, usando l'[hint per la query](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP**.     
    -   A livello di database, con la [configurazione con ambito database](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) **MAXDOP**.

-   Le considerazioni e SQL Server e le raccomandazioni di MAXDOP di lunga data [sono](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) applicabili database SQL di Azure. 

-   Le operazioni tramite cui viene creato o ricompilato un indice o eliminato un indice cluster possono richiedere un elevato utilizzo di risorse. È possibile eseguire l'override del valore MAXDOP del database per le operazioni sull'indice specificando l'opzione MAXDOP index `CREATE INDEX` nell'istruzione o `ALTER INDEX` . Il valore MAXDOP viene applicato all'istruzione al momento dell'esecuzione e non viene archiviato nei metadati dell'indice. Per altre informazioni, vedere [Configurazione di operazioni parallele sugli indici](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Oltre alle query e alle operazioni sull'indice, l'opzione di configurazione con ambito database per MAXDOP controlla anche il parallelismo di altre istruzioni che possono usare l'esecuzione parallela, ad esempio DBCC CHECKTABLE, DBCC CHECKDB e DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Indicazioni  

  La modifica di MAXDOP per il database può avere un impatto significativo sulle prestazioni delle query e sull'utilizzo delle risorse, sia positive che negative. Non esiste tuttavia un singolo valore MAXDOP ottimale per tutti i carichi di lavoro. Le [raccomandazioni](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) per l'impostazione di MAXDOP sono sfumato e dipendono da molti fattori. 

  Alcuni carichi di lavoro simultanei di picco possono funzionare meglio con un MAXDOP diverso rispetto ad altri. Un MAXDOP configurato correttamente deve ridurre il rischio di eventi imprevisti relativi a prestazioni e disponibilità e, in alcuni casi, può ridurre i costi evitando l'utilizzo non necessario delle risorse e quindi riducendo il livello a un obiettivo di servizio inferiore.

### <a name="excessive-parallelism"></a>Parallelismo eccessivo

  Un valore MAXDOP superiore spesso riduce la durata delle query a elevato utilizzo di CPU. Tuttavia, un parallelismo eccessivo può peggiorare le prestazioni di altri carichi di lavoro simultanei causando l'affama di altre query di risorse cpu e thread di lavoro. In casi estremi, un parallelismo eccessivo può utilizzare tutte le risorse del database o del pool elastico, causando timeout delle query, errori e interruzioni dell'applicazione. 

> [!Tip]
> È consigliabile che i clienti evitino di impostare MAXDOP su 0 anche se attualmente non sembra causare problemi.

  Il parallelismo eccessivo diventa più problematico quando sono presenti più richieste simultanee di quelle supportate dalle risorse cpu e thread di lavoro fornite dall'obiettivo di servizio. Evitare MAXDOP 0 per ridurre il rischio di potenziali problemi futuri dovuti a un parallelismo eccessivo se un database viene ridimensionato o se le future generazioni di hardware in database SQL di Azure forniscono più core per lo stesso obiettivo di servizio del database.

### <a name="modifying-maxdop"></a>Modifica di MAXDOP 

  Se si determina che un'impostazione MAXDOP diversa da quella predefinita è ottimale per il carico di lavoro database SQL di Azure, è possibile usare `ALTER DATABASE SCOPED CONFIGURATION` l'istruzione T-SQL. Per esempi, vedere la [sezione Esempi con Transact-SQL](#examples) riportata di seguito. Per impostare MAXDOP su un valore non predefinito per ogni nuovo database creato, aggiungere questo passaggio al processo di distribuzione del database.

  Se MAXDOP non predefinito offre solo un piccolo subset di query nel carico di lavoro, è possibile eseguire l'override di MAXDOP a livello di query aggiungendo l'hint OPTION (MAXDOP). Per esempi, vedere la [sezione Esempi con Transact-SQL](#examples) riportata di seguito. 

  Testare accuratamente le modifiche di configurazione MAXDOP con test di carico che comportano carichi di query simultanei realistici. 

  MAXDOP per le repliche primarie e secondarie può essere configurato in modo indipendente se impostazioni MAXDOP diverse sono ottimali per i carichi di lavoro di lettura/scrittura e di sola lettura. Questo vale per le database SQL di Azure [con scalabilità in lettura,](read-scale-out.md) [la replica geografica](active-geo-replication-overview.md)e le repliche [secondarie Hyperscale.](service-tier-hyperscale.md) Per impostazione predefinita, tutte le repliche secondarie ereditano la configurazione MAXDOP della replica primaria.

## <a name="security"></a><a name="Security"></a> Sicurezza  
  
###  <a name="permissions"></a><a name="Permissions"></a> Autorizzazioni  
  L'istruzione deve essere eseguita come amministratore del server, come membro del ruolo del database o come utente a cui è stata `ALTER DATABASE SCOPED CONFIGURATION` `db_owner` concessa `ALTER ANY DATABASE SCOPED CONFIGURATION` l'autorizzazione.
 
## <a name="examples"></a>Esempio   

  In questi esempi viene utilizzato il database **di esempio AdventureWorksLT** più recente quando si sceglie l'opzione per `SAMPLE` un nuovo database singolo di database SQL di Azure.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Configurazione con ambito database MAXDOP   

  In questo esempio viene illustrato come utilizzare [l'istruzione ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per impostare `MAXDOP` la configurazione su `2` . L'impostazione viene attivata immediatamente per le nuove query. Il cmdlet di PowerShell [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) esegue le query T-SQL da impostare e restituisce la configurazione con ambito database MAXDOP. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Questo esempio è da usare con Azure SQL database con repliche [](active-geo-replication-overview.md)con scalabilità in lettura abilitate, [](read-scale-out.md)replica geografica e database SQL di Azure [secondarie con iperscalabilità.](service-tier-hyperscale.md) Ad esempio, la replica primaria è impostata su un valore MAXDOP predefinito diverso rispetto alla replica secondaria, anticipando che potrebbero esserci differenze tra un carico di lavoro di lettura/scrittura e un carico di lavoro di sola lettura.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  È possibile usare [l'editor](connect-query-portal.md)di query portale di Azure , [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) per eseguire query T-SQL sul database SQL di Azure.

1.  Aprire una nuova finestra Query.

2.  Connettersi al database in cui si vuole modificare MAXDOP. Non è possibile modificare le configurazioni con ambito database nel database master.
  
3.  Copiare e incollare l'esempio seguente nella finestra di query e selezionare **Esegui**. 

#### <a name="maxdop-database-scoped-configuration"></a>Configurazione con ambito database MAXDOP

  Questo esempio illustra come determinare la configurazione con ambito database MAXDOP del database corrente usando la sys.database_scoped_configurations [del](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) catalogo di sistema.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  In questo esempio viene illustrato come utilizzare [l'istruzione ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per impostare `MAXDOP` la configurazione su `8` . L'impostazione ha effetto immediatamente.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Questo esempio è da usare con Azure SQL database con [repliche](read-scale-out.md)con scalabilità in lettura abilitate, [](active-geo-replication-overview.md)replica geografica e repliche [secondarie Hyperscale.](service-tier-hyperscale.md) Ad esempio, la replica primaria è impostata su un VALORE MAXDOP diverso rispetto alla replica secondaria, anticipando che potrebbero esserci differenze tra i carichi di lavoro di lettura/scrittura e di sola lettura. Tutte le istruzioni vengono eseguite nella replica primaria. La `value_for_secondary` colonna di contiene le impostazioni per la replica `sys.database_scoped_configurations` secondaria.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP - hint per la query

  In questo esempio viene illustrato come eseguire una query utilizzando l'hint per la query per forzare `max degree of parallelism` a `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP - opzione di indice

  In questo esempio viene illustrato come ricompilare un indice utilizzando l'opzione index per forzare `max degree of parallelism` su `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Vedi anche  
* [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Configurare operazioni parallele sugli indici](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Hint di query &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Impostare le opzioni di indice](/sql/relational-databases/indexes/set-index-options)     
* [Comprendere e risolvere i database SQL di Azure che bloccano](understand-resolve-blocking.md)

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio e ottimizzazione delle prestazioni](/sql/relational-databases/performance/monitor-and-tune-for-performance)
