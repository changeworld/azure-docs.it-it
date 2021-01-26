---
title: Come eseguire un test batch-LUIS
titleSuffix: Azure Cognitive Services
description: Eseguire il test in batch dell'app Language Understanding (LUIS) per trovare le espressioni con finalità ed entità errate.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787013"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Test in batch con un set di espressioni di esempio

Il test batch convalida la versione con Training attivo per misurare l'accuratezza della stima. Un test batch consente di visualizzare l'accuratezza di ogni finalità ed entità nella versione attiva. Esaminare i risultati dei test di batch per eseguire le azioni appropriate per migliorare l'accuratezza, ad esempio l'aggiunta di altre espressioni di esempio a uno scopo se l'app non riesce a identificare la finalità corretta o a etichettare entità all'interno dell'espressione.

## <a name="group-data-for-batch-test"></a>Raggruppare i dati per il test in batch

È importante che le espressioni usate per i test in batch siano nuove per Language Understanding. Se si dispone di un set di dati di espressioni, dividere le espressioni in tre set: espressioni di esempio aggiunte a un Intent, espressioni ricevute dall'endpoint pubblicato e espressioni usate per eseguire il test di batch LUIS dopo che è stato eseguito il training.

Il file JSON batch usato deve includere espressioni con entità di Machine Learning di livello superiore etichettate, tra cui la posizione iniziale e finale. Le espressioni non devono far parte degli esempi già presenti nell'app. Devono essere espressioni da prevedere positivamente per finalità ed entità.

È possibile separare i test per finalità e/o entità oppure avere tutti i test (fino a 1000 espressioni) nello stesso file. 

### <a name="common-errors-importing-a-batch"></a>Errori comuni nell'importazione di un batch

Se si verificano errori durante il caricamento del file batch in LUIS, verificare i seguenti problemi comuni:

* Più di 1.000 espressioni in un file batch
* Un oggetto JSON di espressione che non ha una proprietà di entità. La proprietà può essere una matrice vuota.
* Parole etichettata in più entità
* Etichette di entità che iniziano o terminano in uno spazio.

## <a name="fixing-batch-errors"></a>Correzione di errori batch

In presenza di errori nei test in batch, è possibile aggiungere più espressioni a una finalità e/o etichettare più espressioni con l'entità per consentire a Language Understanding di distinguere tra le finalità. Se sono state aggiunte espressioni che sono state etichettate e si continuano a riscontrare errori di previsione nei test in batch, è possibile aggiungere un [elenco di frasi](luis-concept-feature.md) con un vocabolario settoriale specifico per consentire a Language Understanding di apprendere più rapidamente.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Test batch tramite il portale LUIS 

### <a name="import-and-train-an-example-app"></a>Importare ed eseguire il training di un'app di esempio

Importare un'app che accetta un ordine di pizza, ad esempio `1 pepperoni pizza on thin crust`.

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Selezionare la freccia accanto a **nuova app** e fare clic su **importa come JSON** per importare il file JSON in una nuova app. Assegnare un nome all'app `Pizza app` .


1. Selezionare **Esegui il training** sulla barra di spostamento per eseguire il training dell'app.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>File di test batch

Il codice JSON di esempio include un'espressione con un'entità etichettata per illustrare l'aspetto di un file di test. Nei propri test è necessario avere più espressioni con finalità corrette ed entità etichettate di Machine Learning.

1. Creare `pizza-with-machine-learned-entity-test.json` in un editor di testo o [scaricarlo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true).

2. Nel file batch in formato JSON aggiungere un'espressione con la **finalità** che si vuole prevedere nel test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Eseguire il batch

