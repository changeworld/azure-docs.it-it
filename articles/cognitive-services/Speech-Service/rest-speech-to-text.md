---
title: Informazioni di riferimento sulle API di sintesi vocale (REST)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API REST per sintesi vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 1577d63822bdb21eff7fcbb3e1343243a3004409
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033643"
---
# <a name="speech-to-text-rest-api"></a>API REST di riconoscimento vocale

Il riconoscimento vocale ha due diverse API REST. Ogni API svolge lo scopo speciale e usa diversi set di endpoint.

Le API REST per sintesi vocale sono:
- L' [API REST di riconoscimento vocale v 3.0](#speech-to-text-rest-api-v30) viene usata per la [trascrizione](batch-transcription.md) e la [riconoscimento vocale personalizzato](custom-speech-overview.md)di batch. v 3.0 è un [successore della versione 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- L' [API REST di sintesi vocale per l'audio breve](#speech-to-text-rest-api-for-short-audio) viene usata per la trascrizione online come alternativa a [Speech SDK](speech-sdk.md). Le richieste che usano questa API possono trasmettere solo fino a 60 secondi di audio per richiesta. 

## <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

L'API REST di riconoscimento vocale v 3.0 viene usata per la [trascrizione](batch-transcription.md) e la [riconoscimento vocale personalizzato](custom-speech-overview.md)di batch. Se è necessario comunicare con la trascrizione OnLine tramite REST, usare l' [API REST per il riconoscimento vocale per l'audio breve](#speech-to-text-rest-api-for-short-audio).

Usare l'API REST v 3.0 per:
- Copiare i modelli in altre sottoscrizioni nel caso in cui si desideri che i colleghi abbiano accesso a un modello compilato o nei casi in cui si desidera distribuire un modello in più di un'area
- Trascrivere i dati da un contenitore (trascrizione bulk) e fornire più URL di file audio
- Caricare dati da account di archiviazione di Azure tramite l'uso di un URI di firma di accesso condiviso
- Ottenere i log per ogni endpoint se sono stati richiesti log per l'endpoint
- Richiedere il manifesto dei modelli creati, allo scopo di configurare i contenitori locali

L'API REST v 3.0 include le funzionalità seguenti:
- **Notifiche-webhook**: tutti i processi in esecuzione del servizio supportano ora le notifiche webhook. L'API REST v 3.0 fornisce le chiamate per consentire la registrazione dei webhook in cui vengono inviate le notifiche
- **Aggiornamento di modelli dietro gli endpoint** 
- **Adattamento dei modelli con più set di dati**: adattare un modello usando più combinazioni di set di dati di dati acustici, di lingua e di pronuncia
- **Bring your own storage**: usare gli account di archiviazione per log, file di trascrizione e altri dati

Vedere gli esempi sull'uso dell'API REST v 3.0 con la trascrizione batch è [questo articolo](batch-transcription.md).

Se si usa l'API REST di sintesi vocale v 2.0, vedere come è possibile eseguire la migrazione a v 3.0 in [questa guida](/azure/cognitive-services/speech-service/migrate-v2-to-v3).

Vedere le informazioni di riferimento complete sull'API REST di sintesi vocale v 3.0 [qui](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

## <a name="speech-to-text-rest-api-for-short-audio"></a>API REST per sintesi vocale per audio breve

In alternativa all' [SDK vocale](speech-sdk.md), il servizio riconoscimento vocale consente di convertire il riconoscimento vocale usando un'API REST. Ogni endpoint accessibile è associato a un'area. L'applicazione richiede una chiave di sottoscrizione per l'endpoint che si intende usare. L'API REST per l'audio breve è molto limitata ed è consigliabile usarla solo nei casi in cui non è possibile usare l' [SDK di riconoscimento vocale](speech-sdk.md) .

Prima di usare l'API REST di sintesi vocale per l'audio breve, tenere presente quanto segue:

* Le richieste che usano l'API REST per l'audio breve e la trasmissione diretta di audio possono contenere solo fino a 60 secondi di audio.
* L'API REST per sintesi vocale per l'audio breve restituisce solo i risultati finali. I risultati parziali non sono disponibili.

Se l'invio di audio più lungo è un requisito per l'applicazione, prendere in considerazione l'uso dell' [SDK di riconoscimento vocale](speech-sdk.md) o dell' [API REST di sintesi vocale v 3.0](#speech-to-text-rest-api-v30).

> [!TIP]
> Vedere [questo articolo](sovereign-clouds.md) per gli endpoint di Azure per enti pubblici e Azure Cina.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Aree ed endpoint

Il formato dell'endpoint per l'API REST per l'audio breve è il seguente:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Sostituire `<REGION_IDENTIFIER>` con l'identificatore corrispondente all'area della sottoscrizione da questa tabella:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Il parametro di lingua deve essere aggiunto all'URL per evitare di ricevere un errore 4xx HTTP. Ad esempio, la lingua impostata su inglese (Stati Uniti) usando l'endpoint per l'area Stati Uniti occidentali è: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="query-parameters"></a>Parametri di query

Questi parametri possono essere inclusi nella stringa di query della richiesta REST.

| Parametro | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `language` | Identifica la lingua parlata che viene riconosciuta. Vedere [Lingue supportate](language-support.md#speech-to-text). | Obbligatorio |
| `format` | Specifica il formato del risultato. I valori accettati sono `simple` e `detailed`. I risultati semplici includono `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. Le risposte dettagliate includono quattro rappresentazioni diverse del testo visualizzato. L'impostazione predefinita è `simple`. | Facoltativo |
| `profanity` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `masked` , che sostituisce la volgarità con `removed` gli asterischi,, che rimuove tutte le parolacce dal risultato, o `raw` , che include la volgarità nel risultato. L'impostazione predefinita è `masked`. | Facoltativo |
| `cid` | Quando si usa il [portale di riconoscimento vocale personalizzato](./custom-speech-overview.md) per creare modelli personalizzati, è possibile usare modelli personalizzati tramite l' **ID endpoint** trovato nella pagina **distribuzione** . Usare l' **ID endpoint** come argomento per il `cid` parametro della stringa di query. | Facoltativo |

### <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

|Intestazione| Descrizione | Obbligatoria / Facoltativa |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | La chiave di sottoscrizione al Servizio di riconoscimento vocale dell'utente. | È necessaria questa intestazione o `Authorization`. |
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | È necessaria questa intestazione o `Ocp-Apim-Subscription-Key`. |
| `Pronunciation-Assessment` | Specifica i parametri per visualizzare i punteggi di pronuncia nei risultati del riconoscimento, che valutano la qualità di pronuncia dell'input vocale, con indicatori di accuratezza, fluidità, completezza e così via. Questo parametro è un JSON con codifica Base64 contenente più parametri dettagliati. Per informazioni su come compilare questa intestazione, vedere [parametri di valutazione della pronuncia](#pronunciation-assessment-parameters) . | Facoltativo |
| `Content-type` | Descrive il formato e il codec dei dati audio forniti. I valori accettati sono `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obbligatorio |
| `Transfer-Encoding` | Specifica che vengono inviati i dati audio in blocchi, anziché un singolo file. Utilizzare questa intestazione solo se vi è stata la suddivisione in blocchi dei dati audio. | Facoltativo |
| `Expect` | Se si usa il trasferimento in blocchi, inviare `Expect: 100-continue`. Il Servizio di riconoscimento vocale legge la richiesta iniziale e attende ulteriori dati.| Obbligatorio in caso di invio di dati audio in blocchi. |
| `Accept` | Se specificato, deve essere `application/json`. Il servizio di riconoscimento vocale fornisce i risultati in formato JSON. Alcuni framework di richiesta forniscono un valore predefinito incompatibile. È consigliabile includere sempre `Accept` . | Facoltativo, ma consigliato. |

### <a name="audio-formats"></a>Formati audio

L'audio viene inviato nel corpo della richiesta HTTP `POST`. Deve essere in uno dei formati elencati in questa tabella:

| Formato | Codec | Velocità in bit | Frequenza di campionamento  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, mono |
| OGG    | OPUS  | 256 kpbs | 16 kHz, mono |

>[!NOTE]
>I formati precedenti sono supportati tramite l'API REST per brevi audio e WebSocket nel servizio di riconoscimento vocale. [Speech SDK](speech-sdk.md) supporta attualmente il formato WAV con codec PCM e [altri formati](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parametri di valutazione della pronuncia

Questa tabella elenca i parametri obbligatori e facoltativi per la valutazione della pronuncia.

| Parametro | Descrizione | Necessaria? |
|-----------|-------------|---------------------|
| ReferenceText | Testo su cui verrà valutata la pronuncia. | Obbligatorio |
| GradingSystem | Sistema di punti per la taratura dei punteggi. Il `FivePoint` sistema restituisce un punteggio a virgola mobile 0-5 e `HundredMark` restituisce un punteggio a virgola mobile 0-100. Impostazione predefinita: `FivePoint`. | Facoltativo |
| Granularità | Granularità della valutazione. I valori accettati sono `Phoneme` , che mostra il punteggio sul livello full-text, Word e fonema, `Word` , che mostra il punteggio a livello di testo intero e di parola, `FullText` , che mostra il punteggio solo sul livello full-text. L'impostazione predefinita è `Phoneme`. | Facoltativo |
| Dimension | Definisce i criteri di output. I valori accettati sono `Basic` , che mostra solo il Punteggio di accuratezza, `Comprehensive` Mostra i punteggi su più dimensioni (ad esempio, il Punteggio di fluidità e il Punteggio di completezza sul livello full-text, il tipo di errore a livello di parola). Controllare i [parametri di risposta](#response-parameters) per visualizzare le definizioni delle diverse dimensioni dei punteggi e dei tipi di errore di Word. L'impostazione predefinita è `Basic`. | Facoltativo |
| EnableMiscue | Abilita il calcolo miscue. Con questa funzionalità abilitata, le parole pronunciate verranno confrontate con il testo di riferimento e verranno contrassegnate con omissione/inserimento in base al confronto. I valori accettati sono `False` e `True`. L'impostazione predefinita è `False`. | Facoltativo |
| ScenarioId | GUID che indica un sistema di punti personalizzato. | Facoltativo |

Di seguito è riportato un esempio di codice JSON contenente i parametri di valutazione della pronuncia:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Il codice di esempio seguente mostra come compilare i parametri di valutazione della pronuncia nell' `Pronunciation-Assessment` intestazione:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

È consigliabile eseguire il caricamento in streaming (chunked) durante l'invio dei dati audio, che può ridurre significativamente la latenza. Vedere il [codice di esempio in diversi linguaggi di programmazione](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) per abilitare il flusso.

>[!NOTE]
>La funzionalità di valutazione della pronuncia è attualmente disponibile solo nelle `westus` `eastasia` aree, e `centralindia` . Questa funzionalità è attualmente disponibile solo in `en-US` lingua.

### <a name="sample-request"></a>Richiesta di esempio

L'esempio seguente include il nome host e le intestazioni richieste. È importante notare che il servizio prevede anche i dati audio, che non sono inclusi in questo esempio. Come accennato in precedenza, la suddivisione in blocchi è consigliabile, tuttavia, non è necessaria.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Per abilitare la valutazione della pronuncia, è possibile aggiungere sotto l'intestazione. Per informazioni su come compilare questa intestazione, vedere [parametri di valutazione della pronuncia](#pronunciation-assessment-parameters) .

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Codice di stato HTTP | Descrizione | Possibile motivo |
|------------------|-------------|-----------------|
| `100` | Continua | La richiesta iniziale è stata accettata. Procedere con l'invio del resto dei dati. (Usato con trasferimento in blocchi) |
| `200` | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un oggetto JSON. |
| `400` | Richiesta non valida | Il codice della lingua non è disponibile, non è un linguaggio supportato, un file audio non valido e così via. |
| `401` | Non autorizzata | La chiave di sottoscrizione o il token di autorizzazione non è valido nell'area specificata o l'endpoint non è valido. |
| `403` | Accesso negato | Manca la chiave di sottoscrizione o il token di autorizzazione. |

### <a name="chunked-transfer"></a>Trasferimento in blocchi

Il trasferimento in blocchi ( `Transfer-Encoding: chunked` ) consente di ridurre la latenza di riconoscimento. Consente al servizio di riconoscimento vocale di iniziare l'elaborazione del file audio durante la trasmissione. L'API REST per l'audio breve non fornisce risultati parziali o intermedi.

Questo esempio di codice mostra come inviare audio in blocchi. Solo il primo blocco deve contenere l'intestazione del file audio. `request` è un `HttpWebRequest` oggetto connesso all'endpoint REST appropriato. `audioFile` è il percorso di un file audio su disco.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Parametri di risposta

I risultati vengono forniti in formato JSON. Il formato `simple` include i campi di primo livello seguenti.

| Parametro | Descrizione  |
|-----------|--------------|
|`RecognitionStatus`|Lo stato, ad esempio `Success` per il riconoscimento con esito positivo. Vedere la tabella successiva.|
|`DisplayText`|Testo riconosciuto dopo l'utilizzo di maiuscole e minuscole, la punteggiatura, la normalizzazione del testo inverso (conversione del testo vocale in forme più brevi, ad esempio 200 per "200" o "Dr. Smith" per "Doctor Smith") e la maschera volgare. Presente solo con esito positivo.|
|`Offset`|Il tempo (in unità di 100 nanosecondi) in corrispondenza del quale inizia l'input vocale riconosciuto nel flusso audio.|
|`Duration`|La durata (in unità di 100 nanosecondi) dell'input vocale riconosciuto nel flusso audio.|

Il campo `RecognitionStatus` può contenere questi valori:

| Stato | Descrizione |
|--------|-------------|
| `Success` | Il riconoscimento ha avuto esito positivo e il campo `DisplayText` è presente. |
| `NoMatch` | La parte parlata è stata rilevata nel flusso audio, ma non sono state trovate corrispondenze per alcuna parola nella lingua di destinazione. In genere significa che la lingua di riconoscimento è una lingua diversa da quella parlata dall'utente. |
| `InitialSilenceTimeout` | La parte iniziale del flusso audio conteneva solo silenzio e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `BabbleTimeout` | La parte iniziale del flusso audio conteneva solo rumore e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `Error` | Il servizio di riconoscimento ha rilevato un errore interno e non è in grado di continuare. Provare di nuovo, se possibile. |

> [!NOTE]
> Se l'audio è costituito solo da contenuto volgare e il parametro di query `profanity` è impostato su `remove`, il servizio non restituisce un risultato di riconoscimento vocale.

Il `detailed` formato include ulteriori forme di risultati riconosciuti.
Quando si usa il formato `detailed`, `DisplayText` viene fornito come `Display` per ogni risultato nell'elenco `NBest`.

L'oggetto nell' `NBest` elenco può includere:

| Parametro | Descrizione |
|-----------|-------------|
| `Confidence` | Il punteggio di attendibilità della voce da 0.0 (nessuna attendibilità) a 1.0 (attendibilità completa) |
| `Lexical` | Il formato lessicale del testo riconosciuto: le parole effettive riconosciute. |
| `ITN` | Il testo riconosciuto dopo la normalizzazione inversa (forma "canonica"), con numeri di telefono, numeri, abbreviazioni ("Dottor Rossi" in "Dr. Rossi") e altre trasformazioni applicate. |
| `MaskedITN` | La forma ITN con la maschera per le volgarità applicata, se richiesta. |
| `Display` | Il modulo di visualizzazione del testo riconosciuto, con l'aggiunta di segni di punteggiatura e maiuscole. Questo parametro è uguale a `DisplayText` fornito quando il formato è impostato su `simple`. |
| `AccuracyScore` | Accuratezza della pronuncia del riconoscimento vocale. L'accuratezza indica quanto i fonemi corrispondono alla pronuncia di un altoparlante nativo. Il Punteggio di accuratezza di Word e full-text viene aggregato dal punteggio di accuratezza del livello fonema. |
| `FluencyScore` | La fluidità del parlato specificato. La fluidità indica il livello di precisione con cui il riconoscimento vocale corrisponde all'utilizzo di interruzioni automatiche da parte di un altoparlante nativo. |
| `CompletenessScore` | Completezza del riconoscimento vocale, determinato calcolando il rapporto tra parole pronunciate e input di testo di riferimento. |
| `PronScore` | Punteggio complessivo che indica la qualità della pronuncia della voce specificata. Viene aggregato da `AccuracyScore` `FluencyScore` e con il `CompletenessScore` peso. |
| `ErrorType` | Questo valore indica se una parola viene omessa, inserita o pronunciata male rispetto a `ReferenceText` . I valori possibili sono `None` (ovvero nessun errore in questa parola) `Omission` , `Insertion` e `Mispronunciation` . |

### <a name="sample-responses"></a>Risposte di esempio

Risposta tipica per il `simple` riconoscimento:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Risposta tipica per il `detailed` riconoscimento:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

Risposta tipica per il riconoscimento con la valutazione della pronuncia:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/)
- [Personalizzare modelli acustici](./how-to-custom-speech-train-model.md)
- [Personalizzare modelli linguistici](./how-to-custom-speech-train-model.md)
- [Acquisire familiarità con la trascrizione batch](batch-transcription.md)