---
title: Query di diagnostica utili-iperscala (CITUS)-database di Azure per PostgreSQL
description: Query per ottenere informazioni sui dati distribuiti e altro ancora
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 4858f650aca1b704ac79482e0158fd83fc0264b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165242"
---
# <a name="useful-diagnostic-queries"></a>Query di diagnostica utili

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>Individuazione del nodo che contiene i dati per un tenant specifico

Nel caso di utilizzo multi-tenant, è possibile determinare quale nodo di lavoro contiene le righe per un tenant specifico.  Iperscale (CITUS) raggruppa le righe di tabelle distribuite in partizioni e inserisce ogni partizione in un nodo del ruolo di lavoro nel gruppo di server. 

Si supponga che i tenant dell'applicazione siano archiviati e che si desideri individuare il nodo del ruolo di lavoro che contiene i dati per l'ID del negozio = 4.  In altre parole, si vuole trovare la posizione per la partizione contenente le righe la cui colonna di distribuzione ha valore 4:

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

L'output contiene l'host e la porta del database Worker.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>Ricerca della colonna di distribuzione per una tabella

Ogni tabella distribuita in iperscala (CITUS) ha una "colonna di distribuzione". Per ulteriori informazioni, vedere la pagina relativa alla [modellazione dei dati distribuiti](concepts-hyperscale-choose-distribution-column.md). Può essere importante comprendere quale colonna è. Ad esempio, quando si uniscono o si filtrano le tabelle, è possibile che vengano visualizzati messaggi di errore con suggerimenti come, "aggiungere un filtro alla colonna di distribuzione".

Le `pg_dist_*` tabelle nel nodo coordinatore contengono metadati diversi sul database distribuito. In particolare `pg_dist_partition` , include informazioni sulla colonna di distribuzione per ogni tabella. È possibile utilizzare una comoda funzione di utilità per cercare il nome della colonna di distribuzione dai dettagli di basso livello nei metadati. Ecco un esempio e il relativo output:

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Output di esempio:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>Rilevamento di blocchi

Questa query verrà eseguita in tutti i nodi del ruolo di lavoro e identificherà i blocchi, il periodo di tempo in cui sono stati aperti e le query danneggiate:

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

Output di esempio:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>Esecuzione di query sulle dimensioni delle partizioni

Questa query fornirà le dimensioni di ogni partizione di una determinata tabella distribuita, denominata `my_distributed_table` :

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

Output di esempio:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>Esecuzione di query sulle dimensioni di tutte le tabelle distribuite

Questa query ottiene un elenco delle dimensioni per ogni tabella distribuita più le dimensioni degli indici.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

Output di esempio:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

Nota sono disponibili altre funzioni di iperscala (CITUS) per l'esecuzione di query sulle dimensioni della tabella distribuita, vedere [determinazione delle dimensioni della tabella](howto-hyperscale-table-size.md).

## <a name="identifying-unused-indices"></a>Identificazione degli indici inutilizzati

La query seguente identifica gli indici inutilizzati nei nodi di lavoro per una determinata tabella distribuita ( `my_distributed_table` )

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

Output di esempio:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>Monitoraggio del numero di connessioni client

Nella query seguente vengono conteggiate le connessioni aperte sul coordinatore e vengono raggruppate in base al tipo.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

Output di esempio:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="index-hit-rate"></a>Percentuale riscontri indice

Questa query fornirà la percentuale di riscontri indice in tutti i nodi. La velocità di hit index è utile per determinare la frequenza con cui vengono usati gli indici durante l'esecuzione di query:

``` postgresql
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT CASE sum(idx_blks_hit)
    WHEN 0 THEN 'NaN'::numeric
    ELSE to_char((sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit + idx_blks_read), '99.99')::numeric
    END AS ratio
  FROM pg_statio_user_indexes
$cmd$);
```

Output di esempio:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 0.88           │
│ 10.0.0.20 │ 0.89           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>Percentuale riscontri cache

La maggior parte delle applicazioni accede in genere a una piccola frazione dei dati totali contemporaneamente. PostgreSQL mantiene i dati ad accesso frequente in memoria per evitare letture lente dal disco. È possibile visualizzarne le statistiche nella visualizzazione [pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) .

Una misura importante è la percentuale di dati proveniente dalla cache di memoria rispetto al disco nel carico di lavoro:

``` postgresql
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS ratio
FROM
  pg_statio_user_tables;
```

Output di esempio:

```
 heap_read | heap_hit |         ratio
-----------+----------+------------------------
         1 |      132 | 0.99248120300751879699
```

Se si riscontra un rapporto significativamente inferiore al 99%, è probabile che si voglia prendere in considerazione l'aumento della cache disponibile per il database.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [tabelle di sistema](reference-hyperscale-metadata.md) utili per la diagnostica
