---
title: Domande frequenti sull'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Risposte alle domande frequenti su concetti, codice e scenari correlati alla API Analisi del testo per servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: aahi
ms.openlocfilehash: 9a4e179767cc38169cd794f4cd629604bdcdaab0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97955043"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Domande frequenti sull'API Analisi del testo

 Trovare le risposte alle domande frequenti su concetti, codice e scenari correlati alla API Analisi del testo in Servizi cognitivi di Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Analisi del testo può identificare il sarcasmo?

L'analisi prende in esame il sentiment positivo/negativo più che rilevare l'umore.

Esiste sempre un certo grado di imprecisione nell'analisi del sentiment, ma il modello è molto utile quando il contenuto non comprende significati nascosti o sottintesi. Ironia, sarcasmo, umorismo e sfumature del contenuto simili appartengono al contesto culturale e al modo di comunicare la finalità. Questo tipo di contenuto è tra i più complessi da analizzare. In genere, la discrepanza maggiore tra un determinato punteggio generato dall'analizzatore e la valutazione soggettiva di una persona si verifica per i contenuti con significato più articolato.

## <a name="can-i-add-my-own-training-data-or-models"></a>È possibile aggiungere i propri modelli o dati di training?

No, i modelli sono sottoposti a training preliminare. Le sole operazioni disponibili per i dati caricati sono punteggio, estrazione di frasi chiave e rilevamento della lingua. Non vengono ospitati modelli personalizzati. Per creare e ospitare modelli di apprendimento automatico personalizzato, considerare le [funzionalità di apprendimento automatico in Microsoft R Server](/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>È possibile richiedere altre lingue?

L'analisi del sentiment e l'estrazione di frasi chiave sono disponibili per un [numero selezionato di lingue](./language-support.md). L'elaborazione del linguaggio naturale è complessa ed è necessario effettuare test sostanziali prima di poter rilasciare una nuova funzionalità. Microsoft evita quindi di preannunciare il supporto per evitare che qualcuno stabilisca una dipendenza da funzionalità non ancora mature. 

Per contribuire a classificare in ordine di priorità le lingue su cui lavorare per prime, votare le lingue specifiche in [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Perché l'estrazione di frasi chiave restituisce alcune parole, ma non altre?

L'estrazione di frasi chiave elimina le parole non essenziali e gli aggettivi autonomi. Le combinazioni nome-aggettivo, ad esempio "vista incredibile" o "tempo nebbioso" vengono restituite insieme.

L'output è in genere costituito da nomi e oggetti della frase. L'output viene elencato in ordine di importanza, con la frase più importante per prima. L'importanza è data dal numero di volte in cui un particolare concetto viene citato o dalla relazione di tale elemento con gli altri elementi del testo.

## <a name="why-does-output-vary-given-identical-inputs"></a>Perché l'output varia, anche se gli input sono identici?

I miglioramenti ai modelli e agli algoritmi vengono annunciati se la modifica è rilevante oppure vengono integrati nel servizio senza notifiche se l'aggiornamento è secondario. Nel corso del tempo, è possibile che lo stesso input di testo restituisca un punteggio del sentiment o un output di frasi chiave diversi. Si tratta di una conseguenza normale e voluta dell'uso di risorse di apprendimento automatico gestite nel cloud.

## <a name="service-availability-and-redundancy"></a>Disponibilità e ridondanza del servizio

### <a name="is-text-analytics-service-zone-resilient"></a>La resilienza della zona di servizio è Analisi del testo?

Sì. Per impostazione predefinita, il servizio Analisi del testo è resiliente per la zona.

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Ricerca per categorie configurare il servizio Analisi del testo per la resilienza della zona?

Non è necessaria alcuna configurazione del cliente per abilitare la resilienza della zona. La resilienza della zona per Analisi del testo risorse è disponibile per impostazione predefinita e viene gestita dal servizio stesso.

## <a name="next-steps"></a>Passaggi successivi

La domanda riguarda una funzione o una funzionalità mancante? È possibile richiederla o votarla nel [sito Web UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Vedi anche

 * [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api) (StackOverflow: API Analisi del testo)   
 * [StackOverflow: Servizi cognitivi](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
