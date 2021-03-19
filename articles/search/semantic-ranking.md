---
title: Classificazione semantica
titleSuffix: Azure Cognitive Search
description: Viene descritto l'algoritmo di classificazione semantica in ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604288"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificazione semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> Le funzionalità di ricerca semantica sono disponibili in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e non sono garantite che abbiano la stessa implementazione a livello generale. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

La classificazione semantica è un'estensione della pipeline di esecuzione delle query che consente di migliorare la precisione e il richiamo riallineando le prime corrispondenze di un set di risultati iniziale. La classificazione semantica è supportata da modelli di comprensione della lettura di macchine complete all'avanguardia, sottoposti a training per le query espresse nel linguaggio naturale anziché alla corrispondenza linguistica delle parole chiave. Diversamente dall'algoritmo di [classificazione di somiglianza predefinito](index-ranking-similarity.md), il rango semantico usa il contesto e il significato delle parole per determinare la pertinenza.

## <a name="how-semantic-ranking-works"></a>Funzionamento della classificazione semantica

La classificazione semantica è a elevato utilizzo di risorse e tempo. Per completare l'elaborazione entro la latenza prevista di un'operazione di query, il modello accetta come input solo i primi 50 documenti restituiti dall'algoritmo di [classificazione di somiglianza](index-ranking-similarity.md)predefinito. I risultati della classificazione iniziale possono includere più di 50 corrispondenze, ma solo il primo 50 verrà riclassificato semanticamente. 

Per la classificazione semantica, il modello USA sia la comprensione della lettura del computer che l'apprendimento del trasferimento per assegnare un punteggio ai documenti in base a quanto corrisponde allo scopo della query.

### <a name="preparation-passage-extraction-phase"></a>Fase preparazione (estrazione del passaggio)

Per ogni documento nei risultati iniziali, si verifica un esercizio di estrazione del passaggio che identifica i passaggi di chiave. Si tratta di un esercizio di ridimensionamento che riduce il contenuto a un importo che può essere elaborato rapidamente.

1. Per ognuno dei documenti 50, ogni campo nel parametro searchFields viene valutato in ordine consecutivo. Il contenuto di ogni campo viene consolidato in una stringa di lunghezza. 

1. La stringa lunga viene quindi ritagliata per garantire che la lunghezza complessiva non superi 8.000 token. Per questo motivo, è consigliabile posizionare prima i campi concisi in modo che siano inclusi nella stringa. Se si dispone di documenti di grandi dimensioni con campi con un numero elevato di testo, qualsiasi elemento dopo il limite del token viene ignorato.

1. Ogni documento è ora rappresentato da un'unica stringa long che è fino a 8.000 token. Queste stringhe vengono inviate al modello di riepilogo, in modo da ridurre ulteriormente la stringa. Il modello di riepilogo valuta la stringa estesa per le frasi chiave o i passaggi che meglio riepilogano il documento o rispondono alla domanda.

1. L'output di questa fase è una didascalia e, facoltativamente, una risposta. La didascalia è al massimo 128 token per documento ed è considerata la più rappresentativa del documento.

### <a name="scoring-and-ranking-phases"></a>Fasi di valutazione e classificazione

In questa fase vengono valutate tutte le didascalie 50 per la valutazione della pertinenza.

1. Il Punteggio è determinato dalla valutazione di ogni didascalia per la pertinenza concettuale e semantica, relativa alla query fornita.

   Il diagramma seguente illustra il significato di "rilevanza semantica". Si consideri il termine "Capital", che può essere usato nel contesto di Finance, Law, Geography o grammatica. Se una query include termini dallo stesso spazio vettoriale (ad esempio, "capitale" e "investimento"), un documento che include anche token nello stesso cluster segnerà un punteggio superiore a quello che non lo è.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Rappresentazione vettoriale per il contesto" border="true":::

1. L'output di questa fase è @search.rerankerScore assegnato a ogni documento. Dopo aver valutato tutti i documenti, questi vengono elencati in ordine decrescente e inclusi nel payload della risposta alla query.

## <a name="next-steps"></a>Passaggi successivi

La classificazione semantica è disponibile nei livelli standard, in aree specifiche. Per ulteriori informazioni e per iscriversi, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing). Un nuovo tipo di query consente le strutture di classificazione e risposta della pertinenza della ricerca semantica. Per iniziare, [creare una query semantica](semantic-how-to-query-request.md).

In alternativa, per informazioni correlate, vedere uno degli articoli seguenti.

+ [Panoramica della ricerca semantica](semantic-search-overview.md)
+ [Restituisce una risposta semantica](semantic-answers.md)