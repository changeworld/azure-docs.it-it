---
title: Usare la sintassi di query Lucene semplice
titleSuffix: Azure Cognitive Search
description: Esempi di query che illustrano la semplice sintassi per la ricerca full-text, la ricerca di filtri e la ricerca geografica rispetto a un indice ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694037"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Usare la sintassi di ricerca "Simple" in Azure ricerca cognitiva

In ricerca cognitiva di Azure, la [sintassi di query semplice](query-simple-syntax.md) richiama il parser di query predefinito per la ricerca full-text. Il parser è veloce e gestisce scenari comuni, tra cui ricerca full-text, ricerca filtrata e sfaccettata e ricerca di prefisso. Questo articolo usa esempi per illustrare l'utilizzo semplice della sintassi in una richiesta di [ricerca di documenti (API REST)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Una sintassi di query alternativa è [Lucene completa](query-lucene-syntax.md), che supporta strutture di query più complesse, come la ricerca fuzzy e la ricerca con caratteri jolly. Per altre informazioni ed esempi, vedere [usare la sintassi Lucene completa](search-query-lucene-examples.md).

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Search" impostato su `*` è una query non specificata, equivalente a null o a una ricerca vuota. Non è particolarmente utile, ma è la ricerca più semplice che è possibile eseguire e Mostra tutti i campi recuperabili nell'indice, con tutti i valori.

+ "queryType" impostato su "Simple" è il valore predefinito e può essere omesso, ma è incluso per rinforzare ulteriormente che gli esempi di query in questo articolo sono espressi nella sintassi semplice.

+ "Select" impostato su un elenco delimitato da virgole di campi viene usato per la composizione dei risultati della ricerca, inclusi solo i campi che risultano utili nel contesto dei risultati della ricerca.

+ "count" restituisce il numero di documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota, il conteggio sarà costituito da tutti i documenti nell'indice (50 nel caso di Hotels-sample-index).

## <a name="example-1-full-text-search"></a>Esempio 1: ricerca full-text

La ricerca full-text può essere un numero qualsiasi di termini autonomi o frasi racchiuse tra virgolette, con o senza operatori booleani. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Una ricerca di parole chiave composta da termini o frasi importanti tende a funzionare meglio. I campi stringa vengono sottoposti a analisi del testo durante l'indicizzazione e l'esecuzione di query, eliminando parole non essenziali quali "The", "and", "it". Per vedere come una stringa di query viene suddivisa in token nell'indice, passare la stringa in una chiamata di [testo Analyze](/rest/api/searchservice/test-analyzer) all'indice.

Il parametro "searchMode" controlla la precisione e il richiamo. Se si desidera un maggior richiamo, utilizzare il valore predefinito "any", che restituisce un risultato se una parte della stringa di query viene confrontata. Se si preferisce la precisione, in cui è necessario trovare una corrispondenza per tutte le parti della stringa, impostare searchMode su "All". Provare la query precedente in entrambi i modi per vedere come searchMode modifica il risultato.

La risposta per la query "Pool Spa + Airport" dovrebbe avere un aspetto simile all'esempio seguente, tagliato per brevità.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Si noti il Punteggio di ricerca nella risposta. Questo è il Punteggio di pertinenza della corrispondenza. Per impostazione predefinita, un servizio di ricerca restituirà le prime 50 corrispondenze in base a questo punteggio.

I punteggi uniformi di "1,0" si verificano quando non è disponibile alcuna classificazione, perché la ricerca non è full-text o perché non è stato fornito alcun criterio. Ad esempio, in una ricerca vuota (Search = `*` ), le righe vengono restituite in ordine arbitrario. Se si includono criteri effettivi, i punteggi di ricerca si convertono in valori significativi.

## <a name="example-2-look-up-by-id"></a>Esempio 2: ricerca per ID

