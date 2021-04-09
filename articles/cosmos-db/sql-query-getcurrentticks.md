---
title: GetCurrentTicks in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524282"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Restituisce il numero di cicli di 100-nanosecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore numerico con segno, il numero corrente di cicli di 100-nanosecondi trascorsi dal periodo UNIX. In altre parole, GetCurrentTicks restituisce il numero di cicli di 100 nanosecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="remarks"></a>Commenti

GetCurrentTicks () è una funzione non deterministica. Il risultato restituito è UTC (Coordinated Universal Time).

> [!NOTE]
> Questa funzione di sistema non utilizzerà l'indice. Se è necessario confrontare i valori con l'ora corrente, ottenere l'ora corrente prima dell'esecuzione della query e usare tale valore stringa costante nella `WHERE` clausola.

## <a name="examples"></a>Esempio

Di seguito è riportato un esempio che restituisce l'ora corrente, misurata in cicli:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
