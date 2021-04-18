---
title: Ottimizzazione delle prestazioni con indice columnstore cluster ordinato
description: Raccomandazioni e considerazioni da tenere presenti quando si usa un indice columnstore cluster ordinato per migliorare le prestazioni delle query in pool SQL dedicati.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ab94a83a64ca9770f0c216ddf42145b262629c6d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598993"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ottimizzazione delle prestazioni con indice columnstore cluster ordinato  

Quando gli utenti eseguiti una query su una tabella columnstore in un pool SQL dedicato, Query Optimizer controlla i valori minimo e massimo archiviati in ogni segmento.  I segmenti esterni ai limiti del predicato della query non vengono letti da disco a memoria.  Una query può ottenere prestazioni più veloci se il numero di segmenti da leggere e le relative dimensioni totali sono ridotte.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Indice columnstore cluster ordinato e non ordinato

Per impostazione predefinita, per ogni tabella creata senza un'opzione di indice, un componente interno (generatore di indici) crea un indice columnstore cluster non ordinato su di essa.  I dati in ogni colonna vengono compressi in un segmento di rowgroup CCI separato.  Sono presenti metadati nell'intervallo di valori di ogni segmento, quindi i segmenti che non sono al di fuori dei limiti del predicato della query non vengono letti dal disco durante l'esecuzione della query.  CCI offre il massimo livello di compressione dei dati e riduce le dimensioni dei segmenti da leggere in modo che le query possano essere eseguite più velocemente. Tuttavia, poiché il generatore di indici non ordina i dati prima di comprimerli in segmenti, potrebbero verificarsi segmenti con intervalli di valori sovrapposti, causando la lettura di più segmenti dal disco da parte delle query e il completamento di più tempo.  

Quando si crea un indice cluster cluster ordinato, il motore del pool SQL dedicato ordina i dati esistenti in memoria in base alle chiavi di ordinamento prima che il generatore di indici li comprime in segmenti di indice.  Con i dati ordinati, la sovrapposizione dei segmenti si riduce consentendo alle query di avere un'eliminazione di segmenti più efficiente e quindi prestazioni più veloci perché il numero di segmenti da leggere dal disco è inferiore.  Se tutti i dati possono essere ordinati in memoria contemporaneamente, è possibile evitare la sovrapposizione dei segmenti.  A causa di tabelle di grandi dimensioni nei data warehouse, questo scenario non si verifica spesso.  

Per controllare gli intervalli di segmenti per una colonna, eseguire il comando seguente con il nome della tabella e il nome della colonna:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id;


```

> [!NOTE] 
> In una tabella CCI ordinata, i nuovi dati risultanti dallo stesso batch di operazioni di caricamento dati o DML vengono ordinati all'interno di tale batch, non esiste alcun ordinamento globale per tutti i dati nella tabella.  Gli utenti possono RICOMPILARE l'CCI ordinato per ordinare tutti i dati nella tabella.  Nel pool SQL dedicato l'indice columnstore REBUILD è un'operazione offline.  Per una tabella partizionata, l'operazione REBUILD viene eseguita una partizione alla volta.  I dati nella partizione da ricompilare sono "offline" e non sono disponibili fino al completamento della ricompilazione per tale partizione. 

## <a name="query-performance"></a>Prestazioni delle query

Il miglioramento delle prestazioni di una query da un CCI ordinato dipende dai modelli di query, dalle dimensioni dei dati, dal livello di ordinamento dei dati, dalla struttura fisica dei segmenti e dalla classe DWU e risorsa scelta per l'esecuzione della query.  Gli utenti devono esaminare tutti questi fattori prima di scegliere le colonne di ordinamento durante la progettazione di una tabella CCI ordinata.

Le query con tutti questi modelli vengono in genere eseguite più velocemente con CCI ordinato.  
1. Le query hanno predicati di uguaglianza, disuguaglianza o intervallo
1. Le colonne del predicato e le colonne CCI ordinate sono uguali.  
1. Le colonne del predicato vengono usate nello stesso ordine dell'ordinale di colonna delle colonne CCI ordinate.  
 
In questo esempio la tabella T1 include un indice columnstore cluster ordinato nella sequenza di Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A);

```

Le prestazioni della query 1 possono trarre vantaggio dall'CCI ordinato rispetto alle altre tre query. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Prestazioni di caricamento dei dati

Le prestazioni del caricamento dei dati in una tabella CCI ordinata sono simili a quelle di una tabella partizionata.  Il caricamento dei dati in una tabella CCI ordinata può richiedere più tempo rispetto a una tabella CCI non ordinata a causa dell'operazione di ordinamento dei dati, tuttavia le query possono essere eseguite più velocemente in seguito con l'CCI ordinato.  

