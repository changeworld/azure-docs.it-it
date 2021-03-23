---
title: Classificazione semantica
titleSuffix: Azure Cognitive Search
description: Informazioni sul funzionamento dell'algoritmo di classificazione semantica in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 1406f865d60e6715b5f6a974225dc48958e8da6d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775171"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificazione semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> Le funzionalità di ricerca semantica sono disponibili in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e non sono garantite che abbiano la stessa implementazione a livello generale. Queste funzionalità sono fatturabili. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

La classificazione semantica è un'estensione della pipeline di esecuzione delle query che consente di migliorare la precisione e il richiamo riallineando le prime corrispondenze di un set di risultati iniziale. La classificazione semantica è supportata da modelli di comprensione delle letture dei computer all'avanguardia, sottoposti a training per le query espresse nel linguaggio naturale anziché alla corrispondenza linguistica nelle parole chiave. Diversamente dall'algoritmo di [classificazione di somiglianza predefinito](index-ranking-similarity.md), il rango semantico usa il contesto e il significato delle parole per determinare la pertinenza.

La classificazione semantica è a elevato utilizzo di risorse e tempo. Per completare l'elaborazione entro la latenza prevista di un'operazione di query, gli input vengono consolidati e semplificati, in modo che il riepilogo e l'analisi possano essere completati nel minor tempo possibile.

## <a name="preparation-for-semantic-ranking"></a>Preparazione per la classificazione semantica

Prima di assegnare un punteggio alla pertinenza, il contenuto deve essere ridotto a una quantità di parametri che possono essere gestiti in modo efficiente dal Ranker semantico. La riduzione del contenuto include la seguente sequenza di passaggi.

1. La riduzione del contenuto inizia usando i risultati iniziali restituiti dall' [algoritmo di classificazione di somiglianza](index-ranking-similarity.md) predefinito usato per la ricerca di parole chiave. I risultati della ricerca possono includere fino a 1.000 corrispondenze, ma la classificazione semantica elaborerà solo i primi 50. 

   Data la query, i risultati iniziali potrebbero essere molto inferiori a 50, a seconda del numero di corrispondenze trovate. Indipendentemente dal numero di documenti, il set di risultati iniziale è il corpus del documento per la classificazione semantica.

1. Nel corpus del documento, il contenuto di ogni campo in "searchFields" viene estratto e combinato in una stringa estesa.

1. Tutte le stringhe eccessivamente lunghe vengono tagliate per garantire che la lunghezza complessiva soddisfi i requisiti di input del modello di riepilogo. Questo esercizio di taglio è il motivo per cui è importante posizionare prima i campi concisi in "searchFields", per assicurarsi che siano inclusi nella stringa. Se si dispone di documenti di grandi dimensioni con campi con un numero elevato di testo, qualsiasi elemento dopo il limite massimo viene ignorato.

Ogni documento è ora rappresentato da un'unica stringa long.

> [!NOTE]
> Gli input dei parametri per i modelli sono token non caratteri o parole. La suddivisione in token è determinata in parte dall'assegnazione dell'analizzatore nei campi disponibili per la ricerca. Per informazioni dettagliate sulla modalità di suddivisione in token delle stringhe, è possibile esaminare l'output del token di un analizzatore usando l' [API REST dell'analizzatore di test](/rest/api/searchservice/test-analyzer).
>
> Attualmente in questa versione di anteprima, le stringhe lunghe possono avere una dimensione massima di 8.000 token. Se la ricerca non è in grado di recapitare una risposta prevista dall'interno di un documento, la conoscenza della rimozione del contenuto consente di comprendere il motivo. 

## <a name="summarization"></a>Riepilogo

Dopo la riduzione della stringa, è ora possibile passare i parametri tramite la comprensione della lettura del computer e la rappresentazione della lingua per determinare quali frasi e frasi riepilogano meglio il modello rispetto alla query.

Gli input per il riepilogo sono la stringa long della fase di preparazione. Da tale input, il modello di riepilogo valuta il contenuto per trovare i passaggi più rappresentativi.

L'output è una [didascalia semantica](semantic-how-to-query-request.md), in testo normale e con evidenziazioni. La didascalia è più piccola della stringa long, in genere meno di 200 parole per documento, ed è considerata la più rappresentativa del documento. 

Verrà restituita una [risposta semantica](semantic-answers.md) anche se è stato specificato il parametro "Answers", se la query è stata rappresentata come una domanda e se è possibile trovare un passaggio nella stringa long che probabilmente fornirà una risposta alla domanda.

## <a name="scoring-and-ranking"></a>Assegnazione di punteggi e classificazione

A questo punto, sono ora disponibili didascalie per ogni documento. Le didascalie vengono valutate per la pertinenza della query.

1. Il Punteggio è determinato dalla valutazione di ogni didascalia per la pertinenza concettuale e semantica, relativa alla query fornita.

   Il diagramma seguente illustra il significato di "rilevanza semantica". Si consideri il termine "Capital", che può essere usato nel contesto di Finance, Law, Geography o grammatica. Se una query include termini dallo stesso spazio vettoriale (ad esempio, "capitale" e "investimento"), un documento che include anche token nello stesso cluster segnerà un punteggio superiore a quello che non lo è.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Rappresentazione vettoriale per il contesto" border="true":::

1. L'output di questa fase è @search.rerankerScore assegnato a ogni documento. Dopo aver valutato tutti i documenti, questi vengono elencati in ordine decrescente e inclusi nel payload della risposta alla query. Il payload include risposte, testo normale e didascalie evidenziate e tutti i campi contrassegnati come recuperabili o specificati in una clausola SELECT.

## <a name="next-steps"></a>Passaggi successivi

La classificazione semantica è disponibile nei livelli standard, in aree specifiche. Per ulteriori informazioni su disponibile e iscrizione, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing). Un nuovo tipo di query consente le strutture di classificazione e risposta della pertinenza della ricerca semantica. Per iniziare, [creare una query semantica](semantic-how-to-query-request.md).

In alternativa, esaminare gli articoli seguenti sulla classificazione predefinita. La classificazione semantica dipende dal Ranker di somiglianza per restituire i risultati iniziali. La conoscenza dell'esecuzione delle query e della classificazione offre una conoscenza approfondita del funzionamento dell'intero processo.

+ [Ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md)
+ [Somiglianza e punteggio in Ricerca cognitiva di Azure](index-similarity-and-scoring.md)
+ [Analizzatori per l'elaborazione del testo in Azure ricerca cognitiva](search-analyzers.md)