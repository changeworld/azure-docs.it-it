---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 7b1bc00e54abed70d4cbb769e15ff0f304c62b85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108837"
---
Questo argomento di avvio rapido illustra i modelli di progettazione comuni per eseguire la sintesi vocale con Speech SDK. Si inizia con la configurazione di base e la sintesi e si passa ad esempi più avanzati per lo sviluppo di applicazioni personalizzate, tra cui:

* Recupero di risposte come flussi in memoria
* Personalizzazione della frequenza di campionamento e della velocità in bit dell'output
* Invio di richieste di sintesi tramite SSML (Speech Synthesis Markup Language)
* Uso di voci neurali

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/text-to-speech) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, usare le istruzioni seguenti:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi di questo articolo, includere le istruzioni import e `using` seguenti all'inizio dello script.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

Esistono diversi modi per inizializzare [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig):

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

In questo esempio viene creato un oggetto [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) usando una chiave e un'area di sottoscrizione. Per ottenere queste credenziali, seguire la procedura descritta in [Provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free). Si crea anche un codice boilerplate di base da usare per la parte restante di questo articolo, che viene modificato per diverse personalizzazioni.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}

void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizzare la voce in un file

Successivamente, creare un oggetto [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer), che esegue le conversioni di sintesi vocale e invia l'output ad altoparlanti, file o altri flussi. [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) accetta come parametri l'oggetto [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) creato nel passaggio precedente e un oggetto [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) che specifica come devono essere gestiti i risultati dell'output.

Per iniziare, creare un oggetto `AudioConfig` per scrivere automaticamente l'output in un file `.wav`, usando la funzione `FromWavFileOutput()`.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Successivamente, creare un'istanza di `SpeechSynthesizer` passando l'oggetto `config` e l'oggetto `audioConfig` come parametri. Quindi, per eseguire la sintesi vocale e scrivere il risultato in un file, basta eseguire `SpeakTextAsync()` con una stringa di testo.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Eseguire il programma. Un file `.wav` sintetizzato viene scritto nella posizione specificata. Si tratta di un esempio valido dell'utilizzo più semplice, ma in seguito viene illustrato come personalizzare l'output e gestire la relativa risposta in un flusso in memoria per scenari personalizzati.

## <a name="synthesize-to-speaker-output"></a>Sintetizzare l'output dell'altoparlante

In alcuni casi, si può scegliere di indirizzare la voce sintetizzata di output direttamente a un altoparlante. A tale scopo, omettere il `AudioConfig` parametro durante `SpeechSynthesizer` la creazione di nell'esempio precedente. Questo sintetizza il dispositivo di output attivo corrente.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Ottenere il risultato come flusso in memoria

Per molti scenari di sviluppo di applicazioni vocali, i dati audio risultanti potrebbero essere necessari come flusso in memoria invece di essere scritti direttamente in un file. In questo modo è possibile creare un comportamento personalizzato per:

* Astrarre la matrice di byte risultante come flusso ricercabile per i servizi downstream personalizzati.
* Integrare il risultato con altri servizi o API.
* Modificare i dati audio, scrivere intestazioni di `.wav` personalizzate e così via.

È semplice apportare questa modifica dall'esempio precedente. Rimuovere prima di tutto `AudioConfig`, perché il comportamento di output verrà gestito manualmente da questo punto in poi per un maggior controllo. Passare quindi `NULL` per `AudioConfig` nel costruttore `SpeechSynthesizer`.

> [!NOTE]
> Se si passa `NULL` per `AudioConfig`, invece di ometterlo come nel precedente esempio di output nell'altoparlante, l'audio non verrà riprodotto per impostazione predefinita nel dispositivo di output attivo corrente.

Questa volta il risultato viene salvato in una variabile [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult). Il getter `GetAudioData` restituisce un oggetto `byte []` dei dati di output. Questo oggetto `byte []` può essere gestito manualmente oppure è possibile usare la classe [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) per gestire il flusso in memoria. In questo esempio si usa la funzione statica `AudioDataStream.FromResult()` per ottenere un flusso dal risultato.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

