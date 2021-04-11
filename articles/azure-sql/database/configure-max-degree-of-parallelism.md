---
title: Configurare il grado massimo di parallelismo (MAXDOP)
titleSuffix: Azure SQL Database
description: Informazioni sul massimo grado di parallelismo (MAXDOP).
ms.date: 03/29/2021
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
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111816"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Configurare il grado massimo di parallelismo (MAXDOP) nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Questo articolo descrive il **grado massimo di parallelismo (MAXDOP)** nel database SQL di Azure e come può essere configurato. 

> [!NOTE]
> **Questo contenuto è incentrato sul database SQL di Azure.** Il database SQL di Azure è basato sulla versione stabile più recente del motore di database di Microsoft SQL Server, quindi la maggior parte del contenuto è simile anche se la risoluzione dei problemi e le opzioni di configurazione sono diverse. Per altre informazioni su MAXDOP in SQL Server, vedere [configurare l'opzione di configurazione del server max degree of parallelism](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Panoramica
  Nel database SQL di Azure l'impostazione predefinita di MAXDOP per ogni nuovo database singolo e database del pool elastico è 8. Ciò significa che il motore di database può eseguire query usando più thread. A differenza di SQL Server, dove il valore predefinito di MAXDOP a livello di server è 0 (illimitato), per impostazione predefinita i nuovi database nel database SQL di Azure sono impostati su MAXDOP 8. Questa impostazione predefinita impedisce l'utilizzo delle risorse non necessarie e garantisce un'esperienza coerente con i clienti. Non è in genere necessario configurare ulteriormente MAXDOP nei carichi di lavoro del database SQL di Azure, ma può offrire vantaggi come esercizio di ottimizzazione delle prestazioni avanzato.

> [!Note]
>   Nel 2020 settembre, in base agli anni di telemetria nel servizio di database SQL di Azure, [MAXDOP 8 è stato scelto](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) come impostazione predefinita per i nuovi database come valore ottimale per la più ampia gamma di carichi di lavoro dei clienti. Questa impostazione predefinita ha consentito di evitare problemi di prestazioni dovuti a un eccessivo parallelismo. Prima di questo, l'impostazione predefinita per i nuovi database era MAXDOP 0. L'opzione di configurazione con ambito database MAXDOP non è stata modificata per i database esistenti creati prima del 2020 settembre.

  In generale, se il motore di database sceglie di eseguire una query utilizzando il parallelismo, il tempo di esecuzione è più veloce. Tuttavia, il parallelismo eccessivo può utilizzare risorse del processore in eccesso senza migliorare le prestazioni di esecuzione delle query. Su larga scala, il parallelismo in eccesso può influire negativamente sulle prestazioni delle query per tutte le query in esecuzione sulla stessa istanza del motore di database, pertanto l'impostazione di un limite superiore per il parallelismo è stata un esercizio di ottimizzazione delle prestazioni comune nei carichi di lavoro SQL Server.

  Nella tabella seguente viene descritto il comportamento del motore di database durante l'esecuzione di query con valori MAXDOP diversi:

| MAXDOP | Comportamento | 
|--|--|
| = 1 | Il motore di database non esegue query con più thread simultanei. | 
| > 1 | Il motore di database imposta un limite superiore per il numero di thread paralleli. Il motore di database sceglie il numero di thread di lavoro aggiuntivi da usare. Il numero totale di thread di lavoro utilizzati per eseguire una query può essere superiore al valore MAXDOP specificato. |
| = 0 | Il motore di database può utilizzare un numero di thread paralleli con un limite superiore a seconda del numero totale di processori logici. Il motore di database sceglie il numero di thread paralleli da usare.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Considerazioni  

-   Nel database SQL di Azure è possibile modificare il valore predefinito di MAXDOP:
    -   A livello di query, usando l'[hint per la query](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP**.     
    -   A livello di database, con la [configurazione con ambito database](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) **MAXDOP**.

-   Per il database SQL di Azure sono applicabili considerazioni e [raccomandazioni](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) di SQL Server MAXDOP di lunga durata. 

-   MAXDOP viene applicato per ogni [attività](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql). Non viene applicato per [richiesta](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) o per query. Ciò significa che durante l'esecuzione di una query parallela, una singola richiesta può generare più attività con un limite superiore determinato dal MAXDOP. Per altre informazioni, vedere la sezione *Pianificazione delle attività in parallelo* in [Guida sull'architettura dei thread e delle attività](/sql/relational-databases/thread-and-task-architecture-guide). 
  
