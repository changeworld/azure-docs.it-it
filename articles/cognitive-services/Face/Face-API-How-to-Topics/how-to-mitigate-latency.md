---
title: Come ridurre la latenza quando si usa il servizio Viso
titleSuffix: Azure Cognitive Services
description: Informazioni su come ridurre la latenza quando si usa il servizio Viso.
services: cognitive-services
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.openlocfilehash: a306883573387a2a5c20a53c7015c6dbd3eddf65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878669"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Procedura: Ridurre la latenza quando si usa il servizio Viso

Quando si usa il servizio Viso, potrebbe verificarsi una latenza. La latenza si riferisce a qualsiasi tipo di ritardo che si verifica durante la comunicazione in rete. In generale, le possibili cause della latenza includono:
- Distanza fisica che ogni pacchetto deve percorrere dall'origine alla destinazione.
- Problemi con il supporto di trasmissione.
- Errori nei router o nei commutatori lungo il percorso di trasmissione.
- Tempo necessario alle applicazioni antivirus, ai firewall e ad altri meccanismi di sicurezza per controllare i pacchetti.
- Malfunzionamenti nelle applicazioni client o server.

In questo argomento vengono trattate le possibili cause di latenza specifiche per l'uso del Servizi cognitivi di Azure e come è possibile attenuare queste cause.

> [!NOTE]
> Servizi cognitivi di Azure non forniscono alcuna Contratto di servizio (SLA) relativa alla latenza.

## <a name="possible-causes-of-latency"></a>Possibili cause della latenza

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Connessione lenta tra Il servizio cognitivo e un URL remoto

Alcuni Servizi cognitivi di Azure forniscono metodi che ottengono dati da un URL remoto fornito dall'utente. Ad esempio, quando si chiama il [metodo DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) del servizio Viso, è possibile specificare l'URL di un'immagine in cui il servizio tenta di rilevare i visi.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Il servizio Viso deve quindi scaricare l'immagine dal server remoto. Se la connessione dal servizio Viso al server remoto è lenta, ciò inciderà sul tempo di risposta del metodo Detect.

Per ovviare a questo problema, è [consigliabile archiviare l'immagine in Archiviazione BLOB Premium di Azure.](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet) Ad esempio:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Dimensioni di caricamento di grandi dimensioni

Alcuni Servizi cognitivi di Azure metodi che ottengono dati da un file caricato. Ad esempio, quando si chiama il [metodo DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) del servizio Viso, è possibile caricare un'immagine in cui il servizio prova a rilevare i visi.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Se il file da caricare è di grandi dimensioni, ciò inciderà sul tempo di risposta del `DetectWithStreamAsync` metodo, per i motivi seguenti:
- Il caricamento del file richiede più tempo.
- Il servizio richiede più tempo per elaborare il file, in proporzione alle dimensioni del file.

Soluzioni:
- Valutare [la possibilità di archiviare l'immagine in Archiviazione BLOB Premium di Azure.](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet) Ad esempio:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Provare a caricare un file più piccolo.
    - Vedere le linee guida relative ai [dati di input per il rilevamento del](../concepts/face-detection.md#input-data) viso e ai dati di input per il riconoscimento del [viso.](../concepts/face-recognition.md#input-data)
    - Per il rilevamento del viso, quando si usa il modello di rilevamento `DetectionModel.Detection01` , la riduzione delle dimensioni del file di immagine aumenterà la velocità di elaborazione. Quando si usa il modello di rilevamento, la riduzione delle dimensioni del file di immagine aumenta la velocità di elaborazione solo se il file di immagine è inferiore a `DetectionModel.Detection02` 1920x1080.
    - Per il riconoscimento del viso, la riduzione delle dimensioni del viso a 200x200 pixel non influisce sull'accuratezza del modello di riconoscimento.
    - Le prestazioni dei `DetectWithUrlAsync` metodi e dipendono anche dal numero di `DetectWithStreamAsync` visi presenti in un'immagine. Il servizio Viso può restituire fino a 100 visi per un'immagine. I visi vengono classificati in base alle dimensioni del rettangolo del viso da grandi a piccole.
    - Se è necessario chiamare più metodi di servizio, è consigliabile chiamarli in parallelo se la progettazione dell'applicazione lo consente. Ad esempio, se è necessario rilevare i visi in due immagini per eseguire un confronto dei visi:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Connessione lenta tra la risorsa di calcolo e il servizio Viso

Se il computer ha una connessione lenta al servizio Viso, questo influisce sul tempo di risposta dei metodi di servizio.

Soluzioni:
- Quando si crea la sottoscrizione Viso, assicurarsi di scegliere l'area più vicina a cui è ospitata l'applicazione.
- Se è necessario chiamare più metodi del servizio, è consigliabile chiamarli in parallelo se la progettazione dell'applicazione lo consente. Per un esempio, vedere la sezione precedente.
- Se le latenze più lunghe influiscono sull'esperienza utente, scegliere una soglia di timeout (ad esempio, un massimo di 5 secondi) prima di ritentare la chiamata API.

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come ridurre la latenza quando si usa il servizio Viso. Successivamente, si apprenderà come aumentare le dimensioni dagli oggetti PersonGroup e FaceList esistenti agli oggetti LargePersonGroup e LargeFaceList, rispettivamente.

> [!div class="nextstepaction"]
> [Esempio: Usare la funzionalità su larga scala](how-to-use-large-scale.md)

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentazione di riferimento (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
