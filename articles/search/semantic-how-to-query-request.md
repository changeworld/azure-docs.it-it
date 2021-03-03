---
title: Creare una query semantica
titleSuffix: Azure Cognitive Search
description: Impostare un tipo di query semantico per allineare i modelli di apprendimento avanzato all'elaborazione delle query, dedurre finalità e contesto come parte del rango di ricerca e della pertinenza.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0af868f62f9bc62ee6b4b2a10d16f8eed632b6d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679839"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Creare una query semantica in ricerca cognitiva

> [!IMPORTANT]
> Il tipo di query semantico è disponibile in anteprima pubblica, disponibile tramite l'API REST di anteprima e portale di Azure. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Durante l'avvio dell'anteprima iniziale, non è previsto alcun addebito per la ricerca semantica. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

In questo articolo viene illustrato come formulare una richiesta di ricerca che utilizza la classificazione semantica e produce didascalie e risposte semantiche.

## <a name="prerequisites"></a>Prerequisiti

+ Un servizio di ricerca a livello standard (S1, S2, S3), situato in una di queste aree: Stati Uniti centro-settentrionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale. Se si dispone di un servizio S1 o Greater esistente in una di queste aree, è possibile richiedere l'accesso senza dover creare un nuovo servizio.

+ Accesso all'anteprima di ricerca semantica: [iscrizione](https://aka.ms/SemanticSearchPreviewSignup)

+ Un indice di ricerca esistente, che contiene contenuto in lingua inglese

+ Un client di ricerca per l'invio di query

  Il client di ricerca deve supportare le API REST di anteprima nella richiesta di query. È possibile usare il [post](search-get-started-rest.md), il [Visual Studio Code](search-get-started-vs-code.md)o il codice modificato per eseguire chiamate REST alle API di anteprima. È anche possibile usare [Esplora ricerche](search-explorer.md) in portale di Azure per inviare una query semantica.

+ Una richiesta di [ricerca dei documenti](/rest/api/searchservice/preview-api/search-documents) con l'opzione semantica e altri parametri descritti in questo articolo.

## <a name="whats-a-semantic-query"></a>Che cos'è una query semantica?

In ricerca cognitiva, una query è una richiesta con parametri che determina l'elaborazione di query e la forma della risposta. Una *query semantica* aggiunge parametri che richiamano l'algoritmo di riclassificazione semantico in grado di valutare il contesto e il significato dei risultati di corrispondenza e di innalzare di livello le corrispondenze più rilevanti.

La richiesta seguente è rappresentativa di una query semantica di base (senza risposte).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Come per tutte le query in ricerca cognitiva, la richiesta è destinata alla raccolta Documents di un singolo indice. Inoltre, una query semantica viene sottoposta alla stessa sequenza di analisi, analisi e analisi come query non semantica. La differenza risiede nella modalità di calcolo della pertinenza. Come definito in questa versione di anteprima, una query semantica è una query *i cui risultati* vengono rielaborati usando algoritmi avanzati, offrendo un modo per esporre le corrispondenze ritenute più rilevanti dal Ranker semantico, anziché i punteggi assegnati dall'algoritmo di classificazione di somiglianza predefinito. 

Solo le prime 50 corrispondenze dei risultati iniziali possono essere classificate in modo semantico e tutte includono didascalie nella risposta. Facoltativamente, è possibile specificare un **`answer`** parametro nella richiesta per estrarre una potenziale risposta. Questo modello formula fino a cinque risposte potenziali alla query, che è possibile scegliere di eseguire il rendering nella parte superiore della pagina di ricerca.

## <a name="query-using-rest-apis"></a>Eseguire query usando le API REST

La specifica completa dell'API REST è disponibile nella pagina [Cerca documenti (anteprima Rest)](/rest/api/searchservice/preview-api/search-documents).

Le query semantiche sono destinate a domande aperte come "Qual è il migliore impianto per gli impollinatori" o "come friggere un uovo". Se si vuole che la risposta includa le risposte, è possibile aggiungere un **`answer`** parametro facoltativo nella richiesta.

Nell'esempio seguente viene usato l'indice Hotels-sample-per creare una richiesta di query semantica con le risposte semantiche e le didascalie:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>Formulare la richiesta

1. Impostare "queryType" su "Semantic" e "queryLanguage" su "en-US. Entrambi i parametri sono obbligatori.

   QueryLanguage deve essere coerente con tutti gli [analizzatori di linguaggio](index-add-language-analyzers.md) assegnati alle definizioni di campo nello schema dell'indice. Se queryLanguage è "en-US", qualsiasi analizzatore di linguaggio deve essere anche una variante inglese ("en. Microsoft" o "en. Lucene"). Gli analizzatori indipendenti dal linguaggio, ad esempio parole chiave o semplici, non presentano conflitti con i valori queryLanguage.

   In una richiesta di query, se si usa anche la [correzione ortografica](speller-how-to-add.md), il queryLanguage impostato viene applicato in modo analogo a correttore ortografico, risposte e didascalie. Nessun override per le singole parti. 

   Mentre il contenuto di un indice di ricerca può essere composto in più lingue, l'input della query è probabilmente in uno. Il motore di ricerca non verifica la compatibilità di queryLanguage, Language Analyzer e la lingua in cui è composto il contenuto. Assicurarsi quindi di definire l'ambito delle query in modo da evitare di produrre risultati non corretti.

