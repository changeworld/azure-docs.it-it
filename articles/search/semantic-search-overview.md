---
title: Ricerca semantica
titleSuffix: Azure Cognitive Search
description: Scopri in che modo ricerca cognitiva usa i modelli di ricerca semantica di Deep Learning da Bing per rendere più intuitivi i risultati della ricerca.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: references_regions
ms.openlocfilehash: 443d6349aab68fd05edfe4c4007fd043c932f4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604271"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Ricerca semantica in Azure ricerca cognitiva

> [!IMPORTANT]
> La ricerca semantica è in anteprima pubblica, disponibile solo tramite l'API REST di anteprima. Le funzionalità di anteprima sono offerte così come sono, in condizioni per l' [utilizzo aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e non sono garantite che abbiano la stessa implementazione a livello generale. Queste funzionalità sono fatturabili. Per altre informazioni, vedere [disponibilità e prezzi](semantic-search-overview.md#availability-and-pricing).

La ricerca semantica è una raccolta di funzionalità correlate alle query che supportano un'esperienza di query più elevata e naturale. 

Queste funzionalità includono il riposizionamento semantico dei risultati della ricerca, nonché l'estrazione di didascalie e risposte, con evidenziazione semantica su termini e frasi pertinenti. I modelli con training avanzato vengono usati per l'estrazione e la classificazione. Per mantenere le prestazioni rapide che gli utenti si aspettano dalla ricerca, il riepilogo semantico e la classificazione vengono applicati solo ai primi 50 risultati, in base all' [algoritmo di Punteggio di somiglianza predefinito](index-similarity-and-scoring.md#similarity-ranking-algorithms). Usando i risultati come corpus del documento, la classificazione semantica ripartisce i risultati in base alla forza semantica della corrispondenza.

La tecnologia sottostante è da Bing e Microsoft Research e integrata nell'infrastruttura ricerca cognitiva come funzionalità di componente aggiuntivo. Per ulteriori informazioni sugli investimenti per la ricerca e l'intelligenza artificiale per la ricerca semantica, vedere la pagina relativa alla [modalità di alimentazione di Azure ricerca cognitiva (Blog di Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Il video seguente offre una panoramica delle funzionalità di.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Componenti e flusso di lavoro

La ricerca semantica migliora la precisione e richiama l'aggiunta delle funzionalità seguenti:

| Funzionalità | Descrizione |
|---------|-------------|
| [Controllo ortografico](speller-how-to-add.md) | Corregge i digitazioni prima che i termini della query raggiungano il motore di ricerca. |
| [Classificazione semantica](semantic-ranking.md) | Usa il contesto o il significato semantico per calcolare un nuovo punteggio di pertinenza. |
| [Didascalie e evidenziazioni semantiche](semantic-how-to-query-request.md) | Frasi e frasi da un documento che meglio riepilogare il contenuto, con le evidenziazioni sui passaggi chiave per semplificare l'analisi. Le didascalie che riepilogano un risultato sono utili quando i singoli campi di contenuto sono troppo densi per la pagina dei risultati. Il testo evidenziato eleva i termini e le frasi più rilevanti in modo che gli utenti possano determinare rapidamente il motivo per cui una corrispondenza è stata considerata pertinente. |
| [Risposte semantiche](semantic-answers.md) | Una sottostruttura facoltativa e aggiuntiva restituita da una query semantica. Fornisce una risposta diretta a una query simile a una domanda. |

### <a name="order-of-operations"></a>Ordine delle operazioni

I componenti della ricerca semantica estendono la pipeline di esecuzione di query esistente in entrambe le direzioni. Se si Abilita la correzione ortografica, il correttore [ortografico corregge i](speller-how-to-add.md) digitazioni all'inizio prima che i termini della query raggiungano il motore di ricerca.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Componenti semantici nell'esecuzione di query" border="true":::

L'esecuzione della query procede come di consueto, con l'analisi dei termini, l'analisi e le analisi degli indici invertiti. Il motore recupera i documenti usando la corrispondenza dei token e assegna punteggi ai risultati usando l' [algoritmo di Punteggio di somiglianza predefinito](index-similarity-and-scoring.md#similarity-ranking-algorithms). I punteggi vengono calcolati in base al grado di somiglianza linguistica tra i termini di query e i termini corrispondenti nell'indice. Se sono stati definiti, i profili di punteggio vengono applicati anche in questa fase. I risultati vengono quindi passati al sottosistema di ricerca semantica.

Nel passaggio di preparazione, il corpus del documento restituito dal set di risultati iniziale viene analizzato a livello di frase e paragrafo per trovare i passaggi che riepilogano ogni documento. Diversamente dalla ricerca di parole chiave, questo passaggio usa la lettura e la comprensione del computer per valutare il contenuto. Come parte della composizione dei risultati, una query semantica restituisce didascalie e risposte. Per formularle, la ricerca semantica usa la rappresentazione del linguaggio per estrarre ed evidenziare i passaggi di chiave che riepilogano meglio un risultato. Se la query di ricerca è una domanda e vengono richieste risposte, la risposta includerà anche un passaggio di testo che meglio risponde alla domanda, come espresso dalla query di ricerca. Per le didascalie e le risposte, nella formulazione viene usato il testo esistente. I modelli semantici non compongono nuove frasi o frasi dal contenuto disponibile, né applicano la logica per giungere a nuove conclusioni. In breve, il sistema non restituirà mai contenuto che non esiste già.

I risultati vengono quindi riassegnati in base alla [somiglianza concettuale](semantic-ranking.md) dei termini di query.

Per usare le funzionalità semantiche nelle query, è necessario apportare piccole modifiche alla [richiesta di ricerca](semantic-how-to-query-request.md), ma non è necessaria alcuna configurazione o reindicizzazione aggiuntiva.

## <a name="availability-and-pricing"></a>Disponibilità e prezzi

Le funzionalità semantiche sono disponibili tramite la [registrazione](https://aka.ms/SemanticSearchPreviewSignup)per l'iscrizione, nei servizi di ricerca creati a livello standard (S1, S2, S3), che si trovano in una di queste aree: Stati Uniti centro-settentrionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali 2, Europa settentrionale, Europa occidentale. 

La correzione ortografica è disponibile nelle stesse aree, ma non prevede restrizioni del livello. Se si dispone di un servizio esistente che soddisfa i criteri di livello e di area, è necessaria solo l'iscrizione.

Tra l'anteprima di avvio del 2 marzo e il 1 ° aprile, la correzione ortografica e la classificazione semantica sono disponibili gratuitamente. Dopo il 1 ° aprile, i costi di calcolo per l'esecuzione di questa funzionalità diventeranno un evento fatturabile. Il costo previsto è circa USD $500 al mese per le query 250.000. È possibile trovare informazioni dettagliate sui costi documentate nella [pagina dei prezzi ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/) e in [stima e gestione dei costi](search-sku-manage-costs.md).

## <a name="next-steps"></a>Passaggi successivi

Un nuovo tipo di query consente le strutture di classificazione e risposta della pertinenza della ricerca semantica.

[Creare una query semantica](semantic-how-to-query-request.md) per iniziare. Per informazioni correlate, vedere gli articoli seguenti.

+ [Aggiungere il controllo ortografico ai termini della query](speller-how-to-add.md)
+ [Restituisce una risposta semantica](semantic-answers.md)
+ [Classificazione semantica](semantic-ranking.md)
+ [Introduzione alla ricerca semantica (post di Blog)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Trovare informazioni significative usando le funzionalità semantiche (video di intelligenza artificiale)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)