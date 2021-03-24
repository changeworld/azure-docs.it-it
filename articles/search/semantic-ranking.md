---
title: Classificazione semantica
titleSuffix: Azure Cognitive Search
description: Informazioni sul funzionamento dell'algoritmo di classificazione semantica in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: c3a0a8bd5805757b92e3f5b046335c8883b4ba72
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888924"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificazione semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> Le funzionalità di ricerca semantica sono disponibili in anteprima pubblica, disponibile tramite l'API REST e il portale di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e non sono garantite che abbiano la stessa implementazione a livello generale. Queste funzionalità sono fatturabili. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

La classificazione semantica è un'estensione della pipeline di esecuzione delle query che migliora la precisione riallineando le prime corrispondenze di un set di risultati iniziale. La classificazione semantica è supportata da reti di grandi dimensioni basate su Transformer, sottoposte a training per l'acquisizione del significato semantico dei termini di query, anziché la corrispondenza linguistica nelle parole chiave. Diversamente dall'algoritmo di [classificazione di somiglianza predefinito](index-ranking-similarity.md), il rango semantico usa il contesto e il significato delle parole per determinare la pertinenza.

La classificazione semantica è a elevato utilizzo di risorse e tempo. Per completare l'elaborazione entro la latenza prevista di un'operazione di query, gli input per la gerarchia semantica vengono consolidati e ridotti, in modo che il riepilogo sottostante e i passaggi di riclassificazione possano essere completati nel minor tempo possibile.

## <a name="preparation-for-semantic-ranking"></a>Preparazione per la classificazione semantica

Prima di assegnare un punteggio alla pertinenza, il contenuto deve essere ridotto a un numero di input che possono essere gestiti in modo efficiente dal Ranker semantico. La riduzione del contenuto include la seguente sequenza di passaggi.

1. La riduzione del contenuto inizia usando il set di risultati iniziale restituito dall' [algoritmo di classificazione di somiglianza](index-ranking-similarity.md) predefinito usato per la ricerca di parole chiave. I risultati della ricerca possono includere fino a 1.000 corrispondenze, ma la classificazione semantica elaborerà solo i primi 50. 

   Data la query, i risultati iniziali potrebbero essere molto inferiori a 50, a seconda del numero di corrispondenze trovate. Indipendentemente dal numero di documenti, il set di risultati iniziale è il corpus del documento per la classificazione semantica.

1. Nel corpus del documento, il contenuto di ogni campo in "searchFields" viene estratto e combinato in una stringa estesa.

1. Tutte le stringhe eccessivamente lunghe vengono tagliate per garantire che la lunghezza complessiva soddisfi i requisiti di input del passaggio di riepilogo. Questo esercizio di taglio è il motivo per cui è importante posizionare prima i campi concisi in "searchFields", per assicurarsi che siano inclusi nella stringa. Se si dispone di documenti di grandi dimensioni con campi con un numero elevato di testo, qualsiasi elemento dopo il limite massimo viene ignorato.

Ogni documento è ora rappresentato da un'unica stringa long.

> [!NOTE]
> Gli input dei parametri per i modelli sono token, non caratteri o parole. La suddivisione in token è determinata in parte dall'assegnazione dell'analizzatore nei campi disponibili per la ricerca. Per informazioni dettagliate sulla modalità di suddivisione in token delle stringhe, è possibile esaminare l'output del token di un analizzatore usando l' [API REST dell'analizzatore di test](/rest/api/searchservice/test-analyzer).

## <a name="summarization"></a>Riepilogo

Al termine della riduzione delle stringhe, è ora possibile passare gli input ridotti attraverso i modelli di rappresentazione del linguaggio e comprensione della lettura del computer per determinare quali frasi e frasi riepilogano meglio il documento rispetto alla query.

Gli input per il riepilogo sono le stringhe lunghe ottenute per ogni documento della fase di preparazione. Da un input specificato, il modello di riepilogo trova un passaggio che meglio rappresenta il documento corrispondente. Questo passaggio costituisce anche una [didascalia semantica](semantic-how-to-query-request.md) per il documento. Ogni didascalia è disponibile in testo normale, con le evidenziazioni e è minore di 200 parole per documento.

Verrà restituita una [risposta semantica](semantic-answers.md) anche se è stato specificato il parametro "Answers", se la query è stata rappresentata come una domanda e se è possibile trovare un passaggio nella stringa long che probabilmente fornirà una risposta alla domanda.

## <a name="scoring-and-ranking"></a>Assegnazione di punteggi e classificazione

1. Le didascalie vengono valutate per rilevanza concettuale e semantica rispetto alla query fornita.

   Il diagramma seguente illustra il significato di "rilevanza semantica". Si consideri il termine "Capital", che può essere usato nel contesto di Finance, Law, Geography o grammatica. Se una query include termini dallo stesso spazio vettoriale (ad esempio, "capitale" e "investimento"), un documento che include anche token nello stesso cluster segnerà un punteggio superiore a quello che non lo è.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Rappresentazione vettoriale per il contesto" border="true":::

1. @search.rerankerScoreViene assegnato a ciascun documento in base alla pertinenza semantica della didascalia.

1. Dopo aver valutato tutti i documenti, questi vengono elencati in ordine decrescente in base al punteggio e inclusi nel payload della risposta alla query. Il payload include risposte, testo normale e didascalie evidenziate e tutti i campi contrassegnati come recuperabili o specificati in una clausola SELECT.

## <a name="next-steps"></a>Passaggi successivi

La classificazione semantica è disponibile nei livelli standard, in aree specifiche. Per ulteriori informazioni sulla disponibilità e l'iscrizione, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing). Un nuovo tipo di query consente le strutture di rango e risposta della ricerca semantica. Per iniziare, [creare una query semantica](semantic-how-to-query-request.md).

In alternativa, esaminare gli articoli seguenti sulla classificazione predefinita. La classificazione semantica dipende dal Ranker di somiglianza per restituire i risultati iniziali. La conoscenza dell'esecuzione delle query e della classificazione offre una conoscenza approfondita del funzionamento dell'intero processo.

+ [Ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md)
+ [Somiglianza e punteggio in Ricerca cognitiva di Azure](index-similarity-and-scoring.md)
+ [Analizzatori per l'elaborazione del testo in Azure ricerca cognitiva](search-analyzers.md)