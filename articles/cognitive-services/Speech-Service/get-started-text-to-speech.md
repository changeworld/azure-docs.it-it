---
title: Guida di avvio rapido sulla sintesi vocale - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Speech SDK per convertire il testo in voce. Questo argomento di avvio rapido illustra la costruzione di oggetti e i modelli di progettazione, i formati di output audio supportati, l'interfaccia della riga di comando di Voce e le opzioni di configurazione personalizzate per la sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: sintesi vocale
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428197"
---
# <a name="get-started-with-text-to-speech"></a>Introduzione alla sintesi vocale

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Ottenere informazioni sulla posizione

Il progetto potrebbe dover essere in grado di stabilire quando una parola è pronunciata da sintesi vocale, in modo da eseguire azioni specifiche in base a tale intervallo. Ad esempio, se si desidera evidenziare le parole come sono state pronunciate, è necessario sapere cosa evidenziare, quando evidenziarlo e per quanto tempo evidenziarlo.

A tale scopo, è possibile usare l' `WordBoundary` evento disponibile all'interno di `SpeechSynthesizer` . Questo evento viene generato all'inizio di ogni nuova parola pronunciata e fornirà un offset temporale all'interno del flusso parlato, oltre a un offset di testo all'interno della richiesta di input.

* `AudioOffset` segnala il tempo trascorso dell'audio di output tra l'inizio della sintesi e l'inizio della parola successiva. Viene misurata in unità di centinaia nanosecondi (HNS) con 10.000 HNS equivalente a 1 millisecondo.
* `WordOffset` indica la posizione del carattere nella stringa di input (testo originale o [SSML](speech-synthesis-markup.md)) immediatamente prima della parola che sta per essere pronunciata.

> [!NOTE]
> `WordBoundary` gli eventi vengono generati quando i dati audio di output diventano disponibili, che saranno più veloci della riproduzione in un dispositivo di output. Il chiamante deve eseguire in modo appropriato la sincronizzazione della temporizzazione del flusso in "tempo reale".

È possibile trovare esempi di uso `WordBoundary` negli [esempi di sintesi vocale](https://aka.ms/csspeech/samples) su GitHub.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Voce personalizzata](how-to-custom-voice.md)
* [Migliorare la sintesi con SSML](speech-synthesis-markup.md)
* Informazioni su come usare l'[API Long Audio](long-audio-api.md) per esempi di testo di grandi dimensioni, come libri e notizie
* Vedere gli [esempi di avvio rapido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) in GitHub
