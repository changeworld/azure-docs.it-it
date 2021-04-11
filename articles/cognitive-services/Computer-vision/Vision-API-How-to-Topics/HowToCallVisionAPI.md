---
title: Chiamare l'API di analisi immagini
titleSuffix: Azure Cognitive Services
description: Informazioni su come chiamare l'API di analisi immagini e configurarne il comportamento.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012269"
---
# <a name="call-the-image-analysis-api"></a>Chiamare l'API di analisi immagini

Questo articolo illustra come chiamare l'API di analisi immagini per restituire informazioni sulle funzionalità visive di un'immagine.

Questa guida presuppone che sia già stata <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" creata una risorsa di visione artificiale "  target="_blank"> creare una risorsa visione artificiale </a> e ottenere una chiave di sottoscrizione e un URL dell'endpoint. Se non è stato fatto, seguire una [Guida introduttiva](../quickstarts-sdk/image-analysis-client-library.md) per iniziare.
  
## <a name="submit-data-to-the-service"></a>Inviare dati al servizio

Si invia un'immagine locale o un'immagine remota all'API di analisi. Per la lingua locale, inserire i dati dell'immagine binaria nel corpo della richiesta HTTP. Per Remote è possibile specificare l'URL dell'immagine formattando il corpo della richiesta come segue: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Determinare come elaborare i dati

###  <a name="select-visual-features"></a>Selezionare le funzionalità visive

L' [API Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) consente di accedere a tutte le funzionalità di analisi delle immagini del servizio. È necessario specificare le funzionalità che si desidera utilizzare impostando i parametri di query dell'URL. Un parametro può avere più valori separati da virgole. Ogni funzionalità specificata richiederà tempo di calcolo aggiuntivo, quindi specificare solo gli elementi necessari.

|Parametro dell'URL | Valore | Descrizione|
|---|---|--|
|`visualFeatures`|`Adult` | rileva se l'immagine è pornografica (Mostra la nudità o un atto sessuale) o se è cruenta (rappresenta una violenza o un sangue eccessivo). Vengono rilevati anche contenuti sessuali (noti anche come contenuto).|
||`Brands` | rileva diversi marchi all'interno di un'immagine, inclusa la posizione approssimativa. L'argomento marchi è disponibile solo in inglese.|
||`Categories` | Categorizza il contenuto dell'immagine in base a una tassonomia definita nella documentazione. Si tratta del valore predefinito per `visualFeatures`.|
||`Color` | determina il colore dell'accento, il colore dominante e se un'immagine è nera&bianco.|
||`Description` | descrive il contenuto dell'immagine con una frase completa nelle lingue supportate.|
||`Faces` | rileva se sono presenti visi. Se presente, genera coordinate, sesso ed età.|
||`ImageType` | rileva se l'immagine è ClipArt o un disegno a linee.|
||`Objects` | rileva vari oggetti all'interno di un'immagine, inclusa la posizione approssimativa. L'argomento Objects è disponibile solo in inglese.|
||`Tags` | contrassegna l'immagine con un elenco dettagliato di parole correlate al contenuto dell'immagine.|
|`details`| `Celebrities` | identifica le celebrità se rilevate nell'immagine.|
||`Landmarks` |identifica i punti di riferimento se vengono rilevati nell'immagine.|

Un URL popolato potrebbe essere simile al seguente:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Specificare le lingue

È inoltre possibile specificare la lingua dei dati restituiti. Il parametro di query dell'URL seguente specifica la lingua. Il valore predefinito è `en`.

|Parametro dell'URL | Valore | Descrizione|
|---|---|--|
|`language`|`en` | Inglese|
||`es` | Spagnolo|
||`ja` | Giapponese|
||`pt` | Portoghese|
||`zh` | Cinese semplificato|

Un URL popolato potrebbe essere simile al seguente:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Chiamate API con ambito**
>
> Alcune delle funzionalità di analisi delle immagini possono essere chiamate direttamente e tramite la chiamata all'API di analisi. Ad esempio, è possibile eseguire un'analisi con ambito solo dei tag di immagine effettuando una richiesta a `https://{endpoint}/vision/v3.2-preview.3/tag` . Vedere la [documentazione di riferimento](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) per altre funzionalità che possono essere chiamate separatamente.

## <a name="get-results-from-the-service"></a>Ottenere risultati dal servizio

Il servizio restituisce una `200` risposta http e il corpo contiene i dati restituiti sotto forma di stringa JSON. Di seguito è riportato un esempio di una risposta JSON.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Vedere la tabella seguente per le spiegazioni dei campi in questo esempio:

Campo | Type | Contenuto
------|------|------|
Tag  | `object` | L'oggetto di primo livello per una matrice di tag.
tags[].Name | `string`    | La parola chiave del classificatore di tag.
tags[].Score    | `number`    | Il punteggio di attendibilità, compreso tra 0 e 1.
description     | `object`    | Oggetto di primo livello per una descrizione dell'immagine.
description.tags[] |    `string`    | L'elenco di tag. Se l'attendibilità nella capacità di produrre una didascalia non è sufficiente, i tag potrebbero essere l'unica informazione disponibile per il chiamante.
description.captions[].text    | `string`    | Frase che descrive l'immagine.
description.captions[].confidence    | `number`    | Il punteggio di attendibilità per la frase.

### <a name="error-codes"></a>Codici di errore

Vedere l'elenco seguente di possibili errori e le relative cause:

* 400
    * InvalidImageUrl-l'URL dell'immagine non è formattato correttamente o non è accessibile.
    * InvalidImageFormat: i dati di input non sono un'immagine valida.
    * InvalidImageSize-l'immagine di input è troppo grande.
    * Il tipo di funzionalità specificato da NotSupportedVisualFeature non è valido.
    * NotSupportedImage: immagine non supportata, ad esempio pornografia infantile.
    * InvalidDetails: valore di parametro non supportato `detail` .
    * NotSupportedLanguage-l'operazione richiesta non è supportata nella lingua specificata.
    * BadArgument: nel messaggio di errore vengono forniti ulteriori dettagli.
* 415: errore del tipo di supporto non supportato. Content-Type non è nei tipi consentiti:
    * Per un URL di immagine: Content-Type deve essere Application/JSON
    * Per i dati di un'immagine binaria: Content-Type deve essere Application/ottetto-Stream o multipart/form-data
* 500
    * FailedToProcess
    * Timeout: timeout dell'elaborazione delle immagini.
    * InternalServerError

## <a name="next-steps"></a>Passaggi successivi

Per provare l'API REST, vedere le informazioni di [riferimento sull'API di analisi delle immagini](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
