---
title: 'Esercitazione: Partizionare i dati nei nodi di lavoro - Hyperscale (Citus) - Database di Azure per PostgreSQL'
description: Questa esercitazione illustra come creare tabelle distribuite e visualizzare la relativa distribuzione dei dati con Database di Azure per PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: 7d93002af866aa653972182a13ea37d37e912ce8
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630310"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Esercitazione: Partizionare i dati nei nodi di lavoro in Database di Azure per PostgreSQL - Hyperscale (Citus)

In questa esercitazione si usa Database di Azure per PostgreSQL - Hyperscale (Citus) per imparare a:

> [!div class="checklist"]
> * Creare partizioni con distribuzione hash
> * Vedere dove vengono inserite le partizioni di tabelle
> * Identificare la distribuzione asimmetrica
> * Creare vincoli nelle tabelle distribuite
> * Eseguire query sui dati distribuiti

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione richiede un gruppo di server Hyperscale (Citus) in esecuzione con due nodi di lavoro. Se non è disponibile un gruppo di server in esecuzione, seguire questa [esercitazione](tutorial-hyperscale-server-group.md) prima di procedere.

## <a name="hash-distributed-data"></a>Dati con distribuzione hash

La distribuzione di righe di tabella in più server PostgreSQL è una tecnica fondamentale per le query scalabili in Hyperscale (Citus). Più nodi possono contenere più dati rispetto a un database tradizionale e in molti casi possono usare le CPU di lavoro in parallelo per eseguire query.

Nella sezione dei prerequisiti è stato creato un gruppo di server Hyperscale (Citus) con due nodi di lavoro.

![Nodo coordinatore e due nodi di lavoro](tutorial-hyperscale-shard/nodes.png)

