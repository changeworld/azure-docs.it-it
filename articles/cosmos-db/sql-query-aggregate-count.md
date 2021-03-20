---
title: CONTEGGIO Azure Cosmos DB linguaggio di query
description: Informazioni sulla funzione di sistema conteggio (conteggio) SQL in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96552379"
---
# <a name="count-azure-cosmos-db"></a>CONTEGGIO (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questa funzione di sistema restituisce il conteggio dei valori nell'espressione.
  
## <a name="syntax"></a>Sintassi
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*scalar_expr*  
   Qualsiasi espressione scalare
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempio
  
Nell'esempio seguente viene restituito il numero totale di elementi in un contenitore:
  
```sql
SELECT COUNT(1)
FROM c
``` 
Il conteggio può assumere qualsiasi espressione scalare come input. La query seguente produrrà risultati equivalenti:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Commenti

Questa funzione di sistema trarrà vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy) per tutte le proprietà nel filtro della query.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Funzioni di aggregazione in Azure Cosmos DB](sql-query-aggregate-functions.md)