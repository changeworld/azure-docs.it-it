---
title: Creare una query semantica
titleSuffix: Azure Cognitive Search
description: Impostare un tipo di query semantico per allineare i modelli di apprendimento avanzato all'elaborazione delle query, dedurre finalità e contesto come parte del rango di ricerca e della pertinenza.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9ff98a2613143474afd6041ccf52d4eb509d646b
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418879"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Creare una query semantica in ricerca cognitiva

> [!IMPORTANT]
> Il tipo di query semantico è disponibile in anteprima pubblica, disponibile tramite l'API REST di anteprima e portale di Azure. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

Questo articolo illustra come formulare una richiesta di ricerca che usa la classificazione semantica. La richiesta restituirà didascalie semantiche e, facoltativamente, [risposte semantiche](semantic-answers.md), con evidenziazioni sui termini e sulle frasi più rilevanti.

Sia le didascalie che le risposte vengono estratti Verbatim dal testo nel documento di ricerca. Il sottosistema semantico determina il contenuto che presenta le caratteristiche di una didascalia o di una risposta, ma non compone nuove frasi o frasi. Per questo motivo, i contenuti che includono spiegazioni o definizioni funzionano meglio per la ricerca semantica.

## <a name="prerequisites"></a>Prerequisiti

+ Un servizio di ricerca a livello standard (S1, S2, S3), situato in una di queste aree: Stati Uniti centro-settentrionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale. Se si dispone di un servizio S1 o Greater esistente in una di queste aree, è possibile richiedere l'accesso senza dover creare un nuovo servizio.

+ Accesso all'anteprima di ricerca semantica: [iscrizione](https://aka.ms/SemanticSearchPreviewSignup)

+ Un indice di ricerca esistente, che contiene contenuto in lingua inglese

+ Un client di ricerca per l'invio di query

  Il client di ricerca deve supportare le API REST di anteprima nella richiesta di query. È possibile usare il [post](search-get-started-rest.md), il [Visual Studio Code](search-get-started-vs-code.md)o il codice modificato per eseguire chiamate REST alle API di anteprima. È anche possibile usare [Esplora ricerche](search-explorer.md) in portale di Azure per inviare una query semantica.

+ Una [richiesta di query](/rest/api/searchservice/preview-api/search-documents) deve includere l'opzione semantica e altri parametri descritti in questo articolo.

## <a name="whats-a-semantic-query"></a>Che cos'è una query semantica?

In ricerca cognitiva, una query è una richiesta con parametri che determina l'elaborazione di query e la forma della risposta. Una *query semantica* aggiunge parametri che richiamano il modello di reranking semantico in grado di valutare il contesto e il significato dei risultati di corrispondenza, promuovere corrispondenze più rilevanti alla parte superiore e restituire le risposte semantiche e le didascalie.

La richiesta seguente è rappresentativa di una query semantica minima (senza risposte).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Come per tutte le query in ricerca cognitiva, la richiesta è destinata alla raccolta Documents di un singolo indice. Inoltre, una query semantica viene sottoposta alla stessa sequenza dell'analisi, dell'analisi, dell'analisi e del punteggio come query non semantica. 

La differenza risiede nella pertinenza e nell'assegnazione dei punteggi. Come definito in questa versione di anteprima, una query semantica è una query i cui *risultati* vengono riclassificati usando un modello di linguaggio semantico, che fornisce un modo per visualizzare le corrispondenze ritenute più rilevanti dal rango semantico, anziché i punteggi assegnati dall'algoritmo di classificazione di somiglianza predefinito.