Di seguito è riportato un esempio di confronto delle prestazioni del caricamento dei dati in tabelle con schemi diversi.

![Grafico a barre che mostra il confronto delle prestazioni di caricamento dei dati in tabelle con schemi diversi.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Di seguito è riportato un esempio di confronto delle prestazioni delle query tra CCI e CCI ordinato.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Ridurre la sovrapposizione dei segmenti

Il numero di segmenti sovrapposti dipende dalle dimensioni dei dati da ordinare, dalla memoria disponibile e dall'impostazione max degree of parallelism (MAXDOP) durante la creazione CCI ordinata. Di seguito sono riportate le opzioni per ridurre la sovrapposizione dei segmenti durante la creazione di CCI ordinati.

- Usare la classe di risorse xlargerc in una DWU superiore per consentire una maggiore quantità di memoria per l'ordinamento dei dati prima che il generatore di indici comprime i dati in segmenti.  Una volta in un segmento di indice, la posizione fisica dei dati non può essere modificata.  Non esiste alcun ordinamento dei dati all'interno di un segmento o tra segmenti.  

- Creare CCI ordinato con MAXDOP = 1.  Ogni thread usato per la creazione CCI ordinata funziona su un subset di dati e lo ordina in locale.  Non esiste alcun ordinamento globale tra i dati ordinati in base a thread diversi.  L'uso di thread paralleli può ridurre il tempo necessario per creare un CCI ordinato, ma genera più segmenti sovrapposti rispetto all'uso di un singolo thread.  Attualmente, l'opzione MAXDOP è supportata solo per la creazione di una tabella CCI ordinata CREATE TABLE comando AS SELECT.  La creazione di un indice cluster ordinato tramite CREATE INDEX o CREATE TABLE non supporta l'opzione MAXDOP. Ad esempio,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Pre-ordinare i dati in base alle chiavi di ordinamento prima di caricarli nelle tabelle.

Di seguito è riportato un esempio di distribuzione ordinata di tabelle CCI con zero segmenti sovrapposti dopo le raccomandazioni precedenti. La tabella CCI ordinata viene creata in un database DWU1000c tramite CTAS da una tabella heap da 20 GB usando MAXDOP 1 e xlargerc.  L'CCI viene ordinato in base a una colonna BIGINT senza duplicati.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Creare CCI ordinati in tabelle di grandi dimensioni

La creazione di un elenco CCI ordinato è un'operazione offline.  Per le tabelle senza partizioni, i dati non saranno accessibili agli utenti fino al completamento del processo di creazione CCI ordinato.   Per le tabelle partizionate, poiché il motore crea la partizione CCI ordinata per partizione, gli utenti possono comunque accedere ai dati nelle partizioni in cui la creazione CCI ordinata non è in corso.   È possibile usare questa opzione per ridurre al minimo il tempo di inattività durante la creazione CCI ordinata in tabelle di grandi dimensioni: 

1.    Creare partizioni nella tabella di grandi dimensioni di destinazione (denominata Table_A).
2.    Creare una tabella CCI ordinata vuota (denominata Table_B) con lo stesso schema di tabella e partizione della tabella A.
3.    Passare una partizione dalla tabella A alla tabella B.
4.    Eseguire ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> nella tabella B per ricompilare la partizione commutata.  
5.    Ripetere i passaggi 3 e 4 per ogni partizione in Table_A.
6.    Dopo che tutte le partizioni sono state Table_A a Table_B e sono state ricompilate, eliminare Table_A e rinominare Table_B in Table_A. 

>[!TIP]
> Per una tabella del pool SQL dedicata con un CCI ordinato, ALTER INDEX REBUILD riordinamentorà i dati usando tempdb. Monitorare tempdb durante le operazioni di ricompilazione. Se è necessario più spazio per tempdb, aumentare le dimensioni del pool. Tornare alle dimensioni precedenti al termine della ricompilazione dell'indice.
>
> Per una tabella del pool SQL dedicata con un CCI ordinato, ALTER INDEX REORGANIZE non riordina i dati. Per utilizzare i dati, usare ALTER INDEX REBUILD.
>
> Per altre informazioni sulla manutenzione CCI ordinata, vedere [Ottimizzazione degli indici columnstore cluster](sql-data-warehouse-tables-index.md#optimizing-clustered-columnstore-indexes).

## <a name="examples"></a>Esempio

**Un. Per verificare la presenza di colonne ordinate e ordinale dell'ordine:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0;
```

**B. Per modificare l'ordinale di colonna, aggiungere o rimuovere colonne dall'elenco degli ordini o passare da CCI a CCI ordinato:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON dbo.InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON);
```

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
