---
title: Ricerca semantica
titleSuffix: Azure Cognitive Search
description: Scopri in che modo ricerca cognitiva usa i modelli di ricerca semantica di Deep Learning da Bing per rendere più intuitivi i risultati della ricerca.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203233"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Ricerca semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> Le funzionalità di ricerca semantica sono disponibili in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La ricerca semantica è una raccolta di funzionalità correlate alle query che supportano un'esperienza di query più elevata e naturale. Le funzionalità includono il riposizionamento semantico dei risultati della ricerca, nonché le didascalie e la generazione delle risposte con l'evidenziazione semantica. I primi 50 risultati restituiti dal [motore di ricerca full-text](search-lucene-query-architecture.md) vengono riclassificati per trovare le corrispondenze più rilevanti.

La tecnologia sottostante sfrutta gli investimenti di Bing e Microsoft Research ed è integrata nell'infrastruttura ricerca cognitiva. Per usarlo, è necessario apportare piccole modifiche alla richiesta di ricerca, ma non è necessaria alcuna configurazione o reindicizzazione aggiuntiva.

Le funzionalità di anteprima pubblica includono:

+ Modello di classificazione semantica che assegna punteggi ai risultati in base al contesto o al significato semantico dei termini della query di ricerca
+ Didascalie semantiche che evidenziano i passaggi rilevanti
+ Risposte semantiche alla query, formulate anche dai risultati
+ Controllo ortografico che corregge i digitazioni prima che i termini della query raggiungano il motore di ricerca

## <a name="semantic-search-architecture"></a>Architettura di ricerca semantica

I componenti della ricerca semantica sono sovrapposti all'inizio della pipeline di esecuzione di query esistente. La correzione ortografica (non mostrata nel diagramma) migliora il richiamo correggendo i digitazioni nei singoli termini di query. Una volta completate tutte le analisi e l'analisi, il motore di ricerca recupera i documenti corrispondenti alla query e li assegna un punteggio usando l'algoritmo di assegnazione dei punteggi [predefinito](index-similarity-and-scoring.md#similarity-ranking-algorithms), BM25 o classico, a seconda del momento in cui è stato creato il servizio. In questa fase vengono anche applicati i profili di punteggio. 

Dopo aver ricevuto le prime 50 corrispondenze, l' [algoritmo di classificazione semantica](semantic-how-to-query-response.md) valuta di nuovo il corpus del documento. I risultati possono includere più di 50 corrispondenze, ma solo il primo 50 verrà riclassificato. Per la classificazione, l'algoritmo usa sia l'apprendimento automatico che l'apprendimento del trasferimento per assegnare un punteggio ai documenti in base al livello di corrispondenza di ognuno dello scopo della query.

Per creare didascalie e risposte, USA modelli di rappresentazione del linguaggio. All'interno di una didascalia, l'algoritmo usa un modello di linguaggio sviluppato da Bing per estrarre le didascalie con i punti salienti che riepilogano meglio i risultati della query. Se la query di ricerca è una domanda e vengono richieste risposte, un modello di linguaggio simile identifica i passaggi di testo che meglio rispondono alla domanda come espresso dalla query di ricerca.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Componenti semantici in una pipeline di query" border="true":::

## <a name="availability-and-pricing"></a>Disponibilità e prezzi

La classificazione semantica è disponibile tramite la [registrazione](https://aka.ms/SemanticSearchPreviewSignup)per l'iscrizione, nei servizi di ricerca creati a livello standard (S1, S2, S3), che si trovano in una di queste aree: Stati Uniti centro-settentrionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale. Un servizio di ricerca esistente a S1 o superiore nelle aree indicate è idoneo per l'anteprima (non è necessario creare un nuovo servizio).

La correzione ortografica è disponibile nelle stesse aree, ma non prevede restrizioni per il livello e nessun requisito per l'iscrizione. 

Tra l'anteprima di avvio del 2 marzo e il 1 ° aprile, la correzione ortografica e la classificazione semantica sono disponibili gratuitamente. Dopo il 1 ° aprile, i costi di calcolo per l'esecuzione di questa funzionalità diventeranno un evento fatturabile. Il costo previsto è circa USD $500 al mese per le query 250.000. È possibile trovare informazioni dettagliate sui costi documentate nella [pagina dei prezzi ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/) e in [stima e gestione dei costi](search-sku-manage-costs.md).

## <a name="next-steps"></a>Passaggi successivi

Un nuovo tipo di query consente le strutture di classificazione e risposta della pertinenza della ricerca semantica. [Creare una query semantica](semantic-how-to-query-request.md) per iniziare. In alternativa, per informazioni correlate, vedere uno degli articoli seguenti.

+ [Aggiungere il controllo ortografico ai termini della query](speller-how-to-add.md)
+ [Classificazione e risposte semantiche](semantic-how-to-query-response.md)