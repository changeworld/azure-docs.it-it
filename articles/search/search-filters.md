---
title: Filtrare i risultati della ricerca
titleSuffix: Azure Cognitive Search
description: Filtrare in base a identità di sicurezza dell'utente, lingua, località geografica o valori numerici per ridurre i risultati della ricerca nelle query in Azure ricerca cognitiva, un servizio di ricerca cloud ospitato su Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a5c8f835d44896a452a945614332dcbc25ca8bb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694428"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtri in ricerca cognitiva di Azure 

Un *filtro* fornisce criteri basati sul valore per la selezione di documenti utilizzati in una query. Un filtro può essere un singolo valore o un' [espressione di filtro](search-query-odata-filter.md)OData. Diversamente dalla ricerca full-text, un valore o un'espressione di filtro restituisce solo una corrispondenza rigida.

Alcune esperienze di ricerca, ad esempio l' [esplorazione](search-filters-facets.md)in base a facet, dipendono dai filtri come parte dell'implementazione, ma è possibile usare i filtri ogni volta che si vuole definire l'ambito di una query su valori specifici. Se invece l'obiettivo è l'ambito di una query per campi specifici, sono disponibili metodi alternativi, descritti di seguito.

## <a name="when-to-use-a-filter"></a>Quando usare un filtro

I filtri sono fondamentali per diverse esperienze di ricerca, ad esempio la ricerca di elementi nelle vicinanze, l'esplorazione in base a facet e i filtri di sicurezza, che mostrano solo i documenti che un utente è autorizzato a visualizzare. Se si implementa una di queste esperienze, è necessario un filtro. Le coordinate di georilevazione, la categoria di facet selezionata dall'utente o l'ID di sicurezza del richiedente vengono forniti dal filtro associato alla query di ricerca.

Gli scenari comuni includono i seguenti:

+ Suddividere i risultati della ricerca in base al contenuto dell'indice. Dato uno schema con località, categorie e servizi, è possibile creare un filtro per soddisfare in modo esplicito i criteri (a Seattle, in acqua con una vista). 

+ Implementare un'esperienza di ricerca viene fornita con un requisito di filtro:

  + L'[esplorazione in base a facet](search-faceted-navigation.md) usa un filtro per passare la categoria di facet selezionata dall'utente.
  + La ricerca geografica usa un filtro per passare le coordinate della posizione corrente alle app che eseguono ricerche di elementi nelle vicinanze. 
  + I [filtri di sicurezza](search-security-trimming-for-azure-search.md) passano gli identificatori di sicurezza come criteri di filtro, in cui una corrispondenza nell'indice funge da proxy per i diritti di accesso al documento.

+ Eseguire una ricerca di numeri. I campi numerici sono recuperabili e possono essere visualizzati nei risultati della ricerca, ma non sono singolarmente ricercabili (soggetto alla ricerca full-text). Se sono necessari criteri di selezione basati su dati numerici, usare un filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Metodi alternativi per ridurre l'ambito

I filtri non sono l'unica opzione disponibile per limitare i risultati della ricerca. Le alternative seguenti potrebbero essere più appropriate, a seconda dell'obiettivo:

+ `searchFields` il parametro di query limita la ricerca a campi specifici. Se, ad esempio, l'indice fornisce campi separati per le descrizioni in inglese e in spagnolo, è possibile usare searchFields per definire i campi da usare per la ricerca full-text. 

+ Il parametro `$select` viene usato per specificare i campi da includere in un set di risultati, per limitare in modo efficace la risposta prima di inviarla all'applicazione chiamante. Questo parametro non perfeziona la query o riduce la raccolta dei documenti, ma se è l'obiettivo di una risposta più piccola, questo parametro è un'opzione da considerare. 