Quando si restituiscono i risultati della ricerca in una query, un passaggio successivo logico consiste nel fornire una pagina di dettagli che includa più campi dal documento. Questo esempio Mostra come restituire un singolo documento usando il [documento di ricerca](/rest/api/searchservice/lookup-document) passando l'ID del documento.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Tutti i documenti dispongono di un identificatore unico. Se si usa il portale, selezionare l'indice dalla scheda **indici** e quindi esaminare le definizioni dei campi per determinare quale campo è la chiave. Tramite REST, la chiamata [Get index](/rest/api/searchservice/get-index) restituisce la definizione di indice nel corpo della risposta.

La risposta per la query precedente è costituita dal documento la cui chiave è 41. Tutti i campi contrassegnati come "recuperabili" nella definizione dell'indice possono essere restituiti nei risultati della ricerca e sottoposti a rendering nell'app.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Esempio 3: filtrare in testo

La [sintassi del filtro](search-query-odata-filter.md) è un'espressione OData che è possibile utilizzare autonomamente o con "Search". Utilizzato insieme, "Filter" viene applicato per primo all'intero indice, quindi la ricerca viene eseguita sui risultati del filtro. I filtri quindi possono essere un'utile tecnica per migliorare le prestazioni delle query perché riducono il set di documenti che la query di ricerca deve elaborare.

I filtri possono essere definiti in qualsiasi campo contrassegnato come "filtrabile" nella definizione dell'indice. Per Hotels-sample-index, i campi filtrabili includono Category, Tags, ParkingIncluded, rating e la maggior parte dei campi Address.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

La risposta per la query precedente ha come ambito solo gli alberghi classificati come "report e Spa", che includono i termini "arte" o "Tours". In questo caso, è presente una sola corrispondenza.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Esempio 4: funzioni di filtro

Le espressioni di filtro possono includere [funzioni "search. IsMatch" e "search. ismatchscoring"](search-query-odata-full-text-search-functions.md), che consentono di compilare una query di ricerca all'interno del filtro. Questa espressione di filtro usa un carattere jolly *gratuito* per selezionare i servizi, tra cui Wi-Fi gratuito, parcheggio gratuito e così via.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

La risposta per la query precedente corrisponde a 19 hotel che offrono servizi gratuiti. Si noti che il Punteggio di ricerca è un "1,0" uniforme per tutti i risultati. Ciò è dovuto al fatto che l'espressione di ricerca è null o vuota, ottenendo corrispondenze di filtro Verbatim, ma nessuna ricerca full-text. I punteggi di pertinenza vengono restituiti solo nella ricerca full-text. Se si usano filtri senza "Search", assicurarsi che siano disponibili campi ordinabili sufficienti per poter controllare il rango di ricerca.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Esempio 5: filtri di intervallo

Il filtro di intervallo è supportato tramite espressioni di filtro per qualsiasi tipo di dati. Negli esempi seguenti vengono illustrati gli intervalli numerici e di stringa. I tipi di dati sono importanti nei filtri di intervallo e funzionano in modo ottimale quando i dati numerici si trovano nei campi numerici e i dati di tipo stringa nei campi stringa. I dati numerici nei campi stringa non sono adatti per gli intervalli perché le stringhe numeriche non sono confrontabili.

La query seguente è un intervallo numerico. In Hotels-sample-index l'unico campo numerico filtrabile è rating.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

La risposta per questa query dovrebbe essere simile all'esempio seguente, troncata per brevità.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

