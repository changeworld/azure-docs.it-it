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
ms.date: 04/20/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: f97ecedd4088a825b9ec5a076f4da70df92a3269
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775404"
---
# <a name="speech-service-release-notes"></a>Note sulla versione del Servizio di riconoscimento vocale

## <a name="text-to-speech-2021-april-release"></a>Versione di sintesi vocale 2021 -aprile

**Neural TTS è disponibile in 21 aree**

- **Sono state aggiunte dodici nuove** aree: Neural TTS è ora disponibile in queste nuove 12 aree: , , , , , `Japan East` , , , `Japan West` , , `Korea Central` `North Central US` `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` `West US 2` . Vedere [qui](regions.md#text-to-speech) per l'elenco completo delle 21 aree supportate.

## <a name="text-to-speech-2021-march-release"></a>Versione di sintesi vocale 2021-marzo

**Nuove lingue e voci aggiunte per la TTS neurale**

-  Sono state introdotte sei nuove lingue: 12 nuove voci in 6 nuove impostazioni locali sono state aggiunte all'elenco delle lingue neurali TTS: Nia in `cy-GB` Catalogna (Regno Unito), Aled in `cy-GB` Irlanda (Regno Unito), Rosa in `en-PH` inglese (Filippine), James in `en-PH` inglese (Filippine), Charline in `fr-BE` Francese (Spagna), Gerard in `fr-BE` Francese (Spagna), Dena in Olandese `nl-BE` (Spagna), Arnaud in Olandese `nl-BE` (Spagna), Polina `uk-UA` in Germania (Germania), Ostap in `uk-UA` Spagna (Germania), Uzma in `ur-PK` Urdu (Spagna), Asad in `ur-PK` Urdu (Spagna).

-  Cinque lingue, dall'anteprima alla versione disponibile a livello generale: 10 voci in 5 impostazioni locali introdotte nel mese di novembre 2020 sono ora disponibili a livello generale: Kert in `et-EE` Estone (Irlanda), Colm in Irlanda, Nils in Lituano `ga-IE` `lv-LV` (Portogallo), Leonas in `lt-LT` Lituano (Lituano), Regno Unito `mt-MT` (Irlanda).

- Aggiunta di una nuova voce per il francese **(Canada):** è disponibile una nuova voce Antoine per il francese `fr-CA` (Canada).

- **Miglioramento della** qualità : riduzione del tasso di errore di pronuncia in ungherese `hu-HU` - 48,17%, norvegese `nb-NO` - 52,76%, olandese `nl-NL` (Paesi Bassi) - 22,11%.

Con questa versione è ora possibile supportare un totale di 142 voci neurali in 60 lingue/impostazioni locali. Sono inoltre disponibili oltre 70 voci standard in 49 lingue/impostazioni locali. Per [l'elenco completo,](language-support.md#text-to-speech) vedere Supporto per le lingue.

**Ottenere eventi di posizione facciale per animare i caratteri**

[L'evento Viseme](how-to-speech-synthesis-viseme.md) viene aggiunto a Neural TTS, che consente agli utenti di ottenere la sequenza di posizione facciale e la durata dalla sintesi vocale. Il viseme può essere usato per controllare il movimento dei modelli avatar 2D e 3D, perfettamente corrispondenti ai movimenti della voce sintetizzata. A questo punto, viseme funziona solo per la voce en-US-AriaNeural.

**Aggiungere l'elemento bookmark in Speech Synthesis Markup Language (SSML)**

[L'elemento bookmark](speech-synthesis-markup.md#bookmark-element) consente di inserire marcatori personalizzati in SSML per ottenere l'offset di ogni marcatore nel flusso audio. Può essere usato per fare riferimento a una posizione specifica nella sequenza di testo o tag.

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK 1.16.0: versione di marzo 2021

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable per Visual Studio 2015, 2017 e 2019. Scaricarla [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

#### <a name="new-features"></a>Nuove funzionalità

- **C++/C#/Java/Python:** è stato spostato alla versione più recente di GStreamer (1.18.3) per aggiungere il supporto per la trascrittura di qualsiasi formato multimediale in Windows, Linux e Android. Vedere la documentazione [qui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python:** aggiunta del supporto per la decodifica dell'audio compresso TTS/sintetizzato nell'SDK. Se si imposta il formato audio di output su PCM e GStreamer è disponibile nel sistema, l'SDK richiederà automaticamente audio compresso al servizio per risparmiare larghezza di banda e decodificare l'audio nel client. È possibile impostare `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` su `false` per disabilitare questa funzionalità. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid) [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python)
- **JavaScript:** Node.js gli utenti possono ora usare [ `AudioConfig.fromWavFileInput` l'API](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_). Questo risolve il [problema di GitHub #252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252).
- **C++/C#/Java/Objective-C/Python:** aggiunta del metodo per TTS per `GetVoicesAsync()` restituire tutte le voci di sintesi disponibili. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods) [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)e [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods)
- **C++/C#/Java/JavaScript/Objective-C/Python:** aggiunta dell'evento per la sintesi vocale/TTS per restituire l'animazione `VisemeReceived` con viseme sincrono. Vedere la documentazione [qui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python:** aggiunta `BookmarkReached` dell'evento per TTS. È possibile impostare segnalibri nell'SSML di input e ottenere gli offset audio per ogni segnalibro. Vedere la documentazione [qui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java:** aggiunta del supporto per le API di riconoscimento del parlante. Dettagli [qui.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/JavaScript/Objective-C/Python:** sono stati aggiunti due nuovi formati audio di output con il contenitore WebM per TTS (Webm16Khz16BitMonoOpus e Webm24Khz16BitMonoOpus). Si tratta di formati migliori per lo streaming audio con il codec Opus. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable) [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat) [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)
- **C++/C#/Java:** aggiunta del supporto per il recupero del profilo vocale per lo scenario di riconoscimento del parlante. Dettagli per [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer) [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)e [Java.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/Objective-C/Python:** aggiunta del supporto per la libreria condivisa separata per il microfono audio e il controllo voce. Ciò consente di usare l'SDK in ambienti che non hanno dipendenze della libreria audio necessarie.
- **Objective-C/Swift:** aggiunta del supporto per il framework del modulo con intestazione umbrella. Ciò consente di importare Speech SDK come modulo nelle app iOS/Mac Objective-C/Swift. Questo risolve il [problema di GitHub #452](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452).
- **Python:** è stato aggiunto il supporto [per Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) ed è stato eliminato il supporto per Python 3.5 per la fine del ciclo di vita di Python [per la versione 3.5.](https://devguide.python.org/devcycle/#end-of-life-branches)

**Problemi noti**

- **C++/C#/Java:** non è possibile usare un oggetto per accedere a un'comandi personalizzati e si verifica `DialogServiceConnector` invece un errore di `CustomCommandsConfig` connessione. Per risolvere questo problema, aggiungere manualmente l'ID applicazione alla richiesta con `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` . Il comportamento previsto `CustomCommandsConfig` di verrà ripristinato nella prossima versione.

#### <a name="improvements"></a>Miglioramenti

- Nell'ambito del lavoro in più versioni per ridurre l'utilizzo della memoria e il footprint del disco di Speech SDK, i file binari Android sono ora più piccoli del 3-5%.
- Miglioramento dell'accuratezza, della leggibilità e delle sezioni see-also della documentazione di riferimento di C# [qui](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet).

#### <a name="bug-fixes"></a>Correzioni di bug

- **JavaScript:** le intestazioni di file WAV di grandi dimensioni vengono ora analizzate correttamente (aumenta la sezione di intestazione a 512 byte). Questo risolve il [problema di GitHub #962](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962).
- **JavaScript:** correzione del problema di temporizzazione del microfono se il flusso del microfono termina prima di arrestare il riconoscimento, correggendo un problema di riconoscimento vocale che non funziona in Firefox.
- **JavaScript:** ora la promessa di inizializzazione viene gestita correttamente quando il browser forza lo spegnimento del microfono prima del completamento di turnOn.
- **JavaScript:** la dipendenza url è stata sostituita con url-parse. Questo risolve il [problema di GitHub #264](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264).
- **Android:** i callback corretti non funzionano quando `minifyEnabled` è impostato su true.
- **C++/C#/Java/Objective-C/Python:** verrà impostato correttamente sull'I/O del socket sottostante per `TCP_NODELAY` TTS per ridurre la latenza.
- **C++/C#/Java/Python/Objective-C/Go:** correzione di un arresto anomalo occasionale quando il riconoscimento è stato eliminato subito dopo l'avvio di un riconoscimento.
- **C++/C#/Java:** correzione di un arresto anomalo occasionale nella distruzione del riconoscimento del parlante.

#### <a name="samples"></a>Esempi

- **JavaScript:** gli [esempi di browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) non richiedono più il download separato del file della libreria JavaScript.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Interfaccia della riga di comando di Voce (nota anche come SPX): versione di marzo 2021

> [!NOTE]
> Per iniziare a usare l'interfaccia della riga di comando del servizio Voce di Azure, [vedere qui](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics). L'interfaccia della riga di comando consente di usare il servizio Voce di Azure senza scrivere codice.

#### <a name="new-features"></a>Nuove funzionalità

- Aggiunta del `spx intent` comando per il riconoscimento delle finalità, sostituendo `spx recognize intent` .
- Il riconoscimento e la finalità possono ora usare le funzioni di Azure per calcolare il tasso di errore delle parole usando `spx recognize --wer url <URL>` .
- Recognize può ora visualizzare i risultati come file VTT usando `spx recognize --output vtt file <FILENAME>` .
- Le informazioni sulla chiave sensibile sono ora nascosti nell'output di debug/dettagliato.
- Aggiunta del controllo dell'URL e del messaggio di errore per il campo del contenuto nella creazione della trascrizione batch.

**Test abbreviato COVID-19**:

Poiché la pandemia in corso continua a richiedere ai tecnici di lavorare da casa, gli script di verifica manuale pre-pandemia sono stati notevolmente ridotti. Viene testato un numero minore di dispositivi con meno configurazioni e la probabilità che si svolgano bug specifici dell'ambiente potrebbe aumentare. La convalida è ancora rigorosa con un ampio set di automazione. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere in buona salute.

## <a name="text-to-speech-2021-february-release"></a>Versione di sintesi vocale 2021-febbraio

**Sintesi vocale neurale ga**

Sintesi vocale neurale è disponibile a livello di ga in febbraio in 13 lingue: cinese (mandarino, semplificato), inglese (Australia), inglese (India), inglese (Regno Unito), inglese (Stati Uniti), francese (Canada), francese (Francia), tedesco (Germania), italiano (Italia), giapponese (Giappone), coreano (Corea), portoghese (Brasile), spagnolo (Messico) e spagnolo (Spagna). Altre informazioni [su Sintesi vocale neurale](custom-neural-voice.md) e [su come usarlo](concepts-guidelines-responsible-deployment-synthetic.md)in modo responsabile. Sintesi vocale neurale richiede la registrazione e Microsoft può limitare l'accesso in base ai criteri di idoneità di Microsoft. Altre informazioni [sull'accesso limitato.](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)  

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0: versione 2021-gennaio

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable Visual Studio 2015, 2017 e 2019. Scaricarla [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Riepilogo delle evidenziazioni**
- Memoria e footprint del disco più piccoli che rende l'SDK più efficiente.
- Formati di output con maggiore fedeltà disponibili per l'anteprima privata della voce neurale personalizzata.
- Riconoscimento finalità può ora ottenere un ritorno maggiore rispetto alla finalità principale, offrendo la possibilità di effettuare una valutazione separata sulla finalità del cliente.
- L'assistente vocale o il bot è ora più facile da configurare ed è possibile arrestare immediatamente l'ascolto ed esercitare un maggiore controllo su come risponde agli errori.
- Miglioramento delle prestazioni del dispositivo grazie alla compressione facoltativa.
- Usare Speech SDK in Windows ARM/ARM64.
- Miglioramento del debug di basso livello.
- La funzionalità di valutazione della pronuncia è ora più ampiamente disponibile.
- Diverse correzioni di bug per risolvere i problemi che l'utente, i clienti più importanti, hanno segnalato in GitHub. Grazie! Mantenere i commenti e i suggerimenti in arrivo.

**Miglioramenti**
- Speech SDK è ora più efficiente e leggero. È stato avviato un impegno in più versioni per ridurre l'utilizzo della memoria e il footprint del disco di Speech SDK. Come primo passaggio è stata apportata una riduzione significativa delle dimensioni dei file nelle librerie condivise nella maggior parte delle piattaforme. Rispetto alla versione 1.14:
  - Le librerie di Windows compatibili con UWP a 64 bit sono di circa il 30% più piccole.
  - Le librerie di Windows a 32 bit non stanno ancora apportando miglioramenti alle dimensioni.
  - Le librerie Linux sono del 20-25% più piccole.
  - Le librerie Android sono del 3-5% più piccole.

**Nuove funzionalità**
- **Tutti:** nuovi formati di output a 48 KHz disponibili per l'anteprima privata della voce neurale personalizzata tramite l'API sintesi vocale TTS: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **Tutto:** la voce personalizzata è anche più facile da usare. Aggiunta del supporto per l'impostazione della voce personalizzata `EndpointId` tramite ([C++,](/cpp/cognitive-services/speech/speechconfig#setendpointid) [C#,](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId) [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Prima di questa modifica, gli utenti vocali personalizzati dovevano impostare l'URL dell'endpoint tramite il `FromEndpoint` metodo . Ora i clienti possono usare `FromSubscription` il metodo come voci pubbliche e quindi fornire l'ID distribuzione impostando `EndpointId` . In questo modo viene semplificata la configurazione di voci personalizzate. 
- **C++/C#/Java/Objective-C/Python:** ottenere più della finalità principale da `IntentRecognizer` . Supporta ora la configurazione del risultato JSON contenente tutte le finalità e non solo la finalità di assegnazione dei punteggi superiore tramite il metodo `LanguageUnderstandingModel FromEndpoint` usando il `verbose=true` parametro uri. Questo risolve il [problema di GitHub #880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Vedere la documentazione aggiornata [qui.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java:** fa in modo che l'assistente vocale o il bot interrompi immediatamente l'ascolto. `DialogServiceConnector` ([C++,](/cpp/cognitive-services/speech/dialog-dialogserviceconnector) [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) ha ora un `StopListeningAsync()` metodo per accompagnare `ListenOnceAsync()` . Ciò interromperà immediatamente l'acquisizione audio e attenderà normalmente un risultato, rendendolo ideale per l'uso con gli scenari di pressione del pulsante "Arresta ora".
- **C++/C#/Java/JavaScript:** consente all'assistente vocale o al bot di reagire meglio agli errori di sistema sottostanti. `DialogServiceConnector` ([C++,](/cpp/cognitive-services/speech/dialog-dialogserviceconnector) [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) ha ora un nuovo `TurnStatusReceived` gestore eventi. Questi eventi facoltativi corrispondono a ogni risoluzione nel bot e segnalano gli errori di esecuzione dei turni quando si verificano, ad esempio a causa di un'eccezione non gestita, un timeout o un calo della rete tra Direct Line Speech e [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) il bot. `TurnStatusReceived` semplifica la risposta alle condizioni di errore. Ad esempio, se un bot richiede troppo tempo per una query di database back-end (ad esempio, la ricerca di un prodotto), consente al client di sapere di ripetere la richiesta con `TurnStatusReceived` "sorry, I didn't quite get that, could you please try again" o qualcosa di simile.
- **C++/C#:** usare Speech SDK in altre piattaforme. Il [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) di Speech SDK supporta ora i file binari nativi desktop ARM/ARM64 di Windows (la piattaforma UWP era già supportata) per rendere Speech SDK più utile in più tipi di computer.
- **Java:** ora ha un metodo che in precedenza era stato escluso involontariamente [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) `setSpeechActivityTemplate()` dal linguaggio. Equivale a impostare la proprietà e richiederà che tutte le attività Bot Framework future originate dal servizio Direct Line Speech uniranno il contenuto fornito nei `Conversation_Speech_Activity_Template` payload JSON.
- **Java:** miglioramento del debug di basso livello. La [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) classe include ora un evento , simile ad altri linguaggi di programmazione `MessageReceived` (C++, C#). Questo evento fornisce accesso di basso livello ai dati in ingresso dal servizio e può essere utile per la diagnostica e il debug.
- **JavaScript:** configurazione più semplice per gli assistenti vocali e i bot tramite , che ora include metodi factory e che semplificano l'uso di percorsi del servizio personalizzati rispetto all'impostazione [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) `fromHost()` manuale delle `fromEndpoint()` proprietà. È stata anche standardizzata la specifica facoltativa di `botId` per l'uso di un bot non predefinito nelle factory di configurazione.
- **JavaScript:** miglioramento delle prestazioni del dispositivo grazie all'aggiunta della proprietà di controllo stringa per la compressione websocket. Per motivi di prestazioni, la compressione websocket è stata disabilitata per impostazione predefinita. Questa opzione può essere ri abilitata per scenari con larghezza di banda ridotta. Ulteriori dettagli [qui](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Questo risolve il [problema di GitHub #242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **JavaScript:** aggiunta del supporto per la valutazione della pronuncia per abilitare la valutazione della pronuncia vocale. Vedere la guida introduttiva [qui.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Correzioni di bug**
- **All** (tranne JavaScript): correzione di una regressione nella versione 1.14, in cui è stata allocata una quantità di memoria insufficiente dal sistema di riconoscimento.
- **C++:** è stato risolto un problema di Garbage Collection con , per risolvere il problema `DialogServiceConnector` [di GitHub #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#:** è stato risolto un problema con l'arresto del thread che causava il blocco degli oggetti per circa un secondo quando vengono eliminati.
- **C++/C#/Java:** correzione di un'eccezione che impedisce a un'applicazione di impostare più volte il token di autorizzazione vocale o il modello di attività in un `DialogServiceConnector` oggetto .
- **C++/C#/Java:** correzione di un arresto anomalo del sistema di riconoscimento a causa race condition durante la rimozione.
- **JavaScript:** [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) in precedenza non rispettava il `botId` parametro facoltativo specificato nelle factory di `BotFrameworkConfig` . In questo modo è stato necessario impostare manualmente `botId` il parametro della stringa di query per usare un bot non predefinito. Il bug è stato corretto e i valori forniti alle factory di verranno rispettati e usati, incluse le nuove aggiunte `botId` `BotFrameworkConfig` e `fromHost()` `fromEndpoint()` . Questo vale anche per il `applicationId` parametro per `CustomCommandsConfig` .
- **JavaScript:** risolto il [problema di GitHub #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), consentendo il nuovo utilizzo dell'oggetto riconoscitore.
- **JavaScript:** risolto un problema a causa del quale lo SD inviava più volte `speech.config` in una sessione TTS, sprecando larghezza di banda.
- **JavaScript:** gestione degli errori semplificata nell'autorizzazione del microfono, che consente di visualizzare un messaggio più descrittivo quando l'utente non ha consentito l'input del microfono nel browser.
- **JavaScript:** risolto il [problema di GitHub #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) errori di tipo in e ha causato un errore di compilazione per gli utenti di `ConversationTranslator` `ConversationTranscriber` TypeScript.
- **Objective-C:** risolto un problema a causa del quale la compilazione di GStreamer non è riuscita per iOS in Xcode 11.4, per risolvere il problema di [GitHub #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python:** risolto il problema [di GitHub #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870), rimuovendo "DeprecationWarning: il modulo imp è deprecato a favore di importlib".

**Esempi**
- [L'esempio di file per il browser JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) ora usa i file per il riconoscimento vocale. In questo modo [viene risolto il problema di GitHub #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Interfaccia della riga di comando di Riconoscimento vocale (nota anche come SPX): versione 2021-gennaio

**Nuove funzionalità**
- L'interfaccia della riga di comando di Riconoscimento vocale è ora disponibile come pacchetto [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) e può essere installata tramite l'interfaccia della riga di comando di .NET come strumento globale .NET che è possibile chiamare dalla shell o dalla riga di comando.
- Il [Riconoscimento vocale personalizzato del modello DevOps è](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) stato aggiornato per usare l'interfaccia della riga di comando di Riconoscimento vocale per i Riconoscimento vocale personalizzato di lavoro.

Test abbreviati **COVID-19:** poiché la pandemia in corso continua a richiedere ai tecnici di lavorare da casa, gli script di verifica manuale pre-pandemia sono stati significativamente ridotti. Viene testato un numero minore di dispositivi con meno configurazioni e la probabilità che si svolgano bug specifici dell'ambiente potrebbe aumentare. La convalida è ancora rigorosa con un ampio set di automazione. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere in buona salute.

## <a name="text-to-speech-2020-december-release"></a>Versione di sintesi vocale 2020-dicembre

**Nuove voci neurali in Ga e anteprima**

Rilasciate 51 nuove voci per un totale di 129 voci neurali in 54 lingue/impostazioni locali:

- **46 nuove** voci nelle impostazioni locali ga: Shakir in arabo `ar-EG` (Spagna), Hamed in Arabo `ar-SA` (Repubblica Araba), Boriziano in `bg-BG` Catalogna (Portogallo), Kpia in `ca-ES` Portogallo (Spagna), Kpiin in `cs-CZ` Ceco (Repubblica Ceco), Jeppe in Danese `da-DK` (Spagna), Jon aas in tedesco (Irlanda), Gennaio in Tedesco `de-AT` `de-CH` (Svizzera), Nestoras in Greco `el-GR` (Svizzera), Liam in `en-CA` inglese (Canada), Liam in inglese `en-IE` (Irlanda), Madhur in `en-IN` Hindi (India), Mohan in `en-IN` Telugu (India), Prabhat in `en-IN` inglese (India) ), Valluvar in `en-IN` Tamil (India), Enric in `es-ES` Norvegia (Spagna), Kert in `et-EE` estone (Ebraico), Harri in finlandese `fi-FI` (Norvegia), Selma in finlandese `fi-FI` (Svizzera), Fabrice in `fr-CH` francese (Svizzera), Colm in `ga-IE` Irlanda, Avri in `he-IL` ebraico (Svizzera), Srecko in `hr-HR` Lituano (Svizzera), Tamas in Ungherese `hu-HU` (Svizzera), Gadis `id-ID` inIstanza (Australia), Leonas in `lt-LT` Lituano (Svizzera), Nils in `lv-LV` Lettone (Svizzera), Osman in `ms-MY` Malese (Svizzera), Svizzera in `mt-MT` Svizzera (Svizzera) , `nb-NO` Norvegese, Bokmål (Norvegia), Pernille in `nb-NO` Norvegese, Bokmål (Norvegia), Poina in olandese `nl-NL` (Paesi Bassi), Maarten in olandese `nl-NL` (Paesi Bassi), Agnieszka in polacco (Svizzera), Agnieszka in polacco `pl-PL` `pl-PL` (Svizzera), Duarte in portoghese `pt-BR` (Brasile), Raquel in portoghese `pt-PT` (Potugal), Emil in Svizzera `ro-RO` (Svizzera), Dmitry in Russo `ru-RU` (Russia), Svetlana in Russo `ru-RU` (Russia), Lione in `sk-SK` Slovak (Slovak), Rok in `sl-SI` Svizzera (Svizzera), Mattias in Svedese `sv-SE` (Svizzera), Sofie in Svedese `sv-SE` (Svizzera), Niwat in `th-TH` Thai (Taiwan), Ahmet in `tr-TR` Turco (Portogallo), NamMinh in `vi-VN` Corea del Sud, Hsiao In Taiwanese `zh-TW` (Taiwan), YunJhe in Taiwanese `zh-TW` (Taiwan), HiuMaan in Cinese `zh-HK` (Hong Kong), Wan Wan In `zh-HK` Cinese (Hong Kong).

- **5 nuove** voci nelle impostazioni locali di anteprima: Kert in estone `et-EE` (Estonia), Colm in `ga-IE` Ireland (Irlanda), Nils in `lv-LV` lettone (Lettone), Leonas in `lt-LT` lituano (Lituania), Jose in `mt-MT` Malta .

Con questa versione è ora possibile supportare un totale di 129 voci neurali in 54 lingue/impostazioni locali. Inoltre, oltre 70 voci standard sono disponibili in 49 lingue/impostazioni locali. Per [l'elenco completo,](language-support.md#text-to-speech) vedere Supporto linguistico.

**Aggiornamenti per Creazione di contenuto audio**
- Interfaccia utente migliorata per la selezione della voce con categorie vocali e descrizioni vocali dettagliate. 
- Ottimizzazione dell'intonazione abilitata per tutte le voci neurali in lingue diverse.
- Automatizza la localizzazione dell'interfaccia utente in base alla lingua del browser.
- Controlli `StyleDegree` abilitati per tutte le `zh-CN` voci neurali.
Visitare lo [strumento Creazione di contenuto audio](https://speech.microsoft.com/audiocontentcreation) per verificare le nuove funzionalità. 

**Aggiornamenti per le voci zh-CN**
- Aggiornamento di tutte `zh-CN` le voci neurali per supportare la lingua inglese.
- Abilitate tutte le `zh-CN` voci neurali per supportare la regolazione dell'intonazione. Lo strumento SSML o Creazione di contenuto audio può essere usato per regolare la migliore intonazione.
- Aggiornamento di `zh-CN` tutte le voci neurali multi-stile per supportare il `StyleDegree` controllo. L'intensità delle emozioni (soft o strong) è regolabile.
- Aggiornato `zh-CN-YunyeNeural` per supportare più stili che possono eseguire emozioni diverse.

## <a name="text-to-speech-2020-november-release"></a>Versione di sintesi vocale 2020-novembre

**Nuove impostazioni locali e voci in anteprima**
- **Nel portfolio Neural** TTS sono state introdotte cinque nuove voci e lingue. Si tratta di: Grace in Grace (Portogallo), Ona in Lituano (Lituano), Anu in Estone (Estone), Orla in Irlanda e Everita in Lettone (Portogallo).
- **Cinque nuove `zh-CN` voci con** più stili e ruoli supportano: Xiaohan, Xiaomo, Xiaorui, Xiaoxuan e Yunxi.

> Queste voci sono disponibili in anteprima pubblica in tre aree di Azure: EastUS, SouthEastAsia ed WestEurope.

**Neural TTS Container GA**
- Con il contenitore Neural TTS, gli sviluppatori possono eseguire la sintesi vocale con le voci digitali più naturali nel proprio ambiente per requisiti specifici di sicurezza e governance dei dati. Vedere [come installare i contenitori voce.](speech-container-howto.md) 

**Nuove funzionalità**
- **voce personalizzata**: consente agli utenti di copiare un modello vocale da un'area a un'altra; sospensione e ripresa dell'endpoint supportate. Passare al [portale](https://speech.microsoft.com/customvoice) qui.
- [Supporto dei tag di silenzio SSML.](speech-synthesis-markup.md#add-silence) 
- Miglioramenti generali della qualità della voce TTS: miglioramento dell'accuratezza della pronuncia a livello di parola in nb-NO. Riduzione dell'errore di pronuncia del 53%.

> Per altre informazioni, [vedere questo blog tecnico.](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)

## <a name="text-to-speech-2020-october-release"></a>Versione di sintesi vocale 2020 -ottobre

**Nuove funzionalità**
- Il modello Supporta un nuovo `newscast` stile. Vedere [come usare gli stili di pronuncia in SSML.](speech-synthesis-markup.md#adjust-speaking-styles)
- **Voci neurali aggiornate al vocoder HiFiNet,** con maggiore fedeltà audio e velocità di sintesi più rapida. Questo vantaggio è vantaggioso per i clienti il cui scenario si basa su audio hi-fi o interazioni lunghe, tra cui video dubbi, audiolibri o materiali per la formazione online. [Per altre informazioni sulla storia e per ascoltare gli esempi vocali, vedere il blog della community tecnica](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[voce personalizzata](https://speech.microsoft.com/customvoice)  &  [Creazione di contenuto audio Studio localizzato](https://speech.microsoft.com/audiocontentcreation) in 17 impostazioni locali.** Gli utenti possono passare facilmente l'interfaccia utente a una lingua locale per un'esperienza più semplice.   
- **Creazione di contenuto audio:** aggiunta del controllo del grado di stile per XiaoxiaoNeural; È stata perfezionata la funzionalità di interruzione personalizzata per includere interruzioni incrementali di 50 ms. 

**Miglioramenti generali della qualità della voce TTS**
- Miglioramento dell'accuratezza della pronuncia a livello di parola in `pl-PL` (riduzione del tasso di errore: 51%) e `fi-FI` (riduzione della frequenza degli errori: 58%)
- Miglioramento `ja-JP` della lettura di singole parole per lo scenario del dizionario. Errore di pronuncia ridotto dell'80%.
- `zh-CN-XiaoxiaoNeural`: miglioramento della qualità vocale sentiment/CustomerService/Newscast/Pervaso/Incauto.
- `zh-CN`: miglioramento della pronuncia Erhua e del tono chiaro e della prosodia spaziale perfezionata, che migliora notevolmente l'intelligibilità. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: versione 2020-ottobre

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable Visual Studio 2015, 2017 e 2019. Scaricarla [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nuove funzionalità**
- **Linux:** aggiunta del supporto per Debian 10 e Ubuntu 20.04 LTS.
- **Python/Objective-C:** aggiunta del supporto per `KeywordRecognizer` l'API. La documentazione sarà [disponibile qui.](./custom-keyword-basics.md)
- **C++/Java/C#**: aggiunta del supporto per impostare qualsiasi `HttpHeader` chiave/valore tramite `ServicePropertyChannel::HttpHeader` .
- **JavaScript:** aggiunta del supporto per `ConversationTranscriber` l'API. Leggere la documentazione [qui.](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 
- **C++/C#:** aggiunta del nuovo `AudioDataStream FromWavFileInput` metodo (per leggere . FILE WAV) [qui (C++)](/cpp/cognitive-services/speech/audiodatastream) [e qui (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/C#/Java/Python/Objective-C/Swift:** è stato aggiunto un metodo per `stopSpeakingAsync()` arrestare la sintesi vocale. Leggere la documentazione di [riferimento qui (C++),](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace)qui [(C#),](/dotnet/api/microsoft.cognitiveservices.speech) [qui (Java)](/java/api/com.microsoft.cognitiveservices.speech), [qui (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)e [qui (Objective-C/Swift).](/objectivec/cognitive-services/speech/)
- **C#, C++, Java:** aggiunta di una funzione alla classe che può essere usata per monitorare gli eventi di connessione e `FromDialogServiceConnector()` `Connection` disconnessione per `DialogServiceConnector` . Leggere la documentazione di [riferimento qui (C#),](/dotnet/api/microsoft.cognitiveservices.speech.connection) [qui (C++)](/cpp/cognitive-services/speech/connection)e [qui (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/C#/Java/Python/Objective-C/Swift:** aggiunta del supporto per la valutazione della pronuncia, che valuta la pronuncia vocale e fornisce ai parlanti feedback sull'accuratezza e la fluenza dell'audio parlato. Leggere la documentazione [qui.](how-to-pronunciation-assessment.md)

**Modifica di rilievo**
- **JavaScript:** PullAudioOutputStream.read() ha una modifica del tipo restituito da una promessa interna a una promessa JavaScript nativa.

**Correzioni di bug**
- **All:** correzione della regressione 1.13 in in cui `SetServiceProperty` i valori con determinati caratteri speciali vengono ignorati.
- **C#:** correzione degli esempi di console di Windows Visual Studio 2019 che non riescono a trovare DLL native.
- **C#: correzione** dell'arresto anomalo con la gestione della memoria se il flusso viene usato come `KeywordRecognizer` input.
- **ObjectiveC/Swift:** correzione dell'arresto anomalo con la gestione della memoria se il flusso viene usato come input del riconoscitore.
- **Windows:** risolto un problema di coesistenza con BT HFP/A2DP nella piattaforma UWP.
- **JavaScript:** corretto il mapping degli ID di sessione per migliorare la registrazione e facilitare le correlazioni di debug/servizio interne.
- **JavaScript:** aggiunta della correzione per `DialogServiceConnector` la `ListenOnce` disabilitazione delle chiamate dopo la prima chiamata.
- **JavaScript:** è stato risolto un problema a causa del quale l'output dei risultati sarebbe "semplice".
- **JavaScript:** risolto un problema di riconoscimento continuo in Safari in macOS.
- **JavaScript:** mitigazione del carico della CPU per uno scenario con velocità effettiva elevata delle richieste.
- **JavaScript:** consente l'accesso ai dettagli del risultato della registrazione del profilo vocale.
- **JavaScript:** aggiunta della correzione per il riconoscimento continuo in `IntentRecognizer` .
- **C++/C#/Java/Python/Swift/ObjectiveC:** corretto URL non corretto per australiaeast e brazilsouth in `IntentRecognizer` .
- **C++/C#:** aggiunto `VoiceProfileType` come argomento durante la creazione di un `VoiceProfile` oggetto.
- **C++/C#/Java/Python/Swift/ObjectiveC:** correzione del potenziale durante il tentativo `SPX_INVALID_ARG` di leggere da una determinata `AudioDataStream` posizione.
- **IOS:** correzione dell'arresto anomalo del sistema con il riconoscimento vocale in Unity

**Esempi**
- **ObjectiveC:** aggiunto un esempio per il riconoscimento delle parole [chiave qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)
- **C#/JavaScript:** aggiunta della guida introduttiva per la trascrizione della conversazione [qui (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) e [qui (JavaScript).](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)
- **C++/C#/Java/Python/Swift/ObjectiveC:** aggiunto un esempio per la valutazione della pronuncia [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin:** aggiornamento della guida introduttiva all'Visual Studio più recente [qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)

**Problema noto**
- Il certificato DigiCert Global Root G2 non è supportato per impostazione predefinita in HoloLens 2 e Android 4.4 (KitKat) e deve essere aggiunto al sistema per rendere funzionale Speech SDK. Il certificato verrà aggiunto alle HoloLens 2 del sistema operativo nel prossimo futuro. I clienti Android 4.4 devono aggiungere il certificato aggiornato al sistema.

**Test abbreviato COVID-19:** A causa del lavoro in modalità remota nelle ultime settimane, non è stato possibile eseguire il test di verifica manuale come di consueto. Non sono state apportate modifiche che si potrebbero avere interrotte e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere in buona salute.

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Interfaccia della riga di comando di Riconoscimento vocale (nota anche come SPX): versione 2020-ottobre
SPX è l'interfaccia della riga di comando per usare il servizio Voce di Azure senza scrivere codice. Scaricare la versione più recente [qui.](./spx-basics.md) <br>

**Nuove funzionalità**
- `spx csr dataset upload --kind audio|language|acoustic` : creare set di dati da dati locali, non solo da URL.
- `spx csr evaluation create|status|list|update|delete` : confrontare i nuovi modelli con la verità di base o altri modelli.
- `spx * list` : supporta l'esperienza non di pagina (non richiede --top X --skip X).
- `spx * --http header A=B` : supporta le intestazioni personalizzate (aggiunte per Office per l'autenticazione personalizzata). 
- `spx help` : testo migliorato e colore del testo con segno di spunta indietro codificato (blu).

## <a name="text-to-speech-2020-september-release"></a>Versione di sintesi vocale 2020-settembre

### <a name="new-features"></a>Nuove funzionalità

* **Neural TTS** 
    * **Estensione per supportare 18 nuove lingue/impostazioni locali.** Sono libese, ceco, tedesco (Svizzera), tedesco (Svizzera), greco, inglese (Irlanda), francese (Svizzera), ebraico, ebraico, ungherese, ungherese, malese, slovacco, slovacco, tamil, telugu e libese. 
    * **Rilasciate 14 nuove voci per arricchire la varietà nelle lingue esistenti.** Vedere [l'elenco completo della lingua e della voce.](language-support.md#neural-voices)
    * **Nuovi stili di pronuncia `en-US` per le voci e `zh-CN` .** Il nuovo sistema vocale in inglese (Stati Uniti) Supporta gli stili di chatbot, servizio clienti e assistente. Sono disponibili 10 nuovi stili di pronuncia con la voce zh-CN, XiaoXiao. Inoltre, la voce neurale XiaoXiao supporta `StyleDegree` l'ottimizzazione. Vedere [come usare gli stili di pronuncia in SSML.](speech-synthesis-markup.md#adjust-speaking-styles)

* **Contenitori: contenitore Neural TTS rilasciato in anteprima pubblica con 16 voci disponibili in 14 lingue.** Altre informazioni su [come distribuire contenitori voce per Neural TTS](speech-container-howto.md)  

Leggere [l'annuncio completo degli aggiornamenti di TTS per Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Sintesi vocale versione di agosto 2020

### <a name="new-features"></a>Nuove funzionalità

* **Neural TTS: nuovo stile di pronuncia per `en-US` Aria voice**. AriaNeural può sembrare un news caster durante la lettura delle notizie. Lo stile "newscast-formal" sembra più grave, mentre lo stile "newscast-casual" è più informale e meno informale. Vedere [come usare gli stili di pronuncia in SSML.](speech-synthesis-markup.md)

* **voce personalizzata: viene rilasciata una nuova funzionalità per controllare automaticamente la qualità dei dati di training.** Quando si caricano i dati, il sistema esaminerà vari aspetti dei dati audio e di trascrizione e correggerà o filtra automaticamente i problemi per migliorare la qualità del modello vocale. Ciò copre il volume dell'audio, il livello di disturbo, l'accuratezza della pronuncia del parlato, l'allineamento del parlato con il testo normalizzato, il silenzio nell'audio, oltre al formato audio e script. 

* **Creazione di contenuto audio: un set di nuove funzionalità per abilitare** funzionalità di ottimizzazione vocale e gestione audio più potenti.

    * Pronuncia: la funzionalità di ottimizzazione della pronuncia viene aggiornata al set di fonemi più recente. È possibile selezionare l'elemento fonema giusto dalla libreria e perfezionare la pronuncia delle parole selezionate. 

    * Download: la funzionalità audio "Download"/"Export" è stata migliorata per supportare la generazione di audio per paragrafo. È possibile modificare il contenuto nello stesso file/SSML, generando più output audio. Viene perfezionata anche la struttura di file di "Download". Ora è possibile ottenere facilmente tutti i file audio in un'unica cartella. 

    * Stato attività: l'esperienza di esportazione di più file è stata migliorata. Quando si esportano più file in passato, se uno dei file ha avuto esito negativo, l'intera attività avrà esito negativo. Ma ora tutti gli altri file verranno esportati correttamente. Il report attività è arricchito con informazioni più dettagliate e strutturate. È ora possibile controllare i log per tutti i file e le frasi con errori con il report. 

    * Documentazione di SSML: collegata al documento SSML che consente di controllare le regole per l'uso di tutte le funzionalità di ottimizzazione.

* **L'API Elenco vocale è stata aggiornata per includere un nome visualizzato descrittivo** e gli stili di pronuncia supportati per le voci neurali.

### <a name="general-tts-voice-quality-improvements"></a>Miglioramenti generali della qualità della voce TTS

* Riduzione della percentuale di errori di pronuncia a livello di parola per `ru-RU` (errori ridotti del 56%) e `sv-SE` (errori ridotti del 49%)

* Miglioramento del 40% della lettura delle parole `en-US` polifono nelle voci neurali. Esempi di parole polifonerie includono "read", "live", "content", "record", "object" e così via. 

* Miglioramento della naturalezza del tono della domanda in `fr-FR` . Guadagno MOS (Punteggio medio opinione): +0,28

* Sono stati aggiornati i vocoder per le voci seguenti, con miglioramenti della fedeltà e un miglioramento generale delle prestazioni del 40%.

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
    * È stato risolto un problema relativo all'aggiornamento automatico. 
    * Sono stati risolti i problemi relativi agli stili voce in zh-CN nell'area Asia orientale sud.
    * È stato risolto un problema di stabilità, tra cui un errore di esportazione con il tag 'break' ed errori di punteggiatura.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nuove impostazioni locali per la sintesi vocale: versione 2020-agosto
La sintesi vocale ha rilasciato 26 nuove impostazioni locali nel mese di agosto: 2 lingue europa e , 5 impostazioni locali in inglese e 19 impostazioni locali spagnole che coprono la maggior parte dei paesi del Sud `cs-CZ` `hu-HU` America. Di seguito è riportato un elenco delle nuove impostazioni locali. Vedere l'elenco completo delle lingue [qui.](./language-support.md)

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
| `es-CU` | Spagnolo (Cuba)                    | 
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
| `es-US` | Spagnolo (Stati Uniti)                     | 
| `es-UY` | Spagnolo (Uruguay)                 | 
| `es-VE` | Spagnolo (Venezuela)               | 
| `hu-HU` | Ungherese (Ungheria)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: versione 2020-luglio

> [!NOTE]
> Speech SDK in Windows dipende dalla versione Microsoft Visual C++ Redistributable per Visual Studio 2015, 2017 e 2019. Scaricare e installare l'app da [qui](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nuove funzionalità**
- **C#:** aggiunta del supporto per la trascrizione asincrona della conversazione. Vedere la documentazione [qui.](./how-to-async-conversation-transcription.md)  
- **JavaScript:** aggiunta del Riconoscimento del parlante per [browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) [ enode.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript:** aggiunta del supporto per il rilevamento automatico della lingua/ID lingua. Vedere la documentazione [qui.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objective-C:** aggiunta del supporto per [la trascrizione di conversazioni e](./multi-device-conversation.md) conversazioni su più [dispositivi.](./conversation-transcription.md) 
- **Python:** aggiunta del supporto audio compresso per Python in Windows e Linux. Vedere la documentazione [qui.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Correzioni di bug**
- **Tutti:** è stato risolto un problema che causava il fatto che KeywordRecognizer non spostava in avanti i flussi dopo un riconoscimento.
- **All:** è stato risolto un problema che causava il fatto che il flusso ottenuto da KeywordRecognitionResult non contiene la parola chiave .
- **All**: è stato risolto un problema per cui SendMessageAsync non invia effettivamente il messaggio in rete dopo che gli utenti hanno terminato l'attesa.
- **Tutti:** correzione di un arresto anomalo nelle API di Riconoscimento del parlante quando gli utenti chiamano più volte il metodo VoiceProfileClient::SpeakerRecEnrollProfileAsync e non attendono il completamento delle chiamate.
- **Tutti:** correzione dell'abilitazione della registrazione dei file nelle classi VoiceProfileClient e SpeakerRecognizer.
- **JavaScript:** è stato risolto un [problema di](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) limitazione delle richieste quando il browser è ridotto al minimo.
- **JavaScript:** risolto un [problema relativo](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) a una perdita di memoria nei flussi.
- **JavaScript:** aggiunta della memorizzazione nella cache per le risposte OCSP da NodeJS.
- **Java:** è stato risolto un problema che causava la restituzione sempre di 0 da parte dei campi BigInteger.
- **iOS:** è stato risolto [un problema](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) relativo alla pubblicazione di app basate su Speech SDK nel App Store iOS.

**Esempi**
- **C++:** è stato aggiunto il codice di esempio Riconoscimento del parlante [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Test abbreviato COVID-19:** A causa del lavoro in modalità remota nelle ultime settimane, non è stato possibile eseguire il test di verifica manuale come di consueto. Non sono state apportate modifiche che si potrebbero avere interrotte e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere in buona salute.

## <a name="text-to-speech-2020-july-release"></a>Versione di sintesi vocale 2020-luglio

### <a name="new-features"></a>Nuove funzionalità

* **Neural TTS, 15 nuove** voci neurali: le nuove voci aggiunte al portfolio Neural TTS sono Salma in arabo `ar-EG` (India), Zariyah in arabo `ar-SA` (Arabia Saudita), Alba in `ca-ES` catalogna (Spagna), Christel in danese `da-DK` (Danimarca), Neerja in inglese (India), Noora in finlandese ((Spagna), Neerja in inglese `es-IN` (India), Noora in finlandese `fi-FI` (Finlandia), Swara in `hi-IN` Hindi (India), Colette in olandese `nl-NL` (Paesi Bassi), Zofia in polacco `pl-PL` (Svizzera), Fernanda in portoghese `pt-PT` (Portogallo), Dariya in russo `ru-RU` (Russia), Colliovi in svedese `sv-SE` (Svezia), Achara in `th-TH` tailandese (Tailandia), HiuGaai in cinese `zh-HK` (Cantonese, Tradizionale) e HsiaoYu in `zh-TW` cinese (mandarino taiwanese). Controllare tutte [le lingue supportate.](./language-support.md#neural-voices)  

* **voce personalizzata** test vocali semplificati con il flusso di training per semplificare l'esperienza utente: con la nuova funzionalità di test, ogni voce verrà testata automaticamente con un set di test predefinito ottimizzato per ogni lingua per coprire scenari generali e di assistente vocale. Questi set di test vengono accuratamente selezionati e testati per includere casi d'uso e fonemi tipici nella lingua. Inoltre, gli utenti possono comunque scegliere di caricare i propri script di test durante il training di un modello.

* **Creazione di contenuto audio: viene rilasciato un set di nuove funzionalità per abilitare funzionalità di ottimizzazione vocale e gestione audio più potenti**

    * `Pitch`, `rate` e sono stati `volume` migliorati per supportare l'ottimizzazione con un valore predefinito, ad esempio lento, medio e veloce. È ora semplice per gli utenti scegliere un valore "costante" per la modifica audio.

    ![Ottimizzazione dell'audio](media/release-notes/audio-tuning.png)

    * Gli utenti possono ora esaminare `Audio history` per il file di lavoro. Con questa funzionalità, gli utenti possono tenere facilmente traccia di tutto l'audio generato correlato a un file di lavoro. Possono controllare la versione della cronologia e confrontare la qualità durante l'ottimizzazione contemporaneamente. 

    ![Cronologia audio](media/release-notes/audio-history.png)

    * La `Clear` funzionalità è ora più flessibile. Gli utenti possono cancellare un parametro di ottimizzazione specifico mantenendo altri parametri disponibili per il contenuto selezionato.  

    * Nella pagina di destinazione [](https://speech.microsoft.com/audiocontentcreation) è stato aggiunto un video di esercitazione che consente agli utenti di iniziare rapidamente a usare l'ottimizzazione vocale TTS e la gestione dell'audio. 

### <a name="general-tts-voice-quality-improvements"></a>Miglioramenti generali della qualità della voce TTS

* Miglioramento del vocoder TTS in per una maggiore fedeltà e una latenza più bassa.

    * Aggiornamento di Rispetto a un nuovo vocoder che ha ottenuto +0,464 CMOS (comparativo punteggio medio di opinione) nella qualità della voce, il 40% più veloce nella sintesi e una riduzione del 30% sulla latenza del primo `it-IT` byte. 
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
    * Correzione del problema relativo alla lettura della valuta per `es-ES` e `es-MX`
     
    | Linguaggio | Input | Readout dopo il miglioramento |
    |---|---|---|
    | `es-MX` | $ 1,58 | un peso cincuenta y ocho centavos |
    | `es-ES` | $ 1,58 | un dólar cincuenta y ocho centavos |

    * Supporto per la valuta negativa (ad esempio "-325 €" ) nelle impostazioni locali seguenti: `en-US` , , , , , `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Miglioramento della lettura degli indirizzi in `pt-PT` .
* Correzione dei problemi di pronuncia di Natasha ( ) e `en-AU` Libby ( `en-UK` ) sulle parole "for" e "four".  
* Correzione di bug Creazione di contenuto audio strumento
    * Sospensione aggiuntiva e imprevista dopo la correzione del secondo paragrafo.  
    * La funzionalità "Nessuna interruzione" viene aggiunta di nuovo da un bug di regressione. 
    * Il problema di aggiornamento casuale Speech Studio risolto.  

### <a name="samplessdk"></a>Esempi/SDK

* JavaScript: risolve il problema di riproduzione in Firefox e Safari in macOS e iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: versione 2020-giugno
**Interfaccia della riga di comando di Riconoscimento vocale (nota anche come SPX)**
-   Sono state aggiunte funzionalità di ricerca della Guida nell'interfaccia della riga di comando:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Aggiornamento per l'utilizzo con le API batch e Riconoscimento vocale personalizzato v3.0 appena distribuite:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nuove funzionalità**
-   **C, \# C++**: Riconoscimento del parlante Preview: questa funzionalità consente l'identificazione del parlante (chi parla?) e la verifica del parlante (è il parlante che dichiara di essere?). Iniziare con una [panoramica,](./speaker-recognition-overview.md)leggere [l'articolo Riconoscimento del parlante informazioni di base sull'API](./get-started-speaker-recognition.md)o la documentazione di riferimento sulle [API](/rest/api/speakerrecognition/).

**Correzioni di bug**
-   **C, \# C++:** la registrazione del microfono fissa non funzionava nella versione 1.12 nel riconoscimento del parlante.
-   **JavaScript:** correzioni per la sintesi vocale in Firefox e Safari in macOS e iOS.
-   Correzione per l'arresto anomalo della violazione di accesso di Verifica applicazione Windows durante la trascrizione della conversazione quando si usa il flusso a otto canali.
-   Correzione per l'arresto anomalo della violazione di accesso di Verifica applicazione Windows nella conversione di conversazioni su più dispositivi.

**Esempi**
-   **C#:** esempio [di codice per](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) il riconoscimento del parlante.
-   **C++:** esempio [di codice per](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) il riconoscimento del parlante.
-   **Java:** [esempio di codice per](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) il riconoscimento delle finalità in Android. 

**Test abbreviati DI COVID-19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali come di consueto. Non sono state apportate modifiche che potrebbero aver interrotto nulla e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: versione di maggio 2020
**Interfaccia della riga di comando di Voce (anche noto come SPX)**
- **SPX** è un nuovo strumento da riga di comando che consente di eseguire il riconoscimento, la sintesi, la traduzione, la trascrizione batch e la gestione del riconoscimento vocale personalizzato dalla riga di comando. Usarlo per testare il Servizio di riconoscimento vocale o per creare script per le attività del Servizio di riconoscimento vocale che è necessario eseguire. Scaricare lo strumento e leggere la documentazione [qui](./spx-overview.md).

**Nuove funzionalità**
- **Go:** nuovo supporto per la lingua Go [per il riconoscimento vocale e](./get-started-speech-to-text.md?pivots=programming-language-go) [l'assistente vocale personalizzato.](./quickstarts/voice-assistants.md?pivots=programming-language-go) Configurare l'ambiente di sviluppo [qui](./quickstarts/setup-platform.md?pivots=programming-language-go). Per il codice di esempio, vedere la sezione Esempi riportata di seguito. 
- **JavaScript:** aggiunta del supporto del browser per la sintesi vocale. Vedere la documentazione [qui.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java:** nuovo oggetto e API supportati nelle piattaforme `KeywordRecognizer` Windows, Android, Linux & iOS. Leggere la documentazione [qui.](./custom-keyword-overview.md) Per il codice di esempio, vedere la sezione Esempi riportata di seguito. 
- **Java:** aggiunta della conversazione multi-dispositivo con il supporto della traduzione. Vedere il documento di riferimento [qui.](/java/api/com.microsoft.cognitiveservices.speech.transcription)

**Miglioramenti & ottimizzazioni**
- **JavaScript:** implementazione ottimizzata del microfono del browser che migliora l'accuratezza del riconoscimento vocale.
- **Java:** refactoring delle associazioni usando l'implementazione JNI diretta senza SWIG. Questa modifica riduce del 10% le dimensioni delle associazioni per tutti i pacchetti Java usati per Windows, Android, Linux e Mac e semplifica ulteriormente lo sviluppo dell'implementazione Java di Speech SDK.
- **Linux:** aggiornata la [documentazione di supporto](./speech-sdk.md?tabs=linux) con le note specifiche di RHEL 7 più recenti.
- Logica di connessione migliorata per tentare di connettersi più volte quando si verificano errori di servizio e di rete.
- È stata [aggiornata portal.azure.com](https://portal.azure.com) guida introduttiva al riconoscimento vocale per consentire agli sviluppatori di eseguire il passaggio successivo nel percorso di Riconoscimento vocale di Azure.

**Correzioni di bug**
- **C#, Java:** risolto un [problema di](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) caricamento delle librerie SDK in Linux ARM (sia a 32 bit che a 64 bit).
- **C#**: correzione dell'eliminazione esplicita degli handle nativi per gli oggetti TranslationRecognizer, IntentRecognizer e Connection.
- **C#**: correzione della gestione della durata dell'input audio per l'oggetto ConversationTranscriber.
- È stato risolto un problema a causa del quale il motivo del risultato non era impostato correttamente quando `IntentRecognizer` si riconosceva la finalità da frasi semplici.
- È stato risolto un problema a causa `SpeechRecognitionEventArgs` del quale l'offset dei risultati non era impostato correttamente.
- Correzione di un race condition in cui l'SDK tentava di inviare un messaggio di rete prima di aprire la connessione websocket. È stato riproducibile per durante `TranslationRecognizer` l'aggiunta di partecipanti.
- Correzione delle perdite di memoria nel motore di riconoscimento delle parole chiave.

**Esempi**
- **Go:** sono state aggiunte guide introduttive per [il riconoscimento vocale e](./get-started-speech-to-text.md?pivots=programming-language-go) [l'assistente vocale personalizzato.](./quickstarts/voice-assistants.md?pivots=programming-language-go) Trovare il codice di [esempio qui](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **JavaScript:** sono state aggiunte guide introduttive [per sintesi](./get-started-text-to-speech.md?pivots=programming-language-javascript)vocale, [traduzione](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script) [e Riconoscimento finalità](./get-started-intent-recognition.md?pivots=programming-language-javascript).
- Esempi di riconoscimento delle parole [chiave per \# C](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) [e Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Test abbreviato COVID-19:** A causa del lavoro in remoto nelle ultime settimane, non è stato possibile eseguire tutti i test di verifica manuali come di consueto. Non sono state apportate modifiche che si potrebbe avere interrotto e tutti i test automatizzati sono stati superati. Se non si è verificato un problema, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Rimanere integri.

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: versione di marzo 2020
**Nuove funzionalità**
- Linux: aggiunta del supporto per Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 con istruzioni su come configurare il sistema per Speech SDK. [](./how-to-configure-rhel-centos-7.md)
- Linux: aggiunta del supporto per .NET Core C# in Linux ARM32 e ARM64. Altre informazioni sono disponibili [qui](./speech-sdk.md?tabs=linux). 
- C#, C++: aggiunto in , un ID coerente tra tutti gli elementi intermedi e il risultato finale `UtteranceId` `ConversationTranscriptionResult` del riconoscimento vocale. Dettagli per [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++.](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)
- Python: aggiunta del supporto per `Language ID` . Vedere speech_sample.py nel [repository GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: è stato aggiunto il supporto del formato di input audio compresso nella piattaforma Windows per tutte le applicazioni console win32. Per informazioni [dettagliate, vedere](./how-to-use-codec-compressed-audio-input-streams.md)qui . 
- JavaScript: supporta la sintesi vocale (sintesi vocale) in NodeJS. Fare clic [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) per altre informazioni. 
- JavaScript: aggiungere nuove API per abilitare l'ispezione di tutti i messaggi inviati e ricevuti. Fare clic [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) per altre informazioni. 
        
**Correzioni di bug**
- C#, C++: è stato risolto un problema per `SendMessageAsync` cui ora invia un messaggio binario come tipo binario. Dettagli per [C#,](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) [C++.](/cpp/cognitive-services/speech/connection)
- C#, C++: risolto un problema a causa del quale l'uso dell'evento può causare un arresto anomalo `Connection MessageReceived` se viene eliminato prima `Recognizer` `Connection` dell'oggetto. Dettagli per [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: le dimensioni del buffer audio dal microfono sono diminuite da 800 ms a 100 ms per migliorare la latenza.
- Android: risolto un [problema con](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) l'emulatore Android x86 in Android Studio.
- JavaScript: aggiunta del supporto per le aree in Cina con `fromSubscription` l'API. Dettagli [qui.](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-) 
- JavaScript: aggiungere altre informazioni sugli errori di connessione da NodeJS.
        
**Esempi**
- Unity: l'esempio pubblico di riconoscimento delle finalità è stato corretto, in cui l'importazione json LUIS ha esito negativo. Dettagli [qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: esempio aggiunto per `Language ID` . Dettagli [qui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Test abbreviati di Covid19:** A causa del lavoro in modalità remota nelle ultime settimane, non è stato possibile eseguire lo stesso test di verifica manuale dei dispositivi come di consueto. Ad esempio, non è stato possibile testare l'input del microfono e l'output del parlante in Linux, iOS e macOS. Non sono state apportate modifiche che si potrebbero avere interrotte in queste piattaforme e tutti i test automatizzati sono stati superati. Nel caso improbabile in cui si sia perso qualcosa, è possibile fare clic su [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br> Grazie per il supporto continuo. Come sempre, inviare domande o commenti su [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) [o Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Rimanere in buona salute.

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: versione 2020-febbraio

**Nuove funzionalità**

 - Sono stati aggiunti pacchetti Python per supportare la nuova versione 3.8 di Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 support (C++, C#, Java, Python).
   > [!NOTE] 
   > I clienti devono configurare OpenSSL in base [a queste istruzioni.](./how-to-configure-openssl-linux.md)
 - Supporto arm32 linux per Debian e Ubuntu.
 - DialogServiceConnector supporta ora un parametro facoltativo "ID bot" in BotFrameworkConfig. Questo parametro consente l'uso di più Direct Line Speech bot con una singola risorsa voce di Azure. Senza il parametro specificato, verrà usato il bot predefinito ,come determinato dalla pagina Direct Line Speech di configurazione del canale.
 - DialogServiceConnector ha ora una proprietà SpeechActivityTemplate. Il contenuto di questa stringa JSON verrà usato da Direct Line Speech per precompilare un'ampia gamma di campi supportati in tutte le attività che raggiungono un bot Direct Line Speech, incluse le attività generate automaticamente in risposta a eventi come il riconoscimento vocale.
 - TTS usa ora la chiave di sottoscrizione per l'autenticazione, riducendo la latenza del primo byte del primo risultato di sintesi dopo la creazione di un sintetizzatore.
 - Aggiornamento dei modelli di riconoscimento vocale per 19 impostazioni locali per una riduzione media della frequenza degli errori di parola del 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). I nuovi modelli apportano miglioramenti significativi in più domini, inclusi gli scenari di dettatura, Call-Center trascrizione e indicizzazione video.

**Correzioni di bug**

 - Correzione del bug per cui Il trascrittore di conversazioni non era in attesa correttamente nelle API JAVA 
 - Correzione dell'emulatore Android x86 per il problema di [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) di Xamarin
 - Aggiungi mancante (Get| Set)Metodi di proprietà su AudioConfig
 - Correzione di un bug TTS per cui non è stato possibile arrestato audioDataStream quando la connessione non riesce
 - L'uso di un endpoint senza un'area causerebbe errori USP per il traduttore di conversazioni
 - La generazione di ID nelle applicazioni di Windows universale ora usa un algoritmo GUID univoco appropriato. In precedenza e per impostazione predefinita era un'implementazione con stub che spesso produceva collisioni su grandi set di interazioni.
 
 **Esempi**
 
 - Esempio unity per l'uso di Speech SDK con il [microfono Unity e lo streaming in modalità push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Altre modifiche**

 - [Documentazione di configurazione OpenSSL aggiornata per Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: versione 2020-gennaio

**Nuove funzionalità**

- Conversazione su più dispositivi: connettere più dispositivi alla stessa conversazione vocale o basata su testo e, facoltativamente, tradurre i messaggi inviati tra di essi. Per altre informazioni, [vedere questo articolo.](multi-device-conversation.md) 
- Aggiunta del supporto per il riconoscimento delle parole chiave per il pacchetto Android .aar e del supporto per le versioni x86 e x64. 
- Objective-C: `SendMessage` e metodi aggiunti `SetMessageProperty` `Connection` all'oggetto . Vedere la documentazione [qui.](/objectivec/cognitive-services/speech/spxconnection)
- L'API C++ TTS supporta ora come input di testo di sintesi, eliminando la necessità di convertire una stringa wstring in stringa prima di `std::wstring` passarla all'SDK. Vedere i dettagli [qui](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [l'ID lingua e](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) la configurazione del [linguaggio](./how-to-specify-source-language.md?pivots=programming-language-csharp) di origine sono ora disponibili.
- JavaScript: aggiunta di una funzionalità `Connection` all'oggetto per passare messaggi personalizzati dal servizio Voce come callback `receivedServiceMessage` .
- JavaScript: aggiunta del supporto per semplificare l'uso con contenitori e `FromHost API` cloud sovrani sul sito. Vedere la documentazione [qui.](speech-container-howto.md)
- JavaScript: ora si rispetta `NODE_TLS_REJECT_UNAUTHORIZED` grazie a un contributo di [orgads.](https://github.com/orgads) Vedere i dettagli [qui](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Modifiche di rilievo**

- `OpenSSL` è stato aggiornato alla versione 1.1.1b ed è collegato in modo statico alla libreria core di Speech SDK per Linux. Ciò può causare un'interruzione se la posta in arrivo non è `OpenSSL` stata installata nella directory del `/usr/lib/ssl` sistema. Per risolvere [il problema,](how-to-configure-openssl-linux.md) consultare la documentazione disponibile nella documentazione di Speech SDK.
- Il tipo di dati restituito per C# è stato modificato da a per consentire l'accesso a quando i dati vocali sono più lunghi `WordLevelTimingResult.Offset` `int` di `long` `WordLevelTimingResults` 2 minuti.
- `PushAudioInputStream` e `PullAudioInputStream` ora inviano informazioni sull'intestazione wav al servizio Voce in base `AudioStreamFormat` a , facoltativamente specificate al momento della creazione. I clienti devono ora usare il [formato di input audio supportato.](how-to-use-audio-input-streams.md) Qualsiasi altro formato otterrà risultati di riconoscimento non ottimali o potrebbe causare altri problemi. 

**Correzioni di bug**

- Vedere `OpenSSL` l'aggiornamento in Modifiche di rilievo riportate sopra. Sono stati risolti sia un arresto anomalo intermittente che un problema di prestazioni (problemi di blocco con carico elevato) in Linux e Java. 
- Java: sono stati apportati miglioramenti alla chiusura degli oggetti in scenari a concorrenza elevata.
- È stato ristrutturato il pacchetto NuGet. Sono state rimosse le tre copie di e nelle cartelle lib, rendendo il pacchetto NuGet più piccolo e più veloce da scaricare e sono state aggiunte le intestazioni necessarie per compilare alcune app `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` native C++.
- Correzione degli esempi di avvio rapido [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Questi si chiudevano senza visualizzare l'eccezione "Microfono non trovato" in Linux, macOS, Windows.
- Correzione dell'arresto anomalo dell'SDK con risultati lunghi del riconoscimento vocale in determinati percorsi di codice come [questo esempio.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Correzione dell'errore di distribuzione dell'SDK nell'ambiente dell'app Web di Azure [per risolvere il problema del cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Correzione di un errore TTS durante l'uso di più `<voice>` tag o tag per risolvere il problema del `<audio>` [cliente.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Correzione di un errore TTS 401 quando l'SDK viene ripristinato dalla sospensione.
- JavaScript: correzione di un'importazione circolare di dati audio grazie a un contributo di [euirim.](https://github.com/euirim) 
- JavaScript: aggiunta del supporto per l'impostazione delle proprietà del servizio, come aggiunto nella versione 1.7.
- JavaScript: è stato risolto un problema a causa del quale un errore di connessione può causare tentativi di riconnessione websocket continui e non riusciti.

**Esempi**

- Aggiunta dell'esempio di riconoscimento delle parole chiave per Android [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- È stato aggiunto l'esempio TTS per lo scenario server [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Sono state aggiunte guide introduttive alle conversazioni per più dispositivi per C# e C++ [qui](quickstarts/multi-device-conversation.md).

**Altre modifiche**

- Dimensioni ottimizzate della libreria principale dell'SDK in Android.
- L'SDK nella versione 1.9.0 e versioni successiva supporta entrambi i tipi e nel campo della versione della firma `int` `string` vocale per il trascrittore di conversazioni.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: versione 2019-novembre

**Nuove funzionalità**

- Aggiunta di un'API per semplificare l'uso con contenitori e `FromHost()` cloud sovrani sul sito.
- Aggiunta dell'origine Rilevamento lingua riconoscimento vocale (in Java e C++)
- Aggiunta `SourceLanguageConfig` dell'oggetto per riconoscimento vocale, usato per specificare le lingue di origine previste (in Java e C++)
- Aggiunta `KeywordRecognizer` del supporto per Windows (UWP), Android e iOS tramite i pacchetti NuGet e Unity
- Aggiunta dell'API Java Conversazione remota per eseguire la trascrizione della conversazione in batch asincroni.

**Modifiche di rilievo**

- Funzionalità trascrittori di conversazione spostate nello spazio dei nomi `Microsoft.CognitiveServices.Speech.Transcription` .
- Parti dei metodi Conversation Transcriber vengono spostate in una nuova `Conversation` classe.
- È stato eliminato il supporto per iOS a 32 bit (ARMv7 e x86)

**Correzioni di bug**

- Correzione dell'arresto anomalo del `KeywordRecognizer` sistema se locale viene usato senza una chiave di sottoscrizione del servizio Voce valida

**Esempi**

- Esempio di Xamarin per `KeywordRecognizer`
- Esempio di Unity per `KeywordRecognizer`
- Esempi di C++ e Java per l'Rilevamento lingua.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: versione 2019-settembre

**Nuove funzionalità**

- Aggiunta del supporto beta per Xamarin in piattaforma UWP (Universal Windows Platform) (UWP), Android e iOS
- Aggiunta del supporto iOS per Unity
- Aggiunta `Compressed` del supporto di input per ALaw, Mulaw, FLAC in Android, iOS e Linux
- Aggiunta `SendMessageAsync` nella classe per `Connection` l'invio di un messaggio al servizio
- Aggiunta `SetMessageProperty` nella classe per `Connection` l'impostazione della proprietà di un messaggio
- TTS ha aggiunto associazioni per Java (JRE e Android), Python, Swift e Objective-C
- TTS ha aggiunto il supporto per la riproduzione per macOS, iOS e Android.
- Aggiunta delle informazioni sul "confine di parola" per TTS.

**Correzioni di bug**

- Correzione del problema di compilazione IL2CPP in Unity 2019 per Android
- Correzione del problema relativo all'elaborazione non corretta delle intestazioni in formato non valido nell'input del file wav
- Risolto un problema per cui gli UUID non sono univoci in alcune proprietà di connessione
- Correzione di alcuni avvisi sugli identificatori di supporto dei valori Null nelle associazioni Swift (potrebbero essere necessarie piccole modifiche al codice)
- Correzione di un bug che causava la chiusura non corretta delle connessioni WebSocket in caso di carico di rete
- È stato risolto un problema in Android che talvolta causava ID di impression duplicati usati da `DialogServiceConnector`
- Miglioramenti alla stabilità delle connessioni tra interazioni a più turni e alla segnalazione di errori (tramite `Canceled` eventi) quando si verificano con `DialogServiceConnector`
- `DialogServiceConnector` L'avvio della sessione ora fornisce correttamente eventi, ad esempio quando si chiama `ListenOnceAsync()` durante un'attività `StartKeywordRecognitionAsync()`
- È stato corretto un arresto anomalo associato `DialogServiceConnector` alle attività ricevute

**Esempi**

- Avvio rapido per Xamarin
- Aggiornamento della guida introduttiva per CPP con le informazioni arm64 di Linux
- Guida introduttiva a Unity aggiornata con informazioni su iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: versione di giugno 2019

**Esempi**

- Esempi di avvio rapido per sintesi vocale in UWP e Unity
- Esempio di avvio rapido per Swift in iOS
- Esempi di Unity per riconoscimento vocale & Riconoscimento finalità traduzione
- Aggiornamento degli esempi di avvio rapido per `DialogServiceConnector`

**Miglioramenti/modifiche**

- Spazio dei nomi dialog:
  - `SpeechBotConnector` è stata rinominata `DialogServiceConnector`
  - `BotConfig` è stata rinominata `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` è stato mappato di nuovo a `DialogServiceConfig::FromBotSecret()`
  - Tutti i Direct Line Speech client esistenti continuano a essere supportati dopo la ridenominazione
- Aggiornare l'adattatore REST TTS per supportare il proxy, connessione persistente
- Migliorare il messaggio di errore quando viene passata un'area non valida
- Swift/Objective-C:
  - Segnalazione errori migliorata: i metodi che possono generare un errore sono ora presenti in due versioni: una che espone un oggetto per la gestione degli errori e una che genera `NSError` un'eccezione. I primi sono esposti a Swift. Questa modifica richiede adattamenti al codice Swift esistente.
  - Gestione degli eventi migliorata

**Correzioni di bug**

- Correzione per TTS: dove il futuro è `SpeakTextAsync` stato restituito senza attendere il completamento del rendering dell'audio
- Correzione per il marshalling delle stringhe in C# per abilitare il supporto completo del linguaggio
- Correzione del problema dell'app .NET Core per caricare la libreria core con il framework di destinazione net461 negli esempi
- Correzione di problemi occasionali relativi alla distribuzione di librerie native nella cartella di output negli esempi
- Correzione per la chiusura affidabile del socket Web
- Correzione di un possibile arresto anomalo durante l'apertura di una connessione con carico elevato in Linux
- Correzione dei metadati mancanti nel bundle del framework per macOS
- Correzione dei problemi relativi `pip install --user` a in Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzioni di bug**

- Correzione di FromSubscription se usato con la trascrizione conversazione.
- Correzione del bug nell'individuare le parole chiave per gli assistenti vocali.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: versione di maggio 2019

**Nuove funzionalità**

- L'spotting delle parole chiave (KWS) è ora disponibile per Windows e Linux. La funzionalità KWS può funzionare con qualsiasi tipo di microfono, il supporto ufficiale di KWS, tuttavia, è attualmente limitato agli array di microfoni presenti nell'hardware Azure Kinect DK o in Speech Devices SDK.
- La funzionalità hint per la frase è disponibile tramite l'SDK. Per altre informazioni, vedere [qui](./get-started-speech-to-text.md).
- La funzionalità di trascrizione della conversazione è disponibile tramite l'SDK. Vedere [qui](./conversation-transcription.md).
- Aggiungere il supporto per gli assistenti vocali usando Direct Line Speech canale.

**Esempi**

- Aggiunta di esempi per nuove funzionalità o nuovi servizi supportati dall'SDK.

**Miglioramenti/modifiche**

- Sono state aggiunte varie proprietà del sistema di riconoscimento per modificare il comportamento del servizio o i risultati del servizio,ad esempio mascherando il contenuto volgare e altre.
- È ora possibile configurare il riconoscimento tramite le proprietà di configurazione standard, anche se è stato creato il riconoscitore `FromEndpoint` .
- Objective-C: `OutputFormat` la proprietà è stata aggiunta a `SPXSpeechConfiguration` .
- L'SDK supporta ora Debian 9 come distribuzione Linux.

**Correzioni di bug**

- È stato risolto un problema a causa del quale la risorsa voce è stata distrutta troppo presto nella sintesi vocale.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Impedire al Web pack di caricare https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: versione 2019-aprile

**Nuove funzionalità**

- L'SDK supporta ora il servizio di sintesi vocale come versione beta. È supportato in Windows e Linux Desktop da C++ e C#. Per altre informazioni, vedere la [panoramica della sintesi vocale.](text-to-speech.md#get-started)
- L'SDK supporta ora i file audio MP3 e Opus/OGG come file di input di flusso. Questa funzionalità è disponibile solo in Linux da C++ e C# ed è attualmente in versione beta (altri dettagli [sono disponibili qui).](how-to-use-codec-compressed-audio-input-streams.md)
- Speech SDK per Java, .NET Core, C++ e Objective-C ha ottenuto il supporto di macOS. Il supporto Objective-C per macOS è attualmente in versione beta.
- iOS: Speech SDK per iOS (Objective-C) è ora pubblicato anche come CocoaPod.
- JavaScript: supporto per microfono non predefinito come dispositivo di input.
- JavaScript: supporto proxy per Node.js.

**Esempi**

- Sono stati aggiunti esempi per l'uso di Speech SDK con C++ e con Objective-C in macOS.
- Sono stati aggiunti esempi che illustrano l'utilizzo del servizio di sintesi vocale.

**Miglioramenti/modifiche**

- Python: le proprietà aggiuntive dei risultati del riconoscimento vengono ora esposte tramite la `properties` proprietà .
- Per il supporto aggiuntivo di sviluppo e debug, è possibile reindirizzare le informazioni di diagnostica e registrazione dell'SDK in un file di log (altre informazioni [sono disponibili qui).](how-to-use-logging.md)
- JavaScript: migliorare le prestazioni di elaborazione audio.

**Correzioni di bug**

- Mac/iOS: è stato risolto un bug che ha causato una lunga attesa quando non è stato possibile stabilire una connessione al servizio Voce.
- Python: migliorare la gestione degli errori per gli argomenti nei callback Python.
- JavaScript: correzione della segnalazione di stato errata per il riconoscimento vocale terminata in RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: aggiornamento 2019-febbraio

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzione di bug**

- Correzione di una perdita di memoria quando si usa l'input del microfono. L'input basato su flusso o su file non è interessato.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: versione di febbraio 2019

**Nuove funzionalità**

- Speech SDK supporta la selezione del microfono di input tramite la `AudioConfig` classe . In questo modo è possibile trasmettere dati audio al servizio Voce da un microfono non predefinito. Per altre informazioni, vedere la documentazione che descrive la selezione [del dispositivo di input audio.](how-to-select-audio-input-devices.md) Questa funzionalità non è ancora disponibile in JavaScript.
- Speech SDK supporta ora Unity in versione beta. Inviare commenti e suggerimenti tramite la sezione relativa al problema nel [repository di esempio GitHub.](https://aka.ms/csspeech/samples) Questa versione supporta Unity in Windows x86 e x64 (applicazioni desktop o per la piattaforma UWP) e Android (ARM32/64, x86). Altre informazioni sono disponibili nell'[Avvio rapido di Unity](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity).
- Il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata nell'SDK di base.

**Esempi**

I nuovi contenuti seguenti sono disponibili nel [repository di esempio](https://aka.ms/csspeech/samples):

- Esempi aggiuntivi per `AudioConfig.FromMicrophoneInput` .
- Altri esempi di Python per il riconoscimento finalità e la traduzione.
- Altri esempi per l'uso `Connection` dell'oggetto in iOS.
- Esempi aggiuntivi per Java per la traduzione con output audio.
- Nuovo esempio per l'uso dell'[API REST di trascrizione batch](batch-transcription.md).

**Miglioramenti/modifiche**

- Python
  - Miglioramento della verifica dei parametri e dei messaggi di errore in `SpeechConfig` .
  - Aggiunta del supporto per `Connection` l'oggetto .
  - Supporto per Python a 32 bit (x86) in Windows.
  - Speech SDK per Python non è incluso nella beta.
- iOS
  - L'SDK è ora basato sull'SDK per iOS versione 12.1.
  - L'SDK supporta ora iOS 9.2 e versioni successive.
  - Miglioramento della documentazione di riferimento e correzione di diversi nomi di proprietà.
- JavaScript
  - Aggiunta del supporto per `Connection` l'oggetto .
  - Aggiunta di file di definizione del tipo per JavaScript in bundle.
  - Supporto iniziale e implementazione per gli hint della frase.
  - Restituzione della raccolta di proprietà con il file JSON del servizio per il riconoscimento.
- Le DLL di Windows contengono ora una risorsa di versione.
- Se si crea un sistema di `FromEndpoint` riconoscimento, è possibile aggiungere parametri direttamente all'URL dell'endpoint. Non `FromEndpoint` è possibile configurare il riconoscimento tramite le proprietà di configurazione standard.

**Correzioni di bug**

- Il nome utente proxy e la password proxy vuoti non erano gestiti correttamente. Con questa versione, se si imposta il nome utente proxy e la password proxy su una stringa vuota, non verranno inviati quando ci si connette al proxy.
- Gli ID sessione creati dall'SDK non erano sempre realmente casuali per alcune lingue&nbsp;/ ambienti. Aggiunta dell'inizializzazione del generatore casuale per risolvere il problema.
- Gestione migliorata del token di autorizzazione. Se si vuole usare un token di autorizzazione, specificare in `SpeechConfig` e lasciare vuota la chiave di sottoscrizione. Creare quindi il sistema di riconoscimento come di consueto.
- In alcuni casi `Connection` l'oggetto non è stato rilasciato correttamente. Il problema è stato risolto.
- L'esempio di JavaScript è stato corretto per supportare l'output audio per la sintesi della traduzione anche in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Attivazione della fine del flusso in corrispondenza di turn.end e non di speech.end.
- Correzione di un bug nel data pump audio che non pianificava l'invio successivo in caso di errore dell'invio corrente.
- Correzione del riconoscimento continuo con il token di autenticazione.
- Correzione di bug per sistemi di riconoscimento/endpoint diversi.
- Miglioramenti alla documentazione.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: versione 2018-dicembre

**Nuove funzionalità**

- Python
  - La versione beta del supporto di Python (versione 3.5 e successive) è disponibile con questa versione. Per altre informazioni, vedere qui](quickstart-python.md).
- JavaScript
  - Speech SDK per JavaScript è ora open source. Il codice sorgente è disponibile in [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - È ora supportato Node.js. Altre informazioni sono reperibili [qui](./get-started-speech-to-text.md).
  - È stata rimossa la restrizione di lunghezza per le sessioni audio. La riconnessione avverrà automaticamente.
- Oggetto `Connection`
  - Da `Recognizer` , è possibile accedere a un `Connection` oggetto . Questo oggetto consente di avviare in modo esplicito la connessione al servizio e di aggiungere una sottoscrizione per connettere e disconnettere gli eventi.
    Questa funzionalità non è ancora disponibile in JavaScript e Python.
- Supporto per Ubuntu 18.04.
- Android
  - Supporto di ProGuard abilitato durante la generazione di APK.

**Miglioramenti**

- Miglioramenti nell'uso dei thread interni, con riduzione del numero di thread, blocchi e mutex.
- Miglioramento della segnalazione errori e delle informazioni sugli errori. In diversi casi, i messaggi di errore non sono stati propagati fino in tutto il tempo.
- Aggiornamento delle dipendenze di sviluppo in JavaScript per usare i moduli aggiornati.

**Correzioni di bug**

- Correzione di perdite di memoria a causa di una mancata corrispondenza del tipo in `RecognizeAsync` .
- In alcuni casi perdita delle eccezioni.
- Correzione della perdita di memoria negli argomenti degli eventi di conversione.
- Correzione di un problema di blocco per la riconnessione in sessioni a esecuzione prolungata.
- È stato risolto un problema che poteva causare la mancanza di un risultato finale per le traduzioni non riuscite.
- C#: se un'operazione non era attesa nel thread principale, era possibile che il riconoscitore potesse essere eliminato prima del completamento `async` dell'attività asincrona.
- Java: risolto un problema che causava un arresto anomalo della macchina virtuale Java.
- Objective-C: mapping fisso dell'enumerazione; È stato restituito RecognizedIntent anziché `RecognizingIntent` .
- JavaScript: impostare il formato di output predefinito su "simple" in `SpeechConfig` .
- JavaScript: rimozione dell'incoerenza tra le proprietà nell'oggetto config in JavaScript e in altri linguaggi.

**Esempi**

- Sono stati aggiornati e corretti diversi esempi ,ad esempio voci di output per la traduzione e così via.
- Aggiunta di esempi di Node.js nel [repository degli esempi](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nuove funzionalità**

- Supporto per Android x86/x64.
- Supporto proxy: nell'oggetto è ora possibile chiamare una funzione per impostare le informazioni `SpeechConfig` del proxy (nome host, porta, nome utente e password). Questa funzionalità non è ancora disponibile in iOS.
- Messaggi e codice di errore migliorati. Se un riconoscimento ha restituito un errore, l'elemento `Reason` (nell'evento annullato) o `CancellationDetails` (nel risultato del riconoscimento) è già stato impostato su `Error`. L'evento annullato contiene ora due membri aggiuntivi: `ErrorCode` e `ErrorDetails`. Se il server ha restituito l'errore corredato da informazioni aggiuntive, le informazioni saranno ora disponibili nei nuovi membri.

**Miglioramenti**

- È stata aggiunta una verifica supplementare nella configurazione del sistema di riconoscimento ed è stato aggiunto un nuovo messaggio di errore.
- È stata migliorata la gestione di un silenzio prolungato nel mezzo di un file audio.
- Pacchetto NuGet: per i progetti .NET Framework, impedisce la compilazione con una configurazione AnyCPU.

**Correzioni di bug**

- Sono state corrette varie eccezioni rilevate nei sistemi di riconoscimento. Inoltre, le eccezioni vengono intercettte e convertite in `Canceled` eventi.
- È stata corretta una perdita di memoria nella gestione delle proprietà.
- È stato corretto un bug per il quale un file di input audio può determinare l'arresto anomalo del sistema di riconoscimento.
- È stato risolto un bug per il quale si continuano a ricevere eventi anche dopo un evento di arresto della sessione.
- Sono state corrette alcune race condition nel threading.
- È stato corretto un problema di compatibilità iOS che può determinare un arresto anomalo.
- Sono stati apportati miglioramenti di stabilità per il supporto di microfoni Android.
- È stato risolto un bug per il quale un sistema di riconoscimento in JavaScript ignorava la lingua di riconoscimento.
- Correzione di un bug che impedisce l'impostazione `EndpointId` di (in alcuni casi) in JavaScript.
- Modifica dell'ordine dei parametri in AddIntent in JavaScript e aggiunta della `AddIntent` firma JavaScript mancante.

**Esempi**

- Sono stati aggiunti esempi C++ e C# per l'utilizzo dei flussi pull e push nel [repository di esempio](https://aka.ms/csspeech/samples).

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

- In questa versione vengono introdotte alcune modifiche di rilievo.
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
  - Si noti che le app UWP compilate con Speech SDK non passano ancora Kit di certificazione app Windows (WACK).
- Supporto del riconoscimento a esecuzione prolungata con riconnessione automatica.

**Modifiche funzionali**

- `StartContinuousRecognitionAsync()` supporta il riconoscimento a esecuzione prolungata.
- Il risultato del riconoscimento contiene più campi. Scostamento da inizio audio e durata (entrambi in tick) del testo riconosciuto, valori aggiuntivi che rappresentano lo stato di riconoscimento, ad esempio `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- Supporto del token di autorizzazione per la creazione di istanze di factory.

**Modifiche di rilievo**

- Eventi di riconoscimento: `NoMatch` il tipo di evento è stato unito all'evento. `Error`
- SpeechOutputFormat in C# è stato rinominato in per `OutputFormat` rimanere allineato a C++.
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
