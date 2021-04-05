---
title: SOMMA in linguaggio Azure Cosmos DB query
description: Informazioni sulla funzione di sistema SQL Sum (SUM) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552391"
---
# <a name="sum-azure-cosmos-db"></a>SOMMA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questa funzione di aggregazione restituisce la somma dei valori nell'espressione.
  
## <a name="syntax"></a>Sintassi
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempio
  
Nell'esempio seguente viene restituita la somma di `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy). Se gli argomenti in `SUM` sono String, Boolean o null, verrà restituita l'intera funzione di sistema di aggregazione `undefined` . Se un argomento ha un `undefined` valore, non avrà alcun effetto sul `SUM` calcolo.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Funzioni di aggregazione in Azure Cosmos DB](sql-query-aggregate-functions.md)