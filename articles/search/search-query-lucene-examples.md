---
title: Usa la sintassi di query Lucene completa
titleSuffix: Azure Cognitive Search
description: Esempi di query che illustrano la sintassi di query Lucene per la ricerca fuzzy, la ricerca per prossimità, l'aumento dei termini, la ricerca di espressioni regolari e le ricerche con caratteri jolly in un indice ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693561"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Usare la sintassi di ricerca Lucene "completa" (query avanzate in Azure ricerca cognitiva)

Quando si creano query per Azure ricerca cognitiva, è possibile sostituire il [parser di query semplice](query-simple-syntax.md) predefinito con il parser di query [Lucene](query-lucene-syntax.md) più potente per formulare espressioni di query specializzate e avanzate.

Il parser Lucene supporta formati di query complessi, ad esempio query con ambito campo, ricerca fuzzy, infissi e ricerca con caratteri jolly suffisso, ricerca di prossimità, boosting dei termini e ricerca di espressioni regolari. Il livello più avanzato comporta requisiti di elaborazione aggiuntivi. È pertanto opportuno prevedere un tempo di esecuzione leggermente superiore. In questo articolo è possibile esaminare gli esempi che illustrano le operazioni di query in base alla sintassi completa.

> [!Note]
> Molte delle costruzioni di query specializzate possibili attraverso la sintassi di query Lucene completa non vengono [analizzate dal punto di vista del testo](search-lucene-query-architecture.md#stage-2-lexical-analysis), fatto che può sembrare sorprendente se ci si aspetta lo stemming o la lemmatizzazione. L'analisi lessicale viene eseguita solo su termini completi, la query di un termine o di una locuzione. I tipi di query con termini incompleti, ad esempio query di prefisso, di caratteri jolly, di espressioni regolari, fuzzy, vengono aggiunte direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita su termini di query parziali è minuscole. 
>

## <a name="hotels-sample-index"></a>Indice di esempio degli hotel

Le query seguenti sono basate su Hotels-sample-index, che è possibile creare seguendo le istruzioni riportate in questa [Guida introduttiva](search-get-started-portal.md).

Le query di esempio sono articolate usando l'API REST e le richieste POST. È possibile incollare ed eseguire tali elementi in un [post](search-get-started-rest.md) o in [Visual Studio Code con l'estensione ricerca cognitiva](search-get-started-vs-code.md).

Le intestazioni della richiesta devono contenere i valori seguenti:

| Chiave | valore |
|-----|-------|
| Content-Type | application/json|
| api-key  | `<your-search-service-api-key>`, una query o una chiave amministratore |

I parametri URI devono includere l'endpoint del servizio di ricerca con il nome dell'indice, le raccolte docs, il comando di ricerca e la versione dell'API, in modo simile all'esempio seguente:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

Il corpo della richiesta deve essere formato come JSON valido:

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Search" impostato su `*` è una query non specificata, equivalente a null o a una ricerca vuota. Non è particolarmente utile, ma è la ricerca più semplice che è possibile eseguire e Mostra tutti i campi recuperabili nell'indice, con tutti i valori.

+ "queryType" impostato su "full" richiama il parser di query Lucene completo ed è necessario per questa sintassi.

+ "Select" impostato su un elenco delimitato da virgole di campi viene usato per la composizione dei risultati della ricerca, inclusi solo i campi che risultano utili nel contesto dei risultati della ricerca.

+ "count" restituisce il numero di documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota, il conteggio sarà costituito da tutti i documenti nell'indice (50 nel caso di Hotels-sample-index).

## <a name="example-1-fielded-search"></a>Esempio 1: ricerca nel campo

Ambito di ricerca in campo, singole espressioni di ricerca incorporate in un campo specifico. Questo esempio cerca i nomi degli alberghi con il termine "Hotel", ma non con "Motel". È possibile specificare più campi utilizzando e. 

Quando si usa questa sintassi di query, è possibile omettere il parametro "searchFields" quando i campi di cui si vuole eseguire una query si trovano nell'espressione di ricerca stessa. Se si include "searchFields" con la ricerca in campo, ha `fieldName:searchExpression` sempre la precedenza su "searchFields".

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

La risposta per questa query dovrebbe essere simile all'esempio seguente, filtrato in "Resort and Spa", restituendo gli hotel che includono "Hotel" o "Motel" nel nome.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