-   Le operazioni tramite cui viene creato o ricompilato un indice o eliminato un indice cluster possono richiedere un elevato utilizzo di risorse. È possibile eseguire l'override del valore di max degree of parallelism del database per le operazioni sugli indici specificando l'opzione di indice MAXDOP nell' `CREATE INDEX` `ALTER INDEX` istruzione o. Il valore MAXDOP viene applicato all'istruzione al momento dell'esecuzione e non viene archiviato nei metadati dell'indice. Per altre informazioni, vedere [Configurazione di operazioni parallele sugli indici](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Oltre alle query e alle operazioni sugli indici, l'opzione di configurazione con ambito database per MAXDOP controlla anche il parallelismo di DBCC CHECKTABLE, DBCC CHECKDB e DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Security"></a> Indicazioni  

  La modifica di MAXDOP per il database può avere un impatto significativo sulle prestazioni delle query e sull'utilizzo delle risorse, sia positivi che negativi. Tuttavia, non esiste un singolo valore MAXDOP ottimale per tutti i carichi di lavoro. Le raccomandazioni per l'impostazione di MAXDOP sono sfumate e dipendono da molti fattori. 

  Alcuni carichi di lavoro simultanei di picco possono funzionare meglio con un MAXDOP diverso rispetto ad altri. Un MAXDOP correttamente configurato dovrebbe ridurre il rischio di eventi imprevisti relativi a prestazioni e disponibilità e, in alcuni casi, ridurre i costi grazie alla possibilità di evitare un utilizzo superfluo delle risorse e quindi di ridurlo a un obiettivo di servizio inferiore.

### <a name="excessive-parallelism"></a>Parallelismo eccessivo

  Un MAXDOP più elevato spesso riduce la durata per le query con utilizzo intensivo della CPU. Tuttavia, il parallelismo eccessivo può peggiorare le altre prestazioni di carico di lavoro simultanee eliminando altre query sulle risorse dei thread di lavoro e della CPU. In casi estremi, il parallelismo eccessivo può utilizzare tutte le risorse del pool elastico o del database, causando timeout delle query, errori e interruzioni delle applicazioni. 

  Si consiglia ai clienti di evitare MAXDOP 0 anche se non sembrano causare problemi al momento. Un parallelismo eccessivo diventa più problematico quando i thread di lavoro e CPU ricevono più richieste simultanee di quelle che possono essere supportate dall'obiettivo di servizio. Evitare MAXDOP 0 per ridurre il rischio di potenziali problemi futuri a causa di un eccessivo parallelismo se viene scalato un database o se le generazioni hardware future nel database SQL di Azure forniscono più core per lo stesso obiettivo di servizio di database.

### <a name="modifying-maxdop"></a>Modifica di MAXDOP 

  Se si stabilisce che un'impostazione di MAXDOP diversa è ottimale per il carico di lavoro del database SQL di Azure, è possibile usare l' `ALTER DATABASE SCOPED CONFIGURATION` istruzione T-SQL. Per esempi, vedere la sezione [esempi di uso di Transact-SQL](#examples) di seguito. Aggiungere questo passaggio al processo di distribuzione per modificare MAXDOP dopo la creazione del database.

  Se MAXDOP non predefinito beneficia solo di un subset di query nel carico di lavoro, è possibile eseguire l'override di MAXDOP a livello di query aggiungendo l'hint OPTION (MAXDOP). Per esempi, vedere la sezione [esempi di uso di Transact-SQL](#examples) di seguito. 

  Testare accuratamente le modifiche alla configurazione di MAXDOP con test di carico che coinvolgono carichi di query simultanei realistici. 

  Il MAXDOP per le repliche primarie e secondarie può essere configurato in modo indipendente per sfruttare le diverse impostazioni MAXDOP ottimali per i carichi di lavoro di lettura/scrittura e di sola lettura. Questo vale per la [scalabilità in lettura](read-scale-out.md)del database SQL di Azure, la [replica geografica](active-geo-replication-overview.md)e le [repliche secondarie con iperscalabilità di database SQL di Azure](service-tier-hyperscale.md). Per impostazione predefinita, tutte le repliche secondarie ereditano la configurazione MAXDOP della replica primaria.

## <a name="security"></a><a name="Security"></a> Sicurezza  
  
###  <a name="permissions"></a><a name="Permissions"></a> Autorizzazioni  
  L' `ALTER DATABASE SCOPED CONFIGURATION` istruzione deve essere eseguita come amministratore del server, come membro del ruolo del database `db_owner` o un utente a cui è stata concessa l' `ALTER ANY DATABASE SCOPED CONFIGURATION` autorizzazione.
 
## <a name="examples"></a>Esempio   

  In questi esempi viene usato il database di esempio **AdventureWorksLT** più recente quando `SAMPLE` si sceglie l'opzione per un nuovo database singolo del database SQL di Azure.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Configurazione con ambito database MAXDOP   

  Questo esempio illustra come usare l'istruzione [ALTER database scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per configurare l' `max degree of parallelism` opzione su `2` . L'impostazione ha effetto immediatamente. Il cmdlet di PowerShell [Invoke-Sqlcmd](/powershell/module/sqlserver/invoke-sqlcmd) esegue le query T-SQL da impostare e restituisce la configurazione con ambito database MAXDOP. 

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

Questo esempio è da usare con i database SQL di Azure con le repliche con [scalabilità orizzontale in lettura abilitate](read-scale-out.md), la [replica geografica](active-geo-replication-overview.md)e le [repliche secondarie con IPERscalabilità di database SQL di Azure](service-tier-hyperscale.md). Ad esempio, la replica primaria è impostata su un MAXDOP predefinito diverso come replica secondaria, con la previsione che potrebbero esserci differenze tra un carico di lavoro di lettura e scrittura e un carico di lavoro di sola lettura.

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
  
  È possibile usare l' [editor di query portale di Azure](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) per eseguire query T-SQL sul database SQL di Azure.

1.  Connettersi al database SQL di Azure. Non è possibile modificare le configurazioni con ambito database nel database master.
  
2.  Dalla barra standard selezionare **nuova query**.   
  
3.  Copiare e incollare l'esempio seguente nella finestra query e selezionare **Execute (Esegui**). 


#### <a name="maxdop-database-scoped-configuration"></a>Configurazione con ambito database MAXDOP

  Questo esempio illustra come determinare la configurazione con ambito database MAXDOP del database corrente usando la vista del catalogo di sistema [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) .

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Questo esempio illustra come usare l'istruzione [ALTER database scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per configurare l' `max degree of parallelism` opzione su `8` . L'impostazione ha effetto immediatamente.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Questo esempio è da usare con i database SQL di Azure con le repliche con [scalabilità orizzontale in lettura abilitate](read-scale-out.md), la [replica geografica](active-geo-replication-overview.md)e le [repliche secondarie con IPERscalabilità di database SQL di Azure](service-tier-hyperscale.md). Ad esempio, la replica primaria è impostata su un MAXDOP predefinito diverso come replica secondaria, con la previsione che potrebbero esserci differenze tra un carico di lavoro di lettura e scrittura e un carico di lavoro di sola lettura. La `value_for_secondary` colonna di `sys.database_scoped_configurations` contiene le impostazioni per la replica secondaria.

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

  Questo esempio illustra come ricompilare un indice usando l'opzione index per forzare l'oggetto `max degree of parallelism` su `12` .  

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
* [Comprendere e risolvere i problemi di blocco del database SQL di Azure](understand-resolve-blocking.md)

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio e ottimizzazione delle prestazioni](/sql/relational-databases/performance/monitor-and-tune-for-performance)
