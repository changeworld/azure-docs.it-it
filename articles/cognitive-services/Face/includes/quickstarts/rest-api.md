---
title: Avvio rapido dell'API REST Viso
description: Usare l'API REST Viso con cURL per rilevare visi, trovare volti simili (ricerca di volti per immagine), identificare i visi (ricerca basata su riconoscimento facciale) ed eseguire la migrazione dei dati sui visi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: be942f73ee0a3d5a8850141c937754bad330db90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912253"
---
Introduzione al riconoscimento facciale con l'API REST Viso. Il servizio Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Usare l'API REST Viso per:

* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)

> [!NOTE]
> Questa guida di avvio rapido usa comandi cURL per chiamare l'API REST. È anche possibile chiamare l'API REST usando un linguaggio di programmazione. In GitHub sono disponibili esempi in [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest) e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata saranno necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Si userà un comando come il seguente per chiamare l'API Viso e ottenere i dati dell'attributo viso da un'immagine. In primo luogo, copiare il codice in un editor di testo. È necessario apportare modifiche ad alcune parti prima di poterlo eseguire.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Apportare le modifiche seguenti:
1. Assegnare `Ocp-Apim-Subscription-Key` alla propria chiave di sottoscrizione Viso valida.
1. Modificare la prima parte dell'URL della query in modo che equivalga all'endpoint che corrisponde alla chiave di sottoscrizione.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Modificare facoltativamente l'URL nel corpo della richiesta in modo che faccia riferimento a un'immagine diversa.

Dopo aver apportato le modifiche, aprire un prompt dei comandi e immettere il nuovo comando. 

### <a name="examine-the-results"></a>Esaminare i risultati

Le informazioni sul viso dovrebbero essere visualizzate come dati JSON nella finestra della console. Ad esempio:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Recuperare gli attributi del viso
 
Per estrarre gli attributi del viso, chiamare di nuovo l'API Rileva ma impostare `detectionModel` su `detection_01`. Aggiungere anche il parametro `returnFaceAttributes` della query. Il comando avrà un aspetto simile al seguente. Come in precedenza, inserire l'endpoint e la chiave di sottoscrizione del servizio Viso.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Esaminare i risultati

Le informazioni sul viso restituite ora includono gli attributi del viso. Ad esempio:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Individuazione di visi simili

Questa operazione confronta un singolo viso rilevato (origine) con un set di altri visi (destinazione) per trovare corrispondenze (ricerca di volti per immagine). Quando trova una corrispondenza, visualizza l'ID del viso corrispondente nella console.

### <a name="detect-faces-for-comparison"></a>Rilevare visi per il confronto

Per il confronto, è prima di tutto necessario rilevare visi nelle immagini. Eseguire questo comando seguendo la stessa procedura della sezione [Rilevare i visi](#detect-faces-in-an-image). Questo metodo di rilevamento è ottimizzato per operazioni di confronto. Non estrae gli attributi dettagliati dei visi come nella sezione precedente e usa un modello di rilevamento diverso.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Trovare il valore `"faceId"` nella risposta JSON e salvarlo in un percorso temporaneo. Chiamare quindi di nuovo il comando precedente per questi altri URL di immagine e salvare anche i rispettivi ID viso. Tali ID verranno usati come gruppo di destinazione di visi da cui trovare un viso simile.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Rilevare infine il viso di origine singolo che verrà usato per la corrispondenza e salvare il rispettivo ID. Mantenere questo ID separato rispetto agli altri.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Trovare le corrispondenze

Copiare il comando seguente in un editor di testo.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Apportare quindi le modifiche seguenti:
1. Assegnare `Ocp-Apim-Subscription-Key` alla propria chiave di sottoscrizione Viso valida.
1. Modificare la prima parte dell'URL della query in modo che equivalga all'endpoint che corrisponde alla chiave di sottoscrizione.

Usare il contenuto JSON seguente per il valore `body`:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Usare l'ID viso di origine per `"faceId"`.
1. Incollare altri ID viso come condizioni nella matrice `"faceIds"`.

### <a name="examine-the-results"></a>Esaminare i risultati

Si riceverà una risposta JSON che elenca gli ID dei visi corrispondenti al viso della query.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare l'API REST Viso per eseguire attività di riconoscimento facciale di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Che cos'è il servizio Viso?](../../overview.md)