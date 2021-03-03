---
title: Come attenuare la latenza quando si usa il servizio Face
titleSuffix: Azure Cognitive Services
description: Informazioni su come ridurre la latenza quando si usa il servizio Face.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 2c771509de5ac246bac0d8e006a5d0b884a410b0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706810"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Procedura: attenuare la latenza quando si usa il servizio Face

È possibile che si verifichi una latenza quando si usa il servizio Face. La latenza si riferisce a qualsiasi tipo di ritardo che si verifica durante la comunicazione in rete. In generale, le possibili cause di latenza includono:
- La distanza fisica di ogni pacchetto deve viaggiare dall'origine alla destinazione.
- Problemi con il supporto di trasmissione.
- Errori nei router o commutatori lungo il percorso di trasmissione.
- Tempo richiesto da applicazioni antivirus, firewall e altri meccanismi di sicurezza per controllare i pacchetti.
- Malfunzionamenti nelle applicazioni client o server.

In questo argomento vengono illustrate le possibili cause di latenza specifiche per l'uso dei servizi cognitivi di Azure e come è possibile attenuare queste cause.

> [!NOTE]
> I servizi cognitivi di Azure non forniscono alcun Contratto di servizio (SLA) per la latenza.

## <a name="possible-causes-of-latency"></a>Possibili cause di latenza

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Connessione lenta tra il servizio cognitivo e un URL remoto

Alcuni servizi cognitivi di Azure forniscono metodi che ottengono dati da un URL remoto fornito dall'utente. Ad esempio, quando si chiama il [Metodo DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) del servizio face, è possibile specificare l'URL di un'immagine in cui il servizio tenta di rilevare i visi.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Il servizio Face deve quindi scaricare l'immagine dal server remoto. Se la connessione dal servizio Face al server remoto è lenta, questo influirà sul tempo di risposta del metodo di rilevamento.

Per attenuare questo problema, è consigliabile [archiviare l'immagine nell'archivio BLOB Premium di Azure](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Ad esempio:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Dimensioni di caricamento grandi

Alcuni servizi cognitivi di Azure forniscono metodi che ottengono dati da un file caricato. Ad esempio, quando si chiama il [Metodo DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) del servizio face, è possibile caricare un'immagine in cui il servizio tenta di rilevare i visi.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Se il file da caricare è di grandi dimensioni, questo avrà un effetto sul tempo di risposta del `DetectWithStreamAsync` metodo, per i motivi seguenti:
- Il caricamento del file richiede più tempo.
- Il servizio viene impiegato più a lungo per elaborare il file, in proporzione alle dimensioni del file.

Soluzioni:
- Si consiglia [di archiviare l'immagine nell'archiviazione BLOB di Azure Premium](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Ad esempio:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Prendere in considerazione il caricamento di un file più piccolo.
    - Vedere le linee guida relative ai [dati di input per il rilevamento viso](../concepts/face-detection.md#input-data) e [i dati di input per il riconoscimento viso](../concepts/face-recognition.md#input-data).
    - Per il rilevamento viso, quando si usa il modello di rilevamento `DetectionModel.Detection01` , la riduzione delle dimensioni del file di immagine aumenta la velocità di elaborazione. Quando si usa il modello `DetectionModel.Detection02` di rilevamento, la riduzione delle dimensioni del file di immagine aumenta solo la velocità di elaborazione se il file di immagine è più piccolo di 1920x1080.
    - Per il riconoscimento viso, la riduzione della dimensione del viso a 200x200 pixel non influisce sull'accuratezza del modello di riconoscimento.
    - Anche le prestazioni dei `DetectWithUrlAsync` `DetectWithStreamAsync` metodi e dipendono dal numero di visi presenti in un'immagine. Il servizio viso può restituire fino a 100 visi per un'immagine. Le facce vengono classificate in base alle dimensioni del rettangolo della faccia da grandi a piccole.
    - Se è necessario chiamare più metodi del servizio, è consigliabile chiamarli in parallelo se ne è consentita la progettazione dell'applicazione. Se, ad esempio, è necessario rilevare i visi in due immagini per eseguire un confronto viso:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Connessione lenta tra la risorsa di calcolo e il servizio Face

Se il computer dispone di una connessione lenta al servizio viso, questo influirà sul tempo di risposta dei metodi del servizio.

Soluzioni:
- Quando si crea la sottoscrizione viso, assicurarsi di scegliere l'area geografica più vicina alla posizione in cui è ospitata l'applicazione.
- Se è necessario chiamare più metodi del servizio, è consigliabile chiamarli in parallelo se ne è consentita la progettazione dell'applicazione. Per un esempio, vedere la sezione precedente.

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come ridurre la latenza quando si usa il servizio Face. Successivamente, si apprenderà come eseguire la scalabilità verticale dagli oggetti gruppo e facet esistenti rispettivamente agli oggetti LargePersonGroup e LargeFaceList.

> [!div class="nextstepaction"]
> [Esempio: Usare la funzionalità su larga scala](how-to-use-large-scale.md)

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)