Da qui è possibile implementare qualsiasi comportamento personalizzato usando l'oggetto `stream` risultante.

## <a name="customize-audio-format"></a>Personalizzare il formato audio

La sezione seguente illustra come personalizzare gli attributi dell'output audio, tra cui:

* Tipo di file audio
* Frequenza di campionamento
* Profondità di bit

Per cambiare il formato audio, usare la funzione `SetSpeechSynthesisOutputFormat()` nell'oggetto `SpeechConfig`. Questa funzione prevede un oggetto `enum` di tipo [`SpeechSynthesisOutputFormat`](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), che è possibile usare per selezionare il formato di output. Per un [elenco di formati audio](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) disponibili, vedere la documentazione di riferimento.

Sono disponibili varie opzioni per tipi di file diversi a seconda dei requisiti. Si noti che, per definizione, i formati non elaborati come `Raw24Khz16BitMonoPcm` non includono intestazioni audio. Usare formati non elaborati solo quando si è certi che l'implementazione downstream possa decodificare un flusso di bit non elaborato o se si prevede di creare manualmente le intestazioni in base alla profondità di bit, alla frequenza di campionamento, al numero di canali e così via.

In questo esempio si specifica un formato RIFF ad alta fedeltà `Riff24Khz16BitMonoPcm` impostando `SpeechSynthesisOutputFormat` nell'oggetto `SpeechConfig`. Analogamente all'esempio della sezione precedente, è possibile usare [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) per ottenere un flusso in memoria del risultato e quindi scriverlo in un file.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

Eseguendo il programma, anche in questo caso verrà scritto un file `.wav` nel percorso specificato.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Usare SSML per personalizzare le caratteristiche vocali

Il linguaggio SSML (Speech Synthesis Markup Language) consente di ottimizzare il tono, la pronuncia, la velocità del parlato, il volume e altro ancora dell'output della sintesi vocale inviando le richieste da XML Schema. In questa sezione viene illustrato un esempio di modifica della voce, ma per una guida più dettagliata, vedere l' [articolo sulle procedure relative a SSML](../../../speech-synthesis-markup.md).

Per iniziare a usare SSML per la personalizzazione, è necessario apportare una semplice modifica che cambia la voce.
Creare prima di tutto un nuovo file XML per la configurazione di SSML nella directory radice del progetto, in questo esempio `ssml.xml`. L'elemento radice è sempre `<speak>` e il wrapping del testo in un elemento `<voice>` consente di cambiare la voce usando il parametro `name`. Vedere l' [elenco completo](../../../language-support.md#neural-voices) delle voci **neurali** supportate.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Successivamente, è necessario cambiare la richiesta di sintesi vocale in modo che faccia riferimento al file XML. La richiesta è essenzialmente la stessa, ma invece di usare la funzione `SpeakTextAsync()` si usa `SpeakSsmlAsync()`. Questa funzione prevede una stringa XML, quindi è prima di tutto necessario caricare la configurazione di SSML come stringa. Da qui, l'oggetto risultato è esattamente lo stesso degli esempi precedenti.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

> [!NOTE]
> Per modificare la voce senza usare SSML, è possibile impostare la proprietà `SpeechConfig` su usando `SpeechConfig.SetSpeechSynthesisVoiceName("en-US-AriaNeural")`

## <a name="get-facial-pose-events"></a>Ottenere gli eventi di posa facciali

Il riconoscimento vocale può essere un modo efficace per guidare l'animazione di espressioni facciali.
Spesso [visemes](../../../how-to-speech-synthesis-viseme.md) vengono usati per rappresentare la chiave che si pone in un discorso osservato, ad esempio la posizione dei labbri, la mascella e la lingua durante la produzione di un fonema particolare.
È possibile sottoscrivere l'evento viseme in Speech SDK.
Quindi, è possibile applicare gli eventi viseme per animare la faccia di un carattere come riproduzione audio vocale.
Informazioni [su come ottenere gli eventi viseme](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
