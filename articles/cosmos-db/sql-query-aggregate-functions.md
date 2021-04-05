---
title: Funzioni di aggregazione in Azure Cosmos DB
description: Informazioni sulla sintassi della funzione di aggregazione SQL, sui tipi di funzioni di aggregazione supportate da Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555413"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funzioni di aggregazione in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Le funzioni di aggregazione eseguono un calcolo su un set di valori nella `SELECT` clausola e restituiscono un singolo valore. Ad esempio, la query seguente restituisce il numero di elementi all'interno di un contenitore:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Tipi di funzioni di aggregazione

L'API SQL supporta le funzioni di aggregazione seguenti. `SUM` e `AVG` operano su valori numerici, e `COUNT` , e `MIN` `MAX` funzionano su numeri, stringhe, valori booleani e valori null.

| Funzione | Descrizione |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | Restituisce la media dei valori nell'espressione. |
| [COUNT](sql-query-aggregate-count.md) | Restituisce il numero di elementi nell'espressione. |
| [MAX](sql-query-aggregate-max.md) | Restituisce il valore massimo dell'espressione. |
| [MIN](sql-query-aggregate-min.md) | Restituisce il valore minimo nell'espressione. |
| [SUM](sql-query-aggregate-sum.md) | Restituisce la somma dei valori nell'espressione. |


È inoltre possibile restituire solo il valore scalare dell'aggregazione utilizzando la parola chiave VALUE. Ad esempio, la query seguente restituisce il numero di valori come un singolo numero:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

I risultati sono:

```json
    [ 2 ]
```

È inoltre possibile combinare le aggregazioni con i filtri. Ad esempio, la query seguente restituisce il numero di elementi con lo stato dell'indirizzo di `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

I risultati sono:

```json
    [ 1 ]
```

## <a name="remarks"></a>Commenti

Queste funzioni di sistema di aggregazione trarranno vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy). Se si prevede di eseguire una `AVG` ,,, `COUNT` `MAX` `MIN` o `SUM` su una proprietà, è necessario [includere il percorso pertinente nei criteri di indicizzazione](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni di sistema](sql-query-system-functions.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)