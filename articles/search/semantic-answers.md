---
title: Restituisce una risposta semantica
titleSuffix: Azure Cognitive Search
description: Viene descritta la composizione di una risposta semantica e viene illustrato come ottenere risposte da un set di risultati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722430"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Restituire una risposta semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> La ricerca semantica è in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e non sono garantite che abbiano la stessa implementazione a livello generale. Queste funzionalità sono fatturabili. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

Quando si formula una [query semantica](semantic-how-to-query-request.md), è possibile estrarre il contenuto dai documenti corrispondenti che "risponde" direttamente alla query. Una o più risposte possono essere incluse nella risposta, che è quindi possibile eseguire il rendering in una pagina di ricerca per migliorare l'esperienza utente dell'app.

In questo articolo viene illustrato come richiedere una risposta semantica, decomprimere la risposta e scoprire quali sono le caratteristiche di contenuto più idonee per la produzione di risposte di alta qualità.

## <a name="prerequisites"></a>Prerequisiti

Tutti i prerequisiti che si applicano alle [query semantiche](semantic-how-to-query-request.md) si applicano anche alle risposte, tra cui il livello di servizio e l'area.

+ La logica di query deve includere i parametri della query semantica, oltre al parametro "Answers". I parametri obbligatori sono descritti in questo articolo.

+ Le stringhe di query immesse dall'utente devono essere formulate nel linguaggio con le caratteristiche di una domanda (cosa, dove, quando, come).

+ I documenti di ricerca devono contenere testo con le caratteristiche di una risposta e il testo deve esistere in uno dei campi elencati in "searchFields". Ad esempio, data una query "che cos'è una tabella hash", se nessuno dei searchFields contiene passaggi che includono "una tabella hash è...", è improbabile che venga restituita una risposta.

## <a name="what-is-a-semantic-answer"></a>Che cos'è una risposta semantica?

Una risposta semantica è una sottostruttura di una [risposta di query semantica](semantic-how-to-query-request.md). È costituito da uno o più passaggi Verbatim da un documento di ricerca, formulati come una risposta a una query simile a una domanda. Affinché venga restituita una risposta, le frasi o le frasi devono esistere in un documento di ricerca con le caratteristiche del linguaggio di una risposta e la query stessa deve essere rappresentata come una domanda.

Ricerca cognitiva usa un modello di comprensione della lettura del computer per scegliere la risposta migliore. Il modello genera un set di potenziali risposte dal contenuto disponibile e, quando raggiunge un livello di confidenza sufficientemente elevato, proporrà una risposta.

Le risposte vengono restituite come un oggetto di livello superiore indipendente nel payload di risposta alla query che è possibile scegliere di eseguire il rendering nelle pagine di ricerca, lungo i risultati della ricerca. Strutturalmente, si tratta di un elemento di matrice all'interno della risposta costituito da testo, una chiave del documento e un punteggio di confidenza.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Come richiedere risposte semantiche in una query

Per restituire una risposta semantica, la query deve avere la semantica "queryType", "queryLanguage", "searchFields" e il parametro "Answers". La specifica del parametro "Answers" non garantisce che venga restituita una risposta, ma la richiesta deve includere questo parametro se l'elaborazione della risposta deve essere richiamata.

Il parametro "searchFields" è fondamentale per restituire una risposta di qualità elevata, sia in termini di contenuto che di ordine (vedere di seguito). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Una stringa di query non deve essere null e deve essere formulata come question. In questa versione di anteprima, i "queryType" e "queryLanguage" devono essere impostati esattamente come illustrato nell'esempio.

