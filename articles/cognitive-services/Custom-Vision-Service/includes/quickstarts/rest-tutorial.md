---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 8890a0132e5f510a0af2862c483206fd025a68d8
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256307"
---
Introduzione all'API REST Visione personalizzata. Seguire questa procedura per chiamare l'API e creare un modello di classificazione immagini. Si creerà un progetto, si aggiungeranno tag, si eseguirà il training del progetto e si userà l'URL dell'endpoint di stima del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'app di riconoscimento immagini personalizzata.

> [!NOTE]
> Visione personalizzata è più facile da usare tramite un SDK della libreria client o tramite le [linee guida basate su browser](../../get-started-build-detector.md).

Usare la libreria client di Visione personalizzata per .NET per:

* Creare un nuovo progetto Visione personalizzata
* Aggiungere tag al progetto
* Caricare e contrassegnare le immagini
* Eseguire il training del progetto
* Pubblicare l'iterazione corrente
* Testare l'endpoint di stima

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="creare una risorsa Visione personalizzata"  target="_blank">creare una risorsa Visione personalizzata <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per creare la risorsa di training e di previsione e ottenere le chiavi e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint delle risorse create sono necessari per connettere l'applicazione a Visione personalizzata. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="create-a-new-custom-vision-project"></a>Creare un nuovo progetto Visione personalizzata

Verrà usato un comando come il seguente per creare un progetto di classificazione immagini. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Copiare il comando in un editor di testo e apportare le modifiche seguenti:

* Sostituire `{subscription key}` con la propria chiave di sottoscrizione Viso valida.
* Sostituire `{endpoint}` con l'endpoint che corrisponde alla chiave di sottoscrizione.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Sostituire `{name}` con il nome del progetto.
* Facoltativamente, impostare altri parametri URL per configurare il tipo di modello che userà il progetto. Per le opzioni, vedere l'[API CreatProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae).

Viene restituita una risposta JSON simile alla seguente. Salvare il valore `"id"` del progetto in una posizione temporanea.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Aggiungere tag al progetto

Usare il comando seguente per definire i tag in base a cui eseguire il training del modello.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Inserire di nuovo la chiave e l'URL dell'endpoint.
* Sostituire `{projectId}` con il proprio ID progetto.
* Sostituire `{name}` con il nome del tag da usare.

Ripetere questo processo per tutti i tag da usare nel progetto. Se si usano le immagini di esempio fornite, aggiungere i tag `"Hemlock"` e `"Japanese Cherry"`.

Viene restituita una risposta JSON simile alla seguente. Salvare il valore `"id"` di ogni tag in una posizione temporanea.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Scaricare quindi le immagini di esempio per questo progetto. Salvare il contenuto della [cartella immagini di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) nel dispositivo locale.

> [!NOTE]
> Trove, un progetto Microsoft Garage, consente di raccogliere e acquistare set di immagini a scopo di training. Una volta raccolte le immagini, è possibile scaricarle e quindi importarle nel progetto di Visione personalizzata nel modo consueto. Per altre informazioni, visitare la [pagina di Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3).

Usare il comando seguente per caricare le immagini e applicare i tag, tutti in una volta per le immagini "Hemlock" e separatamente per le immagini "Japanese Cherry". Per altre opzioni, vedere l'API [Create Images From Data](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5).

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Inserire di nuovo la chiave e l'URL dell'endpoint.
* Sostituire `{projectId}` con il proprio ID progetto.
* Sostituire `{tagArray}` con l'ID di un tag.
* Quindi, popolare il corpo della richiesta con i dati binari delle immagini a cui applicare i tag.

## <a name="train-the-project"></a>Eseguire il training del progetto

Questo metodo esegue il training del modello nelle immagini con tag che sono state caricate e restituisce un ID per l'iterazione del progetto corrente.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Inserire di nuovo la chiave e l'URL dell'endpoint.
* Sostituire `{projectId}` con il proprio ID progetto.
* Sostituire `{tagArray}` con l'ID di un tag.
* Quindi, popolare il corpo della richiesta con i dati binari delle immagini a cui applicare i tag.
* Facoltativamente, usare altri parametri URL. Per le opzioni, vedere l'API [Train Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1).

> [!TIP]
> Eseguire il training con i tag selezionati
>
> Facoltativamente, è possibile eseguire il training solo su un subset dei tag applicati. Questa operazione può essere eseguita se non è ancora stato applicato un numero sufficiente di determinati tag, ma si dispone di un numero sufficiente di altri. Aggiungere il contenuto JSON facoltativo al corpo della richiesta. Popolare la matrice `"selectedTags"` con gli ID dei tag da usare.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

La risposta JSON contiene informazioni sul progetto di cui è stato eseguito il training, incluso l'ID iterazione (`"id"`). Salvare questo valore per il passaggio successivo.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

Questo metodo rende disponibile l'iterazione corrente del modello per l'esecuzione di query. Usare il nome del modello restituito come riferimento per inviare richieste di previsione. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Inserire di nuovo la chiave e l'URL dell'endpoint.
* Sostituire `{projectId}` con il proprio ID progetto.
* Sostituire `{iterationId}` con l'ID restituito nel passaggio precedente.
* Sostituire `{publishedName}` con il nome da assegnare al modello di previsione.
* Sostituire `{predictionId}` con l'ID risorsa di previsione. È possibile trovarlo nella scheda **Panoramica** della risorsa di previsione nel portale di Azure, indicato come **ID sottoscrizione**.
* Facoltativamente, usare altri parametri URL. Vedere l'API[Publish Iteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5).

## <a name="test-the-prediction-endpoint"></a>Testare l'endpoint di stima

Infine, usare questo comando per testare il modello di cui è stato eseguito il training caricando una nuova immagine per la classificazione con i tag. È possibile usare l'immagine nella cartella "Test" dei file di esempio scaricata in precedenza.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Inserire di nuovo la chiave e l'URL dell'endpoint.
* Sostituire `{projectId}` con il proprio ID progetto.
* Sostituire `{publishedName}` con il nome usato nel passaggio precedente.
* Aggiungere i dati binari dell'immagine locale al corpo della richiesta.
* Facoltativamente, usare altri parametri URL. Vedere l'API [Classify Image](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3).

La risposta JSON restituita elencherà tutti i tag applicati dal modello all'immagine, insieme ai punteggi di probabilità per ognuno. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto, sono stati eseguiti tutti i passaggi del processo di classificazione immagini usando l'API REST. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* [Documentazione di riferimento delle API (training)](/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)
* [Documentazione di riferimento delle API (previsione)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
