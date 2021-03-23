---
title: Sintassi di query Lucene
titleSuffix: Azure Cognitive Search
description: Riferimento per la sintassi di query Lucene completa, come usato in Azure ricerca cognitiva per il carattere jolly, la ricerca fuzzy, RegEx e altri costrutti di query avanzati.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801588"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintassi di query Lucene in Azure ricerca cognitiva

Quando si creano query, è possibile optare per la sintassi del [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) per i moduli di query specializzati: caratteri jolly, ricerca fuzzy, ricerca di prossimità, espressioni regolari. Gran parte della sintassi del parser di query Lucene è stata [implementata intatto in Azure ricerca cognitiva](search-lucene-query-architecture.md), ad eccezione delle *ricerche di intervallo* costruite tramite **`$filter`** espressioni. 

Per usare la sintassi Lucene completa, è necessario impostare queryType su "full" e passare un'espressione di query con pattern per il carattere jolly, la ricerca fuzzy o uno degli altri moduli di query supportati dalla sintassi completa. In REST, le espressioni di query vengono fornite nel **`search`** parametro di una richiesta di [ricerca di documenti (API REST)](/rest/api/searchservice/search-documents) .

## <a name="example-full-syntax"></a>Esempio (sintassi completa)

L'esempio seguente è una richiesta di ricerca costruita usando la sintassi completa. Questo particolare esempio mostra la ricerca nel campo e l'incremento di termini. Cerca gli hotel in cui il campo categoria contiene il termine "budget". Tutti i documenti contenenti la frase "rinnovata di recente" vengono classificati più in alto a causa del valore Boost del termine (3).  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

Sebbene non sia specifico di un tipo di query, il **`searchMode`** parametro è pertinente in questo esempio. Quando nella query sono presenti operatori, è in genere consigliabile impostare `searchMode=all` per assicurarsi che venga trovata una corrispondenza per *tutti* i criteri.  

