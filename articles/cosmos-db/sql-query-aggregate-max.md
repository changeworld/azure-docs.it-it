---
title: MASSIMO nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL Max (MAX) in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96552380"
---
# <a name="max-azure-cosmos-db"></a>MASSIMO (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questa funzione di aggregazione restituisce il numero massimo di valori nell'espressione.
  
## <a name="syntax"></a>Sintassi
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argomenti

*scalar_expr*  
   Espressione scalare. 
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un'espressione scalare.  
  
## <a name="examples"></a>Esempio
  
Nell'esempio seguente viene restituito il valore massimo di `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy). Gli argomenti in `MAX` possono essere Number, String, Boolean o null. Eventuali valori non definiti verranno ignorati.

Quando si confrontano dati di tipi diversi, viene usato l'ordine di priorità seguente (in ordine decrescente):

- Stringa
- Numero
- boolean
- Null

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche in Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Funzioni di aggregazione in Azure Cosmos DB](sql-query-aggregate-functions.md)