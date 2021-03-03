---
title: Parole chiave SQL per Azure Cosmos DB
description: Informazioni sulle parole chiave SQL per Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 4711401172c4a2b224c231db4a773e0c4219d3a7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659835"
---
# <a name="keywords-in-azure-cosmos-db"></a>Parole chiave in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo descrive in dettaglio le parole chiave che possono essere usate nelle query SQL Azure Cosmos DB.

## <a name="between"></a>BETWEEN

È possibile usare la `BETWEEN` parola chiave per esprimere query su intervalli di valori stringa o numerici. Ad esempio, la query seguente restituisce tutti gli elementi in cui la qualità del primo figlio è 1-5, inclusi.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

È anche possibile usare la `BETWEEN` parola chiave nella `SELECT` clausola, come nell'esempio seguente.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Diversamente da ANSI SQL, nell'API SQL è possibile esprimere query di intervallo su proprietà di tipi misti. Ad esempio, `grade` può essere un numero come `5` in alcuni elementi e una stringa come `grade4` in altri. In questi casi, come in JavaScript, il confronto tra i due tipi diversi restituisce `Undefined` , quindi l'elemento viene ignorato.

## <a name="distinct"></a>DISTINCT

La `DISTINCT` parola chiave elimina i duplicati nella proiezione della query.

In questo esempio la query proietta i valori per ogni cognome:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

I risultati sono:

```json
[
    "Andersen"
]
```

È anche possibile proiettare oggetti univoci. In questo caso, il campo lastName non esiste in uno dei due documenti, quindi la query restituisce un oggetto vuoto.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

I risultati sono:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` può essere usato anche nella proiezione all'interno di una sottoquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Questa query proietta una matrice che contiene il valore specificato di ogni figlio con i duplicati rimossi. Questa matrice viene sottoposta a alias come ChildNames e proiettata nella query esterna.

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

Le query con una funzione di sistema di aggregazione e una sottoquery con `DISTINCT` non sono supportate. Ad esempio, la query seguente non è supportata:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>LIKE

Restituisce un valore booleano a seconda se una stringa di caratteri specifica corrisponde a un modello specificato. Il modello può contenere caratteri specifici e caratteri jolly. È possibile scrivere query logicamente equivalenti usando la `LIKE` parola chiave o la funzione di sistema [RegexMatch](sql-query-regexmatch.md) . Si osserverà lo stesso utilizzo di indici indipendentemente da quello scelto. Pertanto, è consigliabile utilizzare `LIKE` se si preferisce la sintassi più che le espressioni regolari.

> [!NOTE]
> Poiché `LIKE` può utilizzare un indice, è necessario [creare un indice di intervallo](./index-policy.md) per le proprietà che si desidera confrontare utilizzando `LIKE` .

È possibile usare i caratteri jolly seguenti con LIKE:

| Carattere jolly | Descrizione                                                  | Esempio                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Qualsiasi stringa di zero o più caratteri                      | DOVE c. Description come "% SO% PS%"      |
| _ (carattere di sottolineatura)     | Qualsiasi carattere singolo                                       | DOVE c. Description come "% SO_PS%"      |
| [ ]                  | Qualsiasi carattere singolo compreso nell'intervallo ([a-f]) o nel set ([abcdef]) specificato. | DOVE c. Description come "% SO [t-z] PS%"  |
| [^]                  | Qualsiasi carattere singolo non compreso nell'intervallo ([^ a-f]) o nel set ([^ abcdef]) specificato. | DOVE c. Description come "% SO [^ ABC] PS%" |


### <a name="using-like-with-the--wildcard-character"></a>Utilizzo dell'operatore LIKE con il carattere jolly %

Il `%` carattere corrisponde a qualsiasi stringa di zero o più caratteri. Inserendo ad esempio un oggetto `%` all'inizio e alla fine del modello, la query seguente restituisce tutti gli elementi con una descrizione che contiene `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Se è stato usato solo un `%` carattere all'inizio del modello, si restituiranno solo elementi con una descrizione avviata con `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Utilizzo di NOT LIKE

Nell'esempio seguente vengono restituiti tutti gli elementi con una descrizione che non contiene `fruit` :

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Uso della clausola escape

È possibile cercare i modelli che includono uno o più caratteri jolly usando la clausola ESCAPE. Se, ad esempio, si desidera cercare le descrizioni che contengono la stringa `20-30%` , non è possibile interpretare `%` come un carattere jolly.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Utilizzo di caratteri jolly come valori letterali

È possibile racchiudere i caratteri jolly tra parentesi quadre per considerarli come caratteri letterali. Quando si racchiude un carattere jolly tra parentesi quadre, vengono rimossi tutti gli attributi speciali. Ecco alcuni esempi:

| Modello           | Significato |
| ----------------- | ------- |
| LIKE "20-30 [%]" | 20-30%  |
| LIKE "[_] n"     | _n      |
| LIKE "[[]"    | [       |
| LIKE "]"        | ]       |

## <a name="in"></a>IN

Usare la parola chiave IN per verificare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, la query seguente restituisce tutti gli elementi della famiglia in cui `id` è `WakefieldFamily` o `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Nell'esempio seguente vengono restituiti tutti gli elementi in cui lo stato è uno dei valori specificati:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

L'API SQL fornisce supporto per l' [iterazione su matrici JSON](sql-query-object-array.md#Iteration), con un nuovo costrutto aggiunto tramite la parola chiave in nell'origine da.

Se si include la chiave di partizione nel `IN` filtro, la query verrà automaticamente filtrata solo per le partizioni pertinenti.

## <a name="top"></a>TOP

La parola chiave TOP restituisce il primo `N` numero di risultati della query in un ordine non definito. Come procedura consigliata, utilizzare TOP con la `ORDER BY` clausola per limitare i risultati al primo `N` numero di valori ordinati. La combinazione di queste due clausole è l'unico modo per indicare in modo prevedibile quali righe hanno effetto.

È possibile utilizzare TOP con un valore costante, come nell'esempio seguente, oppure con un valore di variabile utilizzando query con parametri.

```sql
    SELECT TOP 1 *
    FROM Families f
```

I risultati sono:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Join](sql-query-join.md)
- [Sottoquery:](sql-query-subquery.md)