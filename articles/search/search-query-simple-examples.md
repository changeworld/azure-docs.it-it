---
title: Usare la sintassi di query Lucene semplice
titleSuffix: Azure Cognitive Search
description: Per informazioni sull'esecuzione di query in base alla semplice sintassi per la ricerca full-text, la ricerca di filtri, la ricerca geografica e la ricerca in base a un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: 51d36211c7ffa0507a186c9a1e1f2b52d478fe4e
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369098"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Usare la sintassi di ricerca "Simple" in Azure ricerca cognitiva

In ricerca cognitiva di Azure, la [sintassi di query semplice](query-simple-syntax.md) richiama il parser di query predefinito per la ricerca full-text. Il parser è veloce e gestisce scenari comuni, tra cui ricerca full-text, ricerca filtrata e sfaccettata e ricerca di prefisso. Questo articolo usa esempi per illustrare l'utilizzo semplice della sintassi in una richiesta di [ricerca di documenti (API REST)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Una sintassi di query alternativa è [Lucene completa](query-lucene-syntax.md), che supporta strutture di query più complesse, come la ricerca fuzzy e la ricerca con caratteri jolly. Per altre informazioni ed esempi, vedere [usare la sintassi Lucene completa](search-query-lucene-examples.md).

## <a name="nyc-jobs-examples"></a>Esempi di processi NYC

Gli esempi seguenti sfruttano l' [indice di ricerca dei processi NYC](https://azjobsdemo.azurewebsites.net/) costituito da processi disponibili in base a un set di dati fornito dalla [città di New York parte Initiative](https://nycopendata.socrata.com/). Questi dati non devono essere considerati attuali o completi. L'indice si trova in un servizio sandbox fornito da Microsoft, il che significa che non è necessaria una sottoscrizione di Azure o un ricerca cognitiva di Azure per provare queste query.

