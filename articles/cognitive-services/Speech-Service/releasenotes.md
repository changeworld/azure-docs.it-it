---
title: Note sulla versione - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Log in esecuzione delle versioni delle funzionalità del Servizio di riconoscimento vocale, miglioramenti, correzioni di bug e problemi noti.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: e02fbe0f77a53b7d3f8f3ce420bf46189dc85a65
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600982"
---
# <a name="speech-service-release-notes"></a>Note sulla versione del Servizio di riconoscimento vocale

## <a name="text-to-speech-2021-april-release"></a>Versione di sintesi vocale 2021 -aprile

**Neural TTS è disponibile in 21 aree**

- **Sono state aggiunte dodici nuove** aree: Neural TTS è ora disponibile in queste nuove 12 aree: , , , , , `Japan East` , , , `Japan West` , , `Korea Central` `North Central US` `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` `West US 2` . Vedere [qui](regions.md#text-to-speech) per l'elenco completo delle 21 aree supportate.

## <a name="text-to-speech-2021-march-release"></a>Versione di sintesi vocale 2021-marzo

**Nuove lingue e voci aggiunte per la TTS neurale**

-  Sono state introdotte sei nuove lingue: 12 nuove voci in 6 nuove impostazioni locali sono state aggiunte all'elenco delle lingue neurali TTS: Nia in `cy-GB` Catalogna (Regno Unito), Aled in `cy-GB` United Kingdom (Regno Unito), Rosa in `en-PH` Inglese (Filippine), James in `en-PH` Inglese (Filippine), Charline in `fr-BE` Francese (Spagna), Gerard in `fr-BE` Francese (Spagna), Dena in Olandese `nl-BE` (Spagna), Arnaud in Olandese `nl-BE` (Spagna), Polina `uk-UA` in Spagna (Spagna), Ostap in `uk-UA` Dispo (Germania), Uzma in `ur-PK` Urdu (Spagna), Asad in `ur-PK` Urdu (Spagna).

-  Cinque lingue, dall'anteprima alla versione disponibile a livello generale: 10 voci in 5 impostazioni locali introdotte nel mese di novembre 2020 sono ora disponibili a livello generale: Kert in `et-EE` Estone (Irlanda), Colm in Irlanda, Nils in Lituano `ga-IE` `lv-LV` (Portogallo), Leonas in `lt-LT` Lituano (Lituano), Regno Unito `mt-MT` (Irlanda).

- Aggiunta di una nuova voce per il francese **(Canada):** è disponibile una nuova voce Antoine per il francese `fr-CA` (Canada).

- **Miglioramento della** qualità : riduzione del tasso di errore di pronuncia in ungherese `hu-HU` - 48,17%, norvegese `nb-NO` - 52,76%, olandese `nl-NL` (Paesi Bassi) - 22,11%.

Con questa versione è ora possibile supportare un totale di 142 voci neurali in 60 lingue/impostazioni locali. Sono inoltre disponibili oltre 70 voci standard in 49 lingue/impostazioni locali. Per [l'elenco completo,](language-support.md#text-to-speech) vedere Supporto per le lingue.

**Ottenere eventi di posizione facciale per animare i caratteri**

[L'evento Viseme](how-to-speech-synthesis-viseme.md) viene aggiunto a Neural TTS, che consente agli utenti di ottenere la sequenza di pose facciali e la durata dal parlato sintetizzato. Viseme può essere usato per controllare lo spostamento dei modelli avatar 2D e 3D, abbinando perfettamente i movimenti della bocca al parlato sintetizzato. A questo punto, viseme funziona solo per la voce en-US-AriaNeural.

**Aggiungere l'elemento bookmark in Speech Synthesis Markup Language (SSML)**

[L'elemento bookmark](speech-synthesis-markup.md#bookmark-element) consente di inserire marcatori personalizzati in SSML per ottenere l'offset di ogni marcatore nel flusso audio. Può essere usato per fare riferimento a una posizione specifica nella sequenza di testo o tag.

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK 1.16.0: versione 2021-marzo

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable Visual Studio 2015, 2017 e 2019. Scaricarla [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

#### <a name="new-features"></a>Nuove funzionalità

- **C++/C#/Java/Python:** passa alla versione più recente di GStreamer (1.18.3) per aggiungere il supporto per la trascrittura di qualsiasi formato multimediale in Windows, Linux e Android. Vedere la documentazione [qui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python:** aggiunta del supporto per la decodifica dell'audio TTS/sintetizzato compresso nell'SDK. Se si imposta il formato audio di output su PCM e GStreamer è disponibile nel sistema, l'SDK richiederà automaticamente l'audio compresso dal servizio per risparmiare larghezza di banda e decodificare l'audio nel client. È possibile impostare `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` su `false` per disabilitare questa funzionalità. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid) [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python)
- **JavaScript:** Node.js gli utenti possono ora usare [ `AudioConfig.fromWavFileInput` l'API](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_). In questo modo [viene risolto il problema di GitHub #252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252).
- **C++/C#/Java/Objective-C/Python:** aggiunta del metodo per TTS per `GetVoicesAsync()` restituire tutte le voci di sintesi disponibili. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync) [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods), [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods) [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)e [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods)
- **C++/C#/Java/JavaScript/Objective-C/Python:** aggiunta dell'evento per la sintesi vocale/TTS per restituire l'animazione `VisemeReceived` viseme sincrona. Vedere la documentazione [qui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python:** aggiunta `BookmarkReached` dell'evento per TTS. È possibile impostare segnalibri nell'SSML di input e ottenere gli offset audio per ogni segnalibro. Vedere la documentazione [qui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java:** aggiunta del supporto per le API riconoscimento del parlante. Per informazioni [dettagliate, vedere](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)qui .
- **C++/C#/Java/JavaScript/Objective-C/Python:** aggiunta di due nuovi formati audio di output con il contenitore WebM per TTS (Webm16Khz16BitMonoOpus e Webm24Khz16BitMonoOpus). Si tratta di formati migliori per lo streaming audio con il codec Opus. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable) [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat) [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)
- **C++/C#/Java:** aggiunta del supporto per il recupero del profilo vocale per lo scenario di riconoscimento del parlante. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer) [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)e [Java.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/Objective-C/Python:** aggiunta del supporto per la libreria condivisa separata per il controllo voce e microfono audio. In questo modo è possibile usare l'SDK in ambienti che non hanno dipendenze della libreria audio necessarie.
- **Objective-C/Swift:** aggiunta del supporto per il framework del modulo con intestazione generico. Ciò consente di importare Speech SDK come modulo nelle app Objective-C/Swift iOS/Mac. Questo risolve il [problema di GitHub #452](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452).
- **Python:** è stato aggiunto il supporto per [Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) ed è stato eliminato il supporto per Python 3.5 per la fine del ciclo di vita di Python [per la versione 3.5.](https://devguide.python.org/devcycle/#end-of-life-branches)

#### <a name="improvements"></a>Miglioramenti

- Nell'ambito dell'impegno in più versioni per ridurre l'utilizzo della memoria e il footprint del disco di Speech SDK, i file binari Android sono ora dal 3% al 5% più piccoli.
- Miglioramento dell'accuratezza, della leggibilità e delle sezioni see-also della documentazione di riferimento di C# [qui](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet).

#### <a name="bug-fixes"></a>Correzioni di bug

- **JavaScript:** le intestazioni di file WAV di grandi dimensioni vengono ora analizzate correttamente (aumenta la sezione di intestazione a 512 byte). Questo risolve il [problema di GitHub #962](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962).
- **JavaScript:** correzione del problema di temporizzazione del microfono se il flusso del microfono termina prima dell'arresto del riconoscimento, per risolvere un problema relativo al fatto che il riconoscimento vocale non funziona in Firefox.
- **JavaScript:** ora la promessa di inizializzazione viene gestita correttamente quando il browser forza lo spegnimento del microfono prima del completamento di turnOn.
- **JavaScript:** la dipendenza dell'URL è stata sostituita con url-parse. Questo risolve il [problema di GitHub #264](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264).
- **Android:** i callback corretti non funzionano quando `minifyEnabled` è impostato su true.
- **C++/C#/Java/Objective-C/Python:** verrà impostato correttamente sull'I/O del socket sottostante per `TCP_NODELAY` TTS per ridurre la latenza.
- **C++/C#/Java/Python/Objective-C/Go:** correzione di un arresto anomalo occasionale quando il riconoscimento è stato eliminato subito dopo l'avvio di un riconoscimento.
- **C++/C#/Java:** risolto un arresto anomalo occasionale durante la distruzione del riconoscimento del parlante.

#### <a name="samples"></a>Esempi

- **JavaScript:** [gli esempi di browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) non richiedono più il download separato del file della libreria JavaScript.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Interfaccia della riga di comando di Riconoscimento vocale (nota anche come SPX): versione 2021-marzo

> [!NOTE]
> Per iniziare a usare l'interfaccia della riga di comando del servizio Voce di Azure, [vedere qui](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics). L'interfaccia della riga di comando consente di usare il servizio Riconoscimento vocale di Azure senza scrivere codice.

#### <a name="new-features"></a>Nuove funzionalità

- Aggiunta `spx intent` del comando per il riconoscimento delle finalità, sostituendo `spx recognize intent` .
- Riconoscimento e finalità possono ora usare le funzioni di Azure per calcolare la frequenza degli errori delle parole usando `spx recognize --wer url <URL>` .
- Recognize può ora visualizzare i risultati come file VTT usando `spx recognize --output vtt file <FILENAME>` .
- Le informazioni sulla chiave sensibile vengono ora oscurate nell'output di debug/dettagliato.
- Aggiunta del controllo url e del messaggio di errore per il campo del contenuto nella creazione della trascrizione batch.

**Test abbreviato COVID-19**:

Poiché la pandemia in corso continua a richiedere ai tecnici di lavorare da casa, gli script di verifica manuale pre-pandemia sono stati notevolmente ridotti. Viene testato un numero minore di dispositivi con meno configurazioni e la probabilità che si svolgano bug specifici dell'ambiente potrebbe aumentare. Viene comunque convalidata rigorosamente con un set di automazione di grandi dimensioni. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.

## <a name="text-to-speech-2021-february-release"></a>Versione di sintesi vocale 2021-febbraio

**Sintesi vocale neurale ga**

Sintesi vocale neurale è disponibile a febbraio in 13 lingue: cinese (cinese, semplificato), inglese (Australia), inglese (India), inglese (Regno Unito), inglese (Stati Uniti), francese (Canada), francese (Francia), tedesco (Germania), italiano (Italia), giapponese (Giappone), coreano (Corea), portoghese (Brasile), spagnolo (Messico) e spagnolo (Spagna). Altre informazioni [su Sintesi vocale neurale](custom-neural-voice.md) e [su come usarlo](concepts-guidelines-responsible-deployment-synthetic.md)in modo responsabile. Sintesi vocale neurale funzionalità richiede la registrazione e Microsoft può limitare l'accesso in base ai criteri di idoneità di Microsoft. Altre informazioni [sull'accesso limitato.](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)  

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0: versione di gennaio 2021

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable per Visual Studio 2015, 2017 e 2019. Scaricarla [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Riepilogo delle evidenziazioni**
- Memoria e footprint del disco più piccoli, rendendo l'SDK più efficiente.
- Formati di output più fedeltà disponibili per l'anteprima privata della voce neurale personalizzata.
- Riconoscimento finalità può ora ottenere un ritorno maggiore rispetto alla finalità principale, offrendo la possibilità di effettuare una valutazione separata sulla finalità del cliente.
- L'assistente vocale o il bot è ora più facile da configurare ed è possibile fare in modo che interrompi immediatamente l'ascolto e esercitare un maggiore controllo sulla modalità di risposta agli errori.
- Miglioramento delle prestazioni del dispositivo grazie alla compressione facoltativa.
- Usare Speech SDK in Windows ARM/ARM64.
- Miglioramento del debug di basso livello.
- La funzionalità di valutazione della pronuncia è ora più ampiamente disponibile.
- Diverse correzioni di bug per risolvere i problemi che l'utente, i clienti di valore, hanno segnalato in GitHub. Grazie! Mantenere i commenti e suggerimenti in arrivo.

**Miglioramenti**
- Speech SDK è ora più efficiente e leggero. È stato avviato un impegno in più versioni per ridurre l'utilizzo della memoria e il footprint del disco di Speech SDK. Come primo passaggio sono state apportate riduzioni significative delle dimensioni dei file nelle librerie condivise nella maggior parte delle piattaforme. Rispetto alla versione 1.14:
  - Le librerie di Windows compatibili con UWP a 64 bit sono circa il 30% più piccole.
  - Le librerie Windows a 32 bit non hanno ancora visto miglioramenti delle dimensioni.
  - Le librerie Linux sono inferiori del 20-25%.
  - Le librerie Android sono inferiori del 3-5%.

**Nuove funzionalità**
- **Tutti:** nuovi formati di output a 48 KHz disponibili per l'anteprima privata della voce neurale personalizzata tramite l'API sintesi vocale TTS: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **Tutto:** la voce personalizzata è anche più facile da usare. Aggiunta del supporto per l'impostazione della voce personalizzata tramite `EndpointId` ([C++,](/cpp/cognitive-services/speech/speechconfig#setendpointid) [C#,](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId) [Java,](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_) [JavaScript,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId) [Objective-C,](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid) [Python).](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id) Prima di questa modifica, gli utenti vocali personalizzati dovevano impostare l'URL dell'endpoint tramite il `FromEndpoint` metodo . Ora i clienti possono usare il metodo proprio come le voci `FromSubscription` pubbliche e quindi fornire l'ID distribuzione impostando `EndpointId` . In questo modo si semplifica la configurazione di voci personalizzate. 
- **C++/C#/Java/Objective-C/Python:** ottenere più della finalità principale da `IntentRecognizer` . Supporta ora la configurazione del risultato JSON contenente tutte le finalità e non solo la finalità di assegnazione dei punteggi superiore tramite il metodo `LanguageUnderstandingModel FromEndpoint` usando il `verbose=true` parametro uri. Questo risolve il [problema di GitHub #880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Vedere la documentazione aggiornata [qui.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java:** consente di arrestare immediatamente l'ascolto dell'assistente vocale o del bot. `DialogServiceConnector` ([C++,](/cpp/cognitive-services/speech/dialog-dialogserviceconnector) [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) include ora un `StopListeningAsync()` metodo per accompagnare `ListenOnceAsync()` . L'acquisizione audio verrà interrotta immediatamente e si attenderà normalmente un risultato, rendendolo perfetto per l'uso con scenari di pressione del pulsante "Arresta ora".
- **C++/C#/Java/JavaScript:** consente all'assistente vocale o al bot di reagire meglio agli errori di sistema sottostanti. `DialogServiceConnector` ([C++,](/cpp/cognitive-services/speech/dialog-dialogserviceconnector) [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) include ora un nuovo `TurnStatusReceived` gestore eventi. Questi eventi facoltativi corrispondono a ogni risoluzione del bot e segnalano gli errori di esecuzione dei turni quando si verificano, ad esempio in seguito a un'eccezione non gestita, un timeout o un calo della rete tra Direct Line Speech e [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) il bot. `TurnStatusReceived` semplifica la risposta alle condizioni di errore. Ad esempio, se un bot richiede troppo tempo in una query di database back-end (ad esempio, la ricerca di un prodotto), consente al client di sapere di ripetere la richiesta con `TurnStatusReceived` "sorry, I didn't quite get that, could you please try again" (Riprovare) o qualcosa di simile.
- **C++/C#:** usare Speech SDK in più piattaforme. Il [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) speech SDK supporta ora i file binari nativi desktop ARM/ARM64 di Windows (la piattaforma UWP era già supportata) per rendere Speech SDK più utile per più tipi di computer.
- **Java:** ora ha un metodo che in precedenza era stato [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) `setSpeechActivityTemplate()` escluso involontariamente dal linguaggio. Equivale a impostare la proprietà e richiederà che tutte le attività Bot Framework future originate dal servizio Direct Line Speech uniranno il contenuto fornito nei `Conversation_Speech_Activity_Template` payload JSON.
- **Java:** debug di basso livello migliorato. La [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) classe include ora un evento , simile ad altri linguaggi di programmazione `MessageReceived` (C++, C#). Questo evento fornisce accesso di basso livello ai dati in ingresso dal servizio e può essere utile per la diagnostica e il debug.
- **JavaScript:** configurazione più semplice per gli assistenti vocali e i bot tramite , che ora dispone di metodi factory e che semplificano l'uso di percorsi del servizio personalizzati rispetto [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) all'impostazione manuale delle `fromHost()` `fromEndpoint()` proprietà. È stata inoltre standardizzata la specifica facoltativa `botId` di per l'uso di un bot non predefinito nelle factory di configurazione.
- **JavaScript:** miglioramento delle prestazioni del dispositivo grazie all'aggiunta della proprietà di controllo delle stringhe per la compressione websocket. Per motivi di prestazioni, la compressione websocket è stata disabilitata per impostazione predefinita. Questa opzione può essere ri abilitata per scenari con larghezza di banda ridotta. Ulteriori dettagli [qui](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Questo risolve il [problema di GitHub #242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **JavaScript:** aggiunta del supporto per la valutazione della pronuncia per abilitare la valutazione della pronuncia vocale. Vedere la guida introduttiva [qui.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Correzioni di bug**
- **Tutti** (ad eccezione di JavaScript): correzione di una regressione nella versione 1.14, in cui la quantità di memoria allocata dal riconoscitore è stata insufficiente.
- **C++**: risolto un problema di Garbage Collection con , che risolvere il problema di `DialogServiceConnector` [GitHub #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#**: risolto un problema con l'arresto del thread che causava il blocco degli oggetti per circa un secondo in caso di eliminazione.
- **C++/C#/Java:** correzione di un'eccezione che impedisce a un'applicazione di impostare più volte il token di autorizzazione vocale o il modello di attività in un `DialogServiceConnector` oggetto .
- **C++/C#/Java:** correzione di un arresto anomalo del sistema di riconoscimento a causa di un race condition in teardown.
- **JavaScript:** [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) in precedenza non rispettava il parametro `botId` facoltativo specificato nelle `BotFrameworkConfig` factory di . Ciò ha reso necessario impostare manualmente il parametro della `botId` stringa di query per usare un bot non predefinito. Il bug è stato corretto e i valori forniti alle factory verranno rispettati e usati, incluse le `botId` nuove aggiunte `BotFrameworkConfig` e `fromHost()` `fromEndpoint()` . Questo vale anche per il `applicationId` parametro per `CustomCommandsConfig` .
- **JavaScript:** risolto il [problema di GitHub #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), consentendo il nuovo utilizzo dell'oggetto riconoscitore.
- **JavaScript:** risolto un problema a causa del quale lo SD inviava più volte `speech.config` in una sessione TTS, sprecando larghezza di banda.
- **JavaScript:** gestione degli errori semplificata nell'autorizzazione del microfono, che consente di visualizzare un messaggio più descrittivo quando l'utente non ha consentito l'input del microfono nel browser.
- **JavaScript:** è stato risolto [un problema di GitHub #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) a causa del quale gli errori di tipo in e causano un errore di compilazione per gli utenti di `ConversationTranslator` `ConversationTranscriber` TypeScript.
- **Objective-C:** risolto un problema a causa del quale la compilazione di GStreamer non è riuscita per iOS in Xcode 11.4, per risolvere il problema di [GitHub #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python:** correzione del problema di [GitHub #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870), rimuovendo "DeprecationWarning: il modulo imp è deprecato a favore di importlib".

**Esempi**
- [L'esempio from-file per il browser JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) ora usa i file per il riconoscimento vocale. Questo risolve i [problemi di GitHub #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Interfaccia della riga di comando di Voce (nota anche come SPX): versione di gennaio 2021

**Nuove funzionalità**
- L'interfaccia della riga di comando di Voce è ora disponibile come pacchetto [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) e può essere installata tramite l'interfaccia della riga di comando di .NET come strumento globale .NET che è possibile chiamare dalla shell o dalla riga di comando.
- Il [Riconoscimento vocale personalizzato del modello DevOps è](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) stato aggiornato per l'uso dell'interfaccia della riga di comando di Riconoscimento vocale per i relativi Riconoscimento vocale personalizzato di lavoro.

Test abbreviati **DI COVID-19:** poiché la pandemia in corso continua a richiedere ai tecnici di lavorare da casa, gli script di verifica manuale pre-pandemica sono stati significativamente ridotti. Viene testato un minor numero di dispositivi con meno configurazioni e la probabilità che si slittano bug specifici dell'ambiente possa aumentare. Viene comunque convalidata rigorosamente con un set di automazione di grandi dimensioni. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.

## <a name="text-to-speech-2020-december-release"></a>Versione di sintesi vocale 2020 -dicembre

**Nuove voci neurali in ga ga e in anteprima**

Rilasciate 51 nuove voci per un totale di 129 voci neurali in 54 lingue/impostazioni locali:

- **46 nuove** voci nelle impostazioni locali ga: Shakir in arabo `ar-EG` (Arabia Saudita), Hamed in arabo `ar-SA` (Arabia Saudita), Borislav in `bg-BG` Bulgaro (Bulgaria), Giovanna in `ca-ES` Catalogna (Spagna), Annain in Ceco `cs-CZ` (Repubblica Ceca), Jeppe in Danese `da-DK` (Danimarca), Jon aas in tedesco `de-AT` (Svizzera), Jan in tedesco `de-CH` (Svizzera), Nestoras in greco `el-GR` (Grecia), Liam in `en-CA` inglese (Canada), Connor in `en-IE` inglese (Irlanda), Madhur in `en-IN` Hindi (India), Mohan in `en-IN` Telugu (India), Prabhat in `en-IN` inglese (India) ), Valluvar in `en-IN` Tamil (India), Enric `es-ES` in catalano (Spagna), Kert in estone `et-EE` (Estonia), Harri in finlandese `fi-FI` (Finlandia), Selma in finlandese `fi-FI` (Finlandia), Fabrice in francese `fr-CH` (Svizzera), Colm in `ga-IE` Ireland (Irlanda), Avri in `he-IL` ebraico (Israel), Srecko in bosniaco `hr-HR` (Lituania), Tamas in ungherese `hu-HU` (Ungheria), Gadis in `id-ID` Indonesiano (Indonesia), Leonas in `lt-LT` lituano (Lituania), Nils in lettone `lv-LV` (Lettone), Osman in `ms-MY` Malesia (Malesia), Joset in `mt-MT` Malta (Malta) , Finlandese in `nb-NO` norvegese, Bokmål (Norvegia), Pernille in `nb-NO` norvegese, Bokmål (Norvegia), Fenna in olandese `nl-NL` (Paesi Bassi), Maarten in olandese `nl-NL` (Paesi Bassi), Agnieszka in polacco `pl-PL` (Polacco), Marek in polacco `pl-PL` (Svizzera), Duarte in portoghese `pt-BR` (Brasile), Raquel in portoghese `pt-PT` (Potugal), Emil in romeno `ro-RO` (Romania), Dmitry in russo `ru-RU` (Russia), Svetlana in russo `ru-RU` (Russia), Lukas in `sk-SK` Slovacco (Svizzera), Rok in `sl-SI` sloveno (Slovenia), Mattias in svedese `sv-SE` (Svezia), Sofie in svedese `sv-SE` (Svezia), Niwat in `th-TH` Tailandia (Tailandia), Ahmet in turco `tr-TR` (Turco), NamMinh in `vi-VN` taiwanese (Taiwan), HsiaoChen in mandarino taiwanese `zh-TW` (Taiwan), YunJhe in `zh-TW` mandarino taiwanese (Taiwan), HiuMaan in cinese `zh-HK` cantonese (Hong Kong), WanLung in cinese `zh-HK` cinese (Hong Kong).

- **5** nuove voci nelle impostazioni locali di anteprima: Kert in `et-EE` estone (Estone), Colm in `ga-IE` Irlanda, Nils in `lv-LV` Lettone (Lituano), Leonas in `lt-LT` Lituano (Lituano), Regno Unito `mt-MT` (Irlanda).

Con questa versione è ora possibile supportare un totale di 129 voci neurali in 54 lingue/impostazioni locali. Sono inoltre disponibili oltre 70 voci standard in 49 lingue/impostazioni locali. Per [l'elenco completo,](language-support.md#text-to-speech) vedere Supporto per le lingue.

**Aggiornamenti per Creazione di contenuto audio**
- Interfaccia utente migliorata per la selezione della voce con categorie vocali e descrizioni vocali dettagliate. 
- È stata abilitata l'ottimizzazione dell'intonazione per tutte le voci neurali in lingue diverse.
- Localizzazione automatica dell'interfaccia utente in base alla lingua del browser.
- Abilitati `StyleDegree` i controlli per tutte le voci `zh-CN` neurali.
Visitare lo [Creazione di contenuto audio per](https://speech.microsoft.com/audiocontentcreation) verificare le nuove funzionalità. 

**Aggiornamenti per le voci zh-CN**
- Aggiornamento di tutte `zh-CN` le voci neurali per supportare la lingua inglese.
- Abilitato tutte le `zh-CN` voci neurali per supportare la regolazione dell'intonazione. È possibile usare Creazione di contenuto audio SSML o uno strumento per la regolazione per la migliore intonazione.
- Aggiornamento di tutte `zh-CN` le voci neurali multi-stile per supportare il `StyleDegree` controllo. L'intensità delle emozioni (soft o strong) è regolabile.
- Aggiornato `zh-CN-YunyeNeural` per supportare più stili che possono eseguire emozioni diverse.

## <a name="text-to-speech-2020-november-release"></a>Sintesi vocale versione di novembre 2020

**Nuove impostazioni locali e voci in anteprima**
- **Nel portfolio Neural** TTS sono state introdotte cinque nuove voci e lingue. Sono: Grace in Malta (Malta), Ona in lituano (Lituania), Anu in estone (Estonia), Orla in Irlanda (Irlanda) ed Everita in lettone (Lettone).
- **Cinque nuove `zh-CN` voci con** più stili e ruoli supportano: Xiaohan, Xiaomo, Xiaorui, Xiaoxuan e Yunxi.

> Queste voci sono disponibili in anteprima pubblica in tre aree di Azure: EastUS, SouthEastAsia e WestEurope.

**Neural TTS Container GA**
- Con Neural TTS Container, gli sviluppatori possono eseguire la sintesi vocale con le voci digitali più naturali nel proprio ambiente per requisiti specifici di sicurezza e governance dei dati. Controllare [come installare i contenitori voce](speech-container-howto.md). 

**Nuove funzionalità**
- **voce personalizzata**: consente agli utenti di copiare un modello vocale da un'area a un'altra; sospensione e ripresa dell'endpoint supportate. Passare al [portale](https://speech.microsoft.com/customvoice) qui.
- [Supporto dei tag di silenzio SSML.](speech-synthesis-markup.md#add-silence) 
- Miglioramenti generali della qualità della voce TTS: miglioramento dell'accuratezza della pronuncia a livello di parola in nb-NO. Riduzione dell'errore di pronuncia del 53%.

> Per altre informazioni, [vedere questo blog tecnico.](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)

## <a name="text-to-speech-2020-october-release"></a>Versione di sintesi vocale 2020-ottobre

**Nuove funzionalità**
- Jenny supporta un nuovo `newscast` stile. Vedere [come usare gli stili di pronuncia in SSML.](speech-synthesis-markup.md#adjust-speaking-styles)
- **Voci neurali aggiornate al vocoder HiFiNet, con maggiore fedeltà audio e velocità di sintesi più veloce.** Ciò è vantaggioso per i clienti il cui scenario si basa su audio hi-fi o interazioni lunghe, tra cui il doppiaggio video, i libri audio o i materiali di formazione online. [Per altre informazioni sulla storia e per ascoltare gli esempi vocali, vedere il blog della community tech](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[voce personalizzata](https://speech.microsoft.com/customvoice)  &  [Creazione di contenuto audio Studio localizzato](https://speech.microsoft.com/audiocontentcreation) in 17 impostazioni locali.** Gli utenti possono passare facilmente l'interfaccia utente a una lingua locale per un'esperienza più semplice.   
- **Creazione di contenuto audio:** aggiunta del controllo dei gradi di stile per XiaoxiaoNeural; Perfezionata la funzionalità di interruzione personalizzata in modo da includere interruzioni incrementali di 50 ms. 

**Miglioramenti generali della qualità della voce TTS**
- Miglioramento dell'accuratezza della pronuncia a livello di parola in `pl-PL` (riduzione del tasso di errore: 51%) e `fi-FI` (riduzione della frequenza degli errori: 58%)
- Miglioramento `ja-JP` della lettura di singole parole per lo scenario del dizionario. Errore di pronuncia ridotto dell'80%.
- `zh-CN-XiaoxiaoNeural`: miglioramento della qualità della voce in stile sentiment/CustomerService/Newscast/Disartiginato/Disartiginato.
- `zh-CN`: miglioramento della pronuncia e del tono chiaro di Erhua e prosodia dello spazio ottimizzata, che migliora notevolmente l'intelligibilità. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: versione di ottobre 2020

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable per Visual Studio 2015, 2017 e 2019. Scaricarla [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nuove funzionalità**
- **Linux:** aggiunta del supporto per Debian 10 e Ubuntu 20.04 LTS.
- **Python/Objective-C:** aggiunta del supporto per `KeywordRecognizer` l'API. La documentazione sarà [disponibile qui.](./custom-keyword-basics.md)
- **C++/Java/C#:** aggiunta del supporto per impostare qualsiasi `HttpHeader` chiave/valore tramite `ServicePropertyChannel::HttpHeader` .
- **JavaScript:** aggiunta del supporto per `ConversationTranscriber` l'API. Leggere la documentazione [qui.](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 
- **C++/C#:** aggiunta del `AudioDataStream FromWavFileInput` nuovo metodo (per leggere . file WAV) [qui (C++)](/cpp/cognitive-services/speech/audiodatastream) e [qui (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/C#/Java/Python/Objective-C/Swift:** aggiunta di un metodo per arrestare `stopSpeakingAsync()` la sintesi vocale. Leggere la documentazione di riferimento [qui (C++),](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace)qui [(C#),](/dotnet/api/microsoft.cognitiveservices.speech)qui [(Java),](/java/api/com.microsoft.cognitiveservices.speech)qui [(Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)e [qui (Objective-C/Swift).](/objectivec/cognitive-services/speech/)
- **C#, C++, Java:** aggiunta di una funzione alla classe che può essere usata per monitorare gli eventi di connessione `FromDialogServiceConnector()` `Connection` e disconnessione per `DialogServiceConnector` . Leggere la documentazione di [riferimento qui (C#),](/dotnet/api/microsoft.cognitiveservices.speech.connection) [qui (C++)](/cpp/cognitive-services/speech/connection)e [qui (Java).](/java/api/com.microsoft.cognitiveservices.speech.connection)
- **C++/C#/Java/Python/Objective-C/Swift:** aggiunta del supporto per la valutazione della pronuncia, che valuta la pronuncia vocale e fornisce ai parlanti feedback sull'accuratezza e la fluenza dell'audio parlato. Leggere la documentazione [qui.](how-to-pronunciation-assessment.md)

**Modifica di rilievo**
- **JavaScript:** PullAudioOutputStream.read() ha una modifica del tipo restituito da una promessa interna a una promessa JavaScript nativa.

**Correzioni di bug**
- **All**: correzione della regressione 1,13 in cui `SetServiceProperty` i valori con determinati caratteri speciali sono stati ignorati.
- **C#**: sono stati corretti gli esempi di console di Windows Visual Studio 2019 non è riuscito a trovare DLL native.
- **C#**: correzione dell'arresto anomalo con la gestione della memoria se il flusso viene usato come `KeywordRecognizer` input.
- **ObjectiveC/Swift:** risolto un arresto anomalo con la gestione della memoria se il flusso viene usato come input del riconoscitore.
- **Windows:** risolto un problema di coesistenza con BT HFP/A2DP nella piattaforma UWP.
- **JavaScript:** correzione del mapping degli ID di sessione per migliorare la registrazione e facilitare le correlazioni di debug/servizio interne.
- **JavaScript:** aggiunta correzione per `DialogServiceConnector` la `ListenOnce` disabilitazione delle chiamate dopo la prima chiamata.
- **JavaScript:** risolto un problema a causa del quale l'output dei risultati sarebbe "semplice".
- **JavaScript:** risolto un problema di riconoscimento continuo in Safari in macOS.
- **JavaScript:** mitigazione del carico della CPU per uno scenario con velocità effettiva elevata delle richieste.
- **JavaScript:** consente l'accesso ai dettagli del risultato della registrazione del profilo vocale.
- **JavaScript:** aggiunta della correzione per il riconoscimento continuo in `IntentRecognizer` .
- **C++/C#/Java/Python/Swift/ObjectiveC:** corretto url non corretto per australiaeast e brazilsouth in `IntentRecognizer` .
- **C++/C#**: aggiunto `VoiceProfileType` come argomento durante la creazione di un `VoiceProfile` oggetto.
- **C++/C#/Java/Python/Swift/ObjectiveC:** correzione del potenziale durante il tentativo di `SPX_INVALID_ARG` leggere da una determinata `AudioDataStream` posizione.
- **IOS:** risolto un arresto anomalo con il riconoscimento vocale in Unity

**Esempi**
- **ObjectiveC:** aggiunto un esempio per il riconoscimento delle parole [chiave qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/JavaScript:** aggiunta della guida introduttiva per la trascrizione della conversazione qui [(C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) e [qui (JavaScript).](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)
- **C++/C#/Java/Python/Swift/ObjectiveC:** aggiunta dell'esempio per la valutazione della pronuncia [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin:** aggiornamento della guida introduttiva al modello di Visual Studio più [recente qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Problema noto**
- Il certificato DigiCert Global Root G2 non è supportato per impostazione predefinita in HoloLens 2 e Android 4.4 (KitKat) e deve essere aggiunto al sistema per rendere funzionale Speech SDK. Il certificato verrà aggiunto a HoloLens 2 del sistema operativo nel prossimo futuro. I clienti android 4.4 devono aggiungere il certificato aggiornato al sistema.

**Test abbreviato COVID-19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali come di consueto. Non sono state apportate modifiche che potrebbero aver interrotto nulla e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Interfaccia della riga di comando di Voce (nota anche come SPX): versione di ottobre 2020
SPX è l'interfaccia della riga di comando per usare il servizio Voce di Azure senza scrivere codice. Scaricare la versione più recente [qui.](./spx-basics.md) <br>

**Nuove funzionalità**
- `spx csr dataset upload --kind audio|language|acoustic` : creare set di dati da dati locali, non solo da URL.
- `spx csr evaluation create|status|list|update|delete` : confronta i nuovi modelli con la verità di base o altri modelli.
- `spx * list` : supporta l'esperienza senza pagina (non richiede --top X --skip X).
- `spx * --http header A=B` : supporta le intestazioni personalizzate (aggiunte per Office per l'autenticazione personalizzata). 
- `spx help` : testo migliorato e colore del testo con segno di spunta indietro codificato (blu).

## <a name="text-to-speech-2020-september-release"></a>Versione di sintesi vocale 2020-settembre

### <a name="new-features"></a>Nuove funzionalità

* **TTS neurale** 
    * **Esteso per supportare 18 nuove lingue/impostazioni locali.** Si tratta di bulgaro, ceco, tedesco (Svizzera), tedesco (Svizzera), greco, inglese (Irlanda), francese (Svizzera), ebraico, bulgaro, ungherese, malese, romeno, slovacco, sloveno, tamil, telugu e taiwanese. 
    * **Rilasciate 14 nuove voci per arricchire la varietà nelle lingue esistenti.** Vedere [l'elenco completo della lingua e della voce.](language-support.md#neural-voices)
    * **Nuovi stili di pronuncia `en-US` per `zh-CN` e voci.** Jenny, la nuova voce in inglese (Stati Uniti), supporta gli stili di chatbot, servizio clienti e assistente. 10 nuovi stili di conversazione sono disponibili con la voce zh-CN XiaoXiao. Inoltre, la voce neurale XiaoXiao supporta `StyleDegree` l'ottimizzazione. Vedere [come usare gli stili di pronuncia in SSML.](speech-synthesis-markup.md#adjust-speaking-styles)

* **Contenitori: contenitore Neural TTS rilasciato in anteprima pubblica con 16 voci disponibili in 14 lingue.** Altre informazioni su [come distribuire contenitori voce per Neural TTS](speech-container-howto.md)  

Leggere [l'annuncio completo degli aggiornamenti TTS per Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Versione di sintesi vocale 2020-agosto

### <a name="new-features"></a>Nuove funzionalità

* **Neural TTS: nuovo stile di pronuncia per `en-US` Aria voice**. AriaNeural può sembrare un telegiornale durante la lettura delle notizie. Lo stile "newscast-formal" sembra più grave, mentre lo stile "newscast-casual" è più rilassato e informale. Vedere [come usare gli stili di pronuncia in SSML.](speech-synthesis-markup.md)

* **voce personalizzata: viene rilasciata una nuova funzionalità per controllare automaticamente la qualità dei dati di training.** Quando si caricano i dati, il sistema esamina vari aspetti dei dati audio e trascrizione e corregge automaticamente o filtra i problemi per migliorare la qualità del modello vocale. Ciò copre il volume dell'audio, il livello di disturbo, l'accuratezza della pronuncia del parlato, l'allineamento del parlato con il testo normalizzato, il silenzio nell'audio, oltre al formato audio e script. 

* **Creazione di contenuto audio: un set di nuove** funzionalità per abilitare funzionalità di ottimizzazione vocale e gestione audio più potenti.

    * Pronuncia: la funzionalità di ottimizzazione della pronuncia viene aggiornata al set di fonemi più recente. È possibile selezionare l'elemento fonema giusto dalla libreria e perfezionare la pronuncia delle parole selezionate. 

    * Download: la funzionalità audio "Download"/"Export" è stata migliorata per supportare la generazione di audio in base al paragrafo. È possibile modificare il contenuto nello stesso file/SSML, generando più output audio. Viene perfezionata anche la struttura di file di "Download". A questo punto, è possibile ottenere facilmente tutti i file audio in un'unica cartella. 

    * Stato attività: l'esperienza di esportazione di più file è stata migliorata. Quando si esportano più file in passato, se uno dei file ha avuto esito negativo, l'intera attività avrà esito negativo. Ma ora tutti gli altri file verranno esportati correttamente. Il report attività è arricchito con informazioni più dettagliate e strutturate. È possibile controllare i log per tutti i file e le frasi non riuscite ora con il report. 

    * Documentazione di SSML: collegata al documento SSML per controllare le regole per l'uso di tutte le funzionalità di ottimizzazione.

* **L'API Elenco vocale viene aggiornata per includere un nome visualizzato descrittivo** e gli stili di pronuncia supportati per le voci neurali.

### <a name="general-tts-voice-quality-improvements"></a>Miglioramenti generali della qualità della voce TTS

* Riduzione della percentuale di errore di pronuncia a livello di parola `ru-RU` per (errori ridotti del 56%) e `sv-SE` (errori ridotti del 49%)

* Miglioramento del 40% della lettura delle parole `en-US` polifone sulle voci neurali. Esempi di parole polifone includono "read", "live", "content", "record", "object" e così via. 

* Miglioramento della naturalezza del tono della domanda in `fr-FR` . GUADAGNO (mean opinion score) gain: +0.28

* Aggiornamento dei vocoder per le voci seguenti, con miglioramenti della fedeltà e velocità complessiva delle prestazioni del 40%.

    | Locale | Chiamata vocale |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Correzioni di bug

* Correzione di alcuni bug con lo strumento Creazione di contenuto audio 
    * Correzione del problema relativo all'aggiornamento automatico. 
    * Correzione dei problemi relativi agli stili voce in zh-CN nell'area Asia orientale sud.
    * Correzione di un problema di stabilità, tra cui un errore di esportazione con il tag 'break' ed errori di punteggiatura.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nuove impostazioni locali per il riconoscimento vocale: versione di agosto 2020
Il riconoscimento vocale ha rilasciato 26 nuove impostazioni locali nel mese di agosto: 2 lingue europae e , 5 impostazioni locali in inglese e 19 impostazioni locali spagnole che coprono la maggior parte dei paesi dell'America del `cs-CZ` `hu-HU` Sud. Di seguito è riportato un elenco delle nuove impostazioni locali. Vedere l'elenco completo delle lingue [qui.](./language-support.md)

| Impostazioni locali  | Linguaggio                          |
|---------|-----------------------------------|
| `cs-CZ` | Ceco (Repubblica Ceca)            | 
| `en-HK` | Inglese (Hong Kong)               | 
| `en-IE` | Inglese (Irlanda)                 | 
| `en-PH` | Inglese (Filippine)             | 
| `en-SG` | Inglese (Singapore)               | 
| `en-ZA` | Inglese (Sudafrica)            | 
| `es-AR` | Spagnolo (Argentina)               | 
| `es-BO` | Spagnolo (Bolivia)                 | 
| `es-CL` | Spagnolo (Cile)                   | 
| `es-CO` | Spagnolo (Colombia)                | 
| `es-CR` | Spagnolo (Costa Rica)              | 
| `es-CU` | Spagnolo (Spagna)                    | 
| `es-DO` | Spagnolo (Repubblica dominicana)      | 
| `es-EC` | Spagnolo (Ecuador)                 | 
| `es-GT` | Spagnolo (Guatemala)               | 
| `es-HN` | Spagnolo (Honduras)                | 
| `es-NI` | Spagnolo (Nicaragua)               | 
| `es-PA` | Spagnolo (Panama)                  | 
| `es-PE` | Spagnolo (Perù)                    | 
| `es-PR` | Spagnolo (Puerto Rico)             | 
| `es-PY` | Spagnolo (Paraguay)                | 
| `es-SV` | Spagnolo (El Salvador)             | 
| `es-US` | Spagnolo (Usa)                     | 
| `es-UY` | Spagnolo (Uruguay)                 | 
| `es-VE` | Spagnolo (Venezuela)               | 
| `hu-HU` | Ungherese (Ungheria)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: versione di luglio 2020

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable per Visual Studio 2015, 2017 e 2019. Scaricare e installare l'app da [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nuove funzionalità**
- **C#:** aggiunta del supporto per la trascrizione asincrona della conversazione. Vedere la documentazione [qui.](./how-to-async-conversation-transcription.md)  
- **JavaScript:** aggiunta del Riconoscimento del parlante per [browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) [ enode.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript:** aggiunta del supporto per il rilevamento automatico della lingua/ID lingua. Vedere la documentazione [qui.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objective-C:** aggiunta del supporto per [la trascrizione di conversazioni e](./multi-device-conversation.md) conversazioni su più [dispositivi.](./conversation-transcription.md) 
- **Python:** aggiunta del supporto audio compresso per Python in Windows e Linux. Vedere la documentazione [qui.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Correzioni di bug**
- **All**: è stato risolto un problema che causava il fatto che KeywordRecognizer non spostava in avanti i flussi dopo un riconoscimento.
- **All:** risolto un problema che causava il fatto che il flusso ottenuto da KeywordRecognitionResult non contiene la parola chiave .
- **All**: è stato risolto un problema per cui SendMessageAsync non invia effettivamente il messaggio in rete dopo che gli utenti hanno terminato l'attesa.
- **Tutti:** correzione di un arresto anomalo nelle API di Riconoscimento del parlante quando gli utenti chiamano più volte il metodo VoiceProfileClient::SpeakerRecEnrollProfileAsync e non attendono il completamento delle chiamate.
- **Tutti:** correzione dell'abilitazione della registrazione dei file nelle classi VoiceProfileClient e SpeakerRecognizer.
- **JavaScript:** è stato risolto un [problema di](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) limitazione quando il browser è ridotto a icona.
- **JavaScript:** risolto un [problema relativo](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) a una perdita di memoria nei flussi.
- **JavaScript:** aggiunta della memorizzazione nella cache per le risposte OCSP da NodeJS.
- **Java:** è stato risolto un problema che causava la restituzione sempre di 0 da parte dei campi BigInteger.
- **iOS:** è stato risolto [un problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) relativo alla pubblicazione di app basate su Speech SDK nel App Store iOS.

**Esempi**
- **C++:** aggiunto il codice di esempio per Riconoscimento del parlante [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Test abbreviato COVID-19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali come di consueto. Non sono state apportate modifiche che potrebbero aver interrotto nulla e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.

## <a name="text-to-speech-2020-july-release"></a>Sintesi vocale versione di luglio 2020

### <a name="new-features"></a>Nuove funzionalità

* **Neural TTS, 15** nuove voci neurali: le nuove voci aggiunte al portfolio neurale TTS sono Salma in arabo `ar-EG` (India), Zariyah in arabo `ar-SA` (India), Catalogna in `ca-ES` Catalogna (Spagna), `da-DK` Neerja in inglese (India), Noora in Finlandese (Spagna), Neerja in inglese `es-IN` (India), Noora in Finlandese `fi-FI` (Svizzera), Swara in `hi-IN` Hindi (India), Colette in olandese `nl-NL` (Paesi Bassi), Zofia in polacco `pl-PL` (Svizzera), Kpia in portoghese `pt-PT` (Portogallo),Logoiya in `ru-RU` russo (Russia), Kpievi in Svedese `sv-SE` (Norvegia), Achara in `th-TH` Thai (Portogallo), HiuGaai in cinese `zh-HK` (Svizzera, Tradizionale) e HsiaoYu in `zh-TW` cinese (taiwanese taiwanese). Controllare tutte [le lingue supportate.](./language-support.md#neural-voices)  

* **voce personalizzata** test vocale semplificato con il flusso di training per semplificare l'esperienza utente: con la nuova funzionalità di test, ogni voce verrà testata automaticamente con un set di test predefinito ottimizzato per ogni lingua per coprire scenari generali e di assistente vocale. Questi set di test vengono selezionati e testati con attenzione per includere casi d'uso e fonemi tipici nella lingua. Inoltre, gli utenti possono comunque scegliere di caricare i propri script di test durante il training di un modello.

* **Creazione di contenuto audio: viene rilasciato un set di nuove funzionalità per abilitare funzionalità più potenti di ottimizzazione vocale e gestione audio**

    * `Pitch`, `rate` e sono stati migliorati per supportare l'ottimizzazione con un valore predefinito, ad esempio `volume` lento, medio e veloce. È ora semplice per gli utenti scegliere un valore "costante" per la modifica audio.

    ![Ottimizzazione dell'audio](media/release-notes/audio-tuning.png)

    * Gli utenti possono ora esaminare per `Audio history` il file di lavoro. Con questa funzionalità, gli utenti possono tenere facilmente traccia di tutto l'audio generato correlato a un file di lavoro. Possono controllare la versione della cronologia e confrontare la qualità durante l'ottimizzazione contemporaneamente. 

    ![Cronologia audio](media/release-notes/audio-history.png)

    * La `Clear` funzionalità è ora più flessibile. Gli utenti possono cancellare un parametro di ottimizzazione specifico mantenendo altri parametri disponibili per il contenuto selezionato.  

    * Nella pagina di destinazione [](https://speech.microsoft.com/audiocontentcreation) è stato aggiunto un video di esercitazione che consente agli utenti di iniziare rapidamente a usare l'ottimizzazione vocale TTS e la gestione dell'audio. 

### <a name="general-tts-voice-quality-improvements"></a>Miglioramenti generali della qualità della voce TTS

* Miglioramento del vocoder TTS in per una maggiore fedeltà e una latenza più bassa.

    * Aggiornamento di Rispetto a un nuovo vocoder che ha ottenuto +0,464 CMOS (comparativo punteggio medio di opinione) nella qualità della voce, un 40% più veloce nella sintesi e una riduzione del 30% sulla latenza del primo `it-IT` byte. 
    * Aggiornamento di Xiaoxiao al nuovo `zh-CN` vocoder con +0148 guadagno CMOS per il dominio generale, +0,348 per lo stile newscast e +0,195 per lo stile lirica. 

* Modelli di voce e aggiornati per rendere più naturale `de-DE` `ja-JP` l'output TTS.
    
    * È stato aggiornato Katja in con `de-DE` il metodo di modellazione della prosodia più recente, il guadagno di MOS (Mean Opinion Score) è +0,13. 
    * Aggiornamento di Nanami in con un nuovo modello di prosodia dell'accento del tono, il `ja-JP` guadagno di MOS (Mean Opinion Score) è +0,19;  

* Miglioramento dell'accuratezza della pronuncia a livello di parola in cinque lingue.

    | Linguaggio | Riduzione degli errori di pronuncia |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Correzioni di bug

* Lettura della valuta
    * Correzione del problema relativo alla lettura della valuta `es-ES` per e `es-MX`
     
    | Linguaggio | Input | Readout dopo il miglioramento |
    |---|---|---|
    | `es-MX` | $ 1,58 | un peso cincuenta y ocho centavos |
    | `es-ES` | $ 1,58 | un dèlar cincuenta y ocho centavos |

    * Supporto per la valuta negativa (ad esempio "-325 €" ) nelle impostazioni locali seguenti: `en-US` , , , , , `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Miglioramento della lettura degli indirizzi in `pt-PT` .
* Sono stati risolti i problemi di `en-AU` pronuncia Diesha ( ) e Libby ( `en-UK` ) sulle parole "for" e "four".  
* Correzione di bug Creazione di contenuto audio strumento
    * Pausa aggiuntiva e imprevista dopo la correzione del secondo paragrafo.  
    * La funzionalità "Nessuna interruzione" viene nuovamente aggiunta da un bug di regressione. 
    * Il problema di aggiornamento casuale Speech Studio è stato risolto.  

### <a name="samplessdk"></a>Esempi/SDK

* JavaScript: corregge il problema di riproduzione in Firefox e Safari in macOS e iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: versione di giugno 2020
**Interfaccia della riga di comando di Voce (nota anche come SPX)**
-   Aggiunta delle funzionalità di ricerca della Guida nell'interfaccia della riga di comando:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Aggiornamento per l'utilizzo con le API batch e Riconoscimento vocale personalizzato v3.0 appena distribuite:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nuove funzionalità**
-   **C, \# C++:** Riconoscimento del parlante Preview: questa funzionalità consente l'identificazione della voce (chi parla?) e la verifica della voce (è l'altoparlante che dichiara di essere?). Iniziare con una [panoramica,](./speaker-recognition-overview.md)leggere [l'articolo Riconoscimento del parlante nozioni di base o](./get-started-speaker-recognition.md)la documentazione di riferimento [dell'API](/rest/api/speakerrecognition/).

**Correzioni di bug**
-   **C, \# C++:** la registrazione del microfono fissa non funzionava nella versione 1.12 nel riconoscimento del parlante.
-   **JavaScript:** correzioni per la sintesi vocale in Firefox e Safari in macOS e iOS.
-   Correzione per l'arresto anomalo della violazione di accesso di Verifica applicazione Windows durante la trascrizione della conversazione quando si usa un flusso a otto canali.
-   Correzione per l'arresto anomalo della violazione di accesso di Verifica applicazione Windows nella conversione di conversazioni su più dispositivi.

**Esempi**
-   **C#:** esempio [di codice per](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) il riconoscimento del parlante.
-   **C++:** esempio [di codice per](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) il riconoscimento del parlante.
-   **Java:** [esempio di codice per](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) il riconoscimento delle finalità in Android. 

**Test abbreviati DI COVID-19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali come di consueto. Non sono state apportate modifiche che si potrebbe avere interrotto e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: versione di maggio 2020
**Interfaccia della riga di comando di Voce (anche noto come SPX)**
- **SPX** è un nuovo strumento da riga di comando che consente di eseguire il riconoscimento, la sintesi, la traduzione, la trascrizione batch e la gestione del riconoscimento vocale personalizzato dalla riga di comando. Usarlo per testare il Servizio di riconoscimento vocale o per creare script per le attività del Servizio di riconoscimento vocale che è necessario eseguire. Scaricare lo strumento e leggere la documentazione [qui](./spx-overview.md).

**Nuove funzionalità**
- **Go:** nuovo supporto per la lingua Go [per il riconoscimento vocale e](./get-started-speech-to-text.md?pivots=programming-language-go) [l'assistente vocale personalizzato.](./quickstarts/voice-assistants.md?pivots=programming-language-go) Configurare l'ambiente di sviluppo [qui](./quickstarts/setup-platform.md?pivots=programming-language-go). Per il codice di esempio, vedere la sezione Esempi riportata di seguito. 
- **JavaScript:** aggiunta del supporto del browser per la sintesi vocale. Vedere la documentazione [qui.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java:** nuovo oggetto e API supportati nelle piattaforme `KeywordRecognizer` Windows, Android, Linux & iOS. Leggere la documentazione [qui.](./custom-keyword-overview.md) Per il codice di esempio, vedere la sezione Esempi riportata di seguito. 
- **Java:** è stata aggiunta una conversazione su più dispositivi con supporto per la traduzione. Vedere il documento di [riferimento qui.](/java/api/com.microsoft.cognitiveservices.speech.transcription)

**Miglioramenti & ottimizzazioni**
- **JavaScript: implementazione** ottimizzata del microfono del browser che migliora l'accuratezza del riconoscimento vocale.
- **Java:** refactoring delle associazioni tramite l'implementazione JNI diretta senza SWIG. Questa modifica riduce di 10x le dimensioni delle associazioni per tutti i pacchetti Java usati per Windows, Android, Linux e Mac e semplifica ulteriormente lo sviluppo dell'implementazione Java di Speech SDK.
- **Linux:** documentazione di supporto [aggiornata](./speech-sdk.md?tabs=linux) con le note specifiche di RHEL 7 più recenti.
- È stata migliorata la logica di connessione per tentare la connessione più volte quando si verificano errori di servizio e di rete.
- È stata [aggiornata portal.azure.com](https://portal.azure.com) guida introduttiva al riconoscimento vocale per aiutare gli sviluppatori a eseguire il passaggio successivo del percorso di Riconoscimento vocale di Azure.

**Correzioni di bug**
- **C#, Java:** risolto un [problema di](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) caricamento delle librerie SDK in Linux ARM (sia a 32 bit che a 64 bit).
- **C#:** correzione dell'eliminazione esplicita degli handle nativi per gli oggetti TranslationRecognizer, IntentRecognizer e Connection.
- **C#:** correzione della gestione della durata dell'input audio per l'oggetto ConversationTranscriber.
- Risolto un problema a causa del quale il motivo del risultato non `IntentRecognizer` era impostato correttamente durante il riconoscimento delle finalità da frasi semplici.
- Risolto un problema per cui `SpeechRecognitionEventArgs` l'offset dei risultati non era impostato correttamente.
- Correzione di un race condition in cui l'SDK tentava di inviare un messaggio di rete prima di aprire la connessione websocket. È riproducibile per durante `TranslationRecognizer` l'aggiunta di partecipanti.
- Correzione delle perdite di memoria nel motore di riconoscimento delle parole chiave.

**Esempi**
- **Go:** sono state aggiunte guide introduttive per [il riconoscimento vocale e](./get-started-speech-to-text.md?pivots=programming-language-go) [l'assistente vocale personalizzato.](./quickstarts/voice-assistants.md?pivots=programming-language-go) Trovare il codice di [esempio qui](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **JavaScript:** sono state aggiunte guide introduttive [per sintesi](./get-started-text-to-speech.md?pivots=programming-language-javascript)vocale, [traduzione](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script) [e Riconoscimento finalità](./get-started-intent-recognition.md?pivots=programming-language-javascript).
- Esempi di riconoscimento delle parole [chiave per \# C](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) [e Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Test abbreviato COVID-19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali come di consueto. Non sono state apportate modifiche che potrebbero aver interrotto nulla e tutti i test automatizzati sono stati superati. Se non si è verificato un problema, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: versione di marzo 2020
**Nuove funzionalità**
- Linux: aggiunta del supporto per Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 con istruzioni su come configurare il sistema per Speech SDK. [](./how-to-configure-rhel-centos-7.md)
- Linux: aggiunta del supporto per .NET Core C# in Linux ARM32 e ARM64. Altre informazioni sono disponibili [qui](./speech-sdk.md?tabs=linux). 
- C#, C++: aggiunto in , un ID coerente tra tutti gli elementi intermedi e il risultato finale `UtteranceId` `ConversationTranscriptionResult` del riconoscimento vocale. Dettagli per [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++.](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)
- Python: aggiunta del supporto per `Language ID` . Vedere speech_sample.py nel [repository GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: è stato aggiunto il supporto del formato di input audio compresso nella piattaforma Windows per tutte le applicazioni console win32. Per informazioni [dettagliate, vedere](./how-to-use-codec-compressed-audio-input-streams.md)qui . 
- JavaScript: supporto della sintesi vocale (sintesi vocale) in NodeJS. Fare clic [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) per altre informazioni. 
- JavaScript: aggiungere nuove API per abilitare l'ispezione di tutti i messaggi inviati e ricevuti. Fare clic [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) per altre informazioni. 
        
**Correzioni di bug**
- C#, C++: è stato risolto un problema per `SendMessageAsync` cui ora invia un messaggio binario come tipo binario. Dettagli per [C#,](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) [C++.](/cpp/cognitive-services/speech/connection)
- C#, C++: è stato risolto un problema a causa del quale l'uso dell'evento può causare un arresto anomalo `Connection MessageReceived` se viene eliminato prima `Recognizer` `Connection` dell'oggetto. Dettagli per [C#,](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived) [C++.](/cpp/cognitive-services/speech/connection#messagereceived)
- Android: le dimensioni del buffer audio dal microfono sono diminuite da 800 ms a 100 ms per migliorare la latenza.
- Android: è stato risolto [un problema con](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) l'emulatore Android x86 in Android Studio.
- JavaScript: aggiunta del supporto per le aree in Cina con `fromSubscription` l'API. Per informazioni [dettagliate, vedere](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-)qui . 
- JavaScript: aggiungere altre informazioni sugli errori di connessione da NodeJS.
        
**Esempi**
- Unity: l'esempio pubblico di riconoscimento finalità è stato corretto, in cui l'importazione JSON di LUIS ha esito negativo. Per informazioni [dettagliate, vedere](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)qui .
- Python: esempio aggiunto per `Language ID` . Per informazioni [dettagliate, vedere](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)qui .
    
**Test abbreviati di Covid19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali dei dispositivi come di consueto. Ad esempio, non è stato possibile testare l'input del microfono e l'output del parlante in Linux, iOS e macOS. In queste piattaforme non sono state apportate modifiche che potrebbero avere interrotto nulla e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br> Grazie per il supporto continuo. Come sempre, inviare domande o commenti su [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) [o Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Rimanere integri.

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: versione di febbraio 2020

**Nuove funzionalità**

 - Aggiunta di pacchetti Python per supportare la nuova versione 3.8 di Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 support (C++, C#, Java, Python).
   > [!NOTE] 
   > I clienti devono configurare OpenSSL in base [a queste istruzioni.](./how-to-configure-openssl-linux.md)
 - Supporto arm32 linux per Debian e Ubuntu.
 - DialogServiceConnector supporta ora un parametro facoltativo "ID bot" in BotFrameworkConfig. Questo parametro consente l'uso di più Direct Line Speech bot con una singola risorsa voce di Azure. Senza il parametro specificato, verrà usato il bot predefinito ,come determinato dalla pagina Direct Line Speech di configurazione del canale.
 - DialogServiceConnector ha ora una proprietà SpeechActivityTemplate. Il contenuto di questa stringa JSON verrà usato da Direct Line Speech per precompilare un'ampia gamma di campi supportati in tutte le attività che raggiungono un bot Direct Line Speech, incluse le attività generate automaticamente in risposta a eventi come il riconoscimento vocale.
 - TTS usa ora la chiave di sottoscrizione per l'autenticazione, riducendo la latenza del primo byte del primo risultato di sintesi dopo la creazione di un sintetizzatore.
 - Aggiornamento dei modelli di riconoscimento vocale per 19 impostazioni locali per una riduzione media della frequenza degli errori di parola del 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). I nuovi modelli apportano miglioramenti significativi in più domini, inclusi gli scenari di dettatura, Call-Center trascrizione e indicizzazione video.

**Correzioni di bug**

 - Correzione del bug per cui Il trascrittore di conversazione non attendeva correttamente nelle API JAVA 
 - Correzione dell'emulatore Android x86 per il problema di [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) di Xamarin
 - Aggiungi mancante (Get| Set)Metodi delle proprietà su AudioConfig
 - Correzione di un bug TTS per cui non è stato possibile arresto di audioDataStream quando la connessione non riesce
 - L'uso di un endpoint senza un'area causerebbe errori USP per il traduttore di conversazioni
 - La generazione di ID nelle applicazioni di Windows universale ora usa un algoritmo GUID univoco appropriato. In precedenza e per impostazione predefinita era un'implementazione con stub che spesso produceva collisioni su grandi set di interazioni.
 
 **Esempi**
 
 - Esempio unity per l'uso di Speech SDK con il [microfono Unity e lo streaming in modalità push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Altre modifiche**

 - [Documentazione di configurazione OpenSSL aggiornata per Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: versione 2020-gennaio

**Nuove funzionalità**

- Conversazione su più dispositivi: connettere più dispositivi alla stessa conversazione vocale o basata su testo e, facoltativamente, tradurre i messaggi inviati tra di essi. Per altre informazioni, [vedere questo articolo.](multi-device-conversation.md) 
- È stato aggiunto il supporto per il riconoscimento delle parole chiave per il pacchetto Android .aar e il supporto per le versioni x86 e x64. 
- Objective-C: `SendMessage` e metodi aggiunti `SetMessageProperty` `Connection` all'oggetto . Vedere la documentazione [qui.](/objectivec/cognitive-services/speech/spxconnection)
- L'API C++ TTS supporta ora come input di testo di sintesi, eliminando la necessità di convertire una stringa wstring in stringa prima di `std::wstring` passarla all'SDK. Vedere i dettagli [qui](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [l'ID lingua e](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) la configurazione del [linguaggio](./how-to-specify-source-language.md?pivots=programming-language-csharp) di origine sono ora disponibili.
- JavaScript: aggiunta di una funzionalità `Connection` all'oggetto per passare messaggi personalizzati dal servizio Voce come callback `receivedServiceMessage` .
- JavaScript: aggiunta del supporto per semplificare l'uso con contenitori e `FromHost API` cloud sovrani sul sito. Vedere la documentazione [qui.](speech-container-howto.md)
- JavaScript: ora si rispetta `NODE_TLS_REJECT_UNAUTHORIZED` grazie a un contributo di [orgads.](https://github.com/orgads) Vedere i dettagli [qui](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Modifiche di rilievo**

- `OpenSSL` è stato aggiornato alla versione 1.1.1b ed è collegato in modo statico alla libreria core di Speech SDK per Linux. Ciò può causare un'interruzione se la posta in arrivo non è `OpenSSL` stata installata nella directory nel `/usr/lib/ssl` sistema. Per risolvere [il problema,](how-to-configure-openssl-linux.md) vedere la documentazione di Speech SDK.
- Il tipo di dati restituito per C# è stato modificato da a per consentire l'accesso a quando i dati vocali sono più lunghi `WordLevelTimingResult.Offset` `int` di `long` `WordLevelTimingResults` 2 minuti.
- `PushAudioInputStream` e `PullAudioInputStream` ora inviano informazioni sull'intestazione wav al servizio Voce in base `AudioStreamFormat` a , facoltativamente specificate al momento della creazione. I clienti devono ora usare il [formato di input audio supportato.](how-to-use-audio-input-streams.md) Qualsiasi altro formato otterrà risultati di riconoscimento non ottimali o potrebbe causare altri problemi. 

**Correzioni di bug**

- Vedere `OpenSSL` l'aggiornamento in Modifiche di rilievo sopra. Sono stati risolti sia un arresto anomalo intermittente che un problema di prestazioni (problemi di blocco con carico elevato) in Linux e Java. 
- Java: sono stati apportati miglioramenti alla chiusura degli oggetti in scenari di concorrenza elevata.
- È stato ristrutturato il pacchetto NuGet. Sono state rimosse le tre copie di e nelle cartelle lib, rendendo il pacchetto NuGet più piccolo e veloce da scaricare e sono state aggiunte le intestazioni necessarie per compilare alcune `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` app native C++.
- Sono stati corretti esempi di avvio [rapido qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Questi sono stati uscendo senza visualizzare l'eccezione "microfono non trovato" in Linux, macOS, Windows.
- Correzione dell'arresto anomalo dell'SDK con risultati di riconoscimento vocale lunghi in determinati percorsi di codice come [questo esempio.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Correzione dell'errore di distribuzione dell'SDK nell'ambiente dell'app Web di Azure per [risolvere questo problema del cliente.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Correzione di un errore TTS durante l'uso di `<voice>` più tag o tag per risolvere questo problema del `<audio>` [cliente.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Correzione di un errore TTS 401 quando l'SDK viene ripristinato dalla sospensione.
- JavaScript: correzione di un'importazione circolare di dati audio grazie a un contributo di [euirim](https://github.com/euirim). 
- JavaScript: aggiunta del supporto per l'impostazione delle proprietà del servizio, come aggiunto nella versione 1.7.
- JavaScript: risolto un problema a causa del quale un errore di connessione potrebbe causare tentativi di riconnessione websocket continui e non riusciti.

**Esempi**

- Aggiunta dell'esempio di riconoscimento delle parole chiave per Android [qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)
- È stato aggiunto l'esempio TTS per lo scenario server [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Sono state aggiunte guide introduttive alle conversazioni multi-dispositivo per C# e C++ [qui.](quickstarts/multi-device-conversation.md)

**Altre modifiche**

- Dimensioni ottimizzate della libreria principale dell'SDK in Android.
- SDK nella versione 1.9.0 e versioni successiva supporta entrambi i tipi e nel campo della versione della firma `int` `string` vocale per Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: versione di novembre 2019

**Nuove funzionalità**

- Aggiunta di `FromHost()` un'API per semplificare l'uso con i contenitori e i cloud sovrani in uso.
- Aggiunta dell'Rilevamento lingua di origine automatica per il riconoscimento vocale (in Java e C++)
- Aggiunta `SourceLanguageConfig` dell'oggetto per il riconoscimento vocale, usato per specificare le lingue di origine previste (in Java e C++)
- Aggiunta `KeywordRecognizer` del supporto per Windows (UWP), Android e iOS tramite i pacchetti NuGet e Unity
- Aggiunta dell'API Java Conversazione remota per eseguire la trascrizione della conversazione in batch asincroni.

**Modifiche di rilievo**

- Funzionalità di trascrittore di conversazione spostate nello spazio dei nomi `Microsoft.CognitiveServices.Speech.Transcription` .
- Parti dei metodi di trascrittore di conversazione vengono spostate in una nuova `Conversation` classe.
- È stato eliminato il supporto per iOS a 32 bit (ARMv7 e x86)

**Correzioni di bug**

- Correzione dell'arresto anomalo se si `KeywordRecognizer` usa local senza una chiave di sottoscrizione valida del servizio Voce

**Esempi**

- Esempio Xamarin per `KeywordRecognizer`
- Esempio unity per `KeywordRecognizer`
- Esempi di C++ e Java per l'origine automatica Rilevamento lingua.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: versione di settembre 2019

**Nuove funzionalità**

- Aggiunta del supporto beta per Xamarin in piattaforma UWP (Universal Windows Platform) (UWP), Android e iOS
- Aggiunta del supporto iOS per Unity
- Aggiunta `Compressed` del supporto di input per ALaw, Mulaw, FLAC in Android, iOS e Linux
- Aggiunta `SendMessageAsync` nella classe per `Connection` l'invio di un messaggio al servizio
- Aggiunta `SetMessageProperty` nella classe per `Connection` l'impostazione della proprietà di un messaggio
- TTS ha aggiunto binding per Java (JRE e Android), Python, Swift e Objective-C
- TTS ha aggiunto il supporto per la riproduzione per macOS, iOS e Android.
- Sono state aggiunte informazioni sul "limite di parole" per TTS.

**Correzioni di bug**

- Correzione del problema di build IL2CPP in Unity 2019 per Android
- È stato risolto un problema con intestazioni in formato non valido nell'input del file wav elaborato in modo non corretto
- È stato risolto un problema per cui gli UUID non sono univoci in alcune proprietà di connessione
- Correzione di alcuni avvisi sugli identificatori di supporto dei valori Null nelle associazioni Swift (potrebbero essere necessarie piccole modifiche al codice)
- Correzione di un bug che causava la chiusura non corretta delle connessioni Websocket in caso di carico di rete
- È stato risolto un problema in Android che a volte causava ID di impressioni duplicati usati da `DialogServiceConnector`
- Miglioramenti alla stabilità delle connessioni tra interazioni a più turni e alla segnalazione di errori (tramite `Canceled` eventi) quando si verificano con `DialogServiceConnector`
- `DialogServiceConnector` L'avvio della sessione ora fornirà correttamente gli eventi, anche quando si chiama `ListenOnceAsync()` durante un'attività `StartKeywordRecognitionAsync()`
- È stato corretto un arresto anomalo associato `DialogServiceConnector` alle attività ricevute

**Esempi**

- Avvio rapido per Xamarin
- Aggiornamento delle informazioni di avvio rapido CPP con Linux ARM64
- Guida introduttiva di Unity aggiornata con le informazioni su iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: versione 2019-giugno

**Esempi**

- Esempi di avvio rapido per sintesi vocale in UWP e Unity
- Esempio di avvio rapido per Swift in iOS
- Esempi di Unity per speech & Riconoscimento finalità and Translation
- Esempi di avvio rapido aggiornati per `DialogServiceConnector`

**Miglioramenti/modifiche**

- Spazio dei nomi dialog:
  - `SpeechBotConnector` è stata rinominata `DialogServiceConnector`
  - `BotConfig` è stata rinominata `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` è stato mappato di nuovo a `DialogServiceConfig::FromBotSecret()`
  - Tutti i Direct Line Speech esistenti continuano a essere supportati dopo la ridenominazione
- Aggiornare l'adattatore REST TTS per supportare il proxy, connessione permanente
- Migliorare il messaggio di errore quando viene passata un'area non valida
- Swift/Objective-C:
  - Segnalazione degli errori migliorata: i metodi che possono generare un errore sono ora presenti in due versioni: una che espone un oggetto per la gestione degli errori e una che genera `NSError` un'eccezione. I primi sono esposti a Swift. Questa modifica richiede adattamenti al codice Swift esistente.
  - Gestione degli eventi migliorata

**Correzioni di bug**

- Correzione per TTS: dove `SpeakTextAsync` future è stato restituito senza attendere il completamento del rendering dell'audio
- Correzione per il marshalling delle stringhe in C# per abilitare il supporto completo del linguaggio
- Correzione del problema dell'app .NET Core per il caricamento della libreria core con il framework di destinazione net461 negli esempi
- Correzione di problemi occasionali relativi alla distribuzione di librerie native nella cartella di output negli esempi
- Correzione per la chiusura affidabile del Socket Web
- Correzione di un possibile arresto anomalo durante l'apertura di una connessione con carico elevato in Linux
- Correzione dei metadati mancanti nel bundle del framework per macOS
- Correzione dei problemi relativi `pip install --user` a in Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzioni di bug**

- Correzione di FromSubscription se usato con la trascrizione della conversazione.
- Correzione di un bug nell'spotting delle parole chiave per gli assistenti vocali.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: versione 2019-maggio

**Nuove funzionalità**

- L'spotting delle parole chiave (KWS) è ora disponibile per Windows e Linux. La funzionalità KWS potrebbe funzionare con qualsiasi tipo di microfono, il supporto KWS ufficiale, tuttavia, è attualmente limitato agli array di microfoni presenti nell'hardware Azure Kinect DK o speech devices SDK.
- La funzionalità hint per frasi è disponibile tramite l'SDK. Per altre informazioni, vedere [qui](./get-started-speech-to-text.md).
- La funzionalità di trascrizione della conversazione è disponibile tramite l'SDK. Vedere [qui](./conversation-transcription.md).
- Aggiungere il supporto per gli assistenti vocali usando Direct Line Speech canale.

**Esempi**

- Sono stati aggiunti esempi per nuove funzionalità o nuovi servizi supportati dall'SDK.

**Miglioramenti/modifiche**

- Sono state aggiunte varie proprietà di riconoscimento per modificare il comportamento del servizio o i risultati del servizio , ad esempio mascherando contenuto volgare e altre.
- È ora possibile configurare il riconoscimento tramite le proprietà di configurazione standard, anche se è stato creato il riconoscimento `FromEndpoint` .
- Objective-C: `OutputFormat` la proprietà è stata aggiunta a `SPXSpeechConfiguration` .
- L'SDK supporta ora Debian 9 come distribuzione Linux.

**Correzioni di bug**

- Risolto un problema a causa del quale la risorsa parlante era stata distrutta troppo presto nella sintesi vocale.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Impedire al Web pack di caricare https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: versione di aprile 2019

**Nuove funzionalità**

- L'SDK supporta ora il servizio di sintesi vocale come versione beta. È supportato in Windows e Linux Desktop da C++ e C#. Per altre informazioni, vedere [la panoramica della sintesi vocale.](text-to-speech.md#get-started)
- L'SDK supporta ora i file audio MP3 e Opus/OGG come file di input di flusso. Questa funzionalità è disponibile solo in Linux da C++ e C# ed è attualmente in versione beta (altre informazioni sono [disponibili qui).](how-to-use-codec-compressed-audio-input-streams.md)
- Speech SDK per Java, .NET Core, C++ e Objective-C ha ottenuto il supporto macOS. Il supporto di Objective-C per macOS è attualmente in versione beta.
- iOS: Speech SDK per iOS (Objective-C) è ora pubblicato anche come CocoaPod.
- JavaScript: supporto per microfono non predefinito come dispositivo di input.
- JavaScript: supporto proxy per Node.js.

**Esempi**

- Sono stati aggiunti esempi per l'uso di Speech SDK con C++ e con Objective-C in macOS.
- Sono stati aggiunti esempi che illustrano l'utilizzo del servizio di sintesi vocale.

**Miglioramenti/modifiche**

- Python: le proprietà aggiuntive dei risultati del riconoscimento sono ora esposte tramite la `properties` proprietà .
- Per un supporto aggiuntivo per lo sviluppo e il debug, è possibile reindirizzare le informazioni di diagnostica e registrazione dell'SDK in un file di log (altre informazioni [sono disponibili qui).](how-to-use-logging.md)
- JavaScript: migliorare le prestazioni di elaborazione audio.

**Correzioni di bug**

- Mac/iOS: è stato corretto un bug che ha causato una lunga attesa quando non è stata stabilita una connessione al servizio Voce.
- Python: miglioramento della gestione degli errori per gli argomenti nei callback Python.
- JavaScript: correzione della segnalazione di stato errata per il riconoscimento vocale terminata in RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: aggiornamento di febbraio 2019

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzione di bug**

- Correzione di una perdita di memoria quando si usa l'input del microfono. L'input basato su flusso o file non è interessato.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: versione 2019-febbraio

**Nuove funzionalità**

- Speech SDK supporta la selezione del microfono di input tramite la `AudioConfig` classe . In questo modo è possibile trasmettere dati audio al servizio Voce da un microfono non predefinito. Per altre informazioni, vedere la documentazione che descrive la [selezione del dispositivo di input audio.](how-to-select-audio-input-devices.md) Questa funzionalità non è ancora disponibile in JavaScript.
- Speech SDK supporta ora Unity in versione beta. Inviare commenti e suggerimenti tramite la sezione relativa al problema nel [repository di esempio GitHub.](https://aka.ms/csspeech/samples) Questa versione supporta Unity in Windows x86 e x64 (applicazioni desktop o per la piattaforma UWP) e Android (ARM32/64, x86). Altre informazioni sono disponibili nell'[Avvio rapido di Unity](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity).
- Il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata nell'SDK di base.

**Esempi**

I nuovi contenuti seguenti sono disponibili nel [repository di esempio](https://aka.ms/csspeech/samples):

- Esempi aggiuntivi per `AudioConfig.FromMicrophoneInput` .
- Altri esempi di Python per il riconoscimento finalità e la traduzione.
- Esempi aggiuntivi per l'uso `Connection` dell'oggetto in iOS.
- Esempi aggiuntivi per Java per la traduzione con output audio.
- Nuovo esempio per l'uso dell'[API REST di trascrizione batch](batch-transcription.md).

**Miglioramenti/modifiche**

- Python
  - Miglioramento della verifica dei parametri e dei messaggi di errore in `SpeechConfig` .
  - Aggiungere il supporto per `Connection` l'oggetto .
  - Supporto per Python a 32 bit (x86) in Windows.
  - Speech SDK per Python non è incluso nella beta.
- iOS
  - L'SDK è ora basato sull'SDK per iOS versione 12.1.
  - L'SDK supporta ora iOS 9.2 e versioni successive.
  - Miglioramento della documentazione di riferimento e correzione di diversi nomi di proprietà.
- JavaScript
  - Aggiungere il supporto per `Connection` l'oggetto .
  - Aggiunta di file di definizione del tipo per JavaScript in bundle.
  - Supporto iniziale e implementazione per gli hint della frase.
  - Restituzione della raccolta di proprietà con il file JSON del servizio per il riconoscimento.
- Le DLL di Windows contengono ora una risorsa di versione.
- Se si crea un `FromEndpoint` riconoscitore, è possibile aggiungere parametri direttamente all'URL dell'endpoint. Non `FromEndpoint` è possibile configurare il riconoscimento tramite le proprietà di configurazione standard.

**Correzioni di bug**

- Il nome utente proxy e la password proxy vuoti non erano gestiti correttamente. Con questa versione, se si imposta il nome utente proxy e la password proxy su una stringa vuota, non verranno inviati quando ci si connette al proxy.
- Gli ID sessione creati dall'SDK non erano sempre realmente casuali per alcune lingue&nbsp;/ ambienti. Aggiunta dell'inizializzazione casuale del generatore per risolvere questo problema.
- Gestione migliorata del token di autorizzazione. Se si vuole usare un token di autorizzazione, specificare in e `SpeechConfig` lasciare vuota la chiave di sottoscrizione. Creare quindi il sistema di riconoscimento come di consueto.
- In alcuni casi `Connection` l'oggetto non è stato rilasciato correttamente. Il problema è stato risolto.
- L'esempio di JavaScript è stato corretto per supportare l'output audio per la sintesi della traduzione anche in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Attivazione della fine del flusso in corrispondenza di turn.end e non di speech.end.
- Correzione di un bug nel data pump audio che non pianificava l'invio successivo in caso di errore dell'invio corrente.
- Correzione del riconoscimento continuo con il token di autenticazione.
- Correzione di bug per sistemi di riconoscimento/endpoint diversi.
- Miglioramenti alla documentazione.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: versione di dicembre 2018

**Nuove funzionalità**

- Python
  - La versione beta del supporto di Python (versione 3.5 e successive) è disponibile con questa versione. Per altre informazioni, vedere qui](quickstart-python.md).
- JavaScript
  - Speech SDK per JavaScript è ora open source. Il codice sorgente è disponibile in [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - È ora supportato Node.js. Altre informazioni sono reperibili [qui](./get-started-speech-to-text.md).
  - È stata rimossa la restrizione di lunghezza per le sessioni audio. La riconnessione avverrà automaticamente.
- Oggetto `Connection`
  - Da `Recognizer` è possibile accedere a un `Connection` oggetto . Questo oggetto consente di avviare in modo esplicito la connessione al servizio e di aggiungere una sottoscrizione per connettere e disconnettere gli eventi.
    Questa funzionalità non è ancora disponibile in JavaScript e Python.
- Supporto per Ubuntu 18.04.
- Android
  - Supporto di ProGuard abilitato durante la generazione di APK.

**Miglioramenti**

- Miglioramenti nell'uso dei thread interni, con riduzione del numero di thread, blocchi e mutex.
- Miglioramento della segnalazione errori e delle informazioni sugli errori. In molti casi, i messaggi di errore non sono stati propagati fino all'uscita.
- Aggiornamento delle dipendenze di sviluppo in JavaScript per usare i moduli aggiornati.

**Correzioni di bug**

- Correzione delle perdite di memoria a causa di una mancata corrispondenza del tipo in `RecognizeAsync` .
- In alcuni casi perdita delle eccezioni.
- Correzione della perdita di memoria negli argomenti degli eventi di conversione.
- Correzione di un problema di blocco per la riconnessione in sessioni a esecuzione prolungata.
- Risolto un problema che poteva causare la mancanza del risultato finale per le traduzioni non riuscite.
- C#: se un'operazione non era attesa nel thread principale, era possibile che il riconoscitore fosse eliminato prima del completamento `async` dell'attività asincrona.
- Java: è stato risolto un problema che causava un arresto anomalo della macchina virtuale Java.
- Objective-C: corretto il mapping delle enumerazioni; È stato restituito RecognizedIntent anziché `RecognizingIntent` .
- JavaScript: impostare il formato di output predefinito su "simple" in `SpeechConfig` .
- JavaScript: rimozione dell'incoerenza tra le proprietà nell'oggetto config in JavaScript e in altri linguaggi.

**Esempi**

- Sono stati aggiornati e corretti diversi esempi ,ad esempio voci di output per la traduzione e così via.
- Aggiunta di esempi di Node.js nel [repository degli esempi](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nuove funzionalità**

- Supporto per Android x86/x64.
- Supporto proxy: nell'oggetto è ora possibile chiamare una funzione per impostare le informazioni `SpeechConfig` sul proxy (nome host, porta, nome utente e password). Questa funzionalità non è ancora disponibile in iOS.
- Messaggi e codice di errore migliorati. Se un riconoscimento ha restituito un errore, l'elemento `Reason` (nell'evento annullato) o `CancellationDetails` (nel risultato del riconoscimento) è già stato impostato su `Error`. L'evento annullato contiene ora due membri aggiuntivi: `ErrorCode` e `ErrorDetails`. Se il server ha restituito l'errore corredato da informazioni aggiuntive, le informazioni saranno ora disponibili nei nuovi membri.

**Miglioramenti**

- È stata aggiunta una verifica supplementare nella configurazione del sistema di riconoscimento ed è stato aggiunto un nuovo messaggio di errore.
- È stata migliorata la gestione di un silenzio prolungato nel mezzo di un file audio.
- Pacchetto NuGet: per i progetti .NET Framework, impedisce la compilazione con una configurazione AnyCPU.

**Correzioni di bug**

- Sono state corrette varie eccezioni rilevate nei sistemi di riconoscimento. Inoltre, le eccezioni vengono intercette e convertite in `Canceled` eventi.
- È stata corretta una perdita di memoria nella gestione delle proprietà.
- È stato corretto un bug per il quale un file di input audio può determinare l'arresto anomalo del sistema di riconoscimento.
- È stato risolto un bug per il quale si continuano a ricevere eventi anche dopo un evento di arresto della sessione.
- Sono state corrette alcune race condition nel threading.
- È stato corretto un problema di compatibilità iOS che può determinare un arresto anomalo.
- Sono stati apportati miglioramenti di stabilità per il supporto di microfoni Android.
- È stato risolto un bug per il quale un sistema di riconoscimento in JavaScript ignorava la lingua di riconoscimento.
- Correzione di un bug che impedisce `EndpointId` l'impostazione di (in alcuni casi) in JavaScript.
- Modifica dell'ordine dei parametri in AddIntent in JavaScript e aggiunta della `AddIntent` firma JavaScript mancante.

**Esempi**

- Sono stati aggiunti esempi C++ e C# per l'utilizzo del flusso pull e push nel [repository di esempio](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Miglioramenti apportati alla stabilità e correzioni di bug:

- Risoluzione di un potenziale errore irreversibile dovuto a una race condition nell'eliminazione dello strumento di riconoscimento.
- Correzione di un potenziale errore irreversibile quando si verificano proprietà non impostate.
- Aggiunta di un ulteriore controllo di errori e parametri.
- Objective-C: risoluzione di un potenziale errore irreversibile causato dall'override del nome in NSString.
- Objective-C: modifica della visibilità dell'API
- JavaScript: correzioni relative a eventi e payload correlati.
- Miglioramenti alla documentazione.

Al [repository di esempi](https://aka.ms/csspeech/samples) è stato aggiunto un nuovo esempio per JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Speech SDK 1.0.0 di Servizi cognitivi: versione di settembre 2018

**Nuove funzionalità**

- Supporto per Objective-C in iOS. Vedere la [Guida introduttiva su Objective-C per iOS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone).
- Supporto per JavaScript nel browser. Vedere la [Guida introduttiva su JavaScript](./get-started-speech-to-text.md).

**Modifiche di rilievo**

- Con questa versione vengono introdotte alcune modifiche di rilievo.
  Per [informazioni dettagliate, vedere](https://aka.ms/csspeech/breakingchanges_1_0_0) questa pagina.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Speech SDK 0.6.0 di Servizi cognitivi: versione di agosto 2018

**Nuove funzionalità**

- Le app UWP compilate con Speech SDK ora possono superare il Kit di certificazione app Windows (WACK).
  Consultare la [Guida introduttiva della piattaforma UWP](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp).
- Supporto per .NET Standard 2.0 in Linux (Ubuntu 16.04 x64).
- Sperimentale: supporto di Java 8 in Windows (64 bit) e Linux (Ubuntu 16.04 x64).
  Consultare la [Guida introduttiva di Java Runtime Environment](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Modifica funzionale**

- Informazioni aggiuntive dettagliate sull'errore in caso di errori di connessione.

**Modifiche di rilievo**

- In Java (Android), la funzione `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` non richiede più un parametro di percorso. Il percorso viene ora rilevato automaticamente in tutte le piattaforme supportate.
- La funzione di accesso get della proprietà `EndpointUrl` in Java e C# è stata rimossa.

**Correzioni di bug**

- In Java, il risultato di sintesi audio sul sistema di riconoscimento di traduzione è ora implementato.
- È stato risolto un bug che potrebbe causare un maggior numero di socket aperti e inutilizzati e thread inattivi.
- È stato risolto un problema in cui un riconoscimento con esecuzione prolungata terminava la trasmissione a metà.
- Correzione di una race condition nel sistema di riconoscimento di arresto.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Speech SDK 0.5.0 di Servizi cognitivi: versione di luglio 2018

**Nuove funzionalità**

- Supporto della piattaforma Android (API 23: Android Marshmallow 6.0 o versione successiva). Consultare la [Guida introduttiva di Android](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android).
- Supporto di .NET Standard 2.0 in Windows. Consultare la [Guida introduttiva di .NET Core](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Sperimentale: supporto di UWP in Windows (versione 1709 o successiva).
  - Consultare la [Guida introduttiva della piattaforma UWP](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).
  - Si noti che le app UWP compilate con Speech SDK non superano ancora il Kit di certificazione app Windows (WACK).
- Supporto del riconoscimento a esecuzione prolungata con riconnessione automatica.

**Modifiche funzionali**

- `StartContinuousRecognitionAsync()` supporta il riconoscimento a esecuzione prolungata.
- Il risultato del riconoscimento contiene più campi. Scostamento da inizio audio e durata (entrambi in tick) del testo riconosciuto, valori aggiuntivi che rappresentano lo stato di riconoscimento, ad esempio `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- Supporto del token di autorizzazione per la creazione di istanze di factory.

**Modifiche di rilievo**

- Eventi di riconoscimento: `NoMatch` il tipo di evento è stato unito all'evento. `Error`
- SpeechOutputFormat in C# è stato rinominato in `OutputFormat` per rimanere allineato a C++.
- Il tipo restituito di alcuni metodi dell'interfaccia `AudioInputStream` è stato leggermente modificato:
  - In Java, il metodo `read` restituisce ora `long` invece di `int`.
  - In C#, il metodo `Read` restituisce ora `uint` invece di `int`.
  - In C++, i metodi `Read` e `GetFormat` restituiscono ora `size_t` invece di `int`.
- C++: le istanze di flussi di input audio possono ora essere passate solo come `shared_ptr`.

**Correzioni di bug**

- Sono stati corretti i valori restituiti errati nel risultato alla scadenza di `RecognizeAsync()`.
- È stata rimossa la dipendenza dalle librerie di Media Foundation in Windows. L'SDK usa ora le API Audio Core.
- Correzione della documentazione: è stata aggiunta una pagina relativa alle [aree](regions.md) per descrivere le aree supportate.

**Problema noto**

- Speech SDK per Android non segnala i risultati della sintesi vocale per la traduzione. Questo problema verrà risolto nella prossima versione.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Speech SDK di Servizi cognitivi 0.4.0: versione di giugno 2018

**Modifiche funzionali**

- AudioInputStream

  Uno strumento di riconoscimento può ora usare un flusso come origine audio. Per informazioni dettagliate, vedere la [guida pratica](how-to-use-audio-input-streams.md).

- Formato dettagliato dell'output

  Durante la creazione di uno `SpeechRecognizer`, è possibile richiedere un formato di output `Detailed` o `Simple`. Il `DetailedSpeechRecognitionResult` contiene punteggio di attendibilità, testo riconosciuto, forma lessicale non elaborata, forma normalizzata e forma normalizzata con messaggi dal contenuto volgare mascherati.

**Modifica di rilievo**

- Modifica da `SpeechRecognitionResult.RecognizedText` a `SpeechRecognitionResult.Text` in linguaggio C#.

**Correzioni di bug**

- È stato corretto un possibile problema di callback nel livello USP durante l'arresto.
- Se un riconoscimento usa un file di input audio, significa che esso contiene l'handle del file più a lungo rispetto al necessario.
- Sono stati rimossi diversi deadlock tra message pump e strumento di riconoscimento.
- Attiva un risultato `NoMatch` quando la risposta dal servizio è scaduta.
- Le librerie di Media Foundation in Windows sono a caricamento ritardato. Questa libreria è richiesta solo per l'input del microfono.
- La velocità di caricamento dei dati audio è limitata a circa due volte la velocità dell'audio originale.
- In Windows, gli assembly C# .NET hanno ora un nome sicuro.
- Correzione della documentazione: `Region` è un'informazione obbligatoria per la creazione di un riconoscimento.

Sono stati aggiunti altri esempi che sono costantemente in corso l'aggiornamento. Per il set di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Speech SDK di Servizi cognitivi 0.2.12733: versione di maggio 2018

Questa è la prima versione di anteprima pubblica di Speech SDK di Servizi cognitivi.
