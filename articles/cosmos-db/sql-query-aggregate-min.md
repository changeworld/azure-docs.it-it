---
title: MIN in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL min (MIN) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555412"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questa funzione di aggregazione restituisce il numero minimo di valori nell'espressione.
  
## <a name="syntax"></a>Sintassi
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*scalar_expr*  
   Espressione scalare. 
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un'espressione scalare.  
  
## <a name="examples"></a>Esempio
  
Nell'esempio seguente viene restituito il valore minimo di `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy). Gli argomenti in `MIN` possono essere Number, String, Boolean o null. Eventuali valori non definiti verranno ignorati.

Quando si confrontano dati di tipi diversi, viene usato l'ordine di priorità seguente (in ordine crescente):

- Null
- boolean
- d'acquisto
- string

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Funzioni di aggregazione in Azure Cosmos DB](sql-query-aggregate-functions.md)