È necessario disporre di un post o di uno strumento equivalente per emettere la richiesta HTTP su GET o POST. Se non si ha familiarità con questi strumenti, vedere [Guida introduttiva: esplorare Azure ricerca cognitiva API REST](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurare la richiesta

1. Le intestazioni della richiesta devono contenere i valori seguenti:

   | Chiave | valore |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (si tratta della chiave API di query effettiva per il servizio di ricerca sandbox che ospita l'indice dei processi NYC) |

1. Impostare il verbo su **`GET`** .

1. Impostare l'URL su **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + La raccolta Documents nell'indice contiene tutti i contenuti disponibili per la ricerca. La chiave API di query fornita nell'intestazione della richiesta funziona solo per le operazioni di lettura destinate alla raccolta Documents.

   + **`$count=true`** Restituisce un conteggio dei documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota, il conteggio sarà costituito da tutti i documenti nell'indice (circa 2558 nel caso dei processi NYC).

   + **`search=*`** query non specificata, equivalente a una ricerca vuota o null. Non è particolarmente utile, ma è la ricerca più semplice che è possibile eseguire e Mostra tutti i campi recuperabili nell'indice, con tutti i valori.

1. Come fase di verifica, incollare la seguente richiesta in GET e fare clic su **Invia**. I risultati vengono restituiti come documenti JSON dettagliati.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Come richiamare l'analisi di query semplice

Per le query interattive, non è necessario specificare nulla: la sintassi semplice è quella predefinita. Nel codice, se in precedenza è stato richiamato **`queryType=full`** , è possibile reimpostare il valore predefinito con **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Esempio 1: ricerca full-text in campi specifici

Questo primo esempio non è specifico del parser, ma viene presentato per primo come introduzione al primo concetto fondamentale delle query: l'indipendenza. Questo esempio limita l'esecuzione delle query e la risposta ad alcuni campi specifici. È importante sapere come strutturare una risposta JSON leggibile quando lo strumento usato è Postman o Esplora ricerche. 

Questa query è destinata solo a *business_title* in **`searchFields`** , specificando tramite il **`select`** parametro lo stesso campo nella risposta.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Risposta di esempio di Postman" border="false":::

Si sarà notato il punteggio di ricerca nella risposta. I punteggi uniformi di **1** si verificano quando non esiste alcun rango, perché la ricerca non è full-text o perché non è stato fornito alcun criterio. Per una ricerca vuota, le righe vengono restituite in ordine arbitrario. Se si includono criteri effettivi, i punteggi di ricerca si convertono in valori significativi.

## <a name="example-2-look-up-by-id"></a>Esempio 2: ricerca per ID

Quando si restituiscono i risultati della ricerca in una query, un passaggio logico successivo consiste nel fornire una pagina di dettagli che includa più campi dal documento. Questo esempio Mostra come restituire un singolo documento usando un'operazione di [ricerca](/rest/api/searchservice/lookup-document) per passare l'ID del documento.

Tutti i documenti dispongono di un identificatore unico. Per provare la sintassi per una query di ricerca, restituire innanzitutto un elenco di ID documento per trovarne uno da usare. Per NYC Jobs, gli identificatori sono archiviati nel campo `id`.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Recuperare quindi un documento dalla raccolta in base a `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", che è comparso per primo nella risposta precedente. La query seguente restituisce tutti i campi recuperabili per l'intero documento.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Esempio 3: query filtro

La [sintassi del filtro](./search-query-odata-filter.md) è un'espressione OData che è possibile utilizzare autonomamente o con **`search`** . Un filtro autonomo, senza un parametro di ricerca, è utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio (tutti i punteggi corrispondono a 1) e una classificazione. Si noti che la stringa di ricerca è vuota.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Usati insieme, il filtro viene applicato per primo all'intero indice, quindi la ricerca viene eseguita sui risultati del filtro. I filtri quindi possono essere un'utile tecnica per migliorare le prestazioni delle query perché riducono il set di documenti che la query di ricerca deve elaborare.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrare le risposte della query" border="false":::

Un altro modo efficace per combinare il filtro e la ricerca avviene tramite **`search.ismatch*()`** un'espressione di filtro, in cui è possibile usare una query di ricerca all'interno del filtro. L'espressione filtro usa un carattere jolly nell'elemento *plan* per selezionare business_title includendo i termini plan, planner, planning, e così via.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Per altre informazioni sulla funzione, vedere [search.ismatch in "Esempi di filtro"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Esempio 4: filtri di intervallo

Il filtro di intervallo è supportato tramite **`$filter`** espressioni per qualsiasi tipo di dati. Gli esempi seguenti eseguono la ricerca nei campi numerici e nei campi stringa. 

I tipi di dati sono importanti nei filtri di intervallo e funzionano in modo ottimale quando i dati numerici si trovano nei campi numerici e i dati di tipo stringa nei campi stringa. I dati numerici nei campi stringa non sono adatti per gli intervalli perché le stringhe numeriche non sono confrontabili in ricerca cognitiva di Azure.

La query seguente è un intervallo numerico:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtro di intervallo per intervalli numerici" border="false":::

In questa query l'intervallo è posizionato su un campo stringa (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtro di intervallo per intervalli di testo" border="false":::

> [!NOTE]
> L'esplorazione in base a facet su intervalli di valori è un requisito comune delle applicazioni di ricerca. Per ulteriori informazioni ed esempi, vedere [la pagina relativa alla modalità di compilazione di un filtro facet](search-filters-facets.md).

## <a name="example-5-geo-search"></a>Esempio 5: ricerca geografica

L'indice degli esempi include un campo geo_location con le coordinate di latitudine e longitudine. Questo esempio usa la [funzione geo.distance](search-query-odata-geo-spatial-functions.md#examples) che applica il filtro ai documenti all'interno della circonferenza di un punto di partenza, fino a una distanza arbitraria (in chilometri) specificata. È possibile modificare l'ultimo valore nella query (4) per ridurre o aumentare l'area della query.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Per ottenere risultati più leggibili, i risultati della ricerca vengono tagliati per includere il titolo del processo e il percorso di lavoro. Le coordinate di inizio sono state ottenute da un documento casuale nell'indice (in questo caso, per una località di lavoro a Staten Island).

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Mappa di Staten Island" border="false":::

## <a name="example-6-search-precision"></a>Esempio 6: precisione della ricerca

Le query basate su termini sono composte da singoli termini valutati separatamente. Le query basate su frasi sono racchiuse tra virgolette e vengono valutate come stringhe verbatim. La precisione della corrispondenza è controllata da operatori e searchMode.

Esempio 1: `search=fire`  corrisponde ai risultati 140, in cui tutte le corrispondenze contengono la parola Fire in qualche punto del documento.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Esempio 2: `search=fire department` restituisce 2002 risultati. Vengono restituite corrispondenze per i documenti contenenti fire o department.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Esempio 3: `search="fire department"` restituisce 77 risultati. Racchiudendo la stringa tra virgolette si crea una ricerca di frasi costituita da entrambi i termini e le corrispondenze si trovano nei termini in formato token nell'indice costituito dai termini combinati. Questo spiega perché una ricerca come `search=+fire +department` non è equivalente. Entrambi i termini sono necessari, ma vengono analizzati in modo indipendente. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Poiché una query di frase viene specificata tramite virgolette, in questo esempio viene aggiunto un carattere di escape ( `\` ) per mantenere la sintassi.

## <a name="example-7-booleans-with-searchmode"></a>Esempio 7: valori booleani con searchMode

La sintassi semplice supporta gli operatori booleani sotto forma di caratteri (`+, -, |`). Il parametro searchMode informa i compromessi tra precisione e richiamo, **`searchMode=any`** che favorisce il richiamo (la corrispondenza in base a qualsiasi criterio qualifica un documento per il set di risultati) e **`searchMode=all`** privilegia la precisione (tutti i criteri devono corrispondere). 

Il valore predefinito è **`searchMode=any`** , che può essere confuso se si sta impilando una query con più operatori e ottenendo risultati più ampi anziché più ristretti. Questo è particolarmente vero con NOT, dove i risultati includono tutti i documenti che "non contengono" un termine specifico.

Usando il valore predefinito searchMode (any), vengono restituiti i documenti 2800: quelli che contengono la frase "Fire Department", più tutti i documenti che non hanno la frase "Metrotech Center".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="modalità di ricerca = qualsiasi" border="false":::

La modifica di per **`searchMode=all`** impone un effetto cumulativo sui criteri e restituisce un set di risultati più piccolo, ovvero 21 documenti, costituito da documenti che contengono l'intera frase "Fire Department", meno quei processi all'indirizzo Metrotech Center.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="modalità di ricerca = tutto" border="false":::

## <a name="example-8-structuring-results"></a>Esempio 8: strutturazione dei risultati

Più parametri determinano i campi presenti nei risultati di ricerca, il numero di documenti restituiti in ciascun batch e l'ordinamento. Questo esempio illustra alcuni esempi precedenti, limitando i risultati a campi specifici usando l' **`$select`** istruzione e i criteri di ricerca Verbatim, restituendo 82 corrispondenze.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Aggiunto all'esempio precedente, è possibile ordinare in base al titolo. Questo ordinamento funziona perché civil_service_title è *ordinabile* nell'indice.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Il paging dei risultati viene implementato utilizzando il **`$top`** parametro, in questo caso restituendo i primi 5 documenti:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Per ottenere i successivi 5, ignorare il primo batch:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>Passaggi successivi

Provare a specificare le query nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca usando gli Azure SDK.

+ [Eseguire query sull'indice usando .NET SDK](search-get-started-dotnet.md)
+ [Eseguire query sull'indice con Python SDK](search-get-started-python.md)
+ [Eseguire query sull'indice tramite JavaScript SDK](search-get-started-javascript.md)

Un riferimento alla sintassi aggiuntivo, l'architettura di query ed esempi sono disponibili nei collegamenti seguenti:

+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Sintassi di query semplice](query-simple-syntax.md)
+ [Sintassi di query Lucene completa](query-lucene-syntax.md)
+ [Sintassi del filtro](search-query-odata-filter.md)