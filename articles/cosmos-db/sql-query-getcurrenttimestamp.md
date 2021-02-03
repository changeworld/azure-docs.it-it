---
title: GetCurrentTimestamp in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentTimestamp in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524262"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un valore numerico con segno, il numero corrente di millisecondi trascorsi dal periodo UNIX, ovvero il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="remarks"></a>Commenti

GetCurrentTimestamp () è una funzione non deterministica. Il risultato restituito è UTC (Coordinated Universal Time).

> [!NOTE]
> Questa funzione di sistema non utilizzerà l'indice. Se è necessario confrontare i valori con l'ora corrente, ottenere l'ora corrente prima dell'esecuzione della query e usare tale valore stringa costante nella `WHERE` clausola.

## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato come ottenere il timestamp corrente utilizzando la funzione predefinita GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Di seguito è riportato un esempio di set di risultati.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
