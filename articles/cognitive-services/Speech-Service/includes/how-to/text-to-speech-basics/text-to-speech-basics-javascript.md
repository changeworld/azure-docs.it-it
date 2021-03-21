---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: bfaa9f649fd4d44e45b6d4a513e74e12e169c4f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719973"
---
Questo argomento di avvio rapido illustra i modelli di progettazione comuni per eseguire la sintesi vocale con Speech SDK. Si inizia con la configurazione di base e la sintesi e si passa ad esempi più avanzati per lo sviluppo di applicazioni personalizzate, tra cui:

* Recupero di risposte come flussi in memoria
* Personalizzazione della frequenza di campionamento e della velocità in bit dell'output
* Invio di richieste di sintesi tramite SSML (Speech Synthesis Markup Language)
* Uso di voci neurali

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si disponga di un account Azure e di una risorsa del servizio di riconoscimento vocale. Se non si dispone di un account e di una risorsa, [provare a usare il servizio riconoscimento vocale gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK per JavaScript</a>. A seconda della piattaforma, usare le istruzioni seguenti:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web browser </a>

A seconda dell'ambiente di destinazione, inoltre, usare una delle alternative seguenti:

# <a name="script"></a>[script](#tab/script)

Scaricare ed estrarre il file <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK di riconoscimento vocale per JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e inserirlo in una cartella accessibile al file HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se la destinazione è un Web browser e si usa il tag `<script>`, il prefisso `sdk` non è necessario. Il prefisso `sdk` è un alias usato per la denominazione del modulo di `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Per altre informazioni su `import`, vedere <a href="https://javascript.info/import-export" target="_blank">Export e import</a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Per altre informazioni su `require`, vedere <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Informazioni su require</a>.

---


## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig). Questa classe include informazioni sulla risorsa, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

Esistono diversi modi per inizializzare [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig):

* Con una risorsa: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

In questo esempio si crea un oggetto [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) usando una chiave e un'area di risorsa. Per ottenere queste credenziali, seguire la procedura descritta in [Provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free). Si crea anche un codice boilerplate di base da usare per la parte restante di questo articolo, che viene modificato per diverse personalizzazioni.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizzare la voce in un file

Successivamente, creare un oggetto [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer), che esegue le conversioni di sintesi vocale e invia l'output ad altoparlanti, file o altri flussi. [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) accetta come parametri l'oggetto [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) creato nel passaggio precedente e un oggetto [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) che specifica come devono essere gestiti i risultati dell'output.

Per iniziare, creare un oggetto `AudioConfig` per scrivere automaticamente l'output in un file `.wav`, usando la funzione statica `fromAudioFileOutput()`.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Successivamente, creare un'istanza di `SpeechSynthesizer` passando l'oggetto `speechConfig` e l'oggetto `audioConfig` come parametri. Quindi, per eseguire la sintesi vocale e scrivere il risultato in un file, basta eseguire `speakTextAsync()` con una stringa di testo. Il callback del risultato è una valida posizione in cui eseguire la chiamata di `synthesizer.close()`, necessaria per il funzionamento corretto della sintesi.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Eseguire il programma. Un file `.wav` sintetizzato viene scritto nella posizione specificata. Si tratta di un esempio valido dell'utilizzo più semplice, ma in seguito viene illustrato come personalizzare l'output e gestire la relativa risposta in un flusso in memoria per scenari personalizzati.

## <a name="synthesize-to-speaker-output"></a>Sintetizzare l'output dell'altoparlante

In alcuni casi, si può scegliere di indirizzare la voce sintetizzata di output direttamente a un altoparlante. A questo scopo, creare un'istanza di `AudioConfig` usando la funzione statica `fromDefaultSpeakerOutput()`. L'output viene così indirizzato al dispositivo di output attivo corrente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Ottenere il risultato come flusso in memoria

Per molti scenari di sviluppo di applicazioni vocali, i dati audio risultanti potrebbero essere necessari come flusso in memoria invece di essere scritti direttamente in un file. In questo modo è possibile creare un comportamento personalizzato per:

* Astrarre la matrice di byte risultante come flusso ricercabile per i servizi downstream personalizzati.
* Integrare il risultato con altri servizi o API.
* Modificare i dati audio, scrivere intestazioni di `.wav` personalizzate e così via.

È semplice apportare questa modifica dall'esempio precedente. Rimuovere prima di tutto il blocco `AudioConfig`, perché il comportamento di output verrà gestito manualmente da questo punto in poi per un maggior controllo. Passare quindi `undefined` per `AudioConfig` nel costruttore `SpeechSynthesizer`.

> [!NOTE]
> Se si passa `undefined` per `AudioConfig`, invece di ometterlo come nel precedente esempio di output nell'altoparlante, l'audio non verrà riprodotto per impostazione predefinita nel dispositivo di output attivo corrente.

Questa volta il risultato viene salvato in una variabile [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult). La `SpeechSynthesisResult.audioData` proprietà restituisce un oggetto `ArrayBuffer` dei dati di output, il tipo di flusso del browser predefinito. Per il codice server, convertire il arrayBuffer in un flusso del buffer.

Il codice seguente funziona per il codice lato client.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Da qui è possibile implementare qualsiasi comportamento personalizzato usando l'oggetto `ArrayBuffer` risultante. ArrayBuffer è un tipo comune da ricevere in un browser e riprodurre da questo formato.

Per qualsiasi codice basato su server, se è necessario usare i dati come flusso, invece di un ArrayBuffer, è necessario convertire l'oggetto in un flusso.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>Personalizzare il formato audio

La sezione seguente illustra come personalizzare gli attributi dell'output audio, tra cui:

* Tipo di file audio
* Frequenza di campionamento
* Profondità di bit

Per cambiare il formato audio, usare la proprietà `speechSynthesisOutputFormat` nell'oggetto `SpeechConfig`. Questa proprietà prevede un oggetto `enum` di tipo [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat), che viene usato per selezionare il formato di output. Per un [elenco di formati audio](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) disponibili, vedere la documentazione di riferimento.

Sono disponibili varie opzioni per tipi di file diversi a seconda dei requisiti. Si noti che, per definizione, i formati non elaborati come `Raw24Khz16BitMonoPcm` non includono intestazioni audio. Usare formati non elaborati solo quando si è certi che l'implementazione downstream possa decodificare un flusso di bit non elaborato o se si prevede di creare manualmente le intestazioni in base alla profondità di bit, alla frequenza di campionamento, al numero di canali e così via.

> [!NOTE]
> Le voci **en-US-AriaRUS** e **en-US-GuyRUS** vengono create da campioni codificati nella frequenza di campionamento `Riff24Khz16BitMonoPcm`.

In questo esempio si specifica un formato RIFF ad alta fedeltà `Riff24Khz16BitMonoPcm` impostando `speechSynthesisOutputFormat` nell'oggetto `SpeechConfig`. Analogamente all'esempio riportato nella sezione precedente, ottenere i dati audio di `ArrayBuffer` e interagirvi.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Eseguendo il programma, anche in questo caso verrà scritto un file `.wav` nel percorso specificato.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Usare SSML per personalizzare le caratteristiche vocali

Il linguaggio SSML (Speech Synthesis Markup Language) consente di ottimizzare il tono, la pronuncia, la velocità del parlato, il volume e altro ancora dell'output della sintesi vocale inviando le richieste da XML Schema. Questa sezione illustra alcuni esempi pratici, ma per una guida dettagliata, vedere l'[articolo di procedure su SSML](../../../speech-synthesis-markup.md).

Per iniziare a usare SSML per la personalizzazione, è necessario apportare una semplice modifica che cambia la voce.
Creare prima di tutto un nuovo file XML per la configurazione di SSML nella directory radice del progetto, in questo esempio `ssml.xml`. L'elemento radice è sempre `<speak>` e il wrapping del testo in un elemento `<voice>` consente di cambiare la voce usando il parametro `name`. Questo esempio imposta una voce maschile in inglese (Regno Unito). Si noti che questa voce è **standard** e prevede prezzi e disponibilità differenti rispetto alle voci **neurali**. Vedere l'[elenco completo](../../../language-support.md#standard-voices) di voci **standard** supportate.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Successivamente, è necessario cambiare la richiesta di sintesi vocale in modo che faccia riferimento al file XML. La richiesta è essenzialmente la stessa, ma invece di usare la funzione `speakTextAsync()` si usa `speakSsmlAsync()`. Questa funzione prevede una stringa XML, quindi creare prima di tutto una funzione per caricare un file XML e restituirlo come stringa.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Per altre informazioni su `readFileSync`, vedere <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">File system Node.js</a>. Da qui, l'oggetto risultato è esattamente lo stesso degli esempi precedenti.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

L'output funziona, ma è possibile apportare alcune altre semplici modifiche per rendere il suono più naturale. La velocità del parlato generale è leggermente troppo veloce, quindi aggiungere un tag `<prosody>` per ridurla al **90%** di quella predefinita. Inoltre, la pausa dopo la virgola nella frase è un po' troppo breve e risulta poco naturale. Per risolvere questo problema, aggiungere un tag `<break>` per ritardare la voce e impostare il parametro time su **200ms**. Eseguire di nuovo la sintesi per verificare l'effetto di queste personalizzazioni sull'output.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Voci neurali

Le voci neurali sono algoritmi di sintesi vocale basati su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per passare a una voce neurale, impostare `name` su una delle [opzioni di voce neurale](../../../language-support.md#neural-voices). Aggiungere quindi uno spazio dei nomi XML per `mstts` ed eseguire il wrapping del testo nel tag `<mstts:express-as>`. Usare il parametro `style` per personalizzare lo stile di pronuncia. Questo esempio usa `cheerful`, ma provare a impostarlo su `customerservice` o su `chat` per vedere la differenza nello stile di pronuncia.

> [!IMPORTANT]
> Le voci neurali sono supportate **solo** per le risorse Voce create nelle aree *Stati Uniti orientali*, *Asia sud-orientale* ed *Europa occidentale*.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```

## <a name="get-facial-pose-events"></a>Ottenere gli eventi di posa facciali

Il riconoscimento vocale può essere un modo efficace per guidare l'animazione di espressioni facciali.
Spesso [visemes](../../../how-to-speech-synthesis-viseme.md) vengono usati per rappresentare la chiave che si pone in un discorso osservato, ad esempio la posizione dei labbri, la mascella e la lingua durante la produzione di un fonema particolare.
È possibile sottoscrivere l'evento viseme in Speech SDK.
Quindi, è possibile applicare gli eventi viseme per animare la faccia di un carattere come riproduzione audio vocale.
Informazioni [su come ottenere gli eventi viseme](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