Per altre informazioni su questi parametri, vedere [Search Documents > Request > Query parameters](/rest/api/searchservice/search-documents#query-parameters) (Ricerca di documenti > Richiesta > Parametri di query).

## <a name="how-filters-are-executed"></a>Modalità di esecuzione dei filtri

In fase di query, un parser di filtro accetta criteri come input, converte l'espressione in espressioni booleane atomiche rappresentate come albero e quindi valuta l'albero dei filtri su campi filtrabili in un indice.

Il filtro si verifica in tandem con la ricerca, qualificando i documenti da includere nell'elaborazione downstream per il recupero dei documenti e il Punteggio di pertinenza. Una volta abbinato a una stringa di ricerca, il filtro riduce efficacemente il set di richiamo della successiva operazione di ricerca. Se usato da solo (ad esempio, quando la stringa di query è vuota con `search=*`), il criterio di filtro è l'unico input. 

## <a name="defining-filters"></a>Definizione di filtri

I filtri sono espressioni OData, articolate nella [sintassi del filtro](search-query-odata-filter.md) supportata da ricerca cognitiva.

È possibile specificare un filtro per ogni operazione di **ricerca** , ma il filtro stesso può includere più campi, più criteri e se si utilizza una funzione **IsMatch** , più espressioni di ricerca full-text. In un'espressione di filtro multiparte, è possibile specificare predicati in qualsiasi ordine (in base alle regole di precedenza degli operatori). Il riposizionamento dei predicati in una sequenza specifica non offre un miglioramento delle prestazioni rilevante.

Uno dei limiti di un'espressione di filtro è il limite di dimensione massima della richiesta. L'intera richiesta, compreso il filtro, può avere dimensioni massime di 16 MB per POST o 8 KB per GET. Esiste inoltre un limite al numero di clausole nell'espressione di filtro. In genere, se si hanno centinaia di clausole, è probabile che si superi il limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni non vincolate.

Gli esempi seguenti rappresentano definizioni di filtro prototipo in diverse API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrare i modelli di utilizzo

Negli esempi seguenti vengono illustrati diversi modelli di utilizzo per gli scenari di filtro. Per altre idee, vedere [OData expression syntax > Examples](./search-query-odata-filter.md#examples) (Sintassi delle espressioni OData -> Esempi).

+ Elemento **$filter** autonomo, senza una stringa di query, utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio e una classificazione. Si noti che la stringa di ricerca è semplicemente un asterisco, che significa "Match all Documents".

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ Combinazione di stringa di query e **$filter**, dove il filtro crea il subset e la stringa di query fornisce gli input dei termini per la ricerca full-text nel subset filtrato. L'aggiunta di termini (teatri a distanza) introduce i punteggi di ricerca nei risultati, in cui i documenti che soddisfano al meglio i termini sono classificati in un livello superiore. L'uso di un filtro con una stringa di query è il modello di utilizzo più comune.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>Corrispondenze sugli ostelli classificati con un numero di Motels superiore a 4 o 5 stelle.
   $filter = search. ismatchscoring (' Hostel ') e rating GE 4 o search. ismatchscoring (' Motel ') e rating EQ 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>Trova la corrispondenza con "Luxury" o "High-end" nel campo della descrizione o nella categoria esattamente uguale a "Luxury".
   $filter = search. ismatchscoring (' Luxury | High-end ',' Description ') o Category EQ ' Luxury ' &$count = true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = search. ismatchscoring (' pool ') e valutazione GE 4

  ricerca = pool&$filter = valutazione GE 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Language filters](search-filters-language.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Creazione di un campo filtrabile esistente

Non è possibile modificare i campi esistenti per renderli filtrabili. È invece necessario aggiungere un nuovo campo o ricompilare l'indice. Per ulteriori informazioni sulla ricompilazione di un indice o sul ripopolamento dei campi, vedere [How to rebuild an Azure ricerca cognitiva index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Concetti fondamentali sui filtri di testo

I filtri di testo corrispondono ai campi stringa rispetto alle stringhe letterali fornite nel filtro: `$filter=Category eq 'Resort and Spa'`

Diversamente dalla ricerca full-text, non esiste alcuna analisi lessicale o suddivisione in parole per i filtri di testo, quindi i confronti sono solo per corrispondenze esatte. Si supponga, ad esempio, che un campo *f* contenga "Sunny Day", non `$filter=f eq 'Sunny'` corrisponda a, ma a `$filter=f eq 'sunny day'` . 

Per le stringhe di testo viene fatta distinzione tra maiuscole e minuscole. Non sono presenti maiuscole e minuscole di parole maiuscole: `$filter=f eq 'Sunny day'` non troverà "Sunny Day".

### <a name="approaches-for-filtering-on-text"></a>Approcci per l'applicazione di filtri al testo

| Approccio | Descrizione | Utilizzo |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funzione che corrisponde a un campo rispetto a un elenco di stringhe delimitato. | Consigliato per i [filtri di sicurezza](search-security-trimming-for-azure-search.md) e per tutti i filtri in cui è necessario trovare una corrispondenza per molti valori di testo non elaborati con un campo stringa. La funzione **search.in** è progettata per la velocità ed è molto più veloce rispetto a confrontare in modo esplicito il campo con ogni stringa utilizzando `eq` e `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funzione che consente di combinare le operazioni di ricerca full-text con operazioni di filtro esclusivamente booleane nella stessa espressione filtro. | Usare **search. IsMatch** (o l'equivalente di assegnazione dei punteggi, **search. ismatchscoring**) quando si desiderano più combinazioni di filtri di ricerca in un'unica richiesta. Può essere usata anche con un filtro *contains* per applicare un filtro su una stringa parziale all'interno di una stringa più grande. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Espressione definita dall'utente composta da campi, operatori e valori. | Utilizzare questo valore quando si desidera trovare corrispondenze esatte tra un campo stringa e un valore stringa. |

## <a name="numeric-filter-fundamentals"></a>Concetti fondamentali sui filtri numerici

I campi numerici non sono di tipo `searchable` nel contesto della ricerca full-text. Solo le stringhe sono soggette a ricerca full-text. Se, ad esempio, si immette 99,99 come termine di ricerca, non si otterranno gli articoli con prezzo $99,99. Verranno invece visualizzati gli articoli con il numero 99 nei campi stringa del documento. Se quindi si hanno dati numerici, il presupposto è che verranno usati per i filtri, inclusi intervalli, facet, gruppi e così via. 

I documenti che contengono campi numerici (prezzo, dimensioni, SKU, ID) forniscono tali valori nei risultati della ricerca se il campo è contrassegnato come `retrievable`. Il concetto è che la ricerca full-text non è applicabile ai tipi di campi numerici.

## <a name="next-steps"></a>Passaggi successivi

Provare a usare **Esplora ricerche** nel portale per inviare query con parametri **$filter**. L'[indice real-estate-sample](search-get-started-portal.md) fornisce risultati interessanti per le query di filtro seguenti quando le si incolla nella barra di ricerca:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Per altri esempi, vedere [OData Filter Expression Syntax > Examples](./search-query-odata-filter.md#examples) (Sintassi delle espressioni di filtro OData -> Esempi).

## <a name="see-also"></a>Vedi anche

+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
+ [Sintassi di query semplice](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintassi di query Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipi di dati supportati](/rest/api/searchservice/supported-data-types)