Per altri esempi, vedere [esempi di sintassi di query Lucene](search-query-lucene-examples.md). Per informazioni dettagliate sui parametri e la richiesta di query, tra cui searchMode, vedere [eseguire ricerche nei documenti (API REST)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Nozioni fondamentali sulla sintassi  

Le nozioni fondamentali seguenti sulla sintassi si applicano a tutte le query che usano la sintassi Lucene.  

### <a name="operator-evaluation-in-context"></a>Valutazione degli operatori nel contesto

Il posizionamento determina se un simbolo viene interpretato come operatore o semplicemente come un altro carattere in una stringa.

Nella sintassi Lucene completa, ad esempio, la tilde (~) viene usata sia per la ricerca fuzzy che per la ricerca per prossimità. Quando viene inserita dopo una frase tra virgolette, ~ richiama la ricerca per prossimità. Quando viene inserita alla fine di un termine, ~ richiama la ricerca fuzzy.

All'interno di un termine, ad esempio "business~analyst", il carattere non viene valutato come operatore. In questo caso, supponendo che la query sia relativa a un termine o a una frase, la [ricerca full-text](search-lucene-query-architecture.md) con l'[analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) elimina il carattere ~ e suddivide il termine "business~analyst" in due: business OR analyst.

L'esempio precedente è relativo alla tilde (~), ma lo stesso principio si applica a ogni operatore.

### <a name="escaping-special-characters"></a>Escape dei caratteri speciali

Per usare uno degli operatori di ricerca come parte del testo di ricerca, eseguire l'escape del carattere anteponendo una singola barra rovesciata ( `\` ). Ad esempio, per una ricerca con caratteri jolly su `https://` , dove `://` fa parte della stringa di query, è necessario specificare `search=https\:\/\/*` . Analogamente, un modello di numero di telefono con escape potrebbe essere simile al seguente `\+1 \(800\) 642\-7676` .

I caratteri speciali che richiedono l'escape includono quanto segue:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Sebbene l'escape mantenga i token insieme, l' [analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante l'indicizzazione può essere eliminata. Ad esempio, l'analizzatore Lucene standard interrompe le parole su trattini, spazi vuoti e altri caratteri. Se sono necessari caratteri speciali nella stringa di query, potrebbe essere necessario un analizzatore che li conserva nell'indice. Alcune opzioni includono gli [analizzatori del linguaggio](index-add-language-analyzers.md)naturale Microsoft, che conserva le parole con trattino o un analizzatore personalizzato per modelli più complessi. Per ulteriori informazioni, vedere [termini parziali, modelli e caratteri speciali](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codifica dei caratteri riservati e non sicuri negli URL

Assicurarsi che tutti i caratteri riservati e non sicuri siano codificati in un URL. Ad esempio,' #' è un carattere non sicuro perché è un identificatore di frammento/ancoraggio in un URL. Il carattere deve essere codificato al `%23`, se usato in un URL. ' &' è =' sono esempi di caratteri riservati in quanto delimitano i parametri e specificano i valori in Azure ricerca cognitiva. Per altri dettagli, vedere [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

I caratteri non sicuri sono ``" ` < > # % { } | \ ^ ~ [ ]``. I caratteri riservati sono `; / ? : @ = + &`.

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Operatori booleani

È possibile incorporare gli operatori booleani in una stringa di query per migliorare la precisione di una corrispondenza. La sintassi completa supporta gli operatori di testo oltre agli operatori di caratteri. Specificare sempre gli operatori booleani di testo (AND, OR, NOT) in lettere tutte maiuscole.

|Operatore di testo | Carattere | Esempio | Utilizzo |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | Specifica i termini che una corrispondenza deve contenere. Nell'esempio, il motore di query cercherà i documenti contenenti sia `wifi` che `luxury` . Il carattere più ( `+` ) viene usato per i termini richiesti. Ad esempio, `+wifi +luxury` stabilisce che entrambi i termini devono essere presenti nel campo di un singolo documento.|
| OR | `|` | `wifi | luxury` | Trova una corrispondenza quando viene trovato uno o più termini. Nell'esempio, il motore di query restituirà match nei documenti contenenti `wifi` o `luxury` o entrambi. Poiché OR è l'operatore di congiunzione predefinito, è anche possibile escluderlo, in modo che `wifi luxury` sia l'equivalente di `wifi | luxury`.|
| NOT | `!`, `-` | `wifi –luxury` | Restituisce le corrispondenze nei documenti che escludono il termine. Ad esempio, `wifi –luxury` cercherà i documenti con il `wifi` termine ma non `luxury` . <br/><br/>Il `searchMode` parametro in una richiesta di query controlla se un termine con l'operatore Not è individuato o ORed con altri termini nella query (presupponendo che non esista un `+` `|` operatore OR su altri termini). I valori validi sono `any` o `all`.  <br/><br/>`searchMode=any` aumenta il richiamo delle query includendo più risultati e per impostazione predefinita `-` viene interpretato come "o not". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` o quelli non contenenti il termine `luxury`.  <br/><br/>`searchMode=all` aumenta la precisione delle query includendo un minor numero di risultati e, per impostazione predefinita, viene interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine "luxury". Si tratta di un comportamento verosimilmente più intuitivo per l'operatore `-`. Pertanto, è consigliabile utilizzare `searchMode=all` anziché `searchMode=any` se si desidera ottimizzare la ricerca di precisione anziché richiamare *e* gli utenti utilizzano spesso l' `-` operatore nelle ricerche.<br/><br/>Quando si decide di eseguire un' `searchMode` impostazione, prendere in considerazione i modelli di interazione utente per le query in varie applicazioni. È più probabile che gli utenti che eseguono la ricerca di informazioni includano un operatore in una query, anziché i siti di e-commerce che dispongono di più strutture di navigazione predefinite. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Ricerca nel campo

È possibile definire un'operazione di ricerca in campo con la `fieldName:searchExpression` sintassi, in cui l'espressione di ricerca può essere una singola parola o una frase o un'espressione più complessa tra parentesi, facoltativamente con gli operatori booleani. Ecco alcuni esempi:  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

Assicurarsi di inserire più stringhe racchiuse tra virgolette se si vuole che entrambe le stringhe siano valutate come una singola entità, in questo caso per la ricerca di due artisti distinti nel campo `artists`.  

Il campo specificato in `fieldName:searchExpression` deve essere un campo `searchable`.  Per informazioni dettagliate sull'uso di attributi dell'indice nelle definizioni campo, vedere [Create Index](/rest/api/searchservice/create-index) (Creare l'indice).  

> [!NOTE]
> Quando si usano le espressioni di ricerca nel campo, non è necessario usare il `searchFields` parametro perché ogni espressione di ricerca nel campo ha un nome di campo specificato in modo esplicito. Tuttavia, è comunque possibile utilizzare il `searchFields` parametro se si desidera eseguire una query in cui alcune parti hanno come ambito un campo specifico e il resto può essere applicato a più campi. Ad esempio, la query `search=genre:jazz NOT history&searchFields=description` corrisponderà `jazz` solo al `genre` campo, mentre corrisponderebbe al `NOT history` `description` campo. Il nome del campo fornito in ha `fieldName:searchExpression` sempre la precedenza sul `searchFields` parametro, motivo per cui in questo esempio non è necessario includere `genre` nel `searchFields` parametro.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Ricerca fuzzy

Una ricerca fuzzy trova le corrispondenze in termini che hanno una costruzione simile, espandendo un termine fino al massimo di 50 termini che soddisfano i criteri di distanza di due o meno. Per ulteriori informazioni, vedere [ricerca fuzzy](search-query-fuzzy.md).

Per eseguire una ricerca fuzzy, usare il carattere tilde "~" alla fine di una singola parola con un parametro facoltativo, un numero compreso tra 0 e 2 (impostazione predefinita), che specifica il numero minimo di operazioni di modifica o "edit distance". Ad esempio, "blue~" o "blue~1" restituirà "blue", "blues" e "glue".

La ricerca fuzzy può essere applicata solo a termini, non a frasi, ma è possibile aggiungere la tilde a ogni termine singolarmente in un nome in più parti o in una frase. Ad esempio, "Unviersty ~ di ~" wSHINGTON ~ "corrisponderà a" University of Washington ".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Ricerca vicina

Le ricerche per prossimità vengono usate per trovare termini che si trovano vicini in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità. Ad esempio, `"hotel airport"~5` troverà i termini "hotel" e "airport" entro 5 parole di distanza una dall'altra in un documento.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Incremento del termine

Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Questo comportamento è diverso dall'assegnazione di punteggi ai profili, perché questo metodo assegna priorità ad alcuni campi, invece che a termini specifici.  

L'esempio seguente illustra le differenze. Si supponga che esista un profilo di punteggio che migliora le corrispondenze in un determinato campo, ad esempio *genre* (genere) nell'[esempio musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). L'aumento di priorità di un termine si usa per assegnare a determinati termini di ricerca una priorità maggiore rispetto ad altri. Ad esempio, `rock^2 electronic` aumenta la priorità nell'indice dei documenti che contengono tali termini di ricerca nel campo genre rispetto a quelli con altri campi ricercabili. Inoltre, i documenti che contengono il termine di ricerca *rock* verranno classificati con una priorità superiore rispetto all'altro termine di ricerca *electronic* come risultato del valore di priorità del termine (2).  

 Per aumentare la priorità di un termine, usare il carattere accento circonflesso "^", con un fattore di aumento di priorità (un numero) alla fine del termine da cercare. È anche possibile aumentare la priorità delle frasi. Maggiore è il fattore di aumento di priorità, più rilevante sarà il termine rispetto ad altri termini di ricerca. Per impostazione predefinita, il fattore di aumento di priorità è 1. Anche se il fattore di aumento di priorità deve essere positivo, può essere minore di 1 (ad esempio 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Ricerca basata su espressioni regolari  
 Una ricerca di espressioni regolari trova una corrispondenza in base ai modelli validi in Apache Lucene, come documentato nella [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). In ricerca cognitiva di Azure, un'espressione regolare è racchiusa tra barre `/` .

 Ad esempio, per trovare i documenti contenenti "motel" o "hotel", specificare `/[mh]otel/`. Le ricerche basate su espressioni regolari vengono confrontate con parole singole.

Alcuni strumenti e linguaggi impongono requisiti di caratteri di escape aggiuntivi. Per JSON, le stringhe che includono una barra vengono precedute da un carattere di escape con una barra rovesciata: "microsoft.com/azure/" diventa `search=/.*microsoft.com\/azure\/.*/` dove `search=/.* <string-placeholder>.*/` imposta l'espressione regolare e `microsoft.com\/azure\/` è la stringa con una barra con caratteri di escape.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Ricerca con caratteri jolly

È possibile utilizzare la sintassi generalmente riconosciuta per più `*` ricerche con caratteri jolly () o Single ( `?` ). Ad esempio, un'espressione di query `search=alpha*` restituisce "alfanumerico" o "alfabetico". Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.

La sintassi Lucene completa supporta la corrispondenza con prefisso, infisso e suffisso. Tuttavia, se è sufficiente la corrispondenza con prefisso, è possibile usare la sintassi semplice (la corrispondenza dei prefissi è supportata in entrambi).

La corrispondenza dei suffissi, dove `*` o `?` precede la stringa (come in `search=/.*numeric./` ) o la corrispondenza degli infissi richiede la sintassi Lucene completa, nonché i delimitatori di barra delle espressioni regolari `/` . Non è possibile usare un carattere * o ? simbolo come primo carattere di un termine, o all'interno di un termine, senza il `/` . 

> [!NOTE]  
> Di norma, la corrispondenza dei modelli è lenta, quindi è consigliabile esaminare metodi alternativi, ad esempio la suddivisione in token di n-grammi perimetrale che crea token per sequenze di caratteri in un termine. L'indice sarà più grande, ma le query potrebbero essere eseguite più velocemente, a seconda della costruzione del modello e della lunghezza delle stringhe da indicizzare.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Conseguenze di un analizzatore sulle query con caratteri jolly

Durante l'analisi delle query, le query formulate come prefisso, suffisso, carattere jolly o espressioni regolari vengono passate così come sono all'albero delle query, ignorando l' [analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis). Le corrispondenze verranno trovate solo se l'indice contiene le stringhe nel formato specificato dalla query. Nella maggior parte dei casi, è necessario un analizzatore durante l'indicizzazione che conserva l'integrità delle stringhe in modo che i termini parziali e i criteri di ricerca abbiano esito positivo. Per altre informazioni, vedere [ricerca dei termini parziali in Azure ricerca cognitiva query](search-query-partial-matching.md).

Si consideri una situazione in cui è possibile che la query di ricerca ' terminate *' restituisca risultati che contengono termini quali ' terminate ',' Termination ' è terminates '.

Se si utilizza l'analizzatore en. Lucene (English Lucene), viene applicato lo stemming aggressivo di ogni termine. Ad esempio,' terminate ',' Termination ',' terminates ' verrà tutti suddivisi in token nel token ' termi ' nell'indice. Dall'altro lato, i termini nelle query che usano caratteri jolly o la ricerca fuzzy non vengono analizzati. Pertanto, non ci sono risultati corrispondenti alla query ' terminate *'.

Dall'altro lato, gli analizzatori Microsoft (in questo caso, en. Microsoft Analyzer) sono un po' più avanzati e usano lemmatizzazione anziché lo stemming. Ciò significa che tutti i token generati devono essere parole in lingua inglese valide. Ad esempio,' terminate ',' terminazioni ' è terminazione ' rimarranno per intero nell'indice e sarebbero una scelta preferibile per gli scenari che dipendono molto da caratteri jolly e ricerca fuzzy.

## <a name="scoring-wildcard-and-regex-queries"></a> Punteggio delle query con caratteri jolly e regex

Azure ricerca cognitiva usa il punteggio in base alla frequenza ([tf-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) per le query di testo. Per le query con caratteri jolly e regex, in cui l'ambito dei termini può essere potenzialmente ampio, il fattore frequenza viene tuttavia ignorato per evitare che la classificazione privilegi le corrispondenze con termini più rari. Tutte le corrispondenze vengono trattate equamente per le ricerche con caratteri jolly e regex.

## <a name="special-characters"></a>Caratteri speciali

In alcuni casi, è possibile cercare un carattere speciale, ad esempio un'❤' emoji o il segno ' €'. In questi casi, assicurarsi che l'analizzatore usato non filtri tali caratteri. L'analizzatore standard ignora molti caratteri speciali, esclusi dall'indice.

Gli analizzatori che tokenize i caratteri speciali includono l'analizzatore "spazio", che prende in considerazione le sequenze di caratteri separate da spazi vuoti come token (pertanto la stringa "❤" verrebbe considerata un token). Inoltre, un analizzatore del linguaggio come Microsoft English Analyzer ("en. Microsoft") accetta la stringa "€" come token. È possibile [testare un analizzatore](/rest/api/searchservice/test-analyzer) per visualizzare i token generati per una query specifica.

Quando si usano i caratteri Unicode, assicurarsi che i simboli siano preceduti da un carattere di escape nell'URL della query (ad esempio per "❤" utilizzerà la sequenza di escape `%E2%9D%A4+` ). Postazione esegue automaticamente questa conversione.  

## <a name="precedence-grouping"></a>Precedenza (raggruppamento)

È possibile usare le parentesi per creare sottoquery, inclusi gli operatori nell'istruzione tra parentesi. Ad esempio, `motel+(wifi|luxury)` cercherà i documenti contenenti i termini "motel" e "wifi" o "luxury" (oppure entrambi).

Il raggruppamento di campi è simile, ma definisce un singolo campo come ambito del raggruppamento. Ad esempio, `hotelAmenities:(gym+(wifi|pool))` cerca "gym" e "wifi" oppure "gym" e "pool" nel campo "hotelAmenities".  

## <a name="query-size-limits"></a>Limiti delle dimensioni delle query

È previsto un limite per le dimensioni delle query che è possibile inviare al ricerca cognitiva di Azure. In particolare, è possibile avere al massimo 1024 clausole (espressioni separate da AND, OR e così via). È previsto anche un limite di circa 32 KB per la dimensione di ogni singolo termine di una query. Se l'applicazione genera query di ricerca a livello di codice, è consigliabile progettarla in modo che non generi query di dimensioni illimitate.  

## <a name="see-also"></a>Vedi anche

+ [Esempi di query per la ricerca semplice](search-query-simple-examples.md)
+ [Esempi di query per la ricerca Lucene completa](search-query-lucene-examples.md)
+ [ricerca documenti](/rest/api/searchservice/Search-Documents)
+ [Sintassi delle espressioni OData per filtri e ordinamento](query-odata-filter-orderby-syntax.md)   
+ [Sintassi di query semplice in Azure ricerca cognitiva](query-simple-syntax.md)
