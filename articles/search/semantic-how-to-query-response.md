---
title: Struttura di una risposta semantica
titleSuffix: Azure Cognitive Search
description: Viene descritto l'algoritmo di classificazione semantica in ricerca cognitiva e come strutturare ' Semantic Answers ' è Semantic captions ' da un set di risultati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679866"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Classificazione e risposte semantiche in Azure ricerca cognitiva

> [!IMPORTANT]
> L'algoritmo di classificazione semantico e le risposte semantiche/didascalie sono disponibili in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La classificazione semantica migliora la precisione dei risultati della ricerca riclassificando le prime corrispondenze usando un modello di classificazione semantico con training per le query espresse nel linguaggio naturale anziché come parole chiave.

Questo articolo descrive l'algoritmo di classificazione semantica e il modo in cui viene modellata una risposta semantica. Una risposta include didascalie, sia in testo normale che con evidenziazioni e risposte (facoltative).

+ Le didascalie semantiche sono passaggi di testo rilevanti per la query estratta dai risultati della ricerca. Consentono di riepilogare un risultato quando i singoli campi di contenuto sono troppo grandi per la pagina dei risultati. Didascalie funzionalità caratteristiche principali, consentendo agli utenti di scorrere rapidamente i risultati delle query per trovare i documenti più rilevanti, migliorando in tal modo l'esperienza utente complessiva.

+ Le risposte semantiche usano modelli di machine learning da Bing per formulare risposte alle query che hanno un aspetto simile a domande. Le risposte vengono selezionate da un elenco di passaggi più rilevanti per la query, come estratti dai primi documenti del set di risultati della query. Le risposte vengono restituite come un oggetto di livello superiore indipendente nel payload di risposta alla query che è possibile scegliere di eseguire il rendering nelle pagine di ricerca, lungo i risultati della ricerca.

## <a name="prerequisites"></a>Prerequisiti

+ Query formulate usando il tipo di query semantico. Per altre informazioni, vedere [creare una query semantica](semantic-how-to-query-request.md).

## <a name="understanding-a-semantic-response"></a>Informazioni su una risposta semantica

Una risposta semantica include nuove proprietà per i punteggi, le didascalie e le risposte. Una risposta semantica è compilata dalla risposta standard, usando i primi 50 risultati restituiti dal [motore di ricerca full-text](search-lucene-query-architecture.md), che vengono quindi riclassificati usando il rango semantico. Se vengono specificati più di 50, vengono restituiti i risultati aggiuntivi, ma non verranno riclassificati in modo semantico.

Come per tutte le query, una risposta è costituita da tutti i campi contrassegnati come recuperabili o solo da quelli elencati nell'istruzione SELECT. Include anche un campo "answer" e "captions".

+ Per ogni risultato semantico, per impostazione predefinita, esiste una "risposta", restituita come campo distinto di cui è possibile scegliere il rendering in una pagina di ricerca. È possibile specificare fino a cinque. La formulazione della risposta è automatizzata: lettura di tutti i documenti nei risultati iniziali, esecuzione del riepilogo di estrazione, seguito dalla comprensione della lettura del computer e infine promozione di una risposta diretta alla domanda dell'utente nel campo della risposta.

+ Una "didascalia" è un riepilogo basato sull'estrazione del contenuto del documento, restituito in testo normale o con evidenziazioni. I sottotitoli vengono inclusi automaticamente e non possono essere eliminati. Le evidenziazioni vengono applicate usando la comprensione della lettura del computer per identificare le stringhe da enfatizzare. In evidenza è possibile attirare l'attenzione sui passaggi più rilevanti, in modo che sia possibile analizzare rapidamente una pagina di risultati per trovare il documento corretto.

Un set di risultati di riclassificazione semanticamente Ordina i risultati in base al @search.rerankerScore valore. Se si aggiunge una clausola OrderBy, viene restituito un errore HTTP 400 perché tale clausola non è supportata in una query semantica.

## <a name="example"></a>Esempio

@search.rerankerScoreEsiste insieme allo standard @search.score e viene usato per riclassificare i risultati.

Data la query seguente:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

È possibile che venga visualizzato il risultato seguente, rappresentativo di una risposta semantica. Questi risultati mostrano solo le prime tre risposte, classificate in base a " @search.rerankerScore ". Si noti che Oceanside Resort è ora classificato per primo. Con la classificazione predefinita " @search.score ", questo risultato verrebbe restituito in secondo luogo, dopo la fine dei percorsi.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Passaggi successivi

+ [Panoramica della ricerca semantica](semantic-search-overview.md)
+ [Algoritmo di somiglianza](index-ranking-similarity.md)
+ [Creare una query semantica](semantic-how-to-query-request.md)
+ [Creare una query di base](search-query-create.md)