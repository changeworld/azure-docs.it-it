---
title: Sintassi di query semplice
titleSuffix: Azure Cognitive Search
description: Informazioni di riferimento per la sintassi di query semplice utilizzata per le query di ricerca full-text in Azure ricerca cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516581"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintassi di query semplice in Azure ricerca cognitiva

Azure ricerca cognitiva implementa due linguaggi di query basati su Lucene: il parser di query [semplice](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e il [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Il parser semplice è più flessibile e tenterà di interpretare una richiesta anche se non è perfettamente composta. Grazie a questa flessibilità, si tratta dell'impostazione predefinita per le query in Azure ricerca cognitiva.

La sintassi semplice viene usata per le espressioni di query passate nel **`search`** parametro di una richiesta di [ricerca di documenti (API REST)](/rest/api/searchservice/search-documents) , da non confondere con la [sintassi OData](query-odata-filter-orderby-syntax.md) usata per le [**`$filter`**](search-filters.md) [**`$orderby`**](search-query-odata-orderby.md) espressioni e nella stessa richiesta. I parametri OData hanno sintassi e regole diverse per la costruzione di query, l'escape di stringhe e così via.

Sebbene il parser semplice sia basato sulla classe del [parser di query semplice di Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , l'implementazione in ricerca cognitiva esclude la ricerca fuzzy. Se è necessaria la [ricerca fuzzy](search-query-fuzzy.md), considerare invece la [sintassi di query Lucene completa](query-lucene-syntax.md) alternativa.

## <a name="example-simple-syntax"></a>Esempio (sintassi semplice)

Sebbene **`queryType`** sia impostato di seguito, è il valore predefinito e può essere omesso a meno che non si ritorni da un tipo alternativo. Nell'esempio seguente viene illustrata una ricerca su termini indipendenti, con il requisito che tutti i documenti corrispondenti includano "pool".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

Il **`searchMode`** parametro è pertinente in questo esempio. Ogni volta che gli operatori booleani sono nella query, è necessario impostare in genere `searchMode=all` per assicurarsi che *tutti* i criteri corrispondano. In caso contrario, è possibile utilizzare l'impostazione predefinita `searchMode=any` che predilige il richiamo sulla precisione.

Per altri esempi, vedere [esempi di sintassi di query semplici](search-query-simple-examples.md). Per informazioni dettagliate sulla richiesta di query e sui parametri, vedere [eseguire ricerche nei documenti (API REST)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Ricerca di parole chiave su termini e frasi

Le stringhe passate al **`search`** parametro possono includere termini o frasi in qualsiasi linguaggio supportato, operatori booleani, operatori di precedenza, caratteri jolly o prefisso per le query "inizia con", caratteri di escape e caratteri di codifica URL. Il **`search`** parametro è facoltativo. Non specificato, la ricerca ( `search=*` o `search=" "` ) restituisce i primi 50 documenti in ordine arbitrario (non classificato).

+ Una *ricerca* termini è una query di uno o più termini, in cui uno dei termini viene considerato una corrispondenza.

+ Una *frase di ricerca* è una frase esatta racchiusa tra virgolette `" "` . Ad esempio, se ```Roach Motel``` (senza virgolette) Cerca documenti contenenti ```Roach``` e/o ```Motel``` in qualsiasi punto in qualsiasi ordine, ```"Roach Motel"``` (con virgolette) corrisponderà solo ai documenti che contengono l'intera frase e in questo ordine (l'analisi lessicale si applica comunque). 

  A seconda del client di ricerca, potrebbe essere necessario utilizzare caratteri di escape per le virgolette in una ricerca di frasi. Ad esempio, in Substart in una richiesta POST, una frase di ricerca `"Roach Motel"` nel corpo della richiesta viene specificata come `"\"Roach Motel\""` .

Per impostazione predefinita, tutti i termini o le frasi passati nel **`search`** parametro vengono sottoposti ad analisi lessicale. Assicurarsi di comprendere il comportamento di token dell'analizzatore in uso. Spesso, quando i risultati della query sono imprevisti, è possibile tracciare il motivo per il modo in cui i termini vengono suddivisi in token in fase di query.

Qualsiasi testo con uno o più termini è considerato un valido punto di partenza per l'esecuzione di una query. Azure ricerca cognitiva corrisponderà ai documenti che contengono uno o tutti i termini, incluse eventuali variazioni trovate durante l'analisi del testo.

Con la stessa semplicità di questa operazione, esiste un aspetto dell'esecuzione di query in Azure ricerca cognitiva che *potrebbe* produrre risultati imprevisti, aumentando invece di diminuire i risultati della ricerca, perché vengono aggiunti altri termini e operatori alla stringa di input. Il fatto che questa espansione venga effettivamente eseguita dipende dall'inclusione di un operatore NOT, insieme a un' **`searchMode`** impostazione di parametro che determina la modalità di interpretazione di not in termini di comportamenti e o o. Per altre informazioni, vedere operatore NOT sotto [operatori booleani](#boolean-operators).

## <a name="boolean-operators"></a>Operatori booleani

È possibile incorporare gli operatori booleani in una stringa di query per migliorare la precisione di una corrispondenza. Nella sintassi semplice, gli operatori booleani sono basati su caratteri. Gli operatori di testo, ad esempio Word e, non sono supportati.

| Carattere | Esempio | Uso |
|----------- |--------|-------|
| `+` | `pool + ocean` | Operazione AND. Ad esempio, `pool + ocean` stabilisce che un documento deve contenere entrambi i termini.|
| `|` | `pool | ocean` | Un'operazione o trova una corrispondenza quando viene trovato uno o più termini. Nell'esempio, il motore di query restituirà match nei documenti contenenti `pool` o `ocean` o entrambi. Poiché OR è l'operatore di congiunzione predefinito, è anche possibile escluderlo, in modo che `pool ocean` sia l'equivalente di `pool | ocean`.|
| `-` | `pool – ocean` | Un'operazione NOT restituisce corrispondenze sui documenti che escludono il termine. <br/><br/>Per ottenere il comportamento previsto in un'espressione NOT, è consigliabile impostare **`searchMode=all`** sulla richiesta. In caso contrario, in base al valore predefinito di **`searchMode=any`** , si otterranno corrispondenze in `pool` , più corrispondenze in tutti i documenti che non contengono `ocean` , che potrebbero essere numerosi documenti. Il **`searchMode`** parametro in una richiesta di query controlla se un termine con l'operatore Not è individuato o ORed con altri termini nella query (presupponendo che non esista un `+` `|` operatore OR su altri termini). L'utilizzo di **`searchMode=all`** aumenta la precisione delle query includendo un minor numero di risultati e, per impostazione predefinita, viene interpretato come "and not". <br/><br/>Quando si decide di eseguire un' **`searchMode`** impostazione, prendere in considerazione i modelli di interazione utente per le query in varie applicazioni. È più probabile che gli utenti che eseguono la ricerca di informazioni includano un operatore in una query, anziché i siti di e-commerce che dispongono di più strutture di navigazione predefinite. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Query di prefisso

Per le query "inizia con", aggiungere un suffisso operator ( `*` ) come segnaposto per il resto di un termine. Una query di prefisso deve iniziare con almeno un carattere alfanumerico prima di poter aggiungere l'operatore del suffisso.

| Carattere | Esempio | Uso |
|----------- |--------|-------|
| `*` | `lingui*` corrisponderà a "linguistico" o "linguinei" | L'asterisco ( `*` ) rappresenta uno o più caratteri di lunghezza arbitraria, ignorando la distinzione tra maiuscole e minuscole.  |

Analogamente ai filtri, una query di prefisso cerca una corrispondenza esatta. Di conseguenza, non esiste alcun punteggio di pertinenza (tutti i risultati ricevono un punteggio di ricerca di 1,0). Tenere presente che le query di prefisso possono essere lente, soprattutto se l'indice è di grandi dimensioni e il prefisso è costituito da un numero limitato di caratteri. Una metodologia alternativa, ad esempio la suddivisione in token n-grammi perimetrale, potrebbe risultare più rapida.

La sintassi semplice supporta solo la corrispondenza con prefisso. Per la corrispondenza tra suffissi o infissi rispetto alla fine o al centro di un termine, usare la [sintassi Lucene completa per la ricerca con caratteri jolly](query-lucene-syntax.md#bkmk_wildcard).

## <a name="escaping-search-operators"></a>Escape degli operatori di ricerca  

Nella sintassi semplice, gli operatori di ricerca includono i seguenti caratteri: `+ | " ( ) ' \`  

Se uno di questi caratteri fa parte di un token nell'indice, eseguirne l'escape anteponendo una singola barra rovesciata ( `\` ) nella query. Si supponga, ad esempio, di aver usato un analizzatore personalizzato per la suddivisione in token di termini interi e che l'indice contenga la stringa "Luxury + Hotel". Per ottenere una corrispondenza esatta in questo token, inserire un carattere di escape: `search=luxury\+hotel` .

Per semplificare le operazioni per i casi più comuni, esistono due eccezioni a questa regola in cui l'escape non è necessario:  

+ L'operatore NOT `-` deve essere preceduto da un carattere di escape solo se è il primo carattere dopo uno spazio vuoto. Se l'oggetto `-` viene visualizzato al centro (ad esempio, in `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), è possibile ignorare l'escape.

+ L'operatore suffisso `*` deve essere preceduto da un carattere di escape solo se è l'ultimo carattere prima di uno spazio vuoto. Se l'oggetto `*` viene visualizzato al centro (ad esempio, in `4*4=16` ), non è necessario alcun escape.

> [!NOTE]  
> Per impostazione predefinita, l'analizzatore standard eliminerà e interromperà le parole su trattini, spazi vuoti, e commerciali e altri caratteri durante l' [analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se sono necessari caratteri speciali per restare nella stringa di query, potrebbe essere necessario un analizzatore che li mantiene nell'indice. Alcune opzioni includono gli [analizzatori del linguaggio](index-add-language-analyzers.md)naturale Microsoft, che conserva le parole con trattino o un analizzatore personalizzato per modelli più complessi. Per ulteriori informazioni, vedere [termini parziali, modelli e caratteri speciali](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codifica dei caratteri riservati e non sicuri negli URL

Verificare che tutti i caratteri non sicuri e riservati siano codificati in un URL. Ad esempio,' #' è un carattere non sicuro perché è un identificatore di frammento/ancoraggio in un URL. Il carattere deve essere codificato al `%23`, se usato in un URL. ' &' è =' sono esempi di caratteri riservati in quanto delimitano i parametri e specificano i valori in Azure ricerca cognitiva. Per ulteriori informazioni, vedere [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

I caratteri non sicuri sono ``" ` < > # % { } | \ ^ ~ [ ]``. I caratteri riservati sono `; / ? : @ = + &`.

## <a name="special-characters"></a>Caratteri speciali

In alcuni casi, è possibile cercare un carattere speciale, ad esempio un'❤' emoji o il segno ' €'. In questi casi, assicurarsi che l'analizzatore usato non filtri tali caratteri. L'analizzatore standard ignora molti caratteri speciali, esclusi dall'indice.

Gli analizzatori che tokenize i caratteri speciali includono l'analizzatore "spazio", che prende in considerazione le sequenze di caratteri separate da spazi vuoti come token (pertanto la stringa "❤" verrebbe considerata un token). Inoltre, un analizzatore del linguaggio come Microsoft English Analyzer ("en. Microsoft") accetta la stringa "€" come token. È possibile [testare un analizzatore](/rest/api/searchservice/test-analyzer) per visualizzare i token generati per una query specifica.

Quando si usano i caratteri Unicode, assicurarsi che i simboli siano preceduti da un carattere di escape nell'URL della query (ad esempio per "❤" utilizzerà la sequenza di escape `%E2%9D%A4+` ). Postazione esegue automaticamente questa conversione.  

## <a name="precedence-grouping"></a>Precedenza (raggruppamento)

È possibile usare le parentesi per creare sottoquery, inclusi gli operatori nell'istruzione tra parentesi. Ad esempio, `motel+(wifi|luxury)` cercherà i documenti contenenti i termini "motel" e "wifi" o "luxury" (oppure entrambi).

## <a name="query-size-limits"></a>Limiti delle dimensioni delle query

Se l'applicazione genera query di ricerca a livello di codice, è consigliabile progettarla in modo che non generi query di dimensioni illimitate. 

+ Per GET, la lunghezza dell'URL non può superare 8 KB.

+ Per POST (e qualsiasi altra richiesta), in cui il corpo della richiesta include `search` e altri parametri, ad esempio `filter` e `orderby` , le dimensioni massime sono pari a 16 MB, dove il numero massimo di clausole in `search` (espressioni separate da and, or e così via) è 1024. È previsto anche un limite di circa 32 KB per la dimensione di ogni singolo termine di una query. Per altre informazioni, vedere [limiti delle richieste API](search-limits-quotas-capacity.md#api-request-limits).

## <a name="next-steps"></a>Passaggi successivi

Se si intende costruire query a livello di codice, esaminare la [ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md) per comprendere le fasi di elaborazione delle query e le implicazioni dell'analisi del testo.

Per ulteriori informazioni sulla creazione di query, vedere anche gli articoli seguenti:

+ [Esempi di query per la ricerca semplice](search-query-simple-examples.md)
+ [Esempi di query per la ricerca Lucene completa](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents) (API REST di Ricerca di documenti)
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi delle espressioni Filter e Select (OData)](query-odata-filter-orderby-syntax.md)