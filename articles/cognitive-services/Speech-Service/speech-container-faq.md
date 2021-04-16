---
title: Domande frequenti sui contenitori del servizio Voce
titleSuffix: Azure Cognitive Services
description: Installare ed eseguire contenitori voce. La sintesi vocale trascrive i flussi audio al testo in tempo reale che le applicazioni, gli strumenti o i dispositivi possono utilizzare o visualizzare. Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387956"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Domande frequenti sui contenitori del servizio Voce

Quando si usa il servizio Voce con i contenitori, fare affidamento su questa raccolta di domande frequenti prima di eseguire l'escalation al supporto. Questo articolo acquisisce domande di grado variabile, dal generale al tecnico. Per espandere una risposta, fare clic sulla domanda.

## <a name="general-questions"></a>Domande generali

<details>
<summary>
<b>Come funzionano i contenitori Voce e come si configurano?</b>
</summary>

**Risposta:** Quando si configura il cluster di produzione, è necessario prendere in considerazione diversi aspetti. In primo luogo, la configurazione di una singola lingua, più contenitori, nello stesso computer, non deve essere un problema di grandi dimensioni. Se si verificano problemi, potrebbe trattarsi di un problema correlato all'hardware, quindi si dovrebbe prima esaminare la risorsa, cio? Specifiche di CPU e memoria.

Prendere in considerazione per un momento il `ja-JP` contenitore e il modello più recente. Il modello acustico è il componente più impegnativo in base alla CPU, mentre il modello linguistico richiede la maggior quantità di memoria. Quando è stato fatto un benchmark dell'uso, sono necessari circa 0,6 core CPU per elaborare una singola richiesta di riconoscimento vocale quando l'audio scorre in tempo reale (ad esempio dal microfono). Se si sta alimentando l'audio più velocemente del tempo reale (ad esempio da un file), tale utilizzo può raddoppiare (core 1,2x). Nel frattempo, la memoria elencata di seguito è la memoria operativa per la decodifica vocale. Non prende *in* considerazione le dimensioni complete effettive del modello linguistico, che risiederà nella cache dei file. Per `ja-JP` questo si tratta di 2 GB aggiuntivi; per , potrebbe essere più `en-US` (6-7 GB).

Se si dispone di un computer in cui la memoria è insufficiente e si sta tentando di distribuire più lingue, è possibile che la cache dei file sia piena e che il sistema operativo sia obbligato a eseguire il page dei modelli in e out. Per una trascrizione in esecuzione, ciò potrebbe essere un evento catastrofici e potrebbe causare rallentamenti e altre implicazioni sulle prestazioni.

