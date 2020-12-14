---
title: Tipi di query
titleSuffix: Azure Cognitive Search
description: Informazioni sui tipi di query supportati in ricerca cognitiva, inclusi il testo libero, il filtro, il completamento automatico e i suggerimenti, la ricerca geografica, le query di sistema e la ricerca di documenti.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 9ce0ab34aac1a3dda823c9270f4eacebfb99166f
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387667"
---
# <a name="querying-in-azure-cognitive-search"></a>Esecuzione di query in Azure ricerca cognitiva

Azure ricerca cognitiva offre un linguaggio di query avanzato per supportare un'ampia gamma di scenari, dalla ricerca di testo disponibile, ai modelli di query altamente specificati. Questo articolo riepiloga i tipi di query che è possibile creare.

In ricerca cognitiva, una query è una specifica completa di un'operazione di round trip **`search`** , con parametri che comunicano l'esecuzione delle query e formano la risposta che viene restituita. I parametri e i parser determinano il tipo di richiesta di query. L'esempio di query seguente usa i [documenti di ricerca (API REST)](/rest/api/searchservice/search-documents), che hanno come destinazione l' [Indice demo degli hotel](search-get-started-portal.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
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

Parametri utilizzati durante l'esecuzione della query:

+ **`queryType`** imposta il parser, che è il [parser di query semplice predefinito](search-query-simple-examples.md) (ottimale per la ricerca full-text) o il [parser di query Lucene completo](search-query-lucene-examples.md) usato per i costrutti di query avanzati, come le espressioni regolari, la ricerca di prossimità, la ricerca fuzzy e il carattere jolly, per citarne alcuni.

+ **`search`** fornisce i criteri di corrispondenza, in genere termini interi o frasi, con o senza operatori. Qualsiasi campo attribuito come *ricercabile* nello schema dell'indice è un candidato per questo parametro.

+ **`searchFields`** vincola l'esecuzione di query a campi ricercabili specifici.

Parametri usati per la modellazione della risposta:

+ **`select`** Specifica i campi da restituire nella risposta. In un'istruzione SELECT è possibile utilizzare solo i campi contrassegnati come *recuperabili* nell'indice.

+ **`top`** Restituisce il numero specificato di documenti con la migliore corrispondenza. In questo esempio vengono restituiti solo 10 riscontri. Per eseguire la pagina dei risultati, è possibile usare top e Skip (non mostrati).

+ **`count`** indica il numero totale di documenti nell'intero indice corrispondenti, che possono essere maggiori di quelli restituiti. 

+ **`orderby`** viene utilizzato se si desidera ordinare i risultati in base a un valore, ad esempio una classificazione o una località. In caso contrario, per impostazione predefinita viene usato il Punteggio di pertinenza per classificare i risultati. Un campo deve essere attribuito come *ordinabile* come candidato per questo parametro.

L'elenco precedente è rappresentativo ma non esaustivo. Per l'elenco completo dei parametri in una richiesta di query, vedere [cercare documenti (API REST)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipi di query

Con alcune eccezioni significative, una richiesta di query esegue l'iterazione sugli indici invertiti strutturati per le analisi veloci, in cui è possibile trovare una corrispondenza in qualsiasi campo, all'interno di un numero qualsiasi di documenti di ricerca. In ricerca cognitiva la metodologia principale per individuare le corrispondenze è la ricerca full-text o i filtri, ma è anche possibile implementare altre esperienze di ricerca note, come il completamento automatico o la ricerca geografica. Nella parte restante di questo articolo vengono riepilogate le query in ricerca cognitiva e vengono forniti collegamenti ad altre informazioni ed esempi.

## <a name="full-text-search"></a>Ricerca full-text

Se l'app di ricerca include una casella di ricerca che raccoglie gli input dei termini, la ricerca full-text è probabilmente l'operazione di query che esegue il backup dell'esperienza. La ricerca full-text accetta i termini o le frasi passati in un **`search`** parametro in tutti i campi disponibili per la *ricerca* nell'indice. Gli operatori booleani facoltativi nella stringa di query possono specificare criteri di inclusione o di esclusione. Il parser semplice e il parser completo supportano la ricerca full-text.

In ricerca cognitiva, la ricerca full-text è basata sul motore di query di Apache Lucene. Le stringhe di query nella ricerca full-text vengono sottoposte ad analisi lessicali per rendere più efficienti le analisi L'analisi include la combinazione di maiuscole e minuscole per tutti i termini, la rimozione di parole non significative e la riduzione dei termini nei form radice primitivi. L'analizzatore predefinito è Lucene standard.

Quando vengono rilevati termini corrispondenti, il motore di query ricostruisce un documento di ricerca che contiene la corrispondenza, classifica i documenti in ordine di pertinenza e restituisce i primi 50 (per impostazione predefinita) nella risposta.

Se si sta implementando una ricerca full-text, è possibile comprendere il modo in cui il contenuto viene suddiviso in token e consentire il debug di eventuali anomalie Le query su stringhe con trattini o caratteri speciali potrebbero richiedere l'uso di un analizzatore diverso dal Lucene standard predefinito per assicurarsi che l'indice contenga i token corretti. È possibile eseguire l'override dell'impostazione predefinita con gli [analizzatori di linguaggio](index-add-language-analyzers.md#language-analyzer-list) o gli [analizzatori specializzati](index-add-custom-analyzers.md#AnalyzerTable) che modificano l'analisi lessicale. Un esempio è la [parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) che tratta l'intero contenuto di un campo come singolo token. Ciò è utile per i dati come i codici postali, gli ID e alcuni nomi di prodotto. Per altre informazioni, vedere [ricerca a termini parziali e modelli con caratteri speciali](search-query-partial-matching.md).

Se si prevede un uso intensivo di operatori booleani, che è più probabile negli indici che contengono blocchi di testo di grandi dimensioni (un campo di contenuto o descrizioni lunghe), assicurarsi di testare le query con il **`searchMode=Any|All`** parametro per valutare l'effetto di tale impostazione sulla ricerca booleana.

## <a name="autocomplete-and-suggested-queries"></a>Completamento automatico e query suggerite

[I risultati di completamento automatico o suggerito](search-autocomplete-tutorial.md) sono alternative a **`search`** che attivano richieste di query successive basate su input di stringa parziali (dopo ogni carattere) in un'esperienza di ricerca in base al tipo. È possibile usare **`autocomplete`** il **`suggestions`** parametro e insieme o separatamente, come descritto in [questa esercitazione](tutorial-csharp-type-ahead-and-suggestions.md), ma non è possibile usarli con **`search`** . I termini completati e le query suggerite sono derivati dai contenuti degli indici. Il motore non restituirà mai una stringa o un suggerimento inesistente nell'indice. Per altre informazioni, vedere [completamento automatico (API REST)](/rest/api/searchservice/autocomplete) e [Suggerimenti (API REST)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtra ricerca

I filtri sono ampiamente usati nelle app che includono ricerca cognitiva. Nelle pagine dell'applicazione i filtri vengono spesso visualizzati come facet nelle strutture di navigazione dei collegamenti per i filtri indirizzati dall'utente. I filtri vengono usati anche internamente per esporre sezioni di contenuto indicizzato. Ad esempio, è possibile filtrare in una lingua se un indice contiene campi sia in inglese che in francese. 

Potrebbero inoltre essere necessari filtri per richiamare un modulo di query specializzato, come descritto nella tabella seguente. È possibile usare un filtro con una ricerca non specificata ( **`search=*`** ) o con una stringa di query che include termini, frasi, operatori e modelli.

| Scenario di filtro | Descrizione |
|-----------------|-------------|
| Filtri di intervallo | In ricerca cognitiva di Azure, le query di intervallo vengono compilate utilizzando il parametro Filter. Per ulteriori informazioni ed esempi, vedere [esempio di filtro di intervallo](search-query-simple-examples.md#example-4-range-filters). |
| Ricerca geografica | Se un campo ricercabile è di [tipo EDM. GeographyPoint](/rest/api/searchservice/supported-data-types), è possibile creare un'espressione di filtro per i controlli di ricerca "trova nelle vicinanze" o per la ricerca basata su mapping. I campi che guidano la ricerca geografica contengono le coordinate. Per altre informazioni e un esempio, vedere [esempio di ricerca geografica](search-query-simple-examples.md#example-5-geo-search). |
| Esplorazione in base a facet | Una struttura di esplorazione basata su facet diventa strumentale nell'esplorazione indirizzata dall'utente quando si richiama un filtro in risposta a un `onclick` evento in un facet. Di conseguenza, i facet e i filtri vengono usati in modo manuale. Se si aggiunge l'esplorazione facet, sarà necessario disporre di filtri per completare l'esperienza. Per ulteriori informazioni, vedere [la pagina relativa alla modalità di compilazione di un filtro facet](search-filters-facets.md). |

> [!NOTE]
> Il testo utilizzato in un'espressione di filtro non viene analizzato durante l'elaborazione della query. Si presuppone che l'input di testo sia un modello di carattere Verbatim con distinzione tra maiuscole e minuscole che ha esito positivo o negativo sulla corrispondenza. Le espressioni di filtro vengono costruite usando la [sintassi OData](query-odata-filter-orderby-syntax.md) e passate un **`filter`** parametro in tutti i campi *filtrabili* nell'indice. Per altre informazioni, vedere [filtri in ricerca cognitiva di Azure](search-filters.md).

## <a name="document-look-up"></a>Ricerca di documenti

A differenza dei moduli di query descritti in precedenza, questo recupera un singolo [documento di ricerca in base all'ID](/rest/api/searchservice/lookup-document), senza ricerca o analisi dell'indice corrispondente. Viene richiesto e restituito solo un documento. Quando un utente seleziona un elemento nei risultati della ricerca, il recupero del documento e il popolamento di una pagina di dettagli con i campi è una risposta tipica e la ricerca di un documento è l'operazione che la supporta.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Ricerca avanzata: fuzzy, wildcard, prossimità, Regex

Un modulo di query avanzato dipende dal parser Lucene completo e dagli operatori che attivano un comportamento di query specifico.

| Tipo di query | Utilizzo | Altre informazioni ed esempi |
|------------|--------|------------------------------|
| [Ricerca nel campo](query-lucene-syntax.md#bkmk_fields) | **`search`**  parametro **`queryType=full`**  | Compilare un'espressione di query composta destinata a un singolo campo. <br/>[Esempio di ricerca nel campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [Ricerca fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** parametro **`queryType=full`** | Corrispondenze in base alle esigenze di ortografia o di costruzione simile. <br/>[Esempio di ricerca fuzzy](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [ricerca per prossimità](query-lucene-syntax.md#bkmk_proximity) | **`search`** parametro **`queryType=full`** | Trova i termini vicini tra loro in un documento. <br/>[Esempio di ricerca per prossimità](search-query-lucene-examples.md#example-4-proximity-search) |
| [incremento del termine](query-lucene-syntax.md#bkmk_termboost) | **`search`** parametro **`queryType=full`** | Classifica un documento superiore se contiene il termine con boosting, rispetto alle altre che non lo contengono. <br/>[Esempio di aumento della priorità dei termini](search-query-lucene-examples.md#example-5-term-boosting) |
| [ricerca di espressioni regolari](query-lucene-syntax.md#bkmk_regex) | **`search`** parametro **`queryType=full`** | Corrispondenze in base al contenuto di un'espressione regolare. <br/>[Esempio di espressione regolare](search-query-lucene-examples.md#example-6-regex) |
|  [ricerca con caratteri jolly o prefisso](query-lucene-syntax.md#bkmk_wildcard) | **`search`** con * *_`~`_* o **`?`** , **`queryType=full`**| Corrispondenze basate su un prefisso e una tilde (`~`) o un singolo carattere (`?`). <br/>[Esempio di ricerca con caratteri jolly](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Passaggi successivi

Per un'analisi più approfondita dell'implementazione di query, vedere gli esempi per ogni sintassi. Se non si ha familiarità con la ricerca full-text, un'occhiata più approfondita del motore di query potrebbe essere una scelta altrettanto adatta.

+ [Esempi di query semplici](search-query-simple-examples.md)
+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)