---
title: Utilizzo di matrici e oggetti in Azure Cosmos DB
description: Informazioni sulla sintassi SQL per creare matrici e oggetti in Azure Cosmos DB. Questo articolo fornisce anche alcuni esempi per eseguire operazioni sugli oggetti Array
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493788"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Utilizzo di matrici e oggetti in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Una funzionalità chiave dell'API di Azure Cosmos DB SQL è la creazione di matrici e oggetti. Questo documento usa esempi che possono essere ricreati usando il [set di dati della famiglia](sql-query-getting-started.md#upload-sample-data).

Ecco un esempio di elemento in questo set di dati:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Matrici

È possibile creare matrici, come illustrato nell'esempio seguente:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

I risultati sono:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

È anche possibile usare l' [espressione di matrice](sql-query-subquery.md#array-expression) per costruire una matrice dai risultati della [sottoquery](sql-query-subquery.md) . Questa query ottiene tutti i nomi distinti di elementi figlio in una matrice.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

I risultati sono:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iterazione

L'API SQL fornisce supporto per l'iterazione su matrici JSON, con la [parola chiave in](sql-query-keywords.md#in) nell'origine da. Nell'esempio seguente:

```sql
SELECT *
FROM Families.children
```

I risultati sono:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

La query successiva esegue l'iterazione `children` nel `Families` contenitore. La matrice di output è diversa dalla query precedente. Questo esempio divide `children` e rende Flat i risultati in una singola matrice:  

```sql
SELECT *
FROM c IN Families.children
```

I risultati sono:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

È possibile filtrare ulteriormente in ogni singola voce della matrice, come illustrato nell'esempio seguente:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

I risultati sono:

```json
[{
  "givenName": "Lisa"
}]
```

È anche possibile aggregare sul risultato di un'iterazione della matrice. Ad esempio, la query seguente conta il numero di elementi figlio tra tutte le famiglie:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

I risultati sono:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Quando si usa la parola chiave IN per l'iterazione, non è possibile filtrare o proiettare proprietà esterne alla matrice. È invece consigliabile utilizzare i [join](sql-query-join.md).

Per altri esempi, vedere il [post di Blog sull'uso di matrici in Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Join](sql-query-join.md)