Inoltre, pre-pacchetto eseguibili per i computer con il set di istruzioni [avx2 (Advanced Vector Extension).](speech-container-howto.md#advanced-vector-extension-support) Un computer con il set di istruzioni AVX512 richiederà la generazione del codice per la destinazione e l'avvio di 10 contenitori per 10 lingue potrebbe esaurire temporaneamente la CPU. Nei log docker verrà visualizzato un messaggio simile al seguente:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

È possibile impostare il numero di decodificatori desiderato all'interno di un *singolo contenitore* usando `DECODER MAX_COUNT` la variabile . In pratica, è consigliabile iniziare con lo SKU (CPU/memoria) e si può suggerire come ottenere il meglio. Un ottimo punto di partenza fa riferimento alle specifiche delle risorse del computer host consigliate.

<br>
</details>

<details>
<summary>
<b>Può essere utile per la pianificazione della capacità e la stima dei costi dei contenitori di riconoscimento vocale in memoria?</b>
</summary>

**Risposta:** Per la capacità del contenitore in modalità di elaborazione batch, ogni decodificatore può gestire 2-3 volte in tempo reale, con due core CPU, per un singolo riconoscimento. Non è consigliabile mantenere più di due riconoscimenti simultanei per ogni istanza del contenitore, ma è consigliabile eseguire più istanze di contenitori per motivi di affidabilità/disponibilità, dietro un servizio di bilanciamento del carico.

Anche se è possibile che ogni istanza del contenitore sia in esecuzione con più decodificatori. Ad esempio, potrebbe essere possibile configurare 7 decodificatori per ogni istanza del contenitore in un computer a otto core (a più di 2 volte l'uno), producendo una velocità effettiva di 15 volte. È necessario tenere presente un `DECODER_MAX_COUNT` parametro. Nel caso estremo, si verificano problemi di affidabilità e latenza, con una velocità effettiva aumentata in modo significativo. Per un microfono, sarà 1x in tempo reale. L'utilizzo complessivo deve essere di circa un core per un singolo riconoscimento.

Per lo scenario di elaborazione di 1 K ore al giorno in modalità di elaborazione batch, in un caso estremo, 3 macchine virtuali potrebbero gestirlo entro 24 ore, ma non garantito. Per gestire i giorni di picco, il failover, l'aggiornamento e per fornire backup/BCP minimi, è consigliabile usare 4-5 computer anziché 3 per cluster e con più di 2 cluster.

Per l'hardware si usa una macchina virtuale di Azure standard come riferimento (ogni core deve avere una frequenza di 2,6 GHz o successiva, con il set di istruzioni `DS13_v2` AVX2 abilitato).

| Istanza  | vCPU | RAM    | Archiviazione temporanea | Pagamento in base al go con AHB | Riserva di 1 anno con AHB (% di risparmio) | 3 anni riservati con AHB (% risparmi) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0,598/ora            | $0,3528/ora (~41%)                 | $0,2333/ora (~61%)                  |

In base al riferimento alla progettazione (due cluster di 5 macchine virtuali per gestire 1 K hours/day audio batch processing), il costo hardware di 1 anno sarà:

> 2 (cluster) * 5 (MACCHINE VIRTUALI per cluster) * 0,3528 usd/ora * 365 (giorni) * 24 (ore) = $ 31.000 /anno

Quando si esegue il mapping al computer fisico, una stima generale è 1 vCPU = 1 core CPU fisica. In realtà, 1vCPU è più potente di un singolo core.

Per la versione in tempo preliminare, vengono in gioco tutti questi fattori aggiuntivi:For on-prem, all of these additional factors come into play:

- Informazioni sul tipo di CPU fisica e sul numero di core
- Numero di CPU in esecuzione nello stesso box/computer
- Come vengono impostate le macchine virtuali
- Come viene usato l'hyperthreading/multithreading
- Modalità di condivisione della memoria
- Il sistema operativo e così via.

In genere non è ottimizzato come l'ambiente di Azure. Considerando altri costi generali, una stima sicura è di 10 core CPU fisici = 8 vCPU di Azure. Anche se le CPU più popolari hanno solo otto core. Con la distribuzione locale, il costo sarà superiore rispetto all'uso delle macchine virtuali di Azure. Si consideri anche il tasso di ammortamento.

Il costo del servizio è lo stesso del servizio online: $ 1/ora per la sintesi vocale. Il costo del servizio Voce è:

> $1 * 1000 * 365 = $365K

Il costo di manutenzione pagato a Microsoft dipende dal livello di servizio e dal contenuto del servizio. Varia da $ 29,99 al mese per il livello di base a centinaia di migliaia se il servizio locale è coinvolto. Un numero approssimativo è $ 300/ora per il servizio/manutenzione. Il costo delle persone non è incluso. Non sono inclusi altri costi dell'infrastruttura, ad esempio l'archiviazione, le reti e i servizi di bilanciamento del carico.

<br>
</details>

<details>
<summary>
<b>Perché la punteggiatura non è presente nella trascrizione?</b>
</summary>

**Risposta:** `speech_recognition_language=<YOUR_LANGUAGE>` L'oggetto deve essere configurato in modo esplicito nella richiesta se usa il client Carbon.

Ad esempio:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Ecco l'output:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>È possibile usare un modello acustico personalizzato e un modello linguistico con il contenitore Voce?</b>
</summary>

Attualmente è possibile passare solo un ID modello, un modello linguistico personalizzato o un modello acustico personalizzato.

**Risposta:** È stata presa *la decisione* di non supportare contemporaneamente i modelli acustici e linguistici. Questa operazione rimarrà effettiva fino a quando non viene creato un identificatore unificato per ridurre le interruzioni api. Sfortunatamente, questo non è attualmente supportato.

<br>
</details>

<details>
<summary>
<b>È possibile spiegare questi errori dal contenitore personalizzato di riconoscimento vocale?</b>
</summary>

**Errore 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Risposta 1:** Se si sta eseguire il training con il modello personalizzato più recente, attualmente non è possibile. Se si usa una versione precedente per il training, è possibile usare . Microsoft sta ancora lavorando per supportare le versioni più recenti.

In sostanza, i contenitori personalizzati non supportano i modelli acustici basati su Halide o ONNX(impostazione predefinita nel portale di training personalizzato). Ciò è dovuto al fatto che i modelli personalizzati non vengono crittografati e non si vogliono esporre modelli ONNX. I modelli linguistici sono a posto. Il cliente dovrà selezionare in modo esplicito un modello non ONNX precedente per il training personalizzato. L'accuratezza non sarà interessata. Le dimensioni del modello possono essere maggiori (di 100 MB).

> Modello di supporto > 20190220 (v4.5 Unified)

**Errore 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Risposta 2:** È necessario specificare il nome vocale corretto nella richiesta, che fa distinzione tra maiuscole e minuscole. Fare riferimento al mapping completo del nome del servizio.

**Errore 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Risposta 3:** È stata creata una risorsa Voce, non una risorsa di Servizi cognitivi.


<br>
</details>

<details>
<summary>
<b>Quali protocolli API sono supportati, REST o WS?</b>
</summary>

**Risposta:** Per i contenitori di riconoscimento vocale e di riconoscimento vocale personalizzato, attualmente è supportato solo il protocollo basato su websocket. L'SDK supporta solo la chiamata in WS, ma non REST. È disponibile un piano per aggiungere il supporto REST, ma non ETA per il momento. Fare sempre riferimento alla documentazione ufficiale, vedere [endpoint di stima delle query](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>CentOS è supportato per i contenitori voce?</b>
</summary>

**Risposta:** CentOS 7 non è ancora supportato da Python SDK, anche Ubuntu 19.04 non è supportato.

Il pacchetto Speech SDK per Python è disponibile per i sistemi operativi seguenti:
- **Windows** - x64 e x86
- **Mac** - macOS X versione 10.12 o successiva
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 in x64

Per altre informazioni sulla configurazione dell'ambiente, vedere [Configurazione della piattaforma Python.](quickstarts/setup-platform.md?pivots=programming-language-python) Per il momento, Ubuntu 18.04 è la versione consigliata.

<br>
</details>

<details>
<summary>
<b>Perché si verificano errori durante il tentativo di chiamare gli endpoint di stima LUIS?</b>
</summary>

Si usa il contenitore LUIS in una distribuzione IoT Edge e si tenta di chiamare l'endpoint di stima LUIS da un altro contenitore. Il contenitore LUIS è in ascolto sulla porta 5001 e l'URL in uso è il seguente:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

L'errore che viene visualizzato è:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

La richiesta viene visualizzata nei log del contenitore LUIS e il messaggio indica:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Che cosa significa? Cosa manca? L'esempio per Speech SDK è stato seguito da [qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Lo scenario è che l'audio viene rilevato direttamente dal microfono del PC e si tenta di determinare la finalità, in base all'app LUIS di cui è stato fatto il training. L'esempio a cui è stato collegato esegue esattamente questa attività. E funziona bene con il servizio basato sul cloud LUIS. L'uso di Speech SDK sembra non dover effettuare una chiamata esplicita separata all'API speech-to-text e quindi una seconda chiamata a LUIS.

Quindi, tutto quello che sto provando a fare è passare dallo scenario di uso di LUIS nel cloud all'uso del contenitore LUIS. Non è possibile immaginare se Speech SDK funziona per uno, non funzionerà per l'altro.

**Risposta:** Speech SDK non deve essere usato in un contenitore LUIS. Per l'uso del contenitore LUIS, è necessario usare LUIS SDK o l'API REST LUIS. Speech SDK deve essere usato in un contenitore di riconoscimento vocale.

Un cloud è diverso da un contenitore. Un cloud può essere costituito da più contenitori aggregati (talvolta denominati microservizi). È quindi disponibile un contenitore LUIS e quindi un contenitore Voce- Due contenitori separati. Il contenitore Voce esegue solo il riconoscimento vocale. Il contenitore LUIS esegue solo LUIS. Nel cloud, poiché entrambi i contenitori sono noti per essere distribuiti ed è negativo per un client remoto passare al cloud, eseguire la voce, tornare al cloud e fare LUIS, è disponibile una funzionalità che consente al client di passare a Speech, rimanere nel cloud, passare a LUIS e quindi tornare al client. Anche in questo scenario Speech SDK passa quindi al contenitore cloud Speech con audio e quindi il contenitore cloud Speech parla al contenitore cloud LUIS con testo. Il contenitore LUIS non ha alcun concetto di accettazione dell'audio (non avrebbe senso che il contenitore LUIS accetti l'audio in streaming. LUIS è un servizio basato su testo). Con l'ambiente locale, il cliente non ha alcuna certezza di aver distribuito entrambi i contenitori, non si presuppone di orchestrare i contenitori nei locali dei clienti e, se entrambi i contenitori vengono distribuiti in locale, dato che sono più locali al client, non è un onere passare prima al client, tornare al client e richiedere al cliente di prendere il testo e passare a LUIS.

<br>
</details>

<details>
<summary>
<b>Perché si verificano errori con macOS, il contenitore Voce e Python SDK?</b>
</summary>

Quando si invia un file *wav* da trascrivere, il risultato viene restituito con:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Il websocket è configurato correttamente.

**Risposta:** In questo caso, vedere questo [problema di GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) È possibile risolvere il problema, [proposto qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

Carbon ha risolto questo problema alla versione 1.8.


<br>
</details>

<details>
<summary>
<b>Quali sono le differenze negli endpoint del contenitore Voce?</b>
</summary>

È possibile compilare le metriche di test seguenti, incluse le funzioni da testare e come testare l'SDK e le API REST? In particolare, le differenze tra "interactive" e "conversation", che non sono state notata da documenti/esempi esistenti.

| Endpoint                                                | Test funzionale                                                   | SDK | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Sintetizzare il testo (sintesi vocale)                                  |     | Sì      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Endpoint websocket della dettatura locale di Servizi cognitivi v1        | Sì | No       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Endpoint websocket interattivo v1 locale di Servizi cognitivi  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Endpoint websocket della conversazione locale v1 di Servizi cognitivi |     |          |

**Risposta:** Questa è una fusione di:
- Utenti che provano l'endpoint di dettatura per i contenitori (non so come hanno ottenuto l'URL)
- L'endpoint<sup>di 1 entità</sup> che è quello in un contenitore.
- Endpoint di 1<sup>entità che</sup> restituisce messaggi speech.fragment anziché i messaggi restituiti da 3 endpoint della parte rd per l'endpoint `speech.hypothesis` di dettatura.<sup></sup>
- Le guide introduttive carbon sono tutte usate `RecognizeOnce` (modalità interattiva)
- Carbon ha un'asserzione che per i messaggi che richiedono `speech.fragment` non vengono restituiti in modalità interattiva.
- Carbon con asserzioni che vengono rilasciate nelle build di rilascio (che uccidono il processo).

La soluzione alternativa consiste nel passare all'uso del riconoscimento continuo nel codice oppure connettersi (più rapidamente) agli endpoint interattivi o continui nel contenitore.
Per il codice, impostare l'endpoint su `host:port` /speech/recognition/interactive/cognitiveservices/v1

Per le diverse modalità, vedere Modalità voce: vedere di seguito:

## <a name="speech-modes---interactive-conversation-dictation"></a>Modalità voce - Interattiva, conversazione, dettatura

[!INCLUDE [speech-modes](includes/speech-modes.md)]

La correzione corretta è in arrivo con SDK 1.8, che ha il supporto locale (sceglierà l'endpoint corretto, quindi non sarà peggiore del servizio online). Nel frattempo, è disponibile un campione per il riconoscimento continuo, perché non viene fatto riferimento a esso?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Quale modalità è consigliabile usare per vari file audio?</b>
</summary>

**Risposta:** Ecco una guida [introduttiva all'uso di Python.](./get-started-speech-to-text.md?pivots=programming-language-python) È possibile trovare le altre lingue collegate nel sito della documentazione.

Solo per chiarire l'interattività, la conversazione e la dettatura; si tratta di un modo avanzato per specificare il modo specifico in cui il servizio gestirà la richiesta vocale. Sfortunatamente, per i contenitori locali è necessario specificare l'URI completo (perché include il computer locale), quindi queste informazioni sono trapeete dall'astrazione. Stiamo lavorando con il team SDK per renderlo più utilizzabile in futuro.

<br>
</details>

<details>
<summary>
<b>Come è possibile eseguire il benchmark di una misura approssimativa di transazioni/secondo/core?</b>
</summary>

**Risposta:** Ecco alcuni dei numeri approssimativi previsti dal modello esistente (cambierà in modo migliore in quello che verrà spedito in Ga):

- Per i file, la limitazione sarà in Speech SDK, a 2x. I primi cinque secondi di audio non sono limitate. Il decodificatore è in grado di eseguire circa 3 volte in tempo reale. A questo scopo, l'utilizzo complessivo della CPU sarà vicino a 2 core per un singolo riconoscimento.
- Per il microfono, sarà 1x in tempo reale. L'utilizzo complessivo deve essere di circa 1 core per un singolo riconoscimento.

Tutto questo può essere verificato dai log docker. La riga viene effettivamente scaricata con statistiche di sessione e frasi/espressioni, inclusi i numeri RTF.

<br>
</details>

<details>
<summary>
<b>Ricerca per categorie eseguire più contenitori nello stesso host?</b>
</summary>

Il documento indica di esporre una porta diversa, ma il contenitore LUIS è ancora in ascolto sulla porta 5000?

**Risposta:** Provare `-p <outside_unique_port>:5000` . Ad esempio: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Domande tecniche

<details>
<summary>
<b>Come è possibile ottenere API non batch per gestire l'audio &lt; di 15 secondi?</b>
</summary>

**Risposta:** `RecognizeOnce()` in modalità interattiva elabora solo fino a 15 secondi di audio, in quanto la modalità è destinata ai comandi vocali in cui si prevede che le espressioni siano brevi. Se si usa `StartContinuousRecognition()` per la dettatura o la conversazione, non esiste alcun limite di 15 secondi.


<br>
</details>

<details>
<summary>
<b>Quali sono le risorse consigliate, CPU e RAM; per 50 richieste simultanee?</b>
</summary>

Quante richieste simultanee verranno gestite da 4 core e 4 GB di RAM? Se è necessario gestire, ad esempio, 50 richieste simultanee, quante core e RAM sono consigliati?

**Risposta:** In tempo reale, 8 con la versione più recente, quindi è consigliabile usare più `en-US` contenitori Docker oltre 6 richieste simultanee. Ottiene più di 16 core e diventa sensibile al nodo NUMA (Non-Uniform Memory Access). La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore Voce.

# <a name="speech-to-text"></a>[Riconoscimento vocale](#tab/stt)

| Contenitore      | Minima             | Consigliato         |
|----------------|---------------------|---------------------|
| Riconoscimento vocale | 2 core, 2 GB di memoria | 4 core, 4 GB di memoria |

# <a name="custom-speech-to-text"></a>[Riconoscimento vocale personalizzato](#tab/cstt)

| Contenitore             | Minima             | Consigliato         |
|-----------------------|---------------------|---------------------|
| Riconoscimento vocale personalizzato | 2 core, 2 GB di memoria | 4 core, 4 GB di memoria |

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

| Contenitore      | Minima             | Consigliato         |
|----------------|---------------------|---------------------|
| Sintesi vocale | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore             | Minima             | Consigliato         |
|-----------------------|---------------------|---------------------|
| Sintesi vocale personalizzata | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |

***

- Ogni core deve avere almeno 2,6 GHz o superiore.
- Per i file, la limitazione sarà in Speech SDK, a 2x (i primi 5 secondi di audio non sono limitate).
- Il decodificatore è in grado di eseguire circa 2-3 volte in tempo reale. A questo scopo, l'utilizzo complessivo della CPU sarà vicino a due core per un singolo riconoscimento. Per questo motivo non è consigliabile mantenere più di due connessioni attive, per ogni istanza del contenitore. Il lato estremo sarebbe inserire circa 10 decodificatori in tempo reale 2 volte in un computer a otto core come `DS13_V2` . Per il contenitore versione 1.3 e successive, è disponibile un parametro che è possibile provare a impostare `DECODER_MAX_COUNT=20` .
- Per il microfono, sarà 1x in tempo reale. L'utilizzo complessivo deve essere di circa un core per un singolo riconoscimento.

Si consideri il numero totale di ore di audio. Se il numero è elevato, per migliorare l'affidabilità/disponibilità, è consigliabile eseguire più istanze di contenitori, in una singola casella o in più caselle, dietro un servizio di bilanciamento del carico. L'orchestrazione può essere eseguita usando Kubernetes (K8S) e Helm o con Docker compose.

Ad esempio, per gestire 1000 ore/24 ore, si è provato a configurare 3-4 macchine virtuali, con 10 istanze/decodificatori per macchina virtuale.

<br>
</details>

<details>
<summary>
<b>Il contenitore Voce supporta la punteggiatura?</b>
</summary>

**Risposta:** È disponibile l'i maiuscola (ITN) nel contenitore sul sito. La punteggiatura dipende dalla lingua e non è supportata per alcune lingue, tra cui il cinese e il giapponese.

È *disponibile* il supporto della punteggiatura implicita e di base per i contenitori esistenti, ma lo è per `off` impostazione predefinita. Ciò significa che è possibile ottenere il `.` carattere nell'esempio, ma non il `。` carattere. Per abilitare questa logica implicita, ecco un esempio di come eseguire questa operazione in Python usando Speech SDK (sarebbe simile in altri linguaggi):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Perché vengono visualizzati errori 404 durante il tentativo di post-dati nel contenitore di riconoscimento vocale?</b>
</summary>

Ecco un esempio di HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Risposta:** L'API REST non è disponibile in entrambi i contenitori di riconoscimento vocale. Sono supportati solo i WebSockets tramite Speech SDK. Fare sempre riferimento alla documentazione ufficiale. Vedere [endpoint di stima delle query](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Perché il contenitore viene eseguito come utente non radice? Quali problemi potrebbero verificarsi a causa di questo?</b>
</summary>

**Risposta:** Si noti che l'utente predefinito all'interno del contenitore è un utente non radice. Ciò garantisce la protezione dai processi che eseggono dal contenitore e ottengono autorizzazioni di escalation nel nodo host. Per impostazione predefinita, alcune piattaforme come OpenShift Container Platform eseguono già contenitori usando un ID utente assegnato arbitrariamente. Per queste piattaforme, l'utente non root dovrà avere le autorizzazioni per scrivere in qualsiasi volume mappato esternamente che richiede operazioni di scrittura. Ad esempio, una cartella di registrazione o una cartella di download del modello personalizzata.
<br>
</details>

<details>
<summary>
<b>Quando si usa il servizio di riconoscimento vocale, perché viene visualizzato questo errore?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Risposta:** Ciò si verifica in genere quando si alimenta l'audio più velocemente di quanto possa essere usato dal contenitore Riconoscimento vocale. I buffer client si riempiono e viene attivato l'annullamento. È necessario controllare la concorrenza e il formato RTF a cui si invia l'audio.

<br>
</details>

<details>
<summary>
<b>È possibile spiegare questi errori del contenitore di sintesi vocale negli esempi di C++?</b>
</summary>

**Risposta:** Se la versione del contenitore è precedente alla 1.3, è necessario usare questo codice:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

I contenitori meno recenti non hanno l'endpoint necessario per l'utilizzo dell'API da parte di `FromHost` Carbon. Se i contenitori usati per la versione 1.3, è necessario usare questo codice:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Di seguito è riportato un esempio di uso `FromEndpoint` dell'API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 La `SetSpeechSynthesisVoiceName` funzione viene chiamata perché i contenitori con un motore di sintesi vocale aggiornato richiedono il nome della voce.

<br>
</details>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Contenitori di Servizi cognitivi](speech-container-howto.md)
