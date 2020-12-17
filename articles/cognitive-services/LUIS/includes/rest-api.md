---
title: File di inclusione dell'API REST
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/30/2020
ms.topic: include
ms.openlocfilehash: ac5d4ce3080bad36b0f68289bee32910bdc58837
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941521"
---
Seguendo questo argomento di avvio rapido, si eseguiranno tre chiamate REST in sequenza.

- Prima di tutto si caricherà un batch di espressioni di esempio, che verranno usate per eseguire il training del modello di app Pizza, usando la chiamata [REST Batch add labels](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09).
- Verrà quindi avviata una sessione di training per l'app Pizza usando la chiamata [REST Train application version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45).
- Infine, si otterrà lo stato della sessione di training per l'app Pizza usando la chiamata [REST Get version training status](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46).

[Documentazione di riferimento](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Prerequisiti

* Un account [LUIS](https://www.luis.ai) gratuito.
* Un editor di testo, ad esempio [Visual Studio Code](https://code.visualstudio.com/).
* Il programma da riga di comando cURL. Il programma cURL è già installato in macOS, nella maggior parte delle distribuzioni Linux e in Windows 10 build 1803 e versioni successive.

    Se è necessario installare cURL, è possibile scaricarlo dalla [pagina di download di cURL](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Creare l’app Pizza

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]


## <a name="create-a-json-file-to-train-the-pizza-app"></a>Creare un file JSON per eseguire il training dell'app Pizza

Per creare un file JSON con tre espressioni di esempio, salvare i dati JSON seguenti in un file denominato `ExampleUtterances.JSON`:

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Il file JSON di espressioni di esempio segue un formato specifico.

Il campo `text` contiene il testo dell'espressione di esempio. Il campo `intentName` deve corrispondere al nome di una finalità esistente nell'app di LUIS. Il campo `entityLabels` è obbligatorio. Se non si desidera assegnare etichette alle entità, fornire una matrice vuota.

Se la matrice entityLabels non è vuota, è necessario che `startCharIndex` e `endCharIndex` contrassegnino l'entità a cui si fa riferimento nel campo `entityName`. L’indice è in base zero. Se si inizia o termina l'etichetta a uno spazio nel testo, la chiamata API per aggiungere le espressioni non riesce.

## <a name="add-example-utterances"></a>Aggiungere espressioni di esempio

1. Per caricare il batch di espressioni di esempio, copiare questo comando nell'editor di testo:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT**_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    _*_

1. Sostituire i valori che iniziano con `_*_YOUR-` con i propri valori.

    |Informazioni|Scopo|
    |--|--|
    |`_*_YOUR-AUTHORING-ENDPOINT_*_`| L'endpoint dell'URL di creazione. Ad esempio, "https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/". Il nome della risorsa è stato impostato quando è stata creata la risorsa.|
    |`_*_YOUR-APP-ID_*_`| L'ID app di LUIS. |
    |`_*_YOUR-APP-VERSION_*_`| La versione dell'app LUIS. Per l'app Pizza, il numero di versione è "0.1" senza virgolette.|
    |`_*_YOUR-AUTHORING-KEY_*_`|La chiave di creazione di 32 caratteri.|

    Le chiavi e le risorse assegnate sono visibili nel portale LUIS nella sezione Gestisci della pagina _ *Risorse di Azure**. L'ID app è disponibile nella stessa sezione Gestisci della pagina **Impostazioni applicazione**.

1. Avviare un prompt dei comandi (Windows) o un terminale (macOS e Linux) e passare alla directory in cui è stato salvato il file `ExampleUtterances.JSON`.

1. Copiare il comando cURL dall'editor e incollarlo in un prompt dei comandi (Windows) o in un terminale (macOS e Linux). Premere INVIO per eseguire il comando.

    Si dovrebbe vedere la risposta seguente:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Ecco l'output formattato per una migliore leggibilità:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Eseguire il training del modello di app Pizza

1. Per iniziare una sessione di training per l'app Pizza, copiare questo comando nell'editor di testo:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "**_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. Come prima, sostituire i valori che iniziano con `_*_YOUR-` con i propri valori.

1. Copiare il comando cURL dall'editor e incollarlo in un prompt dei comandi (Windows) o in un terminale (macOS e Linux). Premere INVIO per eseguire il comando.

    Si dovrebbe vedere la risposta seguente:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Ecco l'output formattato per una migliore leggibilità:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Ottenere lo stato del training

1. Per ottenere lo stato della sessione di training, copiare questo comando nell'editor di testo:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. Come prima, sostituire i valori che iniziano con `_*_YOUR-` con i propri valori.

1. Copiare il comando cURL dall'editor e incollarlo in un prompt dei comandi (Windows) o in un terminale (macOS e Linux). Premere INVIO per eseguire il comando.

    Si dovrebbe vedere la risposta seguente:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Ecco l'output formattato per una migliore leggibilità:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```


## <a name="get-intent-from-the-prediction-endpoint"></a>Ottenere una finalità dall'endpoint di previsione

Usare cURL per eseguire query sull'[endpoint di previsione](https://aka.ms/luis-apim-v3-prediction) e ottenere un risultato della previsione.

> [!NOTE]
> Questo comando usa l'endpoint di previsione.

1. Copiare questo comando nell'editor di testo:

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    _*_

1. Sostituire i valori che iniziano con `_*_YOUR-` con i propri valori.

    |Informazioni|Scopo|
    |--|--|
    |`_*_YOUR-PREDICTION-ENDPOINT_*_`| L'endpoint dell'URL di previsione. Disponibile nel portale LUIS, pagina delle risorse di Azure per l'app.<br>Ad esempio: `https://westus.api.cognitive.microsoft.com/`.|
    |`_*_YOUR-APP-ID_*_`|L'ID app. Disponibile nel portale LUIS, pagina Impostazioni applicazione per l'app.
    |`_*_YOUR-PREDICTION-KEY_**`|La chiave di previsione di 32 caratteri. Disponibile nel portale LUIS, pagina delle risorse di Azure per l'app.

1. Copiare il testo in una finestra della console e premere INVIO per eseguire il comando:

1. Esaminare la risposta di previsione restituita in formato JSON:

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    Risposta JSON formattata per migliorare la leggibilità:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ]
          }
        }
      }
    }
    ```