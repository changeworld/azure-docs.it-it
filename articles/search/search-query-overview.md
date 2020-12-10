---
title: Tipi di query
titleSuffix: Azure Cognitive Search
description: Informazioni sui tipi di query supportati in ricerca cognitiva, inclusi il testo libero, il filtro, il completamento automatico e i suggerimenti, la ricerca geografica, le query di sistema e la ricerca di documenti.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007858"
---
# <a name="query-types-in-azure-cognitive-search"></a>Tipi di query in Azure ricerca cognitiva

In ricerca cognitiva di Azure, una query è una specifica completa di un'operazione di round trip, con parametri che regolano l'esecuzione delle query e i parametri che formano la risposta che viene restituita.

## <a name="elements-of-a-request"></a>Elementi di una richiesta

L'esempio seguente è una query rappresentativa costruita usando l' [API REST di ricerca documenti](/rest/api/searchservice/search-documents). Questo esempio è destinato all' [Indice demo degli hotel](search-get-started-portal.md) e include parametri comuni che consentono di ottenere un'idea dell'aspetto di una query.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Le query vengono sempre indirizzate alla raccolta Documents di un singolo indice. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query.

+ **`queryType`** imposta il parser, che è il [parser di query semplice predefinito](search-query-simple-examples.md) (ottimale per la ricerca full-text) o il [parser di query Lucene completo](search-query-lucene-examples.md) usato per i costrutti di query avanzati, come le espressioni regolari, la ricerca di prossimità, la ricerca fuzzy e il carattere jolly, per citarne alcuni.

+ **`search`** fornisce i criteri di corrispondenza, in genere termini interi o frasi, ma spesso accompagnati da operatori booleani. I termini singoli autonomi sono query *termine*. Le query in più parti racchiuse tra virgolette sono query di *frasi* . La ricerca può essere indefinita, ad esempio, **`search=*`** ma senza criteri di corrispondenza, il set di risultati è composto da documenti arbitrariamente selezionati.

+ **`searchFields`** vincola l'esecuzione di query a campi specifici. Qualsiasi campo attribuito come *ricercabile* nello schema dell'indice è un candidato per questo parametro.

Anche le risposte sono deformate dai parametri inclusi nella query:

+ **`select`** Specifica i campi da restituire nella risposta. In un'istruzione SELECT è possibile utilizzare solo i campi contrassegnati come *recuperabili* nell'indice.

+ **`top`** Restituisce il numero specificato di documenti con la migliore corrispondenza. In questo esempio vengono restituiti solo 10 riscontri. Per eseguire la pagina dei risultati, è possibile usare top e Skip (non mostrati).

+ **`count`** indica il numero totale di documenti nell'intero indice corrispondenti, che possono essere maggiori di quelli restituiti. 

+ **`orderby`** viene utilizzato se si desidera ordinare i risultati in base a un valore, ad esempio una classificazione o una località. In caso contrario, per impostazione predefinita viene usato il Punteggio di pertinenza per classificare i risultati.

