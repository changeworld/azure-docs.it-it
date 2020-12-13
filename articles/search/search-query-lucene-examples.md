---
title: Usa la sintassi di query Lucene completa
titleSuffix: Azure Cognitive Search
description: Sintassi di query Lucene per la ricerca fuzzy, la ricerca per prossimità, l'aumento dei termini, la ricerca di espressioni regolari e le ricerche con caratteri jolly in un servizio ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 406233fd93ca76a683cf9f9a9e857de9099705ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368546"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Usare la sintassi di ricerca Lucene "completa" (query avanzate in Azure ricerca cognitiva)

Quando si creano query per Azure ricerca cognitiva, è possibile sostituire il [parser di query semplice](query-simple-syntax.md) predefinito con il parser di query Lucene più potente [in Azure ricerca cognitiva](query-lucene-syntax.md) per formulare definizioni di query specializzate e avanzate. 

Il parser Lucene supporta costrutti di query complessi, ad esempio query con ambito campo, ricerca fuzzy, ricerca con caratteri jolly infissi e suffissi, ricerca di prossimità, aumento della priorità dei termini e ricerca di espressioni regolari. Il livello più avanzato comporta requisiti di elaborazione aggiuntivi. È pertanto opportuno prevedere un tempo di esecuzione leggermente superiore. In questo articolo è possibile esaminare gli esempi che illustrano le operazioni di query in base alla sintassi completa.