L'espressione di ricerca può essere costituita da un solo termine o una frase o da un'espressione più complessa tra parentesi, facoltativamente con operatori booleani. Ecco alcuni esempi:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Assicurarsi di inserire una frase tra virgolette se si desidera che entrambe le stringhe vengano valutate come una singola entità, come in questo caso la ricerca di due posizioni distinte nel campo Address/StateProvince. A seconda del client, potrebbe essere necessario utilizzare caratteri di escape ( `\` ) per le virgolette.

Il campo specificato in `fieldName:searchExpression` deve essere un campo ricercabile. Per informazioni dettagliate sul modo in cui vengono attribuiti le definizioni dei campi, vedere [create index (API REST)](/rest/api/searchservice/create-index) .

## <a name="example-2-fuzzy-search"></a>Esempio 2: ricerca fuzzy

La ricerca fuzzy corrisponde a termini simili, incluse le parole errate. Per eseguire una ricerca fuzzy, aggiungere il simbolo tilde `~` alla fine di una parola con un parametro facoltativo, un valore compreso tra 0 e 2, che specifica la distanza di edit. Ad esempio, `blue~` o `blue~1` restituirà blue, blues e glue.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

La risposta per questa query viene risolta in "concierge" nei documenti corrispondenti, tagliata per brevità:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Le frasi non sono supportate direttamente, ma è possibile specificare una corrispondenza fuzzy per ogni termine di una frase in più parti, ad esempio `search=Tags:landy~ AND sevic~` .  Questa espressione di query trova 15 corrispondenze in "servizio di lavanderia".

> [!Note]
> Le query fuzzy non vengono [analizzate](search-lucene-query-architecture.md#stage-2-lexical-analysis). I tipi di query con termini incompleti, ad esempio query di prefisso, di caratteri jolly, di espressioni regolari, fuzzy, vengono aggiunte direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita su termini di query parziali è la maiuscola e minuscola.
>

## <a name="example-3-proximity-search"></a>Esempio 3: ricerca vicina

La ricerca per prossimità trova termini vicini tra loro in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità.

Questa query cerca i termini "Hotel" e "Airport" entro 5 parole l'uno dall'altro in un documento. Le virgolette sono precedute da un carattere di escape ( `\"` ) per mantenere la frase:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

La risposta per questa query dovrebbe essere simile all'esempio seguente:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Esempio 4: incremento del termine

Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Per incrementare un termine, usare il punto di inserimento, `^` , simbolo con un fattore di incremento (un numero) alla fine del periodo di ricerca. Il valore predefinito del fattore di incremento è 1, anche se deve essere positivo, può essere minore di 1 (ad esempio, 0,2). L'aumento priorità dei termini si differenzia dai profili di punteggio per il fatto che questi ultimi aumentano la priorità di alcuni campi e non di termini specifici.

In questa query "before" cercare "accesso alla spiaggia" e notare che sono presenti sette documenti corrispondenti in uno o entrambi i termini.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

In realtà, esiste un solo documento che corrisponde a "accesso" e poiché è l'unica corrispondenza, la posizione è alta (seconda posizione) anche se nel documento manca il termine "spiaggia".

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

Nella query "After" (dopo) ripetere la ricerca, in questo caso l'incremento dei risultati con il termine "Beach" sul termine "Access". Una versione leggibile della query è `search=Description:beach^2 access` . A seconda del client, potrebbe essere necessario esprimere `^2` come `%5E2` .

Dopo aver incrementato il termine "spiaggia", la corrispondenza nell'hotel Carrabelle precedente si sposta al sesto posto.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Esempio 5: Regex

Una ricerca con espressione regolare trova una corrispondenza in base al contenuto incluso tra le barre "/", come indicato nella [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

La risposta per questa query dovrebbe essere simile all'esempio seguente:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Le query Regex non vengono [analizzate](./search-lucene-query-architecture.md#stage-2-lexical-analysis). L'unica trasformazione eseguita su termini di query parziali è la maiuscola e minuscola.
>

## <a name="example-6-wildcard-search"></a>Esempio 6: ricerca con caratteri jolly

È possibile utilizzare la sintassi generalmente riconosciuta per più `*` ricerche con caratteri jolly () o Single ( `?` ). Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.

In questa query cercare i nomi degli hotel che contengono il prefisso "SC". Non è possibile usare `*` un `?` simbolo o come primo carattere di una ricerca.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

La risposta per questa query dovrebbe essere simile all'esempio seguente:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Le query con caratteri jolly non vengono [analizzate](./search-lucene-query-architecture.md#stage-2-lexical-analysis). L'unica trasformazione eseguita su termini di query parziali è la maiuscola e minuscola.
>

## <a name="next-steps"></a>Passaggi successivi

Provare a specificare le query nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca usando gli Azure SDK.

+ [Eseguire query sull'indice usando .NET SDK](search-get-started-dotnet.md)
+ [Eseguire query sull'indice con Python SDK](search-get-started-python.md)
+ [Eseguire query sull'indice tramite JavaScript SDK](search-get-started-javascript.md)

Un riferimento alla sintassi aggiuntivo, l'architettura di query ed esempi sono disponibili nei collegamenti seguenti:

+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Sintassi di query semplice](query-simple-syntax.md)
+ [Full Lucene query syntax](query-lucene-syntax.md) (Sintassi di query completa Lucene)
+ [Sintassi del filtro](search-query-odata-filter.md)