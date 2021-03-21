---
title: Determinare le dimensioni della tabella-iperscala (CITUS)-database di Azure per PostgreSQL
description: Come individuare le dimensioni effettive delle tabelle distribuite in un gruppo di server CITUS (iperscale)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937610"
---
# <a name="determine-table-and-relation-size"></a>Determinare le dimensioni della tabella e della relazione

Il modo consueto per trovare le dimensioni delle tabelle in PostgreSQL, `pg_total_relation_size` , in modo drastico, è la segnalazione delle dimensioni delle tabelle distribuite su iperscala (CITUS).
Tutte le funzioni di questa funzione su un gruppo di server con iperscalabilità (CITUS) consentono di visualizzare le dimensioni delle tabelle nel nodo coordinatore.  In realtà, i dati nelle tabelle distribuite si trovano sui nodi di lavoro (in partizioni), non sul coordinatore. Una vera misura delle dimensioni della tabella distribuita viene ottenuta come somma delle dimensioni della partizione. Iperscale (CITUS) fornisce funzioni helper per eseguire query su queste informazioni.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Funzione</th>
<th>Restituisce</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Dimensione dei dati effettivi nella tabella (il "<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">fork principale</a>").</li>
<li>Una relazione può essere il nome di una tabella o di un indice.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size più:</p>
<blockquote>
<ul>
<li>dimensioni della <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">mappa dello spazio disponibile</a></li>
<li>dimensioni della <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">mappa di visibilità</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size più:</p>
<blockquote>
<ul>
<li>dimensioni degli indici</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Queste funzioni sono analoghe a tre delle funzioni standard di [dimensioni degli oggetti](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)PostgreSQL, tranne nel caso in cui non possano connettersi a un nodo, si sono rivelate errori.

## <a name="example"></a>Esempio

Di seguito viene illustrato come elencare le dimensioni di tutte le tabelle distribuite:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Output:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ridimensionare un gruppo di server](howto-hyperscale-scale-grow.md) per mantenere più dati.
* Distinguere i [tipi di tabella](concepts-hyperscale-nodes.md) in un gruppo di server con iperscalabilità (CITUS).
