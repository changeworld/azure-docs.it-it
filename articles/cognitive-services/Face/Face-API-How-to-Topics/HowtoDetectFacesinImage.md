---
title: Rilevare i visi in un'immagine - Viso
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come usare il rilevamento del viso per estrarre attributi come sesso, età o posizione da una determinata immagine.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 71e98b735b4aa4631d73f8730a48c56a8c7585ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497643"
---
# <a name="get-face-detection-data"></a>Ottenere i dati di rilevamento viso

Questa guida illustra come usare il rilevamento del viso per estrarre attributi come sesso, età o posizione da una determinata immagine. I frammenti di codice in questa guida sono scritti in C# usando la libreria client Servizi cognitivi di Azure Viso. La stessa funzionalità è disponibile tramite [l'API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Questa guida illustra come:

- Ottenere le posizioni e le dimensioni dei visi in un'immagine.
- Ottenere in un'immagine le posizioni dei vari luoghi di interesse del viso, ad esempio i bambini, il viso e le cose.
- Indovinare il sesso, l'età, le emozioni e altri attributi di un viso rilevato.

## <a name="setup"></a>Eseguire la configurazione

In questa guida si presuppone che sia già stato creato un [oggetto FaceClient,](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) denominato , con una chiave di sottoscrizione Viso e un `faceClient` URL dell'endpoint. Da qui è possibile usare la funzionalità di rilevamento viso chiamando [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), usato in questa guida, [o DetectWithStreamAsync.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) Per istruzioni su come configurare questa funzionalità, seguire una delle guide introduttive.

Questa guida è incentrata sulle specifiche della chiamata Detect, ad esempio quali argomenti è possibile passare e cosa è possibile fare con i dati restituiti. È consigliabile eseguire una query solo per le funzionalità necessarie. Il completamento di ogni operazione richiede più tempo.

## <a name="get-basic-face-data"></a>Ottenere i dati di base sui viso

Per trovare i visi e ottenere le relative posizioni in un'immagine, chiamare il metodo [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) o [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) con il _parametro returnFaceId_ impostato su **true.** È l'impostazione predefinita.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

È possibile eseguire una query [sugli oggetti DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) restituiti per i relativi ID univoci e un rettangolo che fornisce le coordinate in pixel del viso.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Per informazioni su come analizzare la posizione e le dimensioni del viso, vedere [FaceRectangle.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle) In genere, questo rettangolo contiene gli occhi, le ciglia, il naso e la bocca. La parte superiore della testa, delle orecchini e del cincino non sono necessariamente incluse. Per usare il rettangolo del viso per ritagliare una testa completa o ottenere un disegno a metà ripresa, ad esempio per un'immagine di tipo ID foto, è possibile espandere il rettangolo in ogni direzione.

## <a name="get-face-landmarks"></a>Ottenere punti di riferimento per i viso

[I punti di riferimento](../concepts/face-detection.md#face-landmarks) dei visi sono un set di punti facili da trovare su un viso, ad esempio gli studenti o la punta del naso. Per ottenere i dati del punto di riferimento del viso, impostare il _parametro detectionModel_ su **DetectionModel.Detection01** e il _parametro returnFaceLandmarks_ su **true.**

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Il codice seguente illustra come recuperare le posizioni del naso e degli studenti:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

È anche possibile usare i dati dei punti di riferimento del viso per calcolare in modo accurato la direzione del viso. Ad esempio, è possibile definire la rotazione del viso come vettore dal centro della bocca al centro degli occhi. Il codice seguente calcola questo vettore:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Quando si conosce la direzione del viso, è possibile ruotare la cornice rettangolare del viso per allinearla più correttamente. Per ritagliare i visi in un'immagine, è possibile ruotare l'immagine a livello di codice in modo che i visi vengano sempre visualizzati in posizione verticale.

## <a name="get-face-attributes"></a>Recuperare gli attributi del viso

Oltre ai rettangoli e ai punti di riferimento, l'API di rilevamento del viso può analizzare diversi attributi concettuali di un viso. Per un elenco completo, vedere la [sezione concettuale Attributi](../concepts/face-detection.md#attributes) viso.

Per analizzare gli attributi viso, impostare il _parametro detectionModel_ su **DetectionModel.Detection01** e il parametro _returnFaceAttributes_ su un elenco di [valori di enumerazione FaceAttributeType.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Ottenere quindi riferimenti ai dati restituiti ed eseguire altre operazioni in base alle esigenze.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Per altre informazioni su ognuno degli attributi, vedere la guida concettuale Rilevamento [del viso e attributi.](../concepts/face-detection.md)

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come usare le varie funzionalità del rilevamento del viso. Integrare quindi queste funzionalità in un'app per aggiungere dati sul viso dagli utenti.

- [Esercitazione: Aggiungere utenti a un servizio Viso](../enrollment-overview.md)

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
