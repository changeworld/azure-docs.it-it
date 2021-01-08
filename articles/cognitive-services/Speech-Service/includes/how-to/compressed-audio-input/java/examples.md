---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: e9b980dcbe42694137c7b29ee3bddaa7802db3da
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978859"
---
Per configurare l'SDK vocale per accettare l'input audio compresso, creare un `PullAudioInputStream` o `PushAudioInputStream` . Quindi, creare un oggetto `AudioConfig` da un'istanza della classe Stream, specificando il formato di compressione del flusso.

Si supponga di disporre di una classe di flusso di input denominata `pullStream` e che usi Opus/OGG. Il codice potrebbe essere simile al seguente:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

// ... omitted for brevity

SpeechConfig speechConfig =
    SpeechConfig.fromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = 
    AudioStreamFormat.getCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig =
    AudioConfig.fromStreamInput(
        pullStream,
        audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();

String text = result.getText();
```
