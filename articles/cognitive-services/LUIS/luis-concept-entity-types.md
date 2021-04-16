---
title: Tipi di entità - LUIS
description: Un'entità estrae i dati da un'espressione utente in fase di esecuzione della stima. Uno _scopo_ secondario facoltativo è aumentare la stima della finalità o di altre entità usando l'entità come funzionalità.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500329"
---
# <a name="entities-in-luis"></a>Entità in LUIS

Un'entità è un elemento o un elemento rilevante per la finalità dell'utente. Le entità definiscono i dati che possono essere estratti dall'espressione ed è essenziale per completare l'azione richiesta di un utente. Ad esempio:

|Espressione|Finalità stimata|Entità estratte|Spiegazione|
|--|--|--|--|
|Ciao come stai?|Greeting (Messaggio introduttivo)|-|Niente da estrarre.|
|Voglio ordinare una pizza piccola|orderPizza| "small" | L'entità "Size" viene estratta come "small".|
|Disattivare la luce della camera da letto|Bivio| "camera da letto" | L'entità "Room" viene estratta come "camera da letto".|
|Controllare il saldo nel conto di risparmio che termina con 4406|checkBalance| "savings", "4406" | L'entità "accountType" viene estratta come entità "savings" e "accountNumber" come "4406".|
|Buy 3 tickets to New York|buyTickets| "3", "New York" | L'entità "ticketsCount" viene estratta come "3" e l'entità "Destination" come "New York".|

Le entità sono facoltative ma consigliate. Non è necessario creare entità per ogni concetto nell'app, solo per quelli in cui:

