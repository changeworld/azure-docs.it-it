---
title: Come ottenere eventi di posa facciali per la sincronizzazione labiale
titleSuffix: Azure Cognitive Services
description: Speech SDK supporta l'evento viseme nella sintesi vocale, che vengono usati per rappresentare la chiave che si pone in un discorso osservato, ad esempio la posizione dei labbri, la mascella e la lingua durante la produzione di un fonema particolare.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: f74a242db2686eb4571ebbea80b88a75dda205d4
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044068"
---
# <a name="get-facial-pose-events"></a>Ottenere gli eventi di posa facciali

> [!NOTE]
> Per il momento, viseme funziona solo per la `en-US-AriaNeural` voce nell'area Stati Uniti occidentali 2 ( `westus2` ).

Un viseme è la descrizione visiva di un fonema in lingua parlata.
Definisce la posizione della faccia e della bocca quando si parla di una parola.
Ogni viseme illustra le pose del viso chiave per un set specifico di fonemi.
Non esiste una corrispondenza uno-a-uno tra visemes e fonemi.
Spesso diversi fonemi corrispondono a un singolo viseme, in quanto diversi fonemi hanno lo stesso aspetto sul viso quando vengono prodotti, ad esempio `s` e `z` .
Vedere la [tabella di mapping tra visemes e fonemi](#map-phonemes-to-visemes).

Con visemes è possibile creare un assistente per la trasmissione di notizie più naturale e intelligente, un gioco più interattivo e caratteri di Cartoon e video più intuitivi sull'insegnamento della lingua. Gli utenti con problemi di udito possono anche prelevare suoni visivi e contenuto vocale "Lip-Read" che mostra visemes su una faccia animata.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Ottenere gli eventi viseme con l'SDK di riconoscimento vocale

Per eseguire eventi viseme, viene convertito il testo di input in un set di sequenze fonemhe e le sequenze viseme corrispondenti. Viene stimata l'ora di inizio di ogni viseme nell'audio vocale. Gli eventi viseme contengono una sequenza di ID viseme, ognuno con un offset nell'audio in cui viene visualizzato viseme. Questi eventi possono animare animazioni che simulano una persona che parla del testo di input.

| Parametro | Descrizione |
|-----------|-------------|
| ID viseme | Numero intero che specifica un viseme. In inglese (Stati Uniti) sono disponibili 22 visemes diversi per rappresentare le forme della bocca per un set specifico di fonemi. Vedere la [tabella di mapping tra ID viseme e fonemi](#map-phonemes-to-visemes).  |
| Offset audio | Ora di inizio di ogni viseme, in cicli (100 nanosecondi). |

Per ottenere gli eventi viseme, sottoscrivere l' `VisemeReceived` evento in Speech SDK.
Nei frammenti di codice seguenti viene illustrato come sottoscrivere l'evento viseme.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Mappa fonemi a visemes

Visemes variano in base alla lingua. Ogni lingua dispone di un set di visemes che corrispondono ai fonemi specifici. Nella tabella seguente viene illustrata la corrispondenza tra i fonemi dell'alfabeto fonetico internazionale (IPA) e gli ID viseme per la lingua inglese (Stati Uniti).

| IPA | Esempio | ID viseme |
|-----|---------|-----------|
| i   | t **EA**   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **te** | 4 |
| ɛ | **e** molto | 4 |
|æ  |   **ctive**        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oO** k          |4|
|oʊ |   **o** LD           |8|
|u  |   **U** BER          |7|
|ʌ  |   **u** NCLE         |1|
|aɪ |   **i** CE           |11|
|aʊ |   **unità organizzativa** t           |9|
|ɔɪ |   **OI** l           |10|
|Ju |   **Yu** ma          |[6, 7]|
|ə  |   **Vai**           |1|
|ɪɹ |   **Ear** s          |[6, 13]|
|ɛɹ |   **piano aereo**      |[4, 13]|
|ʊɹ |   c **il** e          |[4, 13]|
|aɪ (ə) ɹ |   Land **ire**   |[11, 13]|
|aʊ (ə) ɹ |   **ora** s     |[9, 13]|
|ɔɹ |   **o** Ange        |[3, 13]|
|ɑɹ |   TIST **AR**        |[2, 13]|
|ɝ  |   **orecchio**         |[5, 13]|
|ɚ  |   tutte le **er** GY       |[1, 13]|
|w  |   **w** Ith, s **UE** de   |7|
|j  |   **y** ARD, f **e** w     |6|
|p  |   **p** UT           |21|
|b  |   **b** IG           |21|
|u  |   **t** ALK          |19|
|d  |   **d** IG           |19|
|k  |   **c** UT           |20|
|g  |   **g** o            |20|
|m  |   **m** at, s **m** Ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ORK          |18|
|v  |   **v** alore         |18|
|θ  |   **°** in          |17|
|ð  |   **°** en          |17|
|s  |   **s** it           |15|
|z  |   AP **z**           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** ELP          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** Oy           |16|
|l  |   **l** ID, g **l** ad     |14|
|ɹ  |   **r** ed, b **r**    |13|


## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