1. Facoltativo ma consigliato, impostare "searchFields".

   In una query semantica, l'ordine dei campi in "searchFields" riflette la priorità o l'importanza relativa del campo nelle classificazioni semantiche. Verranno utilizzati solo i campi stringa di primo livello (standalone o in una raccolta). Poiché searchFields ha altri comportamenti nelle query Lucene semplici e complete (in cui non esiste un ordine di priorità implicito), i campi e i sottocampi non di stringa non genereranno un errore, ma non verranno usati nella classificazione semantica.

   Quando si specifica searchFields, attenersi alle seguenti linee guida:

   + I campi concisi, ad esempio HotelName o title, devono precedere i campi dettagliati come la descrizione.

   + Se l'indice include un campo URL che è testuale (leggibile, ad esempio `www.domain.com/name-of-the-document-and-other-details` e non incentrato sul computer, ad esempio `www.domain.com/?id=23463&param=eis` ), inserire il secondo nell'elenco (inserire prima se non esiste alcun campo di titolo conciso).

   + Se è stato specificato un solo campo, questo verrà considerato come campo descrittivo per la classificazione semantica dei documenti.  

   + Se non sono specificati campi, tutti i campi ricercabili verranno considerati per la classificazione semantica dei documenti. Tuttavia, questa operazione non è consigliata perché potrebbe non produrre i risultati più ottimali dall'indice di ricerca.

1. Rimuovere le clausole "orderBy", se presenti in una richiesta esistente. Il Punteggio semantico viene usato per ordinare i risultati. Se si include la logica di ordinamento esplicita, viene restituito un errore HTTP 400.

1. Facoltativamente, aggiungere "Answers" impostato su "extractal" e specificare il numero di risposte se si desidera maggiore di 1.

1. Facoltativamente, personalizzare lo stile evidenziato applicato alle didascalie. Nelle didascalie viene applicata la formattazione dell'evidenziazione sui passaggi chiave del documento che riepilogano la risposta. Il valore predefinito è `<em>`. Se si desidera specificare il tipo di formattazione, ad esempio sfondo giallo, è possibile impostare highlightPreTag e highlightPostTag.

1. Specificare tutti gli altri parametri desiderati nella richiesta. Parametri come il [correttore ortografico](speller-how-to-add.md), [SELECT](search-query-odata-select.md)e count migliorano la qualità della richiesta e la leggibilità della risposta.

### <a name="review-the-response"></a>Esaminare la risposta

La risposta per la query precedente restituisce la corrispondenza seguente come inizio selezione. Le didascalie vengono restituite automaticamente, con testo normale e versioni evidenziate. Per ulteriori informazioni sulle risposte semantiche, vedere [Semantic ranking and Responses](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Parametri utilizzati in una query semantica

Nella tabella seguente vengono riepilogati i parametri di query utilizzati in una query semantica, in modo che sia possibile visualizzarli in modo olistico. Per un elenco di tutti i parametri, vedere [cercare documenti (anteprima Rest)](/rest/api/searchservice/preview-api/search-documents)

| Parametro | Tipo | Descrizione |
|-----------|-------|-------------|
| queryType | string | I valori validi includono semplice, completo e semantico. Per le query semantiche è necessario un valore "semantico". |
| queryLanguage | string | Obbligatorio per le query semantiche. Attualmente, viene implementato solo "en-US". |
| searchFields | string | Elenco delimitato da virgole di campi disponibili per la ricerca. Facoltativo ma consigliato. Specifica i campi in base ai quali si verifica la classificazione semantica. </br></br>Diversamente dai tipi di query semplici e completi, l'ordine in cui vengono elencati i campi determina la precedenza.|
| risposte |string | Campo facoltativo per specificare se le risposte semantiche sono incluse nel risultato. Attualmente, viene implementato solo "estrazione". È possibile configurare le risposte per restituire un massimo di cinque. Questo esempio è "estrazione|Count3 "' Mostra un conteggio di tre risposte. Il valore predefinito è 1.|

## <a name="query-with-search-explorer"></a>Eseguire query con Esplora ricerche

La query seguente è destinata all'indice di esempio degli Alberghi predefiniti, usando l'API versione 2020-06-30-Preview e viene eseguito in Esplora ricerche. La `$select` clausola limita i risultati a pochi campi, rendendo più semplice l'analisi nel codice JSON dettagliato in Esplora ricerche.

### <a name="with-querytypesemantic"></a>With queryType = Semantic

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

I primi risultati sono i seguenti.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>With queryType (impostazione predefinita)

Per il confronto, eseguire la stessa query precedente, rimuovendo `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Si noti che non è presente alcun oggetto `"@search.rerankerScore"` in questi risultati e che i diversi alberghi sono visualizzati nelle prime tre posizioni.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Passaggi successivi

Tenere presente che le risposte e la classificazione semantica vengono compilate su un set di risultati iniziale. Tutte le logiche che migliorano la qualità dei risultati iniziali porteranno alla ricerca semantica. Come passaggio successivo, esaminare le funzionalità che contribuiscono ai risultati iniziali, inclusi gli analizzatori che influiscono sul modo in cui le stringhe vengono suddivise in token, i profili di punteggio che possono ottimizzare i risultati e l'algoritmo di pertinenza predefinito.

+ [Analizzatori per l'elaborazione del testo](search-analyzers.md)
+ [Somiglianza e assegnazione dei punteggi in ricerca cognitiva](index-similarity-and-scoring.md)
+ [Aggiungere profili di punteggio](index-add-scoring-profiles.md)
+ [Panoramica della ricerca semantica](semantic-search-overview.md)
+ [Aggiungere il controllo ortografico ai termini della query](speller-how-to-add.md)
