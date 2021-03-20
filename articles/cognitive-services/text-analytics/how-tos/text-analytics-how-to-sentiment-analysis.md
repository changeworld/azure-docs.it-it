---
title: Eseguire attività di analisi del sentiment e opinion mining con l'API REST Analisi del testo
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come rilevare il sentiment ed eseguire attività di opinion mining nel testo con l'API Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2021
ms.author: aahi
ms.openlocfilehash: e9d8e7b514dca7d4930ad33bf08d4ceb07fb860d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599129"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Procedura: Analisi del sentiment e opinion mining

La funzionalità Analisi del sentiment dell'API Analisi del testo consente di rilevare il sentiment positivo e negativo in due modi. Se si invia una richiesta di Analisi del sentiment, l'API restituisce le etichette del sentiment (ad esempio "negativo", "neutrale" e "positivo") e i punteggi di attendibilità a livello di frase e di documento. È anche possibile inviare richieste di Opinion Mining usando l'endpoint Analisi del sentiment, che fornisce informazioni granulari sulle opinioni correlate alle parole, ad esempio gli attributi di prodotti o servizi, nel testo. 

I modelli di intelligenza artificiale usati dall'API vengono forniti dal servizio, quindi è sufficiente inviare contenuto per l'analisi.

## <a name="sentiment-analysis-versions-and-features"></a>Versioni e funzionalità di Analisi del sentiment

| Funzionalità                                   | Analisi del sentiment v3 | Analisi del sentiment v3.1 (anteprima) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metodi per richieste singole e batch    | X                     | X                                 |
| Punteggi ed etichettatura di Analisi del sentiment             | X                     | X                                 |
| [Contenitore Docker](text-analytics-how-to-install-containers.md) basato su Linux | X  |  |
| Opinion mining                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Analisi del sentiment

Analisi del sentiment nella versione 3.x applica le etichette del sentiment al testo, che vengono restituite a livello di frase e documento, con un punteggio di attendibilità per ognuna. 

Le etichette sono *positive*, *negative* e *neutral*. A livello di documento, può anche essere restituita l'etichetta del sentiment *mixed*. Il sentiment del documento è determinato di seguito:

| Valutazione della frase                                                                            | Etichetta di documento restituita |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Nel documento è presente almeno una frase `positive`. Le restanti frasi sono `neutral`. | `positive`              |
| Nel documento è presente almeno una frase `negative`. Le restanti frasi sono `neutral`. | `negative`              |
| Nel documento è presente almeno una frase `negative` e almeno una frase `positive`.    | `mixed`                 |
| Tutte le frasi del documento sono `neutral`.                                                  | `neutral`               |

