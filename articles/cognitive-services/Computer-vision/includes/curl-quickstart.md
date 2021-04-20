---
title: 'Guida introduttiva: API REST di riconoscimento ottico dei caratteri'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva è possibile iniziare a usare l'API REST riconoscimento ottico dei caratteri.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f01b1d222470c49505638be64180948b6f7e046
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728258"
---
Usare l'API REST riconoscimento ottico dei caratteri per leggere testo stampato e scritto a mano.

> [!NOTE]
> Questa guida di avvio rapido usa comandi cURL per chiamare l'API REST. È anche possibile chiamare l'API REST usando un linguaggio di programmazione. In GitHub sono disponibili esempi in [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/) 
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
  * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
  * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* [cURL](https://curl.haxx.se/) installato



## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Il servizio OCR può leggere il testo visibile in un'immagine e convertirlo in un flusso di caratteri. Per altre informazioni sul riconoscimento del testo, vedere Panoramica del riconoscimento ottico dei [caratteri (OCR).](../overview-ocr.md)

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Copiare il comando seguente in un editor di testo.
1. Apportare le modifiche seguenti al comando, dove necessario:
    1. Sostituire il valore di `<subscriptionKey>` con la chiave di sottoscrizione.
    1. Sostituire la prima parte dell'URL della richiesta (`westcentralus`) con il testo dell'URL dell'endpoint.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Facoltativamente, modificare l'URL dell'immagine nel corpo della richiesta (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) nell'URL di un'altra immagine da analizzare.
1. Aprire una finestra del prompt dei comandi.
1. Incollare il comando dall'editor di testo nella finestra del prompt dei comandi e quindi eseguire il comando.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

La risposta includerà un'intestazione `Operation-Location`, il cui valore è un URL univoco. Usare questo URL per eseguire query sui risultati dell'operazione di lettura. L'URL scade dopo 48 ore.

### <a name="get-read-results"></a>Ottenere risultati della lettura

1. Copiare il comando seguente in un editor di testo.
1. Sostituire l'URL con il valore `Operation-Location` copiato nel passaggio precedente.
1. Apportare le modifiche seguenti al comando, dove necessario:
    1. Sostituire il valore di `<subscriptionKey>` con la chiave di sottoscrizione.
1. Aprire una finestra del prompt dei comandi.
1. Incollare il comando dall'editor di testo nella finestra del prompt dei comandi e quindi eseguire il comando.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. L'applicazione di esempio analizza e visualizza una risposta con esito positivo nella finestra del prompt dei comandi, come nell'esempio seguente:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come chiamare l'API REST read. Successivamente, sono disponibili altre informazioni sulle funzionalità dell'API di lettura.

> [!div class="nextstepaction"]
>[Chiamare l'API di lettura](../Vision-API-How-to-Topics/call-read-api.md)

* [Panoramica di OCR](../overview-ocr.md)