Solo le prime 50 corrispondenze dei risultati iniziali possono essere classificate in modo semantico e tutte includono didascalie nella risposta. Facoltativamente, è possibile specificare un **`answer`** parametro nella richiesta per estrarre una potenziale risposta. Per altre informazioni, vedere [risposte semantiche](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Eseguire query con Esplora ricerche

[Esplora ricerche](search-explorer.md) è stato aggiornato per includere le opzioni per le query semantiche. Queste opzioni diventano visibili nel portale dopo aver ottenuto l'accesso all'anteprima. Le opzioni di query consentono di abilitare query semantiche, searchFields e correzioni ortografiche.

È anche possibile incollare i parametri di query obbligatori nella stringa di query.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Opzioni di query in Esplora ricerche" border="true":::

## <a name="query-using-rest"></a>Eseguire query con REST

Usare i [documenti di ricerca (anteprima Rest)](/rest/api/searchservice/preview-api/search-documents) per formulare la richiesta a livello di codice.

Una risposta include didascalie ed evidenziazione automatica. Se si vuole che la risposta includa la correzione ortografica o le risposte, aggiungere un **`speller`** parametro facoltativo o **`answers`** nella richiesta.

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

Nella tabella seguente vengono riepilogati i parametri di query utilizzati in una query semantica, in modo che sia possibile visualizzarli in modo olistico. Per un elenco di tutti i parametri, vedere [cercare documenti (anteprima Rest)](/rest/api/searchservice/preview-api/search-documents)

| Parametro | Tipo | Descrizione |
|-----------|-------|-------------|
| queryType | string | I valori validi includono semplice, completo e semantico. Per le query semantiche è necessario un valore "semantico". |
| queryLanguage | string | Obbligatorio per le query semantiche. Attualmente, viene implementato solo "en-US". |
| searchFields | string | Elenco delimitato da virgole di campi disponibili per la ricerca. Facoltativo ma consigliato. Specifica i campi in base ai quali si verifica la classificazione semantica. </br></br>Diversamente dai tipi di query semplici e completi, l'ordine in cui vengono elencati i campi determina la precedenza. Per altre istruzioni sull'utilizzo, vedere [Step 2: set searchFields](#searchfields). |
| correttore ortografico | string | Parametro facoltativo, non specifico per le query semantiche, che corregge i termini errati prima che raggiungano il motore di ricerca. Per altre informazioni, vedere [aggiungere la correzione ortografica alle query](speller-how-to-add.md). |
| risposte |string | Parametri facoltativi che specificano se le risposte semantiche sono incluse nel risultato. Attualmente, viene implementato solo "estrazione". È possibile configurare le risposte per restituire un massimo di cinque. Il valore predefinito è uno. Questo esempio mostra un conteggio di tre risposte: "estrazione \| Count3". Per altre informazioni, vedere [restituire risposte semantiche](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formulare la richiesta

Questa sezione illustra i parametri di query necessari per la ricerca semantica.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Passaggio 1: impostare queryType e queryLanguage

Aggiungere i parametri seguenti al resto. Entrambi i parametri sono obbligatori.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage deve essere coerente con tutti gli [analizzatori di linguaggio](index-add-language-analyzers.md) assegnati alle definizioni di campo nello schema dell'indice. Se queryLanguage è "en-US", qualsiasi analizzatore di linguaggio deve essere anche una variante inglese ("en. Microsoft" o "en. Lucene"). Gli analizzatori indipendenti dal linguaggio, ad esempio parole chiave o semplici, non presentano conflitti con i valori queryLanguage.

In una richiesta di query, se si usa anche la [correzione ortografica](speller-how-to-add.md), il queryLanguage impostato viene applicato in modo analogo a correttore ortografico, risposte e didascalie. Nessun override per le singole parti. 

Mentre il contenuto di un indice di ricerca può essere composto in più lingue, l'input della query è probabilmente in uno. Il motore di ricerca non verifica la compatibilità di queryLanguage, Language Analyzer e la lingua in cui è composto il contenuto. Assicurarsi quindi di definire l'ambito delle query in modo da evitare di produrre risultati non corretti.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Passaggio 2: impostare searchFields

Questo parametro è facoltativo in quanto non si verifica alcun errore se si omette, ma è consigliabile fornire un elenco ordinato di campi per le didascalie e le risposte.

Il parametro searchFields viene usato per identificare i passaggi da valutare per "somiglianza semantica" alla query. Per l'anteprima, non è consigliabile lasciare vuoto searchFields perché il modello richiede un hint per i campi più importanti da elaborare.

L'ordine di searchFields è critico. Se si usa già searchFields in query Lucene semplici o complete esistenti, assicurarsi di rivisitare questo parametro per verificare l'ordine dei campi quando si passa a un tipo di query semantico.

Seguire queste linee guida per garantire risultati ottimali quando si specificano due o più searchFields:

+ Includere solo i campi stringa e i campi stringa di primo livello nelle raccolte. Se si includono campi non di stringa o campi di livello inferiore in una raccolta, non si verifica alcun errore, ma questi campi non verranno usati nella classificazione semantica.

+ Il primo campo deve essere sempre conciso, ad esempio un titolo o un nome, idealmente inferiore a 25 parole.

+ Se l'indice include un campo URL che è testuale (leggibile, ad esempio `www.domain.com/name-of-the-document-and-other-details` , e non incentrato sul computer, ad esempio `www.domain.com/?id=23463&param=eis` ), posizionarlo secondo nell'elenco o prima se non è presente alcun campo del titolo conciso.

+ Seguire i campi in base ai campi descrittivi in cui è possibile trovare la risposta alle query semantiche, ad esempio il contenuto principale di un documento.

Se è stato specificato un solo campo, utilizzare un campo descrittivo in cui è possibile trovare la risposta alle query semantiche, ad esempio il contenuto principale di un documento. Scegliere un campo che fornisca contenuto sufficiente. Per garantire l'elaborazione tempestiva, solo circa 8.000 token del contenuto collettivo di searchFields subiscono la valutazione semantica e la classificazione.

#### <a name="step-3-remove-orderby-clauses"></a>Passaggio 3: rimuovere le clausole orderBy

Rimuovere eventuali clausole orderBy, se presenti in una richiesta esistente. Il Punteggio semantico viene usato per ordinare i risultati. Se si include la logica di ordinamento esplicita, viene restituito un errore HTTP 400.

#### <a name="step-4-add-answers"></a>Passaggio 4: aggiungere risposte

Facoltativamente, aggiungere "Answers" (risposte) se si desidera includere un'elaborazione aggiuntiva che fornisca una risposta. Le risposte e le didascalie vengono estratte dai passaggi presenti nei campi elencati in searchFields. Assicurarsi di includere i campi con contenuto ricco in searchFields per ottenere le risposte migliori in una risposta. Per ulteriori informazioni, vedere [come restituire risposte semantiche](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Passaggio 5: aggiungere altri parametri

Impostare gli eventuali altri parametri desiderati nella richiesta. Parametri come il [correttore ortografico](speller-how-to-add.md), [SELECT](search-query-odata-select.md)e count migliorano la qualità della richiesta e la leggibilità della risposta.

Facoltativamente, è possibile personalizzare lo stile di evidenziazione applicato alle didascalie. Nelle didascalie viene applicata la formattazione dell'evidenziazione sui passaggi chiave del documento che riepilogano la risposta. Il valore predefinito è `<em>`. Se si desidera specificare il tipo di formattazione, ad esempio sfondo giallo, è possibile impostare highlightPreTag e highlightPostTag.

## <a name="evaluate-the-response"></a>Valutare la risposta

Come per tutte le query, una risposta è costituita da tutti i campi contrassegnati come recuperabili oppure solo dai campi elencati nel parametro Select. Include il Punteggio di pertinenza originale e può includere anche un conteggio o i risultati in batch, a seconda di come è stata formulata la richiesta.

In una query semantica, la risposta ha elementi aggiuntivi: un nuovo punteggio di pertinenza con classificazione semantica, sottotitoli in testo normale e con evidenziazioni e, facoltativamente, una risposta.

In un'app client è possibile strutturare la pagina di ricerca in modo da includere una didascalia come descrizione della corrispondenza, anziché l'intero contenuto di un campo specifico. Questa operazione è utile quando i singoli campi sono troppo densi per la pagina dei risultati della ricerca.

La risposta per la query di esempio precedente restituisce la seguente corrispondenza come inizio selezione. Le didascalie vengono restituite automaticamente, con testo normale e versioni evidenziate. Le risposte vengono omesse dall'esempio perché non è possibile determinare una query e un corpus specifici.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Passaggi successivi

Tenere presente che le risposte e la classificazione semantica vengono compilate su un set di risultati iniziale. Tutte le logiche che migliorano la qualità dei risultati iniziali porteranno alla ricerca semantica. Come passaggio successivo, esaminare le funzionalità che contribuiscono ai risultati iniziali, inclusi gli analizzatori che influiscono sul modo in cui le stringhe vengono suddivise in token, i profili di punteggio che possono ottimizzare i risultati e l'algoritmo di pertinenza predefinito.

+ [Analizzatori per l'elaborazione del testo](search-analyzers.md)
+ [Somiglianza e assegnazione dei punteggi in ricerca cognitiva](index-similarity-and-scoring.md)
+ [Aggiungere profili di punteggio](index-add-scoring-profiles.md)
+ [Panoramica della ricerca semantica](semantic-search-overview.md)
+ [Aggiungere il controllo ortografico ai termini della query](speller-how-to-add.md)
