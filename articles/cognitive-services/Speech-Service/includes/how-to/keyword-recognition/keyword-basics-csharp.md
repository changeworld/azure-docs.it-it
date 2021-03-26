---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 407906727332f3db8d3d0a6840d0c865c6b33ff7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609533"
---
Innanzitutto, caricare il file del modello di parole chiave utilizzando la `FromFile()` funzione statica, che restituisce `KeywordRecognitionModel` . Usare il percorso del `.table` file scaricato da speech studio. Inoltre, è possibile creare un `AudioConfig` usando il microfono predefinito, quindi creare un'istanza di un nuovo `KeywordRecognizer` usando la configurazione audio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Successivamente, l'esecuzione del riconoscimento delle parole chiave viene eseguita con una chiamata a `RecognizeOnceAsync()` passando l'oggetto modello. Viene avviata una sessione di riconoscimento delle parole chiave che dura fino a quando non viene riconosciuta la parola chiave. In questo modo, in genere si usa questo schema progettuale nelle applicazioni multithread o nei casi di utilizzo in cui è possibile che si sia in attesa di una parola di riattivazione per un periodo illimitato.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Nell'esempio riportato di seguito viene usato il riconoscimento delle parole chiave locale, poiché non richiede un `SpeechConfig` oggetto per il contesto di autenticazione e non contatta il back-end. Tuttavia, è possibile eseguire sia il riconoscimento delle parole chiave che [la verifica utilizzando una connessione back-end diretta](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Riconoscimento continuo

Altre classi nell'SDK vocale supportano il riconoscimento continuo (per riconoscimento vocale e preventivo) con riconoscimento delle parole chiave. In questo modo è possibile usare lo stesso codice normalmente usato per il riconoscimento continuo, con la possibilità di fare riferimento a un `.table` file per il modello di parola chiave.

Per la sintesi vocale, seguire lo stesso schema di progettazione illustrato nella [Guida introduttiva](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) per configurare il riconoscimento continuo. Quindi, sostituire la chiamata a `recognizer.StartContinuousRecognitionAsync()` con `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` e passare l' `KeywordRecognitionModel` oggetto. Per arrestare il riconoscimento continuo con il riconoscimento delle parole chiave, usare `recognizer.StopKeywordRecognitionAsync()` anziché `recognizer.StopContinuousRecognitionAsync()` .

Il riconoscimento preventivo usa un modello identico con le [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) funzioni e.