Le tabelle di metadati del nodo coordinatore tengono traccia dei dati dei nodi di lavoro e di quelli distribuiti. È possibile controllare i nodi di lavoro attivi nella tabella [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> I nomi dei nodi in Hyperscale (Citus) sono indirizzi IP interni di una rete virtuale e gli indirizzi effettivi possono variare.

### <a name="rows-shards-and-placements"></a>Righe, partizioni e posizionamenti

Per usare le risorse di CPU e archiviazione dei nodi di lavoro, è necessario distribuire i dati delle tabelle in tutto il gruppo di server.  La distribuzione di una tabella assegna ogni riga a un gruppo logico denominato *partizione*. Creare una tabella e distribuirla:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

Hyperscale (Citus) assegna ogni riga a una partizione in base al valore della *colonna di distribuzione*, che in questo caso è stata impostata su `email`. Ogni riga verrà inserita in una sola partizione e ogni partizione può contenere più righe.

![Tabella users con le righe che puntano alle partizioni](tutorial-hyperscale-shard/table.png)

Per impostazione predefinita, `create_distributed_table()` crea 32 partizioni, come si può verificare conteggiando il numero nella tabella di metadati [pg_dist_shard](reference-hyperscale-metadata.md#shard-table):

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

Hyperscale (Citus) usa la tabella `pg_dist_shard` per assegnare righe alle partizioni, in base a un hash del valore nella colonna di distribuzione. I dettagli degli hash sono irrilevanti per questa esercitazione. Quello che conta è che è possibile eseguire una query per verificare il mapping tra i valori e gli ID partizione:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

Il mapping delle righe alle partizioni è puramente logico. Le partizioni devono essere assegnate a specifici nodi di lavoro per l'archiviazione, in base a quello che in Hyperscale (Citus) viene definito *posizionamento delle partizioni*.

![Partizioni assegnate ai nodi di lavoro](tutorial-hyperscale-shard/shard-placement.png)

È possibile esaminare i posizionamenti delle partizioni in [pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table).
Unendo questa tabella alle altre tabelle di metadati, è possibile vedere dove risiede ogni partizione.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Asimmetria dei dati

Un gruppo di server viene eseguito in modo più efficiente se i dati vengono posizionati uniformemente nei nodi di lavoro e se i dati correlati vengono inseriti insieme negli stessi nodi di lavoro. In questa sezione verrà illustrata la prima parte, ovvero l'uniformità del posizionamento.

Per verificarlo, creare dati di esempio per la tabella `users`:

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

Per vedere le dimensioni delle partizioni, è possibile eseguire [funzioni di dimensionamento delle tabelle](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE).

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Come si può notare, le partizioni hanno dimensioni uguali. Dato che i posizionamenti sono distribuiti uniformemente tra i nodi di lavoro, si può dedurre che i nodi di lavoro contengono approssimativamente lo stesso numero di righe.

Le righe nell'esempio `users` sono distribuite uniformemente a causa delle proprietà della colonna di distribuzione `email`.

1. Il numero di indirizzi di posta elettronica è maggiore o uguale al numero di partizioni.
2. Il numero di righe per ogni indirizzo di posta elettronica è simile (in questo caso, esattamente una riga per ogni indirizzo, perché la colonna email è stata dichiarata come chiave).

Qualsiasi scelta di tabella e colonna di distribuzione in cui una delle proprietà ha esito negativo comporterà dimensioni dei dati non uniformi nei nodi di lavoro, ovvero una *asimmetria dei dati*.

### <a name="add-constraints-to-distributed-data"></a>Aggiungere vincoli ai dati distribuiti

L'uso di Hyperscale (Citus) consente di continuare a sfruttare la sicurezza di un database relazionale, inclusi [vincoli di database](https://www.postgresql.org/docs/current/ddl-constraints.html).
Tuttavia, esiste una limitazione. A causa della natura dei sistemi distribuiti, Hyperscale (Citus) non farà riferimento ai vincoli di univocità o all'integrità referenziale tra i nodi di lavoro.

Si consideri l'esempio di tabella `users` con una tabella correlata.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

Per una maggiore efficienza, distribuire `books` allo stesso modo di `users`, ovvero in base all'indirizzo di posta elettronica del proprietario. La distribuzione in base a valori di colonna simili viene chiamata [coubicazione](concepts-hyperscale-colocation.md).

Non si sono verificati problemi con la distribuzione di libri con una chiave esterna agli utenti, perché la chiave si trovava in una colonna di distribuzione. Tuttavia, si verificherebbero problemi impostando `isbn` come chiave:

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

In una tabella distribuita è consigliabile rendere le colonne univoche rispetto alla colonna di distribuzione:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

Il vincolo precedente rende semplicemente il valore di isbn univoco per ogni utente. Un'altra opzione consiste nel creare una [tabella di riferimento](howto-hyperscale-modify-distributed-tables.md#reference-tables) per i libri anziché una tabella distribuita e creare una tabella distribuita separata che associa i libri agli utenti.

## <a name="query-distributed-tables"></a>Eseguire query sulle tabelle distribuite

Nelle sezioni precedenti è stato illustrato come posizionare le righe di tabelle distribuite in partizioni nei nodi di lavoro. Il più delle volte non è necessario sapere come o dove vengono archiviati i dati in un gruppo di server. Hyperscale (Citus) include un executor di query distribuite che divide automaticamente le normali query SQL. Le esegue in parallelo nei nodi di lavoro vicino ai dati.

È ad esempio possibile eseguire una query per individuare l'età media degli utenti, trattando la tabella distribuita `users` come una normale tabella nel nodo coordinatore.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![Esecuzione di query sulle partizioni tramite nodo coordinatore](tutorial-hyperscale-shard/query-fragments.png)

Dietro le quinte, l'executor di Hyperscale (Citus) crea una query distinta per ogni partizione, le esegue tutte nei nodi di lavoro e combina il risultato. È possibile verificarlo usando il comando EXPLAIN di PostgreSQL:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

L'output mostra un esempio di piano di esecuzione per un *frammento di query* in esecuzione nella partizione 102040 (tabella `users_102040` nel nodo di lavoro 10.0.0.21). Gli altri frammenti non vengono mostrati perché sono simili. Si noterà che il nodo di lavoro analizza le tabelle di partizioni e applica l'aggregazione. Il nodo coordinatore combina le aggregazioni per il risultato finale.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una tabella distribuita e sono state descritti i concetti di partizioni e posizionamenti. Sono stati evidenziati i problemi associati all'uso di vincoli di univocità e di chiave esterna e infine è stato illustrato il funzionamento generale delle query distribuite.

* Leggere altre informazioni sui [tipi di tabella](concepts-hyperscale-nodes.md) di Hyperscale (Citus)
* Vedere altri suggerimenti sulla [scelta di una colonna di distribuzione](concepts-hyperscale-choose-distribution-column.md)
* Vedere i vantaggi della [coubicazione di tabelle](concepts-hyperscale-colocation.md)
