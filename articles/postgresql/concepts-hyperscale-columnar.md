---
title: Archivio tabelle a colonne anteprima-iperscala (CITUS)-database di Azure per PostgreSQL
description: Compressione dei dati con l'archiviazione a colonne (anteprima)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023966"
---
# <a name="columnar-table-storage-preview"></a>Archiviazione tabelle a colonne (anteprima)

> [!IMPORTANT]
> L'archiviazione tabelle a colonne in iperscala (CITUS) è attualmente in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

Database di Azure per PostgreSQL-iperscalabilità (CITUS) supporta l'archiviazione di tabelle a colonne solo accodate per carichi di lavoro analitici e di data warehousing. Quando le colonne (anziché le righe) vengono archiviate in modo contiguo sul disco, i dati diventano più compressibili e le query possono richiedere più rapidamente un subset di colonne.

Per usare l'archiviazione a colonne, specificare `USING columnar` quando si crea una tabella:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Iperscale (CITUS) converte le righe nell'archiviazione a colonne in "strip" durante l'inserimento. Ogni stripe include i dati di una transazione, ovvero 150000 righe, a seconda del valore minore.  (Le dimensioni di striping e gli altri parametri di una tabella a colonne possono essere modificate con la funzione [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) ).

L'istruzione seguente, ad esempio, inserisce tutte le cinque righe nello stesso striping, poiché tutti i valori vengono inseriti in una singola transazione:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Quando possibile, è preferibile creare Strip di grandi dimensioni, perché l'iperscala (CITUS) comprime i dati a colonne separatamente per striscia. È possibile visualizzare i fatti relativi alla tabella a colonne, ad esempio la velocità di compressione, il numero di strip e le righe medie per striping usando `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

L'output mostra che l'iperscala (CITUS) ha utilizzato l'algoritmo di compressione zstd per ottenere la compressione dei dati di 1.31 x. Il tasso di compressione confronta a) la dimensione dei dati inseriti durante la fase di gestione temporanea in memoria rispetto a b) la dimensione dei dati compressi nello striping finale.

A causa del modo in cui viene misurato, la velocità di compressione può corrispondere o meno alla differenza di dimensione tra l'archiviazione di righe e colonne per una tabella. L'unico modo per trovare effettivamente la differenza consiste nel creare una tabella di righe e colonne che contiene gli stessi dati e confrontare:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

Per la tabella di piccole dimensioni, l'archiviazione a colonne utilizza effettivamente più spazio, ma con la crescita dei dati, la compressione vincerà.

## <a name="example"></a>Esempio

L'archiviazione a colonne funziona bene con il partizionamento delle tabelle. Per un esempio, vedere la documentazione della community del motore CITUS, [archiviazione con archiviazione a colonne](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Trucchi

* Compressioni di archiviazione a colonne per striping. Le strisce vengono create per ogni transazione, pertanto l'inserimento di una riga per ogni transazione comporterà la creazione di singole righe nelle rispettive strisce. La compressione e le prestazioni di striping a riga singola saranno inferiori rispetto a una tabella di riga. Inserire sempre in blocco in una tabella a colonne.
* In caso di problemi e columnarize di una serie di striping minuscole, l'utente è bloccato.
  L'unica soluzione consiste nel creare una nuova tabella a colonne e copiare i dati dall'originale in un'unica transazione:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* I dati fondamentalmente non compressibili possono costituire un problema, anche se l'archiviazione a colonne è ancora utile quando si selezionano colonne specifiche. Non è necessario caricare le altre colonne in memoria.
* In una tabella partizionata con una combinazione di partizioni di righe e colonne, gli aggiornamenti devono essere mirati con attenzione. Filtrarli in modo da raggiungere solo le partizioni di riga.
   * Se l'operazione è destinata a una partizione di riga specifica (ad esempio, `UPDATE p2 SET i = i + 1` ), avrà esito positivo. se la destinazione è una partizione a colonne specificata (ad esempio, `UPDATE p1 SET i = i + 1` ), l'operazione avrà esito negativo.
   * Se l'operazione è destinata alla tabella partizionata e dispone di una clausola WHERE che esclude tutte le partizioni a colonne (ad esempio `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` ), avrà esito positivo.
   * Se l'operazione è destinata alla tabella partizionata, ma non filtra le colonne chiave di partizione, avrà esito negativo. Anche se sono presenti clausole WHERE che corrispondono alle righe solo nelle partizioni a colonne, non è sufficiente, è necessario filtrare anche la chiave di partizione.

## <a name="limitations"></a>Limitazioni

Questa funzionalità presenta ancora alcune limitazioni significative. Vedere [limiti e limitazioni di iperscalabilità (CITUS)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Passaggi successivi

* Vedere un esempio di archiviazione a colonne in un'esercitazione su CITUS [timeseries](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (collegamento esterno).
