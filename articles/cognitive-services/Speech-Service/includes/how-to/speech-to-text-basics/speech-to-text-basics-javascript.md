---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: cc5e306aa9677c7370d03dbb26ef3fe69293a630
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180055"
---
Una delle principali funzionalità del servizio Voce è la possibilità di riconoscere e trascrivere la voce umana, ovvero di convertire la voce in testo scritto. Questa guida di avvio rapido illustra come usare Speech SDK in app e prodotti per eseguire la conversione della voce in testo scritto di alta qualità.

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) in GitHub.

In alternativa, vedere l' [esempio React](https://github.com/Azure-Samples/AzureSpeechReactSample) per informazioni su come usare l'SDK di riconoscimento vocale in un ambiente basato su browser.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare l'SDK per la sintesi vocale per Node.js. Se si vuole semplicemente installare il nome del pacchetto, eseguire `npm install microsoft-cognitiveservices-speech-sdk` . Per istruzioni sull'installazione guidata, vedere l'articolo [introduttivo](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet%2Clinux%2Cjre%2Cnodejs&pivots=programming-language-javascript) .

Usare l' `require` istruzione seguente per importare l'SDK.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Per ulteriori informazioni su `require` , vedere la [documentazione di richiesta](https://nodejs.org/en/knowledge/getting-started/what-is-require/).

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione. Creare una classe [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) usando la chiave e l'area. Per trovare la coppia chiave-area, vedere [Trovare chiavi e area](../../../overview.md#find-keys-and-region).

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Esistono alcuni altri modi per inizializzare una classe [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig):

* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

## <a name="recognize-from-microphone-browser-only"></a>Riconoscimento da microfono (solo browser)

Il riconoscimento vocale da un microfono **non è supportato in Node.js** ed è supportato solo in un ambiente JavaScript basato su browser. Vedere l' [esempio React](https://github.com/Azure-Samples/AzureSpeechReactSample) su GitHub per visualizzare il [riconoscimento vocale dall'implementazione del microfono](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29).

> [!NOTE]
> Se si vuole usare un dispositivo di input audio *specifico*, è necessario specificare l'ID del dispositivo nella classe `AudioConfig`. Informazioni su [come ottenere l'ID del dispositivo](../../../how-to-select-audio-input-devices.md) di input audio.

## <a name="recognize-from-file"></a>Riconoscimento da file 

Per eseguire il riconoscimento vocale da un file audio in Node.js, è necessario usare uno schema progettuale alternativo che usa un flusso di push, perché l'oggetto `File` JavaScript non può essere usato in un runtime di Node.js. Il codice seguente:

* Crea un flusso push usando `createPushStream()`
* Apre il file `.wav` creando un flusso di lettura e lo scrive nel flusso push
* Crea una configurazione audio usando il flusso push

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

L'uso di un flusso push come input presuppone che i dati audio siano un PCM non elaborato, ad esempio ignorano le intestazioni.
L'API continuerà a funzionare in determinati casi se l'intestazione non è stata ignorata, ma per ottenere risultati ottimali è consigliabile implementare la logica per leggere le intestazioni, in modo che il `fs` cominci all'*inizio dei dati audio*.

## <a name="error-handling"></a>Gestione degli errori

Gli esempi precedenti ottengono semplicemente il testo riconosciuto da `result.text`, ma per gestire gli errori e altre risposte è necessario scrivere del codice per elaborare il risultato. Il seguente codice valuta la proprietà [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) e:

* Stampa il risultato del riconoscimento: `ResultReason.RecognizedSpeech`
* Se non esiste una corrispondenza di riconoscimento, informa l'utente: `ResultReason.NoMatch`
* Se viene rilevato un errore, stampa il messaggio di errore: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Riconoscimento continuo

Gli esempi precedenti usano il riconoscimento singolo, che riconosce una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio.

Al contrario, si usa il riconoscimento continuo quando si vuole **controllare** il momento in cui interrompere il riconoscimento. Per ottenere i risultati del riconoscimento, è necessario sottoscrivere gli eventi `Recognizing`, `Recognized` e `Canceled`. Per arrestare il riconoscimento, è necessario chiamare [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync). Ecco un esempio di riconoscimento continuo eseguito su un file di input audio.

Per iniziare, definire l'input e inizializzare un [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer):

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Infine, sottoscrivere gli eventi inviati dal [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer).

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): segnale per gli eventi contenenti i risultati del riconoscimento intermedio.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): segnale per gli eventi contenenti i risultati del riconoscimento finale, che indicano un tentativo di riconoscimento riuscito.
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): segnale per gli eventi che indicano la fine di una sessione di riconoscimento (operazione).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): segnale per gli eventi contenenti risultati di riconoscimento annullati (a indicare un tentativo di riconoscimento annullato in seguito a una richiesta di annullamento diretta o, in alternativa, a un errore di trasporto o di protocollo).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Al termine della configurazione, chiamare [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) per avviare il riconoscimento.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modalità dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la corrispondente funzione. In questa modalità l'istanza di SpeechConfig interpreta le descrizioni testuali delle strutture di frasi, ad esempio la punteggiatura. Ad esempio, l'espressione "Vivi in città punto interrogativo" verrebbe interpretata come "Vivi in città?".

Per abilitare la modalità di dettatura, usare il metodo [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) in [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Un'attività comune per il riconoscimento vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in italiano. Nel codice trovare [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig), quindi aggiungere questa riga al di sotto.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

La proprietà [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) si aspetta una stringa in formato è prevista una stringa di formato lingua-impostazioni locali. È possibile specificare qualsiasi valore nella colonna **Locale** nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Migliorare l'accuratezza del riconoscimento

Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località. Se si fornisce un elenco di frasi, è possibile migliorare l'accuratezza del riconoscimento vocale.

Se ad esempio è presente un comando "Sposta in" e una delle possibili destinazioni pronunciabili è "Vaso", è possibile aggiungere la voce "Sposta in vaso". L'aggiunta di una frase aumenta la probabilità che, durante il riconoscimento dell'audio, venga riconosciuta la frase "Sposta in vaso" invece di "Sposta invaso"

All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Una voce di un elenco di frasi viene usata per migliorare il riconoscimento di parole e frasi dell'elenco, anche quando le voci capitano nel mezzo di un'espressione. 

> [!IMPORTANT]
> La funzionalità dell'elenco di frasi è disponibile nelle lingue seguenti: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Per usare un elenco di frasi, creare prima di tutto un oggetto [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar), quindi aggiungere parole o frasi specifiche con [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-).

Tutte le modifiche apportate a [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio Voce.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se è necessario cancellare l'elenco di frasi:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Altre opzioni per migliorare l'accuratezza del riconoscimento

Gli elenchi di frasi sono solo una delle opzioni disponibili per migliorare l'accuratezza del riconoscimento. È anche possibile: 

* [Migliorare l'accuratezza con Riconoscimento vocale personalizzato](../../../custom-speech-overview.md)
* [Migliorare l'accuratezza con i modelli di tenant](../../../tutorial-tenant-model.md)
