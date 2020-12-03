---
title: MEDIA nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL media (AVG) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555424"
---
# <a name="avg-azure-cosmos-db"></a>MEDIA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questa funzione di aggregazione restituisce la media dei valori nell'espressione.
  
## <a name="syntax"></a>Sintassi
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
Nell'esempio seguente viene restituito il valore medio di `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy). Se gli argomenti in `AVG` sono String, Boolean o null, verrà restituita l'intera funzione di sistema di aggregazione `undefined` . Se un argomento ha un `undefined` valore, non avrà alcun effetto sul `AVG` calcolo.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Funzioni di aggregazione in Azure Cosmos DB](sql-query-aggregate-functions.md)