---
title: Finalità ed entità - LUIS
titleSuffix: Azure Cognitive Services
description: Una singola finalità rappresenta un'attività o un'azione che l'utente vuole eseguire. È un obiettivo espresso in un'espressione dell'utente. Definire un set di finalità che corrisponde alle azioni che gli utenti desiderano eseguire nell'applicazione.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 8e76e3e7683d43a7a39bc0c168a29016a988c705
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499411"
---
# <a name="intents-in-your-luis-app"></a>Finalità nell'app LUIS

Una finalità rappresenta un'attività o un'azione che l'utente desidera eseguire. Si tratta di uno scopo o di un obiettivo espresso nell'espressione [di un utente.](luis-concept-utterance.md)

Definire un set di finalità che corrisponde alle azioni che gli utenti desiderano eseguire nell'applicazione. Ad esempio, un'app di viaggi definisce diverse finalità:

Finalità dell'app di viaggi   |   Espressioni di esempio   |
------|------|
 BookFlight (PrenotaVolo)     |   "Prenotami un volo per Rio la settimana prossima" <br/> "Fammi volare a Rio il 24" <br/> "Ho bisogno di un biglietto aereo per Rio de Janeiro per domenica prossima"    |
 Greeting (Messaggio introduttivo)     |   "Ciao" <br/>"Hello" <br/>"Buongiorno"  |
 Meteo | "Com'è il tempo a Boston?" <br/> "Mostrami le previsioni per il weekend" |
 nessuno         | "Dammi una ricetta di biscotti"<br>"Ha vinto la Roma?" |

Tutte le applicazioni hanno la finalità predefinita , "[None](#none-intent)", che è la finalità di fallback.

## <a name="prebuilt-domains-provide-intents"></a>I domini predefiniti forniscono finalità
Oltre alle finalità definite, è possibile usare finalità predefinite di uno dei [domini predefiniti.](./howto-add-prebuilt-models.md)

## <a name="return-all-intents-scores"></a>Restituire i punteggi di tutte le finalità
Assegnare un'espressione a una singola finalità. Quando LUIS riceve un'espressione sull'endpoint, per impostazione predefinita restituisce la finalità principale per tale espressione.

Se si vogliono i punteggi per tutte le finalità per l'espressione, è possibile specificare un flag sulla stringa di query dell'API di stima.

|Versione dell'API di stima|Contrassegno|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Finalità ed entità a confronto
La finalità rappresenta l'azione che l'applicazione deve eseguire per l'utente ed è basata sull'intera espressione. Un'espressione può disporre di una sola finalità punteggio più alto, ma può avere molte entità.

<a name="how-do-intents-relate-to-entities"></a>

 Creare una finalità quando l'_intenzione_ dell'utente avvierebbe un'azione nell'applicazione client, ad esempio una chiamata alla funzione checkweather(). Creare quindi entità per rappresentare i parametri necessari per eseguire l'azione.

|Finalità   | Entità | Espressione di esempio   |
|------------------|------------------------------|------------------------------|
| Meteo | { "type": "location", "entity": "Seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Qual è il meteo in `Seattle` `tomorrow` ? |
| Meteo | { "type": "date_range", "entity": "this weekend" } | Mostrami le previsioni per `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Finalità di domini predefiniti

[I domini predefiniti](./howto-add-prebuilt-models.md) forniscono finalità con espressioni.

## <a name="none-intent"></a>Finalità None

La finalità **None** (Nessuna) viene creata ma lasciata vuota di proposito. La **finalità Nessuno** è una finalità obbligatoria e non può essere eliminata o rinominata. Inserire le espressioni che si trovano all'esterno del dominio.

La **finalità Nessuna** è la finalità di fallback, importante in ogni app e deve avere il 10% delle espressioni totali. Consente di insegnare a LUIS espressioni che non sono importanti nel dominio dell'app (area di interesse). Se non si aggiungono espressioni per la finalità **None** (Nessuna), LUIS forza un'espressione che si trova all'esterno del dominio in una delle finalità del dominio. Questo distorcerà i punteggi di stima perché a LUIS verrà insegnata la finalità errata dell'espressione.

Quando un'espressione viene stimata come finalità Nessuna, l'applicazione client può porre altre domande o fornire un menu per indirizzare l'utente a scelte valide.

## <a name="negative-intentions"></a>Intenzioni negative
Per determinare intenzioni negative e positive, ad esempio "**Voglio** un'auto" e "**Non** voglio un'auto", è possibile creare due finalità (una positiva e una negativa) e aggiungere espressioni appropriate per ognuna. In alternativa, è possibile creare una singola finalità e contrassegnare i due diversi termini positivi e negativi come entità.

## <a name="intents-and-patterns"></a>Finalità e modelli

Se si dispone di espressioni di esempio, che possono essere definite in [](luis-concept-entity-types.md#regex-entity) parte o nell'intero come espressione regolare, è consigliabile usare l'entità di espressione regolare abbinata a un [criterio](luis-concept-patterns.md).

L'uso di un'entità di espressione regolare garantisce l'estrazione dei dati in modo che il criterio corrisponda. I criteri di ricerca garantiscono che viene restituita una finalità esatta.

## <a name="intent-balance"></a>Bilanciamento tra finalità
Nelle finalità del dominio dell'app le espressioni devono essere equamente distribuite. Evitare di avere una finalità con 10 espressioni e un'altra con 500. Questa non è una distribuzione bilanciata. Se dovesse crearsi questa situazione, esaminare la finalità con 500 espressioni per accertare se alcune delle finalità possono essere riorganizzate in un [criterio](luis-concept-patterns.md).

La finalità **None** non è inclusa nel bilanciamento. La finalità dovrebbe contenere il 10% delle espressioni totali nell'app.

## <a name="intent-limits"></a>Limiti della finalità
Rivedere i [limiti](luis-limits.md#model-boundaries) per capire quante finalità è possibile aggiungere a un modello.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se è necessario un numero superiore al numero massimo di finalità
Valutare innanzitutto se il sistema usa troppe finalità.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>È possibile combinare più finalità in una singola finalità con entità
Se le finalità sono troppo simili, LUIS le distinguerà con maggiore difficoltà. Le finalità devono essere sufficientemente varie per acquisire le attività principali che chiede l'utente, anche se non è necessario che acquisiscano ogni percorso accettato dal codice. Ad esempio, BookFlight e BookHotel potrebbero essere finalità separate in un'app di viaggi, ma BookInternationalFlight e BookDomesticFlight sono troppo simili. Se il sistema deve fare distinzione tra di essi, usare le entità o altra logica anziché le finalità.

### <a name="dispatcher-model"></a>Modello dispatcher
Ulteriori informazioni sulla combinazione di app LUIS e QnA Maker con il [modello dispatcher](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Richiedere la guida per le app con un numero significativo di finalità
Se ridurre il numero delle finalità o dividere le finalità in più app non comporta alcun miglioramento, contattare l'assistenza. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulle [entità](luis-concept-entity-types.md), ovvero parole importanti rilevanti per le finalità
* Ulteriori informazioni sull'[aggiunta e la gestione delle finalità](luis-how-to-add-intents.md) nell'app LUIS.
* Rivedere le [procedure consigliate](luis-concept-best-practices.md) delle finalità