* L'applicazione client richiede i dati, o 
* L'entità funge da suggerimento o segnale per un'altra entità o finalità. Per altre informazioni sulle entità come Funzionalità, vedere [Entità come funzionalità.](#entities-as-features)

## <a name="entity-types"></a>Tipi di entità

Per creare un'entità, è necessario assegnargli un nome e un tipo. In LUIS sono disponibili diversi tipi di entità. 

### <a name="list-entity"></a>Entità elenco

Un'entità elenco rappresenta un set fisso e chiuso di parole correlate insieme ai relativi sinonimi. È possibile usare le entità elenco per riconoscere più sinonimi o varianti ed estrarre un output normalizzato. Usare *l'opzione* consigliata per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. 

Un'entità elenco non è basata su Machine Learned, vale a dire che LUIS non individua valori aggiuntivi per le entità elenco. LUIS contrassegna eventuali corrispondenze a un elemento in qualsiasi elenco come un'entità nella risposta.

La corrispondenza nelle entità elenco fa distinzione tra maiuscole e minuscole e deve essere una corrispondenza esatta da estrarre. I valori normalizzati vengono usati anche per la corrispondenza dell'entità elenco. Ad esempio:

|Valore normalizzato|Sinonimi|
|--|--|
|Small|sm, sml, tiny, smallest|
|Media|md, mdm, regular, average, middle|
|large|lg, lrg, big|

Per altre [informazioni, vedere l'articolo di riferimento](reference-entity-list.md) sulle entità elenco.

### <a name="regex-entity"></a>Entità Regex

Un entità di espressione regolare estrae un'entità in base a un criterio di espressione regolare fornito dall'utente. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua. L'espressione regolare è ideale per il testo strutturato o per una sequenza predefinita di valori alfanumerici previsti in un determinato formato. Ad esempio:

|Entità|Espressione regolare‏|Esempio|
|--|--|--|
|Numero di volo|flight [A-Z] {2} [0-9]{4}| flight AS 1234|
|Numero di carta di credito|[0-9]{16}|5478789865437632|

Per altre informazioni, vedere l'articolo di riferimento sulle entità [regex.](reference-entity-regular-expression.md)

### <a name="prebuilt-entity"></a>Entità predefinita

LUIS offre un set di entità predefinite per il riconoscimento di tipi comuni di dati, ad esempio nome, data, numero e valuta.  Il comportamento delle entità predefinite è fisso. Il supporto delle entità predefinite varia in base alle impostazioni cultura dell'app LUIS. Ad esempio:

|Entità predefinita|Valore di esempio|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2 maggio, 8:00 il 2 maggio 2019|

Per altre [informazioni, vedere](./luis-reference-prebuilt-entities.md) l'articolo di riferimento sulle entità predefinite.

### <a name="patternany-entity"></a>Entità Pattern.Any

Modello. Qualsiasi entità è un segnaposto a lunghezza variabile usato solo nell'espressione modello di un criterio per contrassegnare il punto in cui inizia e termina l'entità. Segue una regola o un modello specifico e viene usato meglio per le frasi con struttura lessicale fissa. Ad esempio:

|Espressione di esempio|Modello|Entità|
|--|--|--|
|È possibile avere un hamburger per favore?|È possibile avere un {meal} [please][?]| Hamburger
|È possibile avere una pizza?|Posso avere un {cibo} [per favore][?]| pizza
|Dove è possibile trovare The Great Gatsby?|Dove è possibile trovare {bookName}?| The Great Gatsby|

Per altre informazioni, vedere l'articolo di riferimento sulle entità [Pattern.Any.](./reference-entity-pattern-any.md)

### <a name="machine-learned-ml-entity"></a>Entità Machine Learning (ML)

L'entità basata su Machine Learned usa il contesto per estrarre le entità in base a esempi etichettati. È l'entità preferita per la creazione di applicazioni LUIS. Si basa su algoritmi di Machine Learning e richiede che l'etichettatura sia adattata correttamente all'applicazione. Usare un'entità di Machine Learning per identificare i dati non sempre formattati correttamente, ma che hanno lo stesso significato. 

|Espressione di esempio|Entità prodotto *estratta*|
|--|--|
|Voglio acquistare un libro.|"book"|
|È possibile ottenere queste scarpe per favore?|"scarpe"|
|Aggiungere questi shorts al carrello.|"shorts"|

Per altre informazioni sulle entità di Machine Learned, [vedere](./reference-entity-machine-learned-entity.md).

Per altre [informazioni, vedere l'articolo di riferimento](./reference-entity-pattern-any.md) sulle entità di Machine Learned.

#### <a name="ml-entity-with-structure"></a>Entità ML con struttura

Un'entità ml può essere costituita da sotto-entità più piccole, ognuna delle quali può avere proprietà proprie. Ad esempio, *Address* potrebbe avere la struttura seguente:

* Indirizzo: 4567 Main Street, NY, 98052, USA
    * Numero di edificio: 4567
    * Via: Via principale
    * Stato: NY
    * CAP: 98052
    * Paese: Stati Uniti


### <a name="building-effective-ml-entities"></a>Creazione di entità di Machine Learning efficaci

Per creare entità apprese dal computer in modo efficace, seguire queste procedure consigliate:

* Se si ha un'entità machine learned con sotto-entità, assicurarsi che i diversi ordini e varianti dell'entità e delle sotto-entità siano presentati nelle espressioni etichettate. Le espressioni di esempio etichettate devono includere tutte le forme valide e includere entità che appaiono e sono assenti e riordinate all'interno dell'espressione.

* Evitare di eseguire l'overfitting delle entità in un set molto fisso. L'overfitting si verifica quando il modello non generalizza bene ed è un problema comune nei modelli di Machine Learning. Ciò implica che l'app non funzionerà in modo adeguato su nuovi tipi di esempi. A sua volta, è necessario variare le espressioni di esempio etichettate in modo che l'app possa generalizzare oltre gli esempi limitati forniti.

* L'etichettatura deve essere coerente tra le finalità. Sono incluse anche le espressioni fornite nella finalità *Nessuna* che include questa entità. In caso contrario, il modello non sarà in grado di determinare le sequenze in modo efficace.

## <a name="entities-as-features"></a>Entità come funzionalità

Un'altra funzione importante delle entità è usarle come caratteristiche o distinguere tratti per altre finalità o entità in modo che il sistema le osservi e apprende attraverso di esse.

### <a name="entities-as-features-for-intents"></a>Entità come funzionalità per le finalità

È possibile usare le entità come segnale per una finalità. Ad esempio, la presenza di una determinata entità nell'espressione può distinguere la finalità in cui rientra.

|Espressione di esempio|Entità|Finalità|
|--|--|--|
|Prenotami un *servizio a New York.*|City|Prenotazione di un volo|
|Prenotami la *sala riunioni principale.*|Room|Reserve Room|

### <a name="entities-as-feature-for-entities"></a>Entità come funzionalità per le entità

È anche possibile usare le entità come indicatore della presenza di altre entità. Un esempio comune è l'uso di un'entità precompilata come funzionalità per un'altra entità di Machine Learning.
Se si sta creando un sistema di prenotazione dei voli e l'espressione è simile a "Prenotami un volo da City a Seattle", le entità Origin *City* e *Destination City* saranno entità ml. È consigliabile usare l'entità `GeographyV2` precompilata come funzionalità per entrambe le entità.

Per altre informazioni, vedere l'articolo di riferimento sulle entità [GeographyV2.](./luis-reference-prebuilt-geographyv2.md)

È anche possibile usare le entità come funzionalità necessarie per altre entità. Ciò consente di risoluzione delle entità estratte. Ad esempio, se si crea un'applicazione per l'ordinazione di pizze e si ha un'entità ml, è possibile creare un'entità elenco e usarla come funzionalità necessaria per `Size` `SizeList` `Size` l'entità. L'applicazione restituirà il valore normalizzato come entità estratta dall'espressione. 

Vedere [le](luis-concept-feature.md) funzionalità per altre informazioni ed [entità predefinite per](./luis-reference-prebuilt-entities.md) altre informazioni sulla risoluzione delle entità predefinite disponibili nelle impostazioni cultura. 


## <a name="entity-prediction-status-and-errors"></a>Stato ed errori della stima dell'entità

Il portale LUIS mostra quanto segue quando l'entità ha una stima dell'entità diversa rispetto all'entità etichettata per un'espressione di esempio. Questo punteggio diverso è basato sul modello con training corrente. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Il portale LUIS mostra quando l'entità ha una stima dell'entità diversa da quella selezionata per un'espressione di esempio":::

Il testo che causa l'errore viene evidenziato all'interno dell'espressione di esempio e la riga dell'espressione di esempio ha un indicatore di errore a destra, visualizzato come triangolo rosso. 

Per risolvere gli errori di entità, provare a eseguire una o più delle operazioni seguenti:

* Il testo evidenziato è etichettato in modo erto. Per risolvere il problema, esaminare l'etichetta, correggerla e rieseificare il training dell'app. 
* Creare una [funzionalità](luis-concept-feature.md) per l'entità per identificare il concetto dell'entità.
* Aggiungere altre [espressioni di esempio ed etichettare](luis-concept-utterance.md) con l'entità .
* [Esaminare i suggerimenti di apprendimento](luis-concept-review-endpoint-utterances.md) attivi per tutte le espressioni ricevute nell'endpoint di stima che consentono di identificare il concetto dell'entità.


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle espressioni di [esempio.](luis-concept-utterance.md)
* Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.
* Altre informazioni sui limiti delle [applicazioni](./luis-limits.md)LUIS. 
* Usare [un'esercitazione](tutorial-machine-learned-entity.md) per informazioni su come estrarre dati strutturati da un'espressione usando l'entità di Machine Learning.