La query successiva è un filtro di intervallo su un campo stringa (Address/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

La risposta per questa query dovrebbe essere simile all'esempio seguente, tagliata per brevità. In questo esempio non è possibile eseguire l'ordinamento in base a StateProvince perché il campo non è attribuito come "ordinabile" nella definizione dell'indice.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Esempio 6: ricerca geografica

L'indice Hotels-Sample include un campo geo_location con coordinate di latitudine e longitudine. Questo esempio usa la [funzione geo.distance](search-query-odata-geo-spatial-functions.md#examples) che applica il filtro ai documenti all'interno della circonferenza di un punto di partenza, fino a una distanza arbitraria (in chilometri) specificata. Per ridurre o ingrandire la superficie di attacco della query, è possibile modificare l'ultimo valore nella query (10).

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

La risposta per questa query restituisce tutti gli alberghi entro una distanza di 10 chilometri dalle coordinate fornite:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Esempio 7: valori booleani con searchMode

La sintassi semplice supporta gli operatori booleani sotto forma di caratteri ( `+, -, |` ) per supportare and, or e not logica di query. La ricerca booleana si comporta come previsto, con alcune eccezioni degne di nota. 

Negli esempi precedenti, il parametro "searchMode" è stato introdotto come meccanismo per influenzare la precisione e il richiamo, con "searchMode = any" che favorisce il richiamo (un documento che soddisfa uno dei criteri è considerato una corrispondenza) e "searchMode = all" prediligendo la precisione (tutti i criteri devono essere associati a un documento). 

Nel contesto di una ricerca booleana, il valore predefinito "searchMode = any" può essere confuso se si sta impilando una query con più operatori e ottenendo più ampio anziché risultati più ristretti. Questa operazione è particolarmente valida con NOT, dove i risultati includono tutti i documenti "non contenenti" un termine o una frase specifica.

Nell'esempio seguente viene illustrato questo concetto. Eseguendo la query seguente con searchMode (any), vengono restituiti i documenti 42: quelli che contengono il termine "ristorante", più tutti i documenti che non hanno la frase "aria condizionata". 

Si noti che non è presente alcuno spazio tra l'operatore booleano ( `-` ) e la frase "aria condizionata".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Se si passa a "searchMode = all", viene applicato un effetto cumulativo sui criteri e viene restituito un set di risultati più piccolo (7 corrispondenze) costituito da documenti che contengono il termine "ristorante", meno quelli che contengono la frase "aria condizionata".

La risposta per questa query avrà un aspetto simile all'esempio seguente, troncata per brevità.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Esempio 8: paging dei risultati

Negli esempi precedenti sono stati illustrati i parametri che influiscono sulla composizione dei risultati della ricerca, tra cui "Select", che determina quali campi sono in un risultato, ordinano gli ordini e come includere un conteggio di tutte le corrispondenze. Questo esempio è una continuazione della composizione dei risultati della ricerca sotto forma di parametri di paging che consentono di raggruppare il numero di risultati visualizzati in una determinata pagina. 

Per impostazione predefinita, un servizio di ricerca restituisce le prime 50 corrispondenze. Per controllare il numero di corrispondenze in ogni pagina, usare "Top" per definire le dimensioni del batch, quindi usare "Skip" per selezionare i batch successivi.

Nell'esempio seguente vengono utilizzati un filtro e un ordinamento per il campo rating (la classificazione può essere filtrata e ordinabile) perché è più semplice vedere gli effetti del paging sui risultati ordinati. In una normale query di ricerca completa, le corrispondenze principali vengono classificate e inserite in paging da " @search.score ".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

La query trova 21 documenti corrispondenti, ma poiché è stato specificato "Top", la risposta restituisce solo le prime cinque corrispondenze, con le classificazioni a partire da 4,9 e termina con 4,7 con "Lady of the Lake B & B". 

Per ottenere i successivi 5, ignorare il primo batch:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

La risposta per il secondo batch ignora le prime cinque corrispondenze, restituendo i successivi cinque, a partire da "Pull'r Inn Motel". Per continuare con batch aggiuntivi, mantenere "Top" su 5 e quindi incrementare "Skip" di 5 per ogni nuova richiesta (Skip = 5, skip = 10, Skip = 15 e così via).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver eseguito alcune procedure con la sintassi di base delle query, provare a specificare le query nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca usando gli Azure SDK.

+ [Eseguire query sull'indice usando .NET SDK](search-get-started-dotnet.md)
+ [Eseguire query sull'indice con Python SDK](search-get-started-python.md)
+ [Eseguire query sull'indice tramite JavaScript SDK](search-get-started-javascript.md)

Un riferimento alla sintassi aggiuntivo, l'architettura di query ed esempi sono disponibili nei collegamenti seguenti:

+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Sintassi di query semplice](query-simple-syntax.md)
+ [Full Lucene query syntax](query-lucene-syntax.md) (Sintassi di query completa Lucene)
+ [Sintassi del filtro](search-query-odata-filter.md)