> [!Note]
> Molte delle costruzioni di query specializzate possibili attraverso la sintassi di query Lucene completa non vengono [analizzate dal punto di vista del testo](search-lucene-query-architecture.md#stage-2-lexical-analysis), fatto che può sembrare sorprendente se ci si aspetta lo stemming o la lemmatizzazione. L'analisi lessicale viene eseguita solo su termini completi, la query di un termine o di una locuzione. I tipi di query con termini incompleti, ad esempio query di prefisso, di caratteri jolly, di espressioni regolari, fuzzy, vengono aggiunte direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita su termini di query parziali è minuscole. 
>

## <a name="nyc-jobs-examples"></a>Esempi di processi NYC

Gli esempi seguenti sfruttano l' [indice di ricerca dei processi NYC](https://azjobsdemo.azurewebsites.net/)  costituito da processi disponibili in base a un set di dati fornito dalla [città di New York parte Initiative](https://nycopendata.socrata.com/). Questi dati non devono essere considerati attuali o completi. L'indice si trova in un servizio sandbox fornito da Microsoft, il che significa che non è necessaria una sottoscrizione di Azure o un ricerca cognitiva di Azure per provare queste query.

È necessario disporre di un post o di uno strumento equivalente per emettere la richiesta HTTP su GET o POST. Se non si ha familiarità con questi strumenti, vedere [Guida introduttiva: esplorare Azure ricerca cognitiva API REST](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurare la richiesta

1. Le intestazioni della richiesta devono contenere i valori seguenti:

   | Chiave | valore |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (si tratta della chiave API di query effettiva per il servizio di ricerca sandbox che ospita l'indice dei processi NYC) |

1. Impostare il verbo su **`GET`** .

1. Impostare l'URL su **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + La raccolta Documents nell'indice contiene tutti i contenuti disponibili per la ricerca. Una chiave API di query fornita nell'intestazione della richiesta funziona solo per le operazioni di lettura destinate alla raccolta Documents.

   + **`$count=true`** Restituisce un conteggio dei documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota, il conteggio sarà costituito da tutti i documenti nell'indice (circa 2558 nel caso dei processi NYC).

   + **`search=*`** query non specificata, equivalente a una ricerca vuota o null. Non è particolarmente utile, ma è la ricerca più semplice che è possibile eseguire e Mostra tutti i campi recuperabili nell'indice, con tutti i valori.

   + **`queryType=full`** richiama l'analizzatore Lucene completo.

1. Come fase di verifica, incollare la seguente richiesta in GET e fare clic su **Invia**. I risultati vengono restituiti come documenti JSON dettagliati.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Come richiamare l'analisi completa di Lucene

Aggiungere **`queryType=full`** per richiamare la sintassi di query completa, eseguendo l'override della sintassi di query semplice predefinita. Tutti gli esempi in questo articolo specificano il **`queryType=full`** parametro Search, che indica che la sintassi completa viene gestita dal parser di query Lucene. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Esempio 1: query con ambito per un elenco di campi

Questo primo esempio non è specifico del parser, ma viene presentato per primo come introduzione al primo concetto fondamentale delle query: l'indipendenza. Questo esempio limita l'esecuzione delle query e la risposta ad alcuni campi specifici. È importante sapere come strutturare una risposta JSON leggibile quando lo strumento usato è Postman o Esplora ricerche. 

Questa query è destinata solo a *business_title* in **`searchFields`** , specificando tramite il **`select`** parametro lo stesso campo nella risposta.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  ![Esempio di risposta ai punteggi](media/search-query-lucene-examples/postman-sample-results.png)

Si sarà notato il punteggio di ricerca nella risposta. I punteggi uniformi di **1** si verificano quando non esiste alcun rango, perché la ricerca non è full-text o perché non è stato fornito alcun criterio. Per una ricerca vuota, le righe vengono restituite in ordine arbitrario. Se si includono criteri effettivi, i punteggi di ricerca si convertono in valori significativi.

## <a name="example-2-fielded-search"></a>Esempio 2: ricerca nel campo

La sintassi Lucene completa supporta l'ambito di singole espressioni di ricerca in un campo specifico. Questo esempio cerca i titoli aziendali con il termine senior, ma non Junior. È possibile specificare più campi utilizzando e.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

La risposta per questa query dovrebbe avere un aspetto simile allo screenshot seguente (posting_type non viene visualizzata).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Espressione di ricerca della risposta di esempio del post" border="false":::

L'espressione di ricerca può essere costituita da una singola parola o frase o da un'espressione più complessa tra parentesi, facoltativamente con operatori booleani. Ecco alcuni esempi:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Assicurarsi di inserire più stringhe tra virgolette se si desidera che entrambe le stringhe vengano valutate come una singola entità, come in questo caso la ricerca di due posizioni distinte nel `state` campo. A seconda dello strumento, potrebbe essere necessario utilizzare caratteri di escape ( `\` ) per le virgolette. 

Il campo specificato in **FieldName: searchExpression** deve essere un campo ricercabile. Per informazioni dettagliate sull'uso degli attributi di indice nelle definizioni di campo, vedere [create index (API REST di Azure ricerca cognitiva)](/rest/api/searchservice/create-index) .

> [!NOTE]
> Nell'esempio precedente, il **`searchFields`** parametro viene omesso perché ogni parte della query ha un nome di campo specificato in modo esplicito. Tuttavia, è comunque possibile usare **`searchFields`** se la query contiene più parti (usando le istruzioni e). La query, ad esempio, `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` corrisponderà `senior NOT junior` solo al `business_title` campo, mentre corrisponderebbe a "External" con il `posting_type` campo. Il nome del campo fornito in ha `fieldName:searchExpression` sempre la precedenza su **`searchFields`** , motivo per cui in questo esempio è possibile omettere `business_title` **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Esempio 3: ricerca fuzzy

La sintassi Lucene completa supporta anche la ricerca fuzzy, basata sui termini che hanno una costruzione simile. Per eseguire una ricerca fuzzy, aggiungere il simbolo tilde `~` alla fine di una parola con un parametro facoltativo, un valore compreso tra 0 e 2, che specifica la distanza di edit. Ad esempio, `blue~` o `blue~1` restituirà blue, blues e glue.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Le frasi non sono supportate direttamente, ma è possibile specificare una corrispondenza fuzzy per ogni termine di una frase in più parti, ad esempio `search=business_title:asosiate~ AND comm~` .  Nella schermata riportata di seguito, la risposta include una corrispondenza nell' *associazione della community*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Risposta di Ricerca fuzzy" border="false":::

> [!Note]
> Le query fuzzy non vengono [analizzate](search-lucene-query-architecture.md#stage-2-lexical-analysis). I tipi di query con termini incompleti, ad esempio query di prefisso, di caratteri jolly, di espressioni regolari, fuzzy, vengono aggiunte direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita su termini di query parziali è minuscole.
>

## <a name="example-4-proximity-search"></a>Esempio 4: ricerca di prossimità

Le ricerche per prossimità vengono usate per trovare termini che si trovano vicini in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità. Ad esempio, "hotel airport"~5 troverà i termini hotel e airport entro 5 parole di distanza una dall'altra in un documento.

Questa query cerca i termini "senior" e "Analyst", dove ogni termine è separato da più di una parola e le virgolette sono precedute da un carattere di escape ( `\"` ) per mantenere la frase:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La risposta per questa query dovrebbe avere un aspetto simile allo screenshot seguente 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Query di prossimità" border="false":::

Riprovare, eliminando qualsiasi distanza ( `~0` ) tra i termini "analista senior". Si noti che vengono restituiti 8 documenti per questa query, rispetto ai 10 per la query precedente.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Esempio 5: aumento priorità termine

Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Per incrementare un termine, usare il punto di inserimento, `^` , simbolo con un fattore di incremento (un numero) alla fine del periodo di ricerca.

In questa query "before" cercare le opportunità di lavoro con il termine *computer analyst* e si noti che non vi sono risultati con le parole *computer* e *analyst*, eppure i lavori *computer* sono i primi risultati.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Nella query "after", ripetere la ricerca, questa volta aumentando la priorità dei risultati con il termine *analyst* rispetto al termine *computer* se nessuna delle due parole esiste. Una versione leggibile della query è `search=business_title:computer analyst^2` . Per una query praticabile nel post, `^2` viene codificato come `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Aumento priorità termini &quot;after&quot;" border="false":::

L'aumento priorità dei termini si differenzia dai profili di punteggio per il fatto che questi ultimi aumentano la priorità di alcuni campi e non di termini specifici. L'esempio seguente illustra le differenze.

Considerare un profilo di punteggio che migliora le corrispondenze in un determinato campo, ad esempio **genre** (genere) nell'esempio musicstoreindex. L'aumento di priorità di un termine si usa per assegnare a determinati termini di ricerca una priorità maggiore rispetto ad altri. Ad esempio, "rock^2 electronic" aumenta la priorità nell'indice dei documenti che contengono tali termini di ricerca nel campo **genre** rispetto a quelli con altri campi ricercabili. Inoltre, i documenti che contengono il termine di ricerca "rock" verranno classificati con una priorità superiore rispetto all'altro termine di ricerca "electronic" come risultato il valore di priorità del termine (2).

Quando si imposta il fattore, maggiore è il fattore di aumento, maggiore è la rilevanza del termine relativamente ad altri termini di ricerca. Per impostazione predefinita, il fattore di aumento di priorità è 1. Anche se il fattore di aumento di priorità deve essere positivo, può essere minore di 1 (ad esempio 0,2).

## <a name="example-6-regex"></a>Esempio 6: Regex

Una ricerca con espressione regolare trova una corrispondenza in base al contenuto incluso tra le barre "/", come indicato nella [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Query Regex" border="false":::

> [!Note]
> Le query Regex non vengono [analizzate](./search-lucene-query-architecture.md#stage-2-lexical-analysis). L'unica trasformazione eseguita su termini di query parziali è minuscole.
>

## <a name="example-7-wildcard-search"></a>Esempio 7: ricerca con caratteri jolly

È possibile usare una sintassi generalmente riconosciuta per ricerche con caratteri jolly per trovare più caratteri (\*) o un singolo carattere (?). Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.

In questa query cercare le opportunità di lavoro che contengono il prefisso 'prog' che include le qualifiche professionali con i termini programming e programmer. Non è possibile usare `*` un `?` simbolo o come primo carattere di una ricerca.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Query con caratteri jolly" border="false":::

> [!Note]
> Le query con caratteri jolly non vengono [analizzate](./search-lucene-query-architecture.md#stage-2-lexical-analysis). L'unica trasformazione eseguita su termini di query parziali è minuscole.
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
+ [Sintassi di query Lucene completa](query-lucene-syntax.md)
+ [Sintassi del filtro](search-query-odata-filter.md)