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
ms.openlocfilehash: e3078c8f71f8862cacad552bb3176c08530e79bb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418845"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificazione semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> Le funzionalità di ricerca semantica sono disponibili in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e non sono garantite che abbiano la stessa implementazione a livello generale. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

La classificazione semantica è un'estensione della pipeline di esecuzione delle query che consente di migliorare la precisione e il richiamo riallineando le prime corrispondenze di un set di risultati iniziale. La classificazione semantica è supportata da modelli di comprensione della lettura di macchine complete all'avanguardia, sottoposti a training per le query espresse nel linguaggio naturale anziché alla corrispondenza linguistica delle parole chiave. Diversamente dall'algoritmo di [classificazione di somiglianza predefinito](index-ranking-similarity.md), il rango semantico usa il contesto e il significato delle parole per determinare la pertinenza.

## <a name="how-semantic-ranking-works"></a>Funzionamento della classificazione semantica

La classificazione semantica è a elevato utilizzo di risorse e tempo. Per completare l'elaborazione entro la latenza prevista di un'operazione di query, il modello accetta come input solo i primi 50 documenti restituiti dall'algoritmo di [classificazione di somiglianza](index-ranking-similarity.md)predefinito. I risultati della classificazione iniziale possono includere più di 50 corrispondenze, ma solo il primo 50 verrà riclassificato semanticamente. 

Per la classificazione semantica, il modello USA sia la comprensione della lettura del computer che l'apprendimento del trasferimento per assegnare un punteggio ai documenti in base a quanto corrisponde allo scopo della query.

1. Per ogni documento, il Ranker semantico valuta i campi nel parametro searchFields in ordine, consolidando il contenuto in un'unica stringa di grandi dimensioni.

1. La stringa viene quindi ritagliata per garantire che la lunghezza complessiva non superi 8.000 token. Se si dispone di documenti di grandi dimensioni, con un campo del contenuto o merged_content campo con molte pagine di contenuto, qualsiasi elemento dopo il limite del token viene ignorato.

1. Ognuno dei documenti 50 è ora rappresentato da un'unica stringa long. Questa stringa viene inviata al modello di riepilogo. Il modello di riepilogo produce didascalie (e risposte), usando la comprensione della lettura del computer per identificare i passaggi che sembrano riepilogare il contenuto o rispondere alla domanda. L'output del modello di riepilogo è un'ulteriore stringa ridotta, che rappresenta al massimo 128 token.

1. La stringa più piccola diventa la didascalia del documento e rappresenta i passaggi più rilevanti trovati nella stringa più grande. Il set di 50 (o meno) didascalie viene quindi classificato in pertinenza dell'ordine. 

La pertinenza concettuale e semantica viene stabilita tramite la rappresentazione vettoriale e i cluster dei termini. Mentre un algoritmo di somiglianza delle parole chiave può dare lo stesso peso a qualsiasi termine nella query, il modello semantico è stato sottoposto a training per riconoscere l'interdipendenza e le relazioni tra le parole che non sono altrimenti correlate sulla superficie. Di conseguenza, se una stringa di query include termini dello stesso cluster, un documento che contiene entrambe le dimensioni sarà maggiore di uno.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Rappresentazione vettoriale per il contesto" border="true":::

## <a name="next-steps"></a>Passaggi successivi

La classificazione semantica è disponibile nei livelli standard, in aree specifiche. Per ulteriori informazioni e per iscriversi, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

Un nuovo tipo di query consente le strutture di classificazione e risposta della pertinenza della ricerca semantica. [Creare una query semantica](semantic-how-to-query-request.md) per iniziare.

In alternativa, per informazioni correlate, vedere uno degli articoli seguenti.

+ [Aggiungere il controllo ortografico ai termini della query](speller-how-to-add.md)
+ [Restituisce una risposta semantica](semantic-answers.md)