> [!Tip]
> Prima di scrivere codice, è possibile usare gli strumenti di query per apprendere la sintassi e sperimentare parametri diversi. L'approccio più rapido è lo strumento del portale integrato, [Esplora ricerche](search-explorer.md).
>
> Se è stata seguita questa [Guida introduttiva per creare l'indice demo degli Alberghi](search-get-started-portal.md), è possibile incollare questa stringa di query nella barra di ricerca di Explorer per eseguire la prima query: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Come gli attributi di campo in un indice determinano i comportamenti delle query

La progettazione degli indici e la progettazione delle query sono strettamente associate in Azure ricerca cognitiva. Un aspetto essenziale da conoscere fin dall'inizio è che lo *schema dell'indice*, con gli attributi in ogni campo, determina il tipo di query che è possibile compilare. 

Gli attributi dell'indice in un campo impostano le operazioni consentite - se un campo è *ricercabile* nell'indice *recuperabile* nei risultati *ordinabile*, *filtrabile* e così via. Nella stringa di query di esempio, `"$orderby": "Rating"` funziona solo perché il campo rating è contrassegnato come *ordinabile* nello schema dell'indice. 

![Definizione di indice per l'esempio di Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definizione di indice per l'esempio di Hotel")

Lo screenshot precedente è un elenco parziale degli attributi di indice per l'esempio di Hotel. È possibile visualizzare lo schema dell'intero indice nel portale. Per ulteriori informazioni sugli attributi dell’indice, vedere [Creare indice API REST](/rest/api/searchservice/create-index).

> [!Note]
> Alcune funzionalità di query sono abilitate a livello di indice anziché in base al campo. Queste funzionalità includono: [mappe sinonimi](search-synonyms.md), [analizzatori personalizzati](index-add-custom-analyzers.md), [costrutti del suggerimento (per il completamento automatico e query suggerite)](index-add-suggesters.md), [logica di assegnazione dei punteggi per i risultati di rango](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Scegliere un parser: semplice | completo

Azure ricerca cognitiva offre una scelta tra due parser di query per la gestione di query tipiche e specializzate. Le richieste che usano il parser semplice vengono formulate tramite il [la sintassi di query semplice](query-simple-syntax.md), selezionata come predefinita per la velocità e l'efficienza nelle query di testo in formato libero. Questa sintassi supporta diversi operatori di ricerca comuni, tra cui AND, OR, NOT, frase, suffisso e operatori di precedenza.

La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), che viene abilitata quando si aggiunge `queryType=full` alla richiesta, espone il linguaggio di query espressivo e ampiamente usato sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). La Sintassi completa estende la sintassi semplice. Qualsiasi query scritta per la sintassi semplice viene eseguita con il parser di Lucene full. 

Gli esempi seguenti illustrano il punto: eseguire una query, ma con impostazioni queryType diverse, genera risultati diversi. Nella prima query il metodo `^3` after `historic` viene considerato come parte del termine di ricerca. Il risultato in primo piano per questa query è "Marquis Plaza & Suites", che ha l' *oceano* nella sua descrizione.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

La stessa query che usa il parser Lucene completo interpreta `^3` come un richiamo di termine nel campo. Il passaggio dei parser comporta la modifica del rango, con risultati che contengono il termine spostamento *cronologico* verso l'alto.

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipi di query

Azure ricerca cognitiva supporta un'ampia gamma di tipi di query. 

| Tipo di query | Utilizzo | Altre informazioni ed esempi |
|------------|--------|-------------------------------|
| Ricerca di testo in formato libero | Parametro di ricerca ed entrambi i parser| La ricerca full-text scansiona uno o più termini in tutti i campi *ricercabili* dell'indice e funziona come un motore di ricerca, ad esempio Google o Bing. L'esempio nella sezione introduttiva è di ricerca full-text.<br/><br/>La ricerca full-text viene sottoposta ad analisi lessicale usando l'analizzatore Lucene standard (per impostazione predefinita) per ridurre il maiuscolo di tutti i termini, rimuovere parole non significative come "The". È possibile eseguire l'override dell'impostazione predefinita con gli [analizzatori non in lingua inglese](index-add-language-analyzers.md#language-analyzer-list) o con [analizzatori indipendenti dal linguaggio specializzati](index-add-custom-analyzers.md#AnalyzerTable) che modificano l'analisi lessicale. Ad esempio [parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) gestisce l'intero contenuto di un campo come un token singolo. Ciò è utile per i dati come i codici postali, gli ID e alcuni nomi di prodotto. | 
| Ricerca filtrata | [Espressione di filtro OData](query-odata-filter-orderby-syntax.md) ed entrambi i parser | Le query filtro valutano un'espressione booleana su tutti i campi *filtrabili* in un indice. Contrariamente alla ricerca, una query filtro corrisponde al contenuto esatto di un campo, inclusa la distinzione tra maiuscole e minuscole nei campi della stringa. Un'altra differenza è che le query filtro vengono espresse nella sintassi di OData. <br/>[Esempio di espressione filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Ricerca geografica | [Tipo Edm.Geographypoint](/rest/api/searchservice/supported-data-types) sul campo, l'espressione filtro ed entrambi i parser | Le coordinate archiviate in un campo con un Edm.geographypoint sono utilizzate per "ricerca nelle vicinanze" o per controlli di ricerca basati su mappa. <br/>[Esempio di ricerca geografica](search-query-simple-examples.md#example-5-geo-search)|
| Ricerca immagini | espressione di filtro e parser semplice | In ricerca cognitiva di Azure, le query di intervallo vengono compilate utilizzando il parametro Filter. <br/>[Esempio di filtro di intervallo](search-query-simple-examples.md#example-4-range-filters) | 
| [Ricerca nel campo](query-lucene-syntax.md#bkmk_fields) | Parametro di ricerca ed parser completo | Compilare un'espressione di query composta destinata a un singolo campo. <br/>[Esempio di ricerca nel campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [Completamento automatico o risultati suggeriti](search-autocomplete-tutorial.md) | Parametro di completamento automatico o suggerimento | Un modulo di query alternativo che viene eseguito in base alle stringhe parziali in un'esperienza di ricerca in base al tipo. È possibile utilizzare il completamento automatico e i suggerimenti insieme o separatamente. |
| [Ricerca fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parametro di ricerca ed parser completo | Corrispondenze in base alle esigenze di ortografia o di costruzione simile. <br/>[Esempio di ricerca fuzzy](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [ricerca per prossimità](query-lucene-syntax.md#bkmk_proximity) | Parametro di ricerca ed parser completo | Trova i termini vicini tra loro in un documento. <br/>[Esempio di ricerca per prossimità](search-query-lucene-examples.md#example-4-proximity-search) |
| [incremento del termine](query-lucene-syntax.md#bkmk_termboost) | Parametro di ricerca ed parser completo | Classifica un documento superiore se contiene il termine con boosting, rispetto alle altre che non lo contengono. <br/>[Esempio di aumento della priorità dei termini](search-query-lucene-examples.md#example-5-term-boosting) |
| [ricerca di espressioni regolari](query-lucene-syntax.md#bkmk_regex) | Parametro di ricerca ed parser completo | Corrispondenze in base al contenuto di un'espressione regolare. <br/>[Esempio di espressione regolare](search-query-lucene-examples.md#example-6-regex) |
|  [ricerca con caratteri jolly o prefisso](query-lucene-syntax.md#bkmk_wildcard) | Parametro di ricerca ed parser completo | Corrispondenze basate su un prefisso e una tilde (`~`) o un singolo carattere (`?`). <br/>[Esempio di ricerca con caratteri jolly](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Passaggi successivi

Usare il portale o un altro strumento, ad esempio postazione o Visual Studio Code, oppure uno degli SDK per esplorare le query in modo più approfondito. I collegamenti seguenti consentono di iniziare.

+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](./search-get-started-dotnet.md)
+ [Come eseguire query in REST](./search-get-started-powershell.md)