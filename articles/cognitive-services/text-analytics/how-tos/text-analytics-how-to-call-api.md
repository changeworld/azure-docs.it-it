---
title: Chiamare l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come chiamare l'API REST Servizi cognitivi di Azure Analisi del testo e Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728483"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Come chiamare l'API REST Analisi del testo

In questo articolo vengono usate l'API REST Analisi del testo e [Postman](https://www.postman.com/downloads/) per illustrare i concetti chiave. L'API fornisce diversi endpoint sincroni e asincroni per l'uso delle funzionalità del servizio. 

## <a name="create-a-text-analytics-resource"></a>Creare una risorsa Analisi del testo

> [!NOTE]
> * È necessaria una risorsa Analisi del testo usando un piano [](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) tariffario Standard (S) se si vogliono usare `/analyze` gli endpoint o `/health` . `/analyze`L'endpoint è incluso nel [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Prima di usare l'API Analisi del testo, è necessario creare una risorsa di Azure con una chiave e un endpoint per le applicazioni. 

1.  Per prima cosa, passare [alla portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e creare una nuova risorsa Analisi del testo, se non è già presente. Scegliere un [piano tariffario.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

2.  Selezionare l'area da usare per l'endpoint.  Si noti che gli endpoint e sono disponibili solo nelle aree seguenti: Stati Uniti occidentali 2, Stati Uniti orientali `/analyze` `/health` 2, Stati Uniti centrali, Europa settentrionale ed Europa occidentale.

3.  Creare la Analisi del testo risorse e passare al pannello "chiavi ed endpoint" a sinistra della pagina. Copiare la chiave da usare in un secondo momento quando si chiamano le API. Verrà aggiunto in un secondo momento come valore per `Ocp-Apim-Subscription-Key` l'intestazione .

4. Per controllare il numero di record di testo inviati usando la risorsa Analisi del testo:

    1. Passare alla risorsa Analisi del testo nella portale di Azure. 
    2. Fare **clic su Metriche** in **Monitoraggio** nel menu di spostamento a sinistra. 
    3. Selezionare *Record di testo elaborati* nella casella a discesa per **Metrica.**
    
Un record di testo è di 1000 caratteri.

## <a name="change-your-pricing-tier"></a>Modificare il piano tariffario 

Se si dispone di una risorsa Analisi del testo esistente che usa il piano tariffario da S0 a S4, è necessario aggiornarla per usare il piano [tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)Standard (S). I piani tariffari da S0 a S4 verranno ritirati. Per aggiornare i prezzi della risorsa:

1. Passare alla risorsa Analisi del testo nel portale di Azure [.](https://portal.azure.com/)
2. Selezionare **Piano tariffario** nel menu di spostamento a sinistra. Sarà sotto **RESOURCE MANAGEMENT**. 
3. Scegliere il piano tariffario Standard (S). Quindi fare clic su **Seleziona**.

È anche possibile creare una nuova risorsa Analisi del testo con il piano tariffario Standard (S) ed eseguire la migrazione delle applicazioni per usare le credenziali per la nuova risorsa. 

## <a name="using-the-api-synchronously"></a>Uso sincrono dell'API

È possibile chiamare Analisi del testo in modo sincrono (per scenari a bassa latenza). È necessario chiamare ogni API (funzionalità) separatamente quando si usa l'API sincrona. Se è necessario chiamare più funzionalità, vedere la sezione seguente su come chiamare Analisi del testo in modo asincrono. 

## <a name="using-the-api-asynchronously"></a>Uso asincrono dell'API

A partire dalla versione 3.1-preview.3, l'API Analisi del testo fornisce due endpoint asincroni: 

* L'endpoint per Analisi del testo consente di analizzare lo stesso set di documenti di testo con più funzionalità di analisi del `/analyze` testo in una sola chiamata API. In precedenza, per usare più funzionalità era necessario effettuare chiamate API separate per ogni operazione. Considerare questa funzionalità quando è necessario analizzare set di documenti di grandi dimensioni con più Analisi del testo funzionalità.

* L'endpoint per Analisi del testo per l'integrità, che può estrarre ed etichettare le `/health` informazioni mediche rilevanti dai documenti clinichi.  

Si noti che gli endpoint /analyze e /health sono disponibili solo nelle aree seguenti: Stati Uniti occidentali 2, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale ed Europa occidentale.

Vedere la tabella seguente per vedere quali funzionalità possono essere usate in modo asincrono. Si noti che dall'endpoint è possibile chiamare solo alcune `/analyze` funzionalità. 

| Funzionalità | Sincrono | Asincrono |
|--|--|--|
| Rilevamento della lingua | ✔ |  |
| Analisi del sentiment | ✔ |  |
| Opinion mining | ✔ |  |
| Estrazione di frasi chiave | ✔ | ✔* |
| Riconoscimento entità denominata (incluse informazioni personali e phI) | ✔ | ✔* |
| Collegamento di entità | ✔ | ✔* |
| Analisi del testo per l'integrità (contenitore) | ✔ |  |
| Analisi del testo per l'integrità (API) |  | ✔  |

`*` - Chiamato in modo asincrono tramite `/analyze` l'endpoint.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Formati di richiesta API

È possibile inviare chiamate sincrone e asincrone all Analisi del testo API.

#### <a name="synchronous"></a>[Sincrono](#tab/synchronous)

### <a name="synchronous-requests"></a>Richieste sincrone

Il formato per le richieste API è lo stesso per tutte le operazioni sincrone. I documenti vengono inviati in un oggetto JSON come testo non elaborato non strutturato. XML non è supportato. Lo schema JSON è costituito da elementi descritti di seguito.

| Elemento | Valori validi | Necessaria? | Utilizzo |
|---------|--------------|-----------|-------|
|`id` |Il tipo di dati è stringa, ma in pratica gli ID documento tendono a essere numeri interi. | Obbligatoria | Il sistema usa gli ID immessi per strutturare l'output. Per ogni ID della richiesta vengono generati codici di lingua, frasi chiave e punteggi di sentiment.|
|`text` | Testo non elaborato non strutturato, fino a 5.120 caratteri. | Obbligatoria | Per il rilevamento della lingua, il testo può essere espresso in qualsiasi lingua. Per l'analisi del sentiment, l'estrazione delle frasi chiave e l'identificazione delle entità, il testo deve essere in una [lingua supportata](../language-support.md). |
|`language` | Codice [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) di 2 caratteri per una [lingua supportata](../language-support.md) | Varia | Richiesto per l'analisi del sentiment, l'estrazione delle frasi chiave e il collegamento delle entità; facoltativo per il rilevamento della lingua. Se lo si omette non si verifica nessun errore ma l'analisi risulta più debole. Il codice della lingua deve corrispondere al `text` fornito. |

Di seguito è riportato un esempio di richiesta API per gli endpoint Analisi del testo sincroni. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Asincrono](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Richieste asincrone `/analyze` all'endpoint

> [!NOTE]
> La versione preliminare più recente della libreria client Analisi del testo consente di chiamare operazioni di analisi asincrona usando un oggetto client. È possibile trovare esempi in GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`L'endpoint consente di scegliere quali funzionalità Analisi del testo supportate da usare in una singola chiamata API. Questo endpoint supporta attualmente:

* Estrazione frasi chiave 
* Riconoscimento entità denominata (incluse informazioni personali e phI)
* Collegamento delle entità

| Elemento | Valori validi | Necessaria? | Utilizzo |
|---------|--------------|-----------|-------|
|`displayName` | Stringa | Facoltativo | Utilizzato come nome visualizzato per l'identificatore univoco del processo.|
|`analysisInput` | Include il `documents` campo seguente | Obbligatoria | Contiene le informazioni per i documenti da inviare. |
|`documents` | Include i `id` campi e `text` seguenti | Obbligatoria | Contiene informazioni per ogni documento inviato e il testo non elaborato del documento. |
|`id` | Stringa | Obbligatoria | Gli ID forniti vengono usati per strutturare l'output. |
|`text` | Testo non elaborato non strutturato, fino a 125.000 caratteri. | Obbligatoria | Deve essere in lingua inglese, che è l'unica lingua attualmente supportata. |
|`tasks` | Include le funzionalità Analisi del testo seguenti: `entityRecognitionTasks` `entityLinkingTasks` , o `keyPhraseExtractionTasks` `entityRecognitionPiiTasks` . | Obbligatoria | Una o più delle Analisi del testo da usare. Si noti `entityRecognitionPiiTasks` che dispone di un parametro facoltativo che può essere impostato su o e di per il rilevamento dei tipi di entità `domain` `pii` `phi` `pii-categories` selezionati. Se il `domain` parametro non è specificato, il valore predefinito del sistema è `pii` . |
|`parameters` | Include i `model-version` campi e `stringIndexType` seguenti | Obbligatoria | Questo campo è incluso nelle attività delle funzionalità precedenti selezionate. Contengono informazioni sulla versione del modello che si vuole usare e sul tipo di indice. |
|`model-version` | Stringa | Obbligatoria | Specificare la versione del modello chiamato da usare.  |
|`stringIndexType` | Stringa | Obbligatoria | Specificare il decodificatore di testo che corrisponde all'ambiente di programmazione.  I tipi supportati `textElement_v8` sono (impostazione predefinita), `unicodeCodePoint` , `utf16CodeUnit` . Per altre [informazioni, vedere](../concepts/text-offsets.md#offsets-in-api-version-31-preview) l'articolo Offset di testo.  |
|`domain` | Stringa | Facoltativo | Si applica solo come parametro `entityRecognitionPiiTasks` all'attività e può essere impostato su o `pii` `phi` . Il valore predefinito è `pii` se non specificato.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Richieste asincrone `/health` all'endpoint

Il formato per le richieste API al Analisi del testo per l'API ospitata per l'integrità è uguale a quello per il relativo contenitore. I documenti vengono inviati in un oggetto JSON come testo non elaborato non strutturato. XML non è supportato. Lo schema JSON è costituito da elementi descritti di seguito.  Compilare e inviare il modulo di richiesta [di Servizi](https://aka.ms/csgate) cognitivi per richiedere l'accesso al Analisi del testo per l'anteprima pubblica sull'integrità. Non verranno addebitati i Analisi del testo per l'utilizzo dell'integrità. 

| Elemento | Valori validi | Necessaria? | Utilizzo |
|---------|--------------|-----------|-------|
|`id` |Il tipo di dati è stringa, ma in pratica gli ID documento tendono a essere numeri interi. | Obbligatoria | Il sistema usa gli ID immessi per strutturare l'output. |
|`text` | Testo non elaborato non strutturato, fino a 5.120 caratteri. | Obbligatoria | Si noti che è attualmente supportato solo il testo in lingua inglese. |
|`language` | Codice [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) di 2 caratteri per una [lingua supportata](../language-support.md) | Obbligatoria | Attualmente `en` è supportato solo . |

Di seguito è riportato un esempio di richiesta API per il Analisi del testo per gli endpoint di integrità. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Per informazioni [su velocità e limiti di](../concepts/data-limits.md) dimensioni per l'invio di dati all'API Analisi del testo dati, vedere l'articolo Limiti di velocità e dati.


## <a name="set-up-a-request"></a>Configurare una richiesta 

In Postman (o un altro strumento di test dell'API Web) aggiungere l'endpoint per la funzionalità che si vuole usare. Usare la tabella seguente per trovare il formato dell'endpoint appropriato e `<your-text-analytics-resource>` sostituirlo con l'endpoint della risorsa. Ad esempio:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Sincrono](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Endpoint per l'invio di richieste sincrone

| Funzionalità | Tipo di richiesta | Endpoint delle risorse |
|--|--|--|
| Rilevamento della lingua | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Analisi del sentiment | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Opinion mining | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Estrazione di frasi chiave | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Riconoscimento di entità denominate - Generale | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Riconoscimento di entità denominate - Informazioni personali | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Riconoscimento di entità denominate - PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asincrono](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Endpoint per l'invio di richieste asincrone `/analyze` all'endpoint

| Funzionalità | Tipo di richiesta | Endpoint delle risorse |
|--|--|--|
| Inviare il processo di analisi | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Ottenere lo stato e i risultati dell'analisi | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Endpoint per l'invio di richieste asincrone `/health` all'endpoint

| Funzionalità | Tipo di richiesta | Endpoint delle risorse |
|--|--|--|
| Inviare Analisi del testo per il processo di integrità  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Ottenere lo stato e i risultati del processo | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Annulla processo | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Dopo aver creato l'endpoint, in Postman (o in un altro strumento di test dell'API Web):

1. Scegliere il tipo di richiesta per la funzionalità che si vuole usare.
2. Incollare l'endpoint dell'operazione appropriata desiderata dalla tabella precedente.
3. Impostare le tre intestazioni della richiesta:

   + `Ocp-Apim-Subscription-Key`: chiave di accesso, ottenuta da portale di Azure
   + `Content-Type`: application/json
   + `Accept`: application/json

    Se si usa Postman, la richiesta dovrebbe essere simile allo screenshot seguente, presupponendo un `/keyPhrases` endpoint.
    
    ![Screenshot di richiesta con l'endpoint e intestazioni](../media/postman-request-keyphrase-1.png)
    
4. Scegliere **raw** per il formato del **corpo**
    
    ![Screenshot di richiesta con impostazioni relative al corpo](../media/postman-request-body-raw.png)

5. Incollare alcuni documenti JSON in un formato valido. Usare gli esempi nella sezione relativa al **formato della** richiesta API precedente e per altre informazioni ed esempi, vedere gli argomenti seguenti:

      + [Rilevamento della lingua](text-analytics-how-to-language-detection.md)
      + [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md)
      + [Analisi del sentiment](text-analytics-how-to-sentiment-analysis.md)
      + [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Inviare la richiesta

Inviare la richiesta API. Se è stata effettuata la chiamata a un endpoint sincrono, la risposta verrà visualizzata immediatamente, come singolo documento JSON, con un elemento per ogni ID documento specificato nella richiesta.

Se è stata effettuata la chiamata agli endpoint asincroni o , verificare `/analyze` di aver ricevuto un codice di risposta `/health` 202. sarà necessario ottenere la risposta per visualizzare i risultati:

1. Nella risposta dell'API trovare `Operation-Location` dall'intestazione , che identifica il processo inviato all'API. 
2. Creare una richiesta GET per l'endpoint usato. Fare riferimento alla tabella [precedente per](#set-up-a-request) il formato dell'endpoint ed esaminare la documentazione di [riferimento dell'API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Ad esempio:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Aggiungere `Operation-Location` l'oggetto alla richiesta.

4. La risposta sarà un singolo documento JSON, con un elemento per ogni ID documento specificato nella richiesta.

Si noti che per le operazioni asincrone o , i risultati della richiesta GET nel passaggio 2 precedente sono disponibili per 24 ore dal momento in cui è stato `/analyze` `/health` creato il processo.  Questa ora è indicata dal `expirationDateTime` valore nella risposta GET.  Dopo questo periodo di tempo, i risultati vengono eliminati e non sono più disponibili per il recupero.    

## <a name="example-api-responses"></a>Risposte api di esempio
 
# <a name="synchronous"></a>[Sincrono](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Risposte di esempio per un'operazione sincrona

Le risposte dell'endpoint sincrono variano a seconda dell'endpoint in uso. Per risposte di esempio, vedere gli articoli seguenti.

+ [Rilevamento della lingua](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analisi del sentiment](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asincrono](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Risposte di esempio per le operazioni asincrone

Se ha esito positivo, la richiesta GET `/analyze` all'endpoint restituirà un oggetto contenente le attività assegnate. Ad esempio, `keyPhraseExtractionTasks`. Queste attività contengono l'oggetto risposta dalla funzionalità Analisi del testo appropriata. Vedi gli articoli seguenti per altre informazioni.

+ [Estrazione delle frasi chiave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Riconoscimento delle entità](text-analytics-how-to-entity-linking.md#view-results)
+ [Analisi del testo per la sanità](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Domande frequenti](../text-analytics-resource-faq.md)</br>
* [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712)
* [Uso della libreria client di Analisi del testo](../quickstarts/client-libraries-rest-api.md)
* [Novità](../whats-new.md)
