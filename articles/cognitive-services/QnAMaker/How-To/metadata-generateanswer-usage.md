---
title: Metadati con l'API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, alle coppie di domande e risposte. È possibile filtrare i risultati per le query utente e archiviare informazioni aggiuntive che possono essere usate nelle conversazioni di completamento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232206"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Ottenere una risposta con l'API GenerateAnswer

Per ottenere la risposta prevista alla domanda di un utente, usare l'API GenerateAnswer. Quando si pubblica una Knowledge base, è possibile visualizzare informazioni su come usare questa API nella pagina **pubblica** . È anche possibile configurare l'API per filtrare le risposte in base ai tag dei metadati e testare la Knowledge base dall'endpoint con il parametro della stringa di query di test.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Ottenere le stime delle risposte con l'API GenerateAnswer

Usare l' [API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nel bot o nell'applicazione per eseguire una query sulla Knowledge base con una domanda per l'utente, in modo da ottenere la migliore corrispondenza dalle coppie di domande e risposte.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Pubblica per ottenere l'endpoint GenerateAnswer

Dopo aver pubblicato la Knowledge base, dal portale di [QnA Maker](https://www.qnamaker.ai)o usando l' [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), è possibile ottenere i dettagli dell'endpoint GenerateAnswer.

Per ottenere i dettagli dell'endpoint:
1. Accedere a [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. In **Knowledge** base selezionare **Visualizza codice** per la Knowledge base.
    ![Screenshot delle Knowledge base](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Ottenere i dettagli dell'endpoint GenerateAnswer.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

    ![Schermata dei dettagli dell'endpoint](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

    ![Screenshot dei dettagli dell'endpoint gestiti](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

È anche possibile ottenere i dettagli dell'endpoint dalla scheda **Settings** (Impostazioni) della knowledge base.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configurazione della richiesta GenerateAnswer

È possibile chiamare GenerateAnswer con una richiesta HTTP POST. Per il codice di esempio che illustra come chiamare GenerateAnswer, vedere le [guide introduttive](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

La richiesta POST USA:

* [Parametri URI](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) obbligatori
* Proprietà di intestazione obbligatoria, `Authorization` , per la sicurezza
* [Proprietà del corpo](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)obbligatorie.

L'URL di GenerateAnswer ha il formato seguente:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Ricordarsi di impostare la proprietà dell'intestazione HTTP di `Authorization` con un valore della stringa `EndpointKey` con uno spazio finale, quindi la chiave dell'endpoint trovata nella pagina **Impostazioni** .

Un esempio di corpo JSON ha un aspetto simile al seguente:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Altre informazioni su [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Il codice JSON precedente ha richiesto solo le risposte al 30% o oltre il Punteggio di soglia.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Proprietà della risposta GenerateAnswer

La [risposta](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) è un oggetto JSON che include tutte le informazioni necessarie per visualizzare la risposta e il successivo turno nella conversazione, se disponibile.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Il codice JSON precedente ha risposto con una risposta con un punteggio pari al 38,5%.

## <a name="match-questions-only-by-text"></a>Corrisponde solo alle domande, testo

Per impostazione predefinita, QnA Maker cerca le domande e le risposte. Se si desidera cercare solo le domande, per generare una risposta, utilizzare `RankerType=QuestionOnly` nel corpo post della richiesta GenerateAnswer.

È possibile eseguire ricerche nella KB pubblicata, usando `isTest=false` o nella KB di test usando `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>Usare QnA Maker con un bot in C #

Bot Framework fornisce l'accesso alle proprietà del QnA Maker con l' [API getanswer](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Il codice JSON precedente ha richiesto solo le risposte al 30% o oltre il Punteggio di soglia.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Usare QnA Maker con un bot in Node.js

Bot Framework fornisce l'accesso alle proprietà del QnA Maker con l' [API getanswer](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Il codice JSON precedente ha richiesto solo le risposte al 30% o oltre il Punteggio di soglia.

## <a name="get-precise-answers-with-generateanswer-api"></a>Ottieni risposte precise con l'API GenerateAnswer

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

Sono disponibili funzionalità di risposta precise solo con la versione gestita di QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

L'utente può abilitare [risposte precise](../reference-precise-answering.md) quando si usa la risorsa gestita QnA Maker. Il parametro answerSpanRequest deve essere aggiornato per lo stesso oggetto.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

Analogamente, gli utenti possono scegliere di disabilitare le risposte esatte non impostando il parametro answerSpanRequest.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Impostazioni bot

Per configurare le impostazioni di risposta esatte per il servizio bot, passare alla risorsa del servizio app per l'utente bot. Quindi è necessario aggiornare le configurazioni aggiungendo la seguente impostazione.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Configurazione dello schermo|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Solo risposte esatte|true|true|
|Solo risposte lunghe|false|false|
|Risposte lunghe e precise|true|false|

---

## <a name="common-http-errors"></a>Errori HTTP comuni

|Codice|Spiegazione|
|:--|--|
|2xx|Operazione riuscita|
|400|I parametri della richiesta non sono corretti, vale a dire che i parametri obbligatori mancano, non solo validi o sono troppo grandi|
|400|Il corpo della richiesta non è corretto, vale a dire che la stringa JSON manca, non è valida o è troppo grande|
|401|Chiave non valida|
|403|Operazione non consentita: non si dispone delle autorizzazioni corrette|
|404|La knowledge base non esiste|
|410|L'API è deprecata e non è più disponibile|

## <a name="next-steps"></a>Passaggi successivi

La pagina di **pubblicazione** fornisce anche informazioni per [generare una risposta](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) con il post o il curl.

> [!div class="nextstepaction"]
> [Ottenere analisi sulla Knowledge Base](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Punteggio di confidenza](../Concepts/confidence-score.md)
