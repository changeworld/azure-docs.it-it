---
title: Aggiungi controllo ortografico
titleSuffix: Azure Cognitive Search
description: Aggiungere la correzione ortografica alla pipeline di query per correggere i digitazioni sui termini di query prima di eseguire la query.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: da172e9a7605876711e4a4f32bf4fac698b35109
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694802"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Aggiungere il controllo ortografico alle query in ricerca cognitiva

> [!IMPORTANT]
> La correzione ortografica è in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Durante l'avvio dell'anteprima iniziale, non è previsto alcun addebito per il correttore ortografico. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

È possibile migliorare il richiamo mediante la correzione ortografica dei singoli termini di query di ricerca prima che raggiungano il motore di ricerca. Il parametro del **correttore ortografico** è supportato per tutti i tipi di query: [Simple](query-simple-syntax.md), [full](query-lucene-syntax.md)e la nuova opzione [semantica](semantic-how-to-query-request.md) attualmente disponibile in anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

+ Un indice di ricerca esistente, che contiene contenuto in lingua inglese

+ Un client di ricerca per l'invio di query

  Il client di ricerca deve supportare le API REST di anteprima nella richiesta di query. È possibile usare il [post](search-get-started-rest.md), il [Visual Studio Code](search-get-started-vs-code.md)o il codice modificato per eseguire chiamate REST alle API di anteprima.

+ [Una richiesta di query](/rest/api/searchservice/preview-api/search-documents) che usa la correzione ortografica include "API-Version = 2020-06 -30-preview", "Speller = Lexicon" e "queryLanguage = en-US".

  Il queryLanguage è obbligatorio per il correttore ortografico e attualmente "en-US" è l'unico valore valido.

> [!Note]
> Il parametro del correttore ortografico è disponibile in tutti i livelli, nelle stesse aree che forniscono la ricerca semantica. Non è necessario iscriversi per accedere a questa funzionalità in anteprima. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Correzione ortografica con ricerca semplice

Nell'esempio seguente viene utilizzato l'indice di esempio Hotels incorporato per dimostrare la correzione ortografica in una semplice query di testo in formato libero. Senza la correzione ortografica, la query restituisce zero risultati. Con la correzione, la query restituisce un risultato per la ricorrenza orientata alla famiglia di Johnson.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Correzione ortografica con Lucene completo

La correzione ortografica si verifica nei singoli termini di query sottoposti ad analisi, motivo per cui è possibile usare il parametro del correttore ortografico con alcune query Lucene, ma non altri.

+ I moduli di query non compatibili che ignorano l'analisi del testo includono caratteri jolly, Regex, fuzzy
+ I moduli di query compatibili includono: ricerca nel campo, prossimità, aumento del termine

In questo esempio viene usata la ricerca FIELDed sul campo Category, con la sintassi Lucene completa e un termine di query con errori di ortografia. Includendo il correttore ortografico, il digitazione in "suiite" viene corretto e la query ha esito positivo.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Correzione ortografica con ricerca semantica

Questa query, con errori di digitazione in ogni termine tranne uno, subisce correzioni ortografiche per restituire i risultati pertinenti. Per altre informazioni, vedere [creare una query semantica](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Considerazioni sul linguaggio

Il parametro queryLanguage richiesto per il correttore ortografico deve essere coerente con tutti gli [analizzatori di linguaggio](index-add-language-analyzers.md) assegnati alle definizioni di campo nello schema dell'indice. 

+ queryLanguage determina i lessico usati per il controllo ortografico e viene usato anche come input per l'algoritmo di [classificazione semantica](semantic-how-to-query-response.md) se si usa "QueryType = Semantic".

+ Gli analizzatori di linguaggio vengono usati durante l'indicizzazione e l'esecuzione di query per trovare i documenti corrispondenti nell'indice di ricerca. Un esempio di definizione di campo che usa un analizzatore del linguaggio è `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Per ottenere risultati ottimali quando si usa il correttore ortografico, se queryLanguage è "en-US", tutti gli analizzatori di linguaggio devono anche essere una variante inglese ("en. Microsoft" o "en. Lucene").

> [!NOTE]
> Gli analizzatori indipendenti dal linguaggio (ad esempio parola chiave, Simple, standard, stop, spazi vuoti o `standardasciifolding.lucene` ) non sono in conflitto con le impostazioni di queryLanguage.

In una richiesta di query, il queryLanguage impostato si applica in modo analogo a correttore ortografico, risposte e didascalie. Nessun override per le singole parti.

Mentre il contenuto di un indice di ricerca può essere composto in più lingue, l'input della query è probabilmente in uno. Il motore di ricerca non verifica la compatibilità di queryLanguage, Language Analyzer e la lingua in cui è composto il contenuto. Assicurarsi quindi di definire l'ambito delle query in modo da evitare di produrre risultati non corretti.

## <a name="next-steps"></a>Passaggi successivi

+ [Creare una query semantica](semantic-how-to-query-request.md)
+ [Creare una query di base](search-query-create.md)
+ [Usa la sintassi di query Lucene completa](query-Lucene-syntax.md)
+ [Usare la sintassi di query semplice](query-simple-syntax.md)
+ [Panoramica della ricerca semantica](semantic-search-overview.md)