+ Il parametro "searchFields" determina i campi stringa che forniscono i token al modello di estrazione. Gli stessi campi che producono didascalie generano anche risposte. Per indicazioni precise su come impostare questo campo in modo che funzioni per le didascalie e le risposte, vedere [set searchFields](semantic-how-to-query-request.md#searchfields). 

+ Per "Answers", la costruzione di parametri è `"answers": "extractive"` , dove il numero predefinito di risposte restituito è uno. È possibile aumentare il numero di risposte aggiungendo un conteggio, come illustrato nell'esempio precedente, fino a un massimo di cinque.  Se è necessaria più di una risposta dipende dall'esperienza utente dell'app e da come si desidera eseguire il rendering dei risultati.

## <a name="deconstruct-an-answer-from-the-response"></a>Decostruire una risposta dalla risposta

Le risposte vengono fornite nella @search.answers matrice, che viene visualizzata per prima nella risposta. Se una risposta è indeterminata, la risposta viene visualizzata come `"@search.answers": []` . Quando si progetta una pagina dei risultati di ricerca che include risposte, assicurarsi di gestire i casi in cui non vengono trovate le risposte.

In @search.answers la "chiave" è la chiave del documento o l'ID della corrispondenza. Data una chiave del documento, è possibile usare l'API di [ricerca di documenti](/rest/api/searchservice/lookup-document) per recuperare una o tutte le parti del documento di ricerca da includere nella pagina di ricerca o in una pagina di dettaglio.

Sia "Text" che "Highlights" forniscono contenuto identico, sia in testo normale che con evidenziato. Per impostazione predefinita, gli Highlight sono con stile `<em>` , che è possibile eseguire l'override usando i parametri highlightPreTag e highlightPostTag esistenti. Come indicato altrove, la sostanza di una risposta è il contenuto Verbatim di un documento di ricerca. Il modello di estrazione cerca le caratteristiche di una risposta per trovare il contenuto appropriato, ma non compone la nuova lingua nella risposta.

Il "Punteggio" è un punteggio di confidenza che riflette la forza della risposta. Se nella risposta sono presenti più risposte, questo punteggio viene usato per determinare l'ordine. Le principali risposte e le didascalie principali possono derivare da documenti di ricerca diversi, in cui la risposta principale ha origine da un documento e dalla didascalia principale di un'altra, ma in generale verranno visualizzati gli stessi documenti nelle prime posizioni all'interno di ogni matrice.

Le risposte sono seguite dalla matrice "value", che include sempre i punteggi, le didascalie e tutti i campi che possono essere recuperati per impostazione predefinita. Se il parametro Select è stato specificato, la matrice "value" è limitata ai campi specificati. Per ulteriori informazioni sugli elementi nella risposta, vedere [creare una query semantica](semantic-how-to-query-request.md).

Data la query "How do Clouds form", nella risposta viene restituita la risposta seguente:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Suggerimenti per la produzione di risposte di alta qualità

Per ottenere risultati ottimali, restituire le risposte semantiche in un corpus di documenti con le caratteristiche seguenti:

+ "searchFields" deve fornire campi che offrano un testo sufficiente in cui è probabile che venga trovata una risposta. Solo il testo Verbatim di un documento può sembrare una risposta.

+ le stringhe di query non devono essere null (Search = `*` ) e la stringa deve avere le caratteristiche di una domanda, in contrapposizione a una ricerca di parole chiave (un elenco sequenziale di termini o frasi arbitrarie). Se la stringa di query non sembra rispondere, l'elaborazione delle risposte viene ignorata, anche se la richiesta specifica "Answers" come parametro di query.

+ L'estrazione semantica e il riepilogo sono limitati al numero di token per documento che possono essere analizzati in modo tempestivo. In pratica, se si dispone di documenti di grandi dimensioni che vengono eseguiti in centinaia di pagine, provare a suddividere il contenuto in documenti più piccoli.

## <a name="next-steps"></a>Passaggi successivi

+ [Panoramica della ricerca semantica](semantic-search-overview.md)
+ [Algoritmo di classificazione semantica](semantic-ranking.md)
+ [Algoritmo di classificazione di somiglianza](index-ranking-similarity.md)
+ [Creare una query semantica](semantic-how-to-query-request.md)