I punteggi di attendibilità sono compresi tra 1 e 0. I punteggi più prossimi a 1 indicano una maggiore attendibilità nella classificazione dell'etichetta, mentre i punteggi inferiori indicano un'attendibilità inferiore. Per ogni documento o ogni frase, i punteggi stimati associati alle etichette (positive, negative e neutral) raggiungono il punteggio 1. Per altre informazioni, vedere la [nota sulla trasparenza di Analisi del testo](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Opinion mining

Opinion mining è una funzionalità di Analisi del sentiment, a partire dalla versione 3.1. Nota anche come Analisi del sentiment basata su aspetto nell'elaborazione del linguaggio naturale (PNL), questa funzionalità fornisce informazioni più granulari sulle opinioni correlate agli attributi di prodotti o servizi nel testo. L'API espone le opinioni come destinazione (sostantivo o verbo) e una valutazione (aggettivo).

Se, ad esempio, un cliente lascia commenti e suggerimenti su un hotel, ad esempio "la stanza è stata fantastica, ma il personale era scortese.", Opinion Mining individuerà le destinazioni (aspetti) nel testo e le valutazioni associate (opinioni) e i sentimenti. Analisi del sentiment potrebbe segnalare solo un sentiment negativo.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Diagramma dell'esempio di opinion mining" lightbox="../media/how-tos/opinion-mining.png":::

Per ottenere i risultati di opinion mining, è necessario includere il flag `opinionMining=true` in una richiesta di analisi del sentiment. I risultati di opinion mining verranno inclusi nella risposta dell'analisi del sentiment. La funzionalità di opinion mining è un'estensione di Analisi del sentiment ed è inclusa nel [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) corrente.


## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST 

### <a name="preparation"></a>Preparazione

L'analisi del sentiment genera risultati di qualità superiore quando viene eseguita su quantità ridotte di testo, a differenza dell'estrazione di espressioni chiave, che offre prestazioni migliori in blocchi di testo di maggiori dimensioni. Per ottenere risultati ottimali da entrambe le operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo e lingua. Analisi del sentiment supporta una vasta gamma di linguaggi, mentre altri disponibili in anteprima. Per altre informazioni, vedere [Linguaggi supportati](../language-support.md).

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Per informazioni sul numero massimo di documenti consentiti in una raccolta, vedere l'articolo sui [limiti per i dati](../concepts/data-limits.md?tabs=version-3) in Concetti. La raccolta viene inviata nel corpo della richiesta.

## <a name="structure-the-request"></a>Strutturare la richiesta

Creare una richiesta POST. È possibile [usare Postman](text-analytics-how-to-call-api.md) o la **console di test dell'API** nei collegamenti di riferimento seguenti per strutturarne e inviarne rapidamente una. 

#### <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

[Informazioni di riferimento su Analisi del sentiment v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

[Informazioni di riferimento su Analisi del sentiment v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Endpoint di richiesta

Impostare l'endpoint HTTP per l'analisi del sentiment usando una risorsa di Analisi del testo in Azure oppure un'istanza di [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md). È necessario includere l'URL corretto per la versione che si vuole usare. Ad esempio:

> [!NOTE]
> È possibile trovare la chiave e l'endpoint per la risorsa Analisi del testo nel portale di Azure. Si trovano in **Gestione risorse** nella pagina **Avvio rapido** della risorsa. 

#### <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

**Analisi del sentiment**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment`

**Opinion mining**

Per ottenere i risultati di opinion mining, è necessario includere il parametro `opinionMining=true`. Ad esempio:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment?opinionMining=true`

Questo parametro è impostato su `false` per impostazione predefinita. 

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

**Analisi del sentiment**

Nella versione 3.0, l'unico endpoint disponibile è per Analisi del sentiment.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Impostare un'intestazione della richiesta per includere la chiave dell'API Analisi del testo. Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Esempio di richiesta di Analisi del sentiment e opinion mining  

Di seguito è riportato un esempio di contenuto che può essere inviato per l'analisi del sentiment. Il formato della richiesta è identico per `v3.0` e `v3.1-preview`.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

L'API Analisi del testo è senza stato. Non vengono archiviati dati nell'account e i risultati vengono restituiti immediatamente nella risposta.


### <a name="view-the-results"></a>View the results

L'output viene restituito immediatamente. È possibile trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale. Importare quindi l'output in un'applicazione che consente di ordinare, cercare e modificare i dati. A causa del supporto multilingue e emoji, la risposta può contenere offset di testo. Per altre informazioni, vedere [come elaborare gli offset](../concepts/text-offsets.md).

#### <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Esempio di risposta di Analisi del sentiment e opinion mining

> [!IMPORTANT]
> Il codice JSON seguente è un esempio dell'uso delle funzionalità di opinion mining con Analisi del sentiment della versione 3.1 dell'API. Se non si richiede opinion mining, la risposta dell'API sarà la stessa della **versione 3.0**.  

Analisi del sentiment v3.1 può restituire oggetti della risposta sia per Analisi del sentiment che per opinion mining.
  
L'analisi del sentiment restituisce un'etichetta del sentiment e un punteggio di attendibilità per l'intero documento e per ogni frase al suo interno. I punteggi più prossimi a 1 indicano una maggiore attendibilità nella classificazione dell'etichetta, mentre i punteggi inferiori indicano un'attendibilità inferiore. Un documento può includere più frasi e la somma dei punteggi di attendibilità all'interno di ogni documento o frase è pari a 1. assessments 

Opinion Mining individuerà le destinazioni (sostantivi o verbi) nel testo e la valutazione associata (aggettivo). Nella risposta riportata di seguito, la frase *del ristorante era un ottimo cibo e il cameriere era descrittivo* con due obiettivi: *cibo* e *cameriere*. Ogni proprietà della destinazione `relations` contiene un `ref` valore con il riferimento URI agli `documents` oggetti, e associati `sentences` `assessments` .

L'API restituisce le opinioni come destinazione (sostantivo o verbo) e una valutazione (aggettivo).

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 49,
              "length": 8,
              "text": "friendly",
              "isNegated": false
            }
          ]
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>Esempio di risposta di Analisi del sentiment

L'analisi del sentiment restituisce un'etichetta del sentiment e un punteggio di attendibilità per l'intero documento e per ogni frase al suo interno. I punteggi più prossimi a 1 indicano una maggiore attendibilità nella classificazione dell'etichetta, mentre i punteggi inferiori indicano un'attendibilità inferiore. Un documento può includere più frasi e la somma dei punteggi di attendibilità all'interno di ogni documento o frase è pari a 1.

Le risposte di Analisi del sentiment v3 contengono le etichette e i punteggi del sentiment per ogni frase e ogni documento analizzato.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Summary

In questo articolo sono stati appresi i concetti e il flusso di lavoro per l'analisi del sentiment eseguita con l'API Analisi del testo. In sintesi:

+ Analisi del sentiment e opinion mining sono disponibili solo per specifiche lingue.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST viene inviata a un endpoint `/sentiment` usando [una chiave di accesso e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizzati validi per la sottoscrizione.
+ Usare `opinionMining=true` nelle richieste di Analisi del sentiment per ottenere i risultati di opinion mining.
+ L'output di risposta, costituito da un punteggio del sentiment per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, Ad esempio, Excel e Power BI.

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/client-libraries-rest-api.md)
* [Novità](../whats-new.md)