1. Selezionare **Test** nella barra di spostamento in alto.

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra.

    ![Collegamento Batch Testing (Test in batch)](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Selezionare **Importa**. Nella finestra di dialogo visualizzata selezionare **Choose file (Scegli file** ) e individuare un file JSON con il formato JSON corretto che *non contiene più di 1.000* espressioni da testare.

    Gli errori di importazione vengono segnalati in una barra di notifica rossa nella parte superiore del browser. Quando un'importazione presenta degli errori, non viene creato alcun set di dati. Per ulteriori informazioni, vedere[Errori comuni](#common-errors-importing-a-batch).

4. Scegliere il percorso del file `pizza-with-machine-learned-entity-test.json`.

5. Assegnare un nome al set di dati `pizza test` e selezionare **Done** (Fatto).

6. Selezionare il pulsante **Run** (Esegui). Dopo l'esecuzione del test batch, selezionare **Visualizza risultati**. 

    > [!TIP]
    > * Selezionando **Scarica** si scaricherà lo stesso file caricato.
    > * Se il test batch non è riuscito, almeno una finalità dell'espressione non corrisponde alla stima.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Esaminare i risultati del batch per le finalità

Per esaminare i risultati del test in batch, selezionare **See results** (Visualizza risultati). I risultati del test mostrano graficamente il modo in cui le espressioni di test sono state previste rispetto alla versione attiva.

Il grafico relativo al batch visualizza quattro quadranti d risultati. Nella parte destra del grafico è presente un filtro. Il filtro contiene finalità ed entità. Quando si seleziona una [sezione del grafico](#review-batch-results-for-intents) oppure un punto nel grafico, le espressioni associate vengono visualizzate sotto il grafico.

Quando si passa il mouse sul grafico, la rotellina del mouse consente di ingrandire o ridurre la visualizzazione del grafico. Ciò è utile quando sono presenti molti punti nel grafico strettamente raggruppati.

Il grafico è diviso in quattro quadranti, con due sezioni visualizzate in rosso.

1. Selezionare la finalità **ModifyOrder** nell'elenco dei filtri. L'espressione è stimata come **Vero positivo**, il che significa che corrisponde correttamente alla previsione positiva elencata nel file batch.

    > [!div class="mx-imgBorder"]
    > ![Espressione correttamente corrispondente alla previsione positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    I segni di spunta verdi nell'elenco dei filtri indicano anche l'esito positivo del test per ogni finalità. Tutte le altre finalità sono elencate con un punteggio positivo 1/1 perché l'espressione è stata testata rispetto a ogni finalità, come test negativo per le finalità non elencate nel test batch.

1. Selezionare la finalità **Confirmation**. Questa finalità non è elencata nel test batch, pertanto si tratta di un test negativo dell'espressione presente nel test.

    > [!div class="mx-imgBorder"]
    > ![Espressione prevista correttamente come negativa per la finalità non elencata nel file batch](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Il test negativo ha avuto esito positivo, come indicato dal testo verde nel filtro e nella griglia.

### <a name="review-batch-test-results-for-entities"></a>Esaminare i risultati del test batch per le entità

L'entità ModifyOrder, come entità macchina con sottoentità, Visualizza se l'entità di primo livello corrisponde e come vengono stimate le sottoentità.

1. Selezionare l'entità **ModifyOrder** nell'elenco dei filtri e quindi selezionare il cerchio nella griglia.

1. Sotto il grafico viene visualizzata la previsione dell'entità. La visualizzazione include linee continue per le previsioni che corrispondono alle aspettative e linee punteggiate per quelle che non corrispondono.

    > [!div class="mx-imgBorder"]
    > ![Elemento padre dell'entità previsto correttamente nel file batch](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrare i risultati del grafico

Per filtrare il grafico in base a una finalità o entità specifica, selezionare la finalità o l'entità nel pannello filtro sulla destra. I punti dati e la relativa distribuzione vengono aggiornati nel grafico in base alla selezione.

![Risultato del test in batch visualizzato](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Esempi di risultati del grafico

Il grafico nel portale LUIS, è possibile eseguire le azioni seguenti:
 
#### <a name="view-single-point-utterance-data"></a>Visualizzare i dati dell'espressione single-point

Nel grafico passare il mouse su un punto dati per visualizzare il punteggio di certezza della relativa stima. Selezionare un punto dati per recuperare l'espressione corrispondente nell'elenco nella parte inferiore della pagina.

![Espressione selezionata](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Visualizzare i dati sezione

Nel grafico a quattro sezioni selezionare il nome della sezione, ad esempio **False Positive** (Falso positivo) in alto a destra del grafico. Sotto il grafico, tutte le espressioni in quella sezione vengono visualizzate sotto il grafico in un elenco.

![Espressioni selezionate per sezione](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In questa immagine precedente l'espressione `switch on` viene etichettata con la finalità TurnAllOn, ma ha ricevuto la stima della finalità None (Nessuna). Indica che la finalità TurnAllOn necessita di più espressioni di esempio per elaborare la stima prevista.

Le due sezioni del grafico in rosso indicano le espressioni che non corrispondevano alla stima prevista. Indicano le espressioni per le quali LUIS necessita di ulteriore training.

Le due sezioni del grafico in verde corrispondevano alla stima prevista.

## <a name="batch-testing-using-the-rest-api"></a>Test batch con l'API REST 

LUIS consente di eseguire il test in batch usando il portale LUIS e l'API REST. Gli endpoint per l'API REST sono elencati di seguito. Per informazioni sui test batch tramite il portale LUIS, vedere [esercitazione: set di dati di test batch](). Usare gli URL completi indicati di seguito, sostituendo i valori segnaposto con la chiave e l'endpoint di stima di LUIS. 

Ricordarsi di aggiungere la chiave LUIS a `Ocp-Apim-Subscription-Key` nell'intestazione e impostare `Content-Type` su `application/json` .

### <a name="start-a-batch-test"></a>Avviare un test batch

Avviare un test batch usando un ID versione dell'app o uno slot di pubblicazione. Inviare una richiesta **post** a uno dei seguenti formati di endpoint. Includere il file batch nel corpo della richiesta.

Slot di pubblicazione
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

ID versione app
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Questi endpoint restituiranno un ID operazione che si utilizzerà per controllare lo stato e ottenere risultati. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Ottenere lo stato di un test batch in corso

Usare l'ID operazione del test batch avviato per ottenere lo stato dai formati di endpoint seguenti: 

Slot di pubblicazione
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

ID versione app
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Ottenere i risultati da un test batch

Usare l'ID operazione del test batch avviato per ottenere i risultati dai formati di endpoint seguenti: 

Slot di pubblicazione
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

ID versione app
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>File batch di espressioni

Inviare un file batch di espressioni, noto come set di *dati*, per i test batch. Il set di dati è un file in formato JSON contenente un massimo di 1.000 espressioni con etichetta. È possibile testare fino a 10 set di dati in un'app. Se è necessario testare più, eliminare un set di dati e quindi aggiungerne uno nuovo. Tutte le entità personalizzate nel modello vengono visualizzate nel filtro del test in batch anche se nei dati del file batch non sono presenti entità corrispondente.

Il file batch è costituito da espressioni. Ogni espressione deve avere una previsione preventivo prevista insieme a qualsiasi [entità di Machine Learning](luis-concept-entity-types.md#types-of-entities) che si prevede venga rilevata.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Modello di sintassi di batch per finalità con entità

Usare il modello seguente per avviare il file batch:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Il file batch usare le proprietà **startPos** e **endPos** per notare l'inizio e la fine di un'entità. I valori sono basati su zero e non devono iniziare o terminare in uno spazio. Questo è diverso dai log di query, che usano proprietà startIndex ed endIndex.

Se non si intende testare le entità, includere la proprietà `entities` e impostare il valore come una matrice vuota `[]`.

### <a name="rest-api-batch-test-results"></a>Risultati del test batch dell'API REST

Sono disponibili diversi oggetti restituiti dall'API:

* Informazioni sui modelli Intent ed Entities, ad esempio precisione, richiamo e Punteggio F.
* Informazioni sui modelli di entità, ad esempio precisione, richiamo e Punteggio F, per ogni entità 
  * Utilizzando il `verbose` flag, è possibile ottenere ulteriori informazioni sull'entità, ad esempio `entityTextFScore` e `entityTypeFScore` .
* Sono state fornite espressioni con i nomi di Intent stimati e con etichetta
* Elenco di entità false positive e un elenco di entità false negative.

## <a name="next-steps"></a>Passaggi successivi

Se il test indica che l'app LUIS non riconosce le finalità e le entità corrette, è possibile migliorare le prestazioni dell'app LUIS etichettando più espressioni o aggiungendo funzionalità.

* [Etichettare le espressioni suggerite con LUIS](luis-how-to-review-endpoint-utterances.md)
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md)
