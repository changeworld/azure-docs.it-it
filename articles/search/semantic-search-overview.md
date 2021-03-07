---
title: Ricerca semantica
titleSuffix: Azure Cognitive Search
description: Scopri in che modo ricerca cognitiva usa i modelli di ricerca semantica di Deep Learning da Bing per rendere più intuitivi i risultati della ricerca.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432980"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Ricerca semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> Le funzionalità di ricerca semantica sono disponibili in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La ricerca semantica è una raccolta di funzionalità correlate alle query che supportano un'esperienza di query più elevata e naturale. Le funzionalità includono il riposizionamento semantico dei risultati della ricerca, nonché le didascalie e la generazione delle risposte con l'evidenziazione semantica. I primi 50 risultati restituiti dal [motore di ricerca full-text](search-lucene-query-architecture.md) vengono riclassificati per trovare le corrispondenze più rilevanti.

La tecnologia sottostante è da Bing e Microsoft Research e integrata nell'infrastruttura ricerca cognitiva. Per ulteriori informazioni sugli investimenti per la ricerca e l'intelligenza artificiale per la ricerca semantica, vedere la pagina relativa alla [modalità di alimentazione di Azure ricerca cognitiva (Blog di Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Per utilizzare la ricerca semantica nelle query, è necessario apportare piccole modifiche alla richiesta di ricerca, ma non è necessaria alcuna configurazione o reindicizzazione aggiuntiva.

Le funzionalità di anteprima pubblica includono:

+ Modello di classificazione semantica che usa il contesto o il significato semantico per calcolare un punteggio di pertinenza
+ Didascalie semantiche che riepilogano i passaggi principali da un risultato per semplificare l'analisi
+ Risposte semantiche alla query, se la query è una domanda
+ Evidenziazioni semantiche che portano lo stato attivo a frasi chiave e termini
+ Controllo ortografico che corregge i digitazioni prima che i termini della query raggiungano il motore di ricerca

## <a name="availability-and-pricing"></a>Disponibilità e prezzi

La classificazione semantica è disponibile tramite la [registrazione](https://aka.ms/SemanticSearchPreviewSignup)per l'iscrizione, nei servizi di ricerca creati a livello standard (S1, S2, S3), che si trovano in una di queste aree: Stati Uniti centro-settentrionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale. La correzione ortografica è disponibile nelle stesse aree, ma non prevede restrizioni del livello. Se si dispone di un servizio esistente che soddisfa i criteri di livello e di area, è necessaria solo l'iscrizione.

Tra l'anteprima di avvio del 2 marzo e il 1 ° aprile, la correzione ortografica e la classificazione semantica sono disponibili gratuitamente. Dopo il 1 ° aprile, i costi di calcolo per l'esecuzione di questa funzionalità diventeranno un evento fatturabile. Il costo previsto è circa USD $500 al mese per le query 250.000. È possibile trovare informazioni dettagliate sui costi documentate nella [pagina dei prezzi ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/) e in [stima e gestione dei costi](search-sku-manage-costs.md).

## <a name="semantic-search-architecture"></a>Architettura di ricerca semantica

I componenti della ricerca semantica sono sovrapposti all'inizio della pipeline di esecuzione di query esistente. La correzione ortografica (non mostrata nel diagramma) migliora il richiamo correggendo i digitazioni nei singoli termini di query. Al termine dell'analisi e dell'analisi, il motore di ricerca recupera i documenti corrispondenti alla query e li assegna un punteggio usando l'algoritmo di assegnazione dei punteggi [predefinito](index-similarity-and-scoring.md#similarity-ranking-algorithms), ovvero BM25 o classico, a seconda del momento in cui è stato creato il servizio. In questa fase vengono anche applicati i profili di punteggio.

Dopo aver ricevuto le prime 50 corrispondenze, il [modello di classificazione semantica](semantic-how-to-query-response.md) valuta nuovamente il corpus del documento. I risultati possono includere più di 50 corrispondenze, ma solo il primo 50 verrà riclassificato. Per la classificazione, il modello USA sia l'apprendimento automatico che quello di trasferimento per assegnare un punteggio ai documenti in base al livello di corrispondenza con lo scopo della query.

Per creare didascalie e risposte, la ricerca semantica usa la rappresentazione del linguaggio per estrarre ed evidenziare i passaggi chiave che riepilogano meglio un risultato. Se la query di ricerca è una domanda e vengono richieste risposte, la risposta includerà un passaggio di testo che meglio risponde alla domanda, come espresso dalla query di ricerca.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Componenti semantici in una pipeline di query" border="true":::

## <a name="next-steps"></a>Passaggi successivi

Un nuovo tipo di query consente le strutture di classificazione e risposta della pertinenza della ricerca semantica.

[Creare una query semantica](semantic-how-to-query-request.md) per iniziare. In alternativa, per informazioni correlate, vedere uno degli articoli seguenti.

+ [Aggiungere il controllo ortografico ai termini della query](speller-how-to-add.md)
+ [Classificazione e risposte semantiche (risposte e didascalie)](semantic-how-to-query-response.md)