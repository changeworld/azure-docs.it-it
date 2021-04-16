---
title: Limiti - LUIS
description: Questo articolo illustra i limiti di LUIS (Language Understanding) dei Servizi cognitivi di Azure. LUIS ha diverse aree di limiti. Il limite del modello controlla finalità, entità e funzionalità in LUIS. I limiti di quota si basano sul tipo di chiave. La combinazione di tasti controlla il sito Web di LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 1f917087eb15d8c77356995299e27dfc1657cb5d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497201"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limiti per il modello LUIS e le chiavi
LUIS ha diverse aree limite. Il primo è il [limite del modello](#model-limits), che controlla finalità, entità e funzionalità in LUIS. La seconda area è [limiti di quota](#key-limits) basata sul tipo di chiave. Una terza area di limiti è la combinazione [di tasti](#keyboard-controls) per il controllo del sito Web LUIS. Una quarta area è data dal [mapping dell'area globale](luis-reference-regions.md) tra il sito Web di creazione LUIS e le API dell'[endpoint LUIS](luis-glossary.md#endpoint).

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Limiti del modello

Se l'app supera i limiti del modello LUIS, è consigliabile usare un'app [luiS Dispatch](luis-concept-enterprise.md#dispatch-tool-and-model) o un [contenitore LUIS.](luis-container-howto.md)

|Area|Limite|
|--|:--|
| [Nome app][luis-get-started-create-app] | *Numero max predefinito di caratteri |
| Applicazioni| 500 applicazioni per risorsa di creazione di Azure |
| [Test in batch][batch-testing]| 10 set di dati, 1000 espressioni per ogni set di dati|
| Elenco esplicito | 50 per applicazione|
| Entità esterne | nessun limite |
| [Intenti][intents]|500 per applicazione: 499 finalità personalizzate e la _finalità None obbligatoria._<br>[L'applicazione basata su](https://aka.ms/dispatch-tool) dispatch ha 500 origini di invio corrispondenti.|
| [Elencare entità](./luis-concept-entity-types.md) | Padre: 50, figlio: 20.000 elementi. Il nome canonico è il *numero max predefinito di caratteri. I sinonimi non hanno restrizioni di lunghezza. |
| [entità e ruoli di Machine Learning:](./luis-concept-entity-types.md)<br> Composito<br>Semplice<br>ruolo entità|Un limite di 100 entità padre o 330 entità, a seconda del limite raggiunto per primo dall'utente. Un ruolo viene conteggiato come entità ai fini di questo limite. Un esempio è un composito con un'entità semplice, che ha 2 ruoli: 1 ruolo composito + 1 ruolo semplice + 2 = 4 delle 330 entità.<br>Le entità secondarie possono essere annidate fino a 5 livelli, con un massimo di 20 elementi figlio per ogni livello.|
|Modello come funzionalità| Numero massimo di modelli che possono essere usati come funzionalità per un modello specifico per essere 10 modelli. Il numero massimo di elenchi di frasi usato come funzionalità per un modello specifico è 10 elenchi di frasi.|
| [Anteprima - Entità elenco dinamico](./luis-migration-api-v3.md)|2 elenchi di ~1.000 per ogni richiesta dell'endpoint di stima delle query|
| [Modelli](luis-concept-patterns.md)|500 criteri per ogni applicazione.<br>Il criterio può contenere al massimo 400 caratteri.<br>3 entità pattern.any per criterio<br>Il criterio può contenere al massimo 2 testi facoltativi annidati|
| [Pattern.any](./luis-concept-entity-types.md)|100 per applicazione, 3 entità pattern.any per criterio |
| [Elenco di frasi][phrase-list]|500 elenchi di frasi. 10 elenchi di frasi globali a causa del modello come limite di funzionalità. L'elenco di frasi non intercambiabile ha un massimo di 5.000 frasi. L'elenco di frasi intercambiabile ha un massimo di 50.000 frasi. Numero massimo di frasi totali per applicazione di 500.000 frasi.|
| [Entità predefinite](./howto-add-prebuilt-models.md) | nessun limite|
| [Entità di espressione regolare](./luis-concept-entity-types.md)|20 entità<br>È consentito un numero massimo di 500 caratteri. per ogni criterio di entità di espressione regolare|
| [Ruoli](./luis-concept-entity-types.md)|300 ruoli per ogni applicazione. 10 per entità|
| [Espressione][utterances] | 500 caratteri<br><br>Se il testo è più lungo di questo limite di caratteri, è necessario segmentare l'espressione prima dell'input in LUIS e si riceveranno risposte di finalità singole per segmento. È possibile usare interruzioni ovvie, ad esempio segni di punteggiatura e lunghe pause nel parlato.|
| [Esempi di espressioni][utterances] | 15.000 per ogni applicazione: non è previsto alcun limite al numero di espressioni per finalità<br><br>Se è necessario eseguire il training dell'applicazione con altri esempi, usare un approccio [del modello di](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) distribuzione. È possibile eseguire il training di singole app LUIS (note come app figlio all'app dispatch padre) con una o più finalità e quindi eseguire il training di un'app dispatch che eserciti gli esempi dalle espressioni di ogni app LUIS figlio per indirizzare la richiesta di stima all'app figlio corretta. |
| [Versions](./luis-concept-app-iteration.md) (Versioni)| 100 versioni per ogni applicazione |
| [Nome della versione][luis-how-to-manage-versions] | 128 caratteri |

*Il numero max predefinito di caratteri è 50.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Univocità dei nomi

I nomi degli oggetti devono essere univoci rispetto ad altri oggetti dello stesso livello.

|Oggetti|Restrizioni|
|--|--|
|Finalità, entità|Tutti i nomi di finalità ed entità devono essere univoci in una versione di un'app.|
|Componenti dell'entità ml|Tutti i componenti dell'entità di Machine Learning (entità figlio) devono essere univoci, all'interno di tale entità per i componenti allo stesso livello.|
|Funzionalità | Tutte le funzionalità denominate, ad esempio gli elenchi di frasi, devono essere univoche all'interno di una versione di un'app.|
|Ruoli entità|Tutti i ruoli in un'entità o in un componente dell'entità devono essere univoci quando sono allo stesso livello di entità (padre, figlio, child e così via).|

## <a name="object-naming"></a>Denominazione degli oggetti

Non usare i caratteri seguenti nei nomi seguenti.

|Oggetto|Escludi caratteri|
|--|--|
|Nomi di finalità, entità e ruoli|`:`<br>`$` <br> `&`|
|Nome della versione|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Utilizzo e limiti delle risorse

Language Understand include risorse separate, un tipo per la creazione e un tipo per l'esecuzione di query sull'endpoint di stima. Per altre informazioni sulle differenze tra i tipi di chiave, vedere [Chiavi di creazione e di endpoint per query di stima in LUIS](luis-how-to-azure-subscription.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limiti delle risorse di creazione

Usare il _tipo_, `LUIS.Authoring` , per filtrare le risorse nel portale di Azure. LUIS limita 500 applicazioni per ogni risorsa di creazione di Azure.

|Risorsa di creazione|Creazione di TPS|
|--|--|
|Starter|1 milione/mese, 5/secondo|
|F0 - Livello gratuito |1 milione/mese, 5/secondo|

* TPS = Transazioni al secondo

[Altre informazioni sui prezzi.][pricing]

### <a name="query-prediction-resource-limits"></a>Limiti delle risorse di stima delle query

Usare il _tipo_ `LUIS` , , quando si filtrano le risorse nel portale di Azure. La risorsa endpoint di stima delle query LUIS, usata nel runtime, è valida solo per le query dell'endpoint.

|Risorsa Stima query|Query TPS|
|--|--|
|F0 - Livello gratuito |10.000/mese, 5/secondo|
|S0 - Livello Standard|50/secondo|

### <a name="sentiment-analysis"></a>Analisi del sentiment

[L'integrazione dell'analisi](luis-how-to-publish-app.md#enable-sentiment-analysis)del sentiment, che fornisce informazioni sul sentiment, viene fornita senza richiedere un'altra risorsa di Azure.

### <a name="speech-integration"></a>Integrazione riconoscimento vocale

[L'integrazione vocale](../speech-service/how-to-recognize-intents-from-speech-csharp.md) offre 1.000 richieste di endpoint per costo unitario.

[Altre informazioni sui prezzi.][pricing]

## <a name="keyboard-controls"></a>Controlli tastiera

|Input da tastiera | Descrizione |
|--|--|
|Controllo + E|passa dai token alle entità nell'elenco delle espressioni|

## <a name="website-sign-in-time-period"></a>Periodo di tempo di accesso al sito Web

Il periodo di tempo di accesso è di **60 minuti**. Trascorso questo tempo, si verificherà il seguente errore. Sarà necessario eseguire nuovamente l'accesso.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
