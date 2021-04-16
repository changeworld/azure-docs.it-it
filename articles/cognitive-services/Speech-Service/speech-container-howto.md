---
title: Installare ed eseguire contenitori Docker per le API del servizio Voce
titleSuffix: Azure Cognitive Services
description: Usare i contenitori Docker per il servizio Voce per eseguire il riconoscimento vocale, la trascrizione, la generazione e altro ancora in locale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: locale, Docker, contenitore
ms.openlocfilehash: cb99dc3c5e16ee117df46d7fda0caab9c57f0853
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388092"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Installare ed eseguire contenitori Docker per le API del servizio Voce 

I contenitori consentono di eseguire alcune delle API del servizio Voce nell'ambiente in uso. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati. In questo articolo si apprenderà come scaricare, installare ed eseguire un contenitore del servizio Voce.

I contenitori del servizio Voce permettono ai clienti di creare un'architettura per le applicazioni ottimizzata sia per le funzionalità cloud avanzate e che per la posizione fisica dei dispositivi perimetrali. Sono disponibili diversi contenitori, che usano gli [stessi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) prezzi dei Servizi di riconoscimento vocale di Azure basati sul cloud.


> [!IMPORTANT]
> I contenitori voce seguenti sono ora disponibili a livello generale:
> * Riconoscimento vocale standard
> * Riconoscimento vocale personalizzato
> * Sintesi vocale standard
> * Sintesi vocale neurale
>
> I contenitori voce seguenti sono in anteprima recisa.
> * Sintesi vocale personalizzata
> * Riconoscimento vocale Rilevamento lingua 
>
> Per usare i contenitori di riconoscimento vocale, è necessario inviare una richiesta online e richiederla. Per altre informazioni, vedere la sezione **Richiedere l'approvazione per l'esecuzione** del contenitore riportata di seguito.

| Contenitore | Funzionalità | Ultima versione |
|--|--|--|
| Riconoscimento vocale | Analizza il sentiment e trascrive continue registrazioni vocali in tempo reale o audio batch con risultati intermedi.  | 2.11.0 |
| Riconoscimento vocale personalizzato | Usando un modello personalizzato dal portale [Riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech), trascrive continue registrazioni vocali in tempo reale o audio batch in testo con risultati intermedi. | 2.11.0 |
| Sintesi vocale | Converte il testo in sintesi vocale naturale con input di testo normale o SSML (Speech Synthesis Markup Language). | 1.13.0 |
| Sintesi vocale personalizzata | Usando un modello personalizzato dal portale [voce personalizzata](https://aka.ms/custom-voice-portal), converte il testo in sintesi vocale naturale con input di testo normale o SSML (Speech Synthesis Markup Language). | 1.13.0 |
| Riconoscimento vocale Rilevamento lingua | Rilevare la lingua parlata nei file audio. | 1,0 |
| Sintesi vocale neurale | Converte il testo in un parlato dal suono naturale usando una tecnologia di rete neurale profonda, consentendo una sintesi vocale più naturale. | 1.5.0 |

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti prima di usare i contenitori Voce:

| Necessario | Scopo |
|--|--|
| Motore Docker | È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br> |
| Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base. |
| Risorsa voce | Per usare questi contenitori, è necessario avere:<br><br>Risorsa _di_ Riconoscimento vocale di Azure per ottenere la chiave API e l'URI dell'endpoint associati. Entrambi i valori sono disponibili nelle pagine Speech **Overview** e Keys (Panoramica del riconoscimento vocale e Chiavi) del portale di Azure. Entrambi sono necessari per avviare il contenitore.<br><br>**{API_KEY}**: una delle due chiavi di risorsa disponibili nella **pagina** Chiavi<br><br>**{ENDPOINT_URI}**: endpoint come specificato nella pagina **Panoramica** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto avanzato dell'estensione vettoriale

L'**host** è il computer che esegue il contenitore Docker. L'host *deve supportare* [AvX2 (Advanced Vector Extensions).](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) È possibile verificare il supporto di AVX2 sugli host Linux con il comando seguente:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Il computer host è *necessario per* supportare AVX2. Il *contenitore non funzionerà* correttamente senza il supporto di AVX2.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore Voce.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Riconoscimento vocale | 2 core, 2 GB di memoria | 4 core, 4 GB di memoria |
| Riconoscimento vocale personalizzato | 2 core, 2 GB di memoria | 4 core, 4 GB di memoria |
| Sintesi vocale | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |
| Sintesi vocale personalizzata | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |
| Riconoscimento vocale Rilevamento lingua | 1 core, 1 GB di memoria | 1 core, 1 GB di memoria |
| Sintesi vocale neurale | 6 core, 12 GB di memoria | 8 core, 16 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

> [!NOTE]
> I valori minimi e consigliati sono basati sui limiti di Docker, *non* sulle risorse del computer host. Ad esempio, i contenitori speech-to-text mappano le parti  di un modello linguistico di grandi dimensioni ed è consigliabile che l'intero file si adatti alla memoria, ovvero altri 4-6 GB. Inoltre, la prima esecuzione di uno dei due contenitori può richiedere più tempo, poiché i modelli vengono disartirti in memoria.

## <a name="request-approval-to-the-run-the-container"></a>Richiedere l'approvazione per l'esecuzione del contenitore

Compilare e inviare il [modulo di richiesta](https://aka.ms/csgate) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Le immagini del contenitore per Voce sono disponibili nel Registro Contenitori seguente.

# <a name="speech-to-text"></a>[Sintesi vocale](#tab/stt)

| Contenitore | Archivio |
|-----------|------------|
| Riconoscimento vocale | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Riconoscimento vocale personalizzato](#tab/cstt)

| Contenitore | Archivio |
|-----------|------------|
| Riconoscimento vocale personalizzato | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

| Contenitore | Archivio |
|-----------|------------|
| Sintesi vocale | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Sintesi vocale neurale](#tab/ntts)

| Contenitore | Archivio |
|-----------|------------|
| Sintesi vocale neurale | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore | Archivio |
|-----------|------------|
| Sintesi vocale personalizzata | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Riconoscimento vocale Rilevamento lingua](#tab/lid)

> [!TIP]
> Per ottenere i risultati più utili, è consigliabile usare il contenitore Rilevamento lingua voce con i contenitori Riconoscimento vocale o Riconoscimento vocale personalizzato. 

| Contenitore | Archivio |
|-----------|------------|
| Riconoscimento vocale Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Pull Docker per i contenitori Voce

# <a name="speech-to-text"></a>[Riconoscimento vocale](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Pull di Docker per il contenitore Riconoscimento vocale

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore da Registro Contenitori Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> Il `latest` tag estrae le `en-US` impostazioni locali. Per altre impostazioni locali, [vedere Impostazioni locali per la sintesi vocale.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Impostazioni locali del riconoscimento vocale

Tutti i tag, ad eccezione di `latest` , sono nel formato seguente e con distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Il tag seguente è un esempio del formato :

```
2.6.0-amd64-en-us
```

Per tutte le impostazioni locali supportate del **contenitore di** riconoscimento vocale, vedere Tag delle immagini di riconoscimento [vocale.](../containers/container-image-tags.md#speech-to-text)

# <a name="custom-speech-to-text"></a>[Riconoscimento vocale personalizzato](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker pull per il contenitore Riconoscimento vocale personalizzato da testo a testo

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore da Registro Contenitori Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> e `locale` `voice` per i contenitori di Riconoscimento vocale personalizzati sono determinati dal modello personalizzato inserimento dal contenitore.

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker pull per il contenitore di sintesi vocale

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore da Registro Contenitori Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> Il `latest` tag estrae le impostazioni `en-US` locali e la `ariarus` voce. Per altre impostazioni locali, vedere [Impostazioni locali per la sintesi vocale.](#text-to-speech-locales)

#### <a name="text-to-speech-locales"></a>Impostazioni locali per la sintesi vocale

Tutti i tag, ad eccezione di `latest` , hanno il formato seguente e distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Il tag seguente è un esempio del formato:

```
1.8.0-amd64-en-us-ariarus
```

Per tutte le impostazioni locali supportate e  le voci corrispondenti del contenitore di sintesi vocale, vedere Tag immagine sintesi [vocale](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Quando si costruisce *un* POST HTTP di sintesi vocale, il messaggio [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) richiede un `voice` elemento con un attributo `name` . Il valore è la voce e le impostazioni locali del contenitore corrispondenti, note anche come ["nome breve".](language-support.md#standard-voices) Ad esempio, il `latest` tag avrebbe un nome vocale . `en-US-AriaRUS`

# <a name="neural-text-to-speech"></a>[Sintesi vocale neurale](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Pull Docker per il contenitore Neural Text-to-Speech

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal Registro Contenitori Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> Il `latest` tag estrae le impostazioni `en-US` locali e la `arianeural` voce. Per altre impostazioni locali, vedere Impostazioni locali per la sintesi vocale [neurale.](#neural-text-to-speech-locales)

#### <a name="neural-text-to-speech-locales"></a>Impostazioni locali per la sintesi vocale neurale

Tutti i tag, ad eccezione di sono nel formato seguente e con `latest` distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Il tag seguente è un esempio del formato :

```
1.3.0-amd64-en-us-arianeural
```

Per tutte le impostazioni locali supportate e le voci corrispondenti del contenitore di sintesi vocale **neurale,** vedere Tag di immagine di sintesi vocale [neurale.](../containers/container-image-tags.md#neural-text-to-speech)

> [!IMPORTANT]
> Quando si costruisce un POST HTTP di sintesi vocale *neurale,* il messaggio [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) richiede un `voice` elemento con un attributo `name` . Il valore è la voce e le impostazioni locali del contenitore corrispondenti, note anche come ["nome breve".](language-support.md#neural-voices) Ad esempio, il `latest` tag avrebbe un nome vocale . `en-US-AriaNeural`

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Pull docker per il contenitore di sintesi vocale personalizzato

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal Registro Contenitori Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> E `locale` per `voice` i contenitori Speech personalizzati è determinato dal modello personalizzato ingerito dal contenitore.

# <a name="speech-language-detection"></a>[Riconoscimento vocale Rilevamento lingua](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Pull di Docker per il contenitore di Rilevamento lingua voce

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore da Registro Contenitori Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](speech-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire una query sull'endpoint di stima del contenitore.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni [dettagliate su come](#gathering-required-parameters) ottenere i valori e , vedere Raccolta `{Endpoint_URI}` dei parametri `{API_Key}` obbligatori. Sono [disponibili](speech-container-configuration.md#example-docker-run-commands) anche altri `docker run` esempi del comando .

# <a name="speech-to-text"></a>[Riconoscimento vocale](#tab/stt)

Per eseguire il *contenitore Riconoscimento vocale* standard, eseguire il comando `docker run` seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *contenitore di riconoscimento vocale dall'immagine* del contenitore.
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

> [!NOTE]
> I contenitori supportano l'input audio compresso in Speech SDK tramite GStreamer.
> Per installare GStreamer in un contenitore, seguire le istruzioni di Linux per GStreamer in [Usare l'input audio compresso da codec con Speech SDK.](how-to-use-codec-compressed-audio-input-streams.md)

#### <a name="diarization-on-the-speech-to-text-output"></a>Diarizzazione dell'output del riconoscimento vocale
La diarizzazione è abilitata per impostazione predefinita. Per ottenere la diarizzazione nella risposta, usare `diarize_speech_config.set_service_property` .

1. Impostare il formato di output della frase su `Detailed` .
2. Impostare la modalità di diarizzazione. Le modalità supportate sono `Identity` e `Anonymous` .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> La modalità "Identity" restituisce `"SpeakerId": "Customer"` o `"SpeakerId": "Agent"` .
> La modalità "Anonima" restituisce `"SpeakerId": "Speaker 1"` o `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analizzare il sentiment nell'output del riconoscimento vocale 
A partire dalla versione 2.6.0 del contenitore speech-to-text, è consigliabile usare l'endpoint API TextAnalytics 3.0 anziché quello di anteprima. Ad esempio:
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> L'API Analisi del testo non è compatibile con le versioni precedenti `v3.0` Analisi del testo `v3.0-preview.1` . Per ottenere il supporto della funzionalità sentiment più recente, usare `v2.6.0` l'immagine del contenitore speech-to-text e Analisi del testo `v3.0` .

A partire dalla versione 2.2.0 del contenitore speech-to-text, è possibile chiamare [l'API sentiment analysis v3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) nell'output. Per chiamare l'analisi del sentiment, è necessario un endpoint Analisi del testo risorsa API. Ad esempio: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Se si accede a un endpoint di Analisi del testo nel cloud, è necessaria una chiave. Se si esegue l'Analisi del testo locale, potrebbe non essere necessario fornire questo valore.

La chiave e l'endpoint vengono passati al contenitore Speech come argomenti, come nell'esempio seguente.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Questo comando:

* Esegue gli stessi passaggi del comando precedente.
* Archivia un endpoint Analisi del testo API e una chiave per l'invio di richieste di analisi del sentiment. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Elenco di frasi v2 nell'output della sintesi vocale 

A partire dalla versione 2.6.0 del contenitore speech-to-text, è possibile ottenere l'output con le proprie frasi, l'intera frase o le frasi al centro. Ad *esempio, l'alto* nella frase seguente:

* "Questa è una frase che **l'uomo alto** è un'altra frase."

Per configurare un elenco di frasi, è necessario aggiungere frasi specifiche quando si effettua la chiamata. Ad esempio:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

Se sono presenti più frasi da aggiungere, chiamare `.addPhrase()` per ogni frase per aggiungerla all'elenco di frasi. 


# <a name="custom-speech-to-text"></a>[Riconoscimento vocale personalizzato](#tab/cstt)

Il *Riconoscimento vocale personalizzato da testo a* testo si basa su un modello di riconoscimento vocale personalizzato. Il training del modello personalizzato deve essere [stato creato](how-to-custom-speech-train-model.md) tramite il portale di riconoscimento [vocale personalizzato.](https://speech.microsoft.com/customspeech)

Per eseguire il **contenitore** è necessario l'ID modello di riconoscimento vocale personalizzato. È disponibile nella pagina **Training** del portale di riconoscimento vocale personalizzato. Nel portale di riconoscimento vocale personalizzato passare alla pagina **Training (Training)** e selezionare il modello.
<br>

![Pagina di training del riconoscimento vocale personalizzato](media/custom-speech/custom-speech-model-training.png)

Ottenere **l'ID** modello da usare come argomento per `ModelId` il parametro del `docker run` comando.
<br>

![Dettagli del modello di riconoscimento vocale personalizzato](media/custom-speech/custom-speech-model-details.png)

La tabella seguente rappresenta i vari `docker run` parametri e le relative descrizioni corrispondenti:

| Parametro | Descrizione |
|---------|---------|
| `{VOLUME_MOUNT}` | Montaggio del [volume del computer host,](https://docs.docker.com/storage/volumes/)che docker usa per rendere persistente il modello personalizzato. Ad esempio, *C:\CustomSpeech* in cui si trova *l'unità C* nel computer host. |
| `{MODEL_ID}` | ID Riconoscimento vocale personalizzato **modello** dalla pagina **Training** del portale di riconoscimento vocale personalizzato. |
| `{ENDPOINT_URI}` | L'endpoint è necessario per la misurazione e la fatturazione. Per altre informazioni, vedere [Raccolta dei parametri obbligatori](#gathering-required-parameters). |
| `{API_KEY}` | La chiave API è obbligatoria. Per altre informazioni, vedere [Raccolta dei parametri obbligatori](#gathering-required-parameters). |

Per eseguire il *Riconoscimento vocale personalizzato contenitore* da testo a testo, eseguire il comando `docker run` seguente:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *Riconoscimento vocale personalizzato contenitore da testo a testo* dall'immagine del contenitore.
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria.
* Carica il *Riconoscimento vocale personalizzato da testo a* testo dal montaggio di input del volume, ad esempio *C:\CustomSpeech*.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Scarica il modello in base a `ModelId` (se non viene trovato nel montaggio del volume).
* Se il modello personalizzato è stato scaricato in precedenza, `ModelId` viene ignorato.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Download del modello di base nel contenitore di riconoscimento vocale personalizzato  
A partire dalla versione 2.6.0 del contenitore custom-speech-to-text, è possibile ottenere le informazioni disponibili sul modello di base usando l'opzione `BaseModelLocale=<locale>` . Questa opzione consente di visualizzare un elenco dei modelli di base disponibili nelle impostazioni locali nell'account di fatturazione. Ad esempio:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *Riconoscimento vocale personalizzato contenitore da testo a* testo dall'immagine del contenitore.
* Controllare e restituire i modelli di base disponibili delle impostazioni locali di destinazione.

L'output fornisce un elenco di modelli di base con le impostazioni locali delle informazioni, l'ID del modello e l'ora della data di creazione. È possibile usare l'ID modello per scaricare e usare il modello di base specifico preferito. Ad esempio:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Pronuncia personalizzata nel contenitore di riconoscimento vocale personalizzato 
A partire dalla versione 2.5.0 del contenitore custom-speech-to-text, è possibile ottenere un risultato di pronuncia personalizzato nell'output. È necessario solo configurare regole di pronuncia personalizzate nel modello personalizzato e montare il modello nel contenitore di riconoscimento vocale personalizzato.


# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

Per eseguire il *contenitore Di sintesi vocale* standard, eseguire il comando `docker run` seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *contenitore di sintesi vocale* standard dall'immagine del contenitore.
* Alloca 1 core CPU e 2 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="neural-text-to-speech"></a>[Sintesi vocale neurale](#tab/ntts)

Per eseguire il *contenitore Neural Text-to-Speech,* eseguire il comando `docker run` seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *contenitore di sintesi vocale neurale* dall'immagine del contenitore.
* Alloca 6 core CPU e 12 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

Il *contenitore di sintesi vocale* personalizzato si basa su un modello di voce personalizzato. Il training del modello personalizzato deve essere [stato creato](how-to-custom-voice-create-voice.md) tramite il portale [vocale personalizzato.](https://aka.ms/custom-voice-portal) **L'ID modello voce** personalizzato è necessario per eseguire il contenitore. È disponibile nella pagina **Training** del portale vocale personalizzato. Nel portale vocale personalizzato passare alla pagina **Training (Training)** e selezionare il modello.
<br>

![Pagina di training vocale personalizzato](media/custom-voice/custom-voice-model-training.png)

Ottenere **l'ID modello** da usare come argomento per `ModelId` il parametro del comando docker run.
<br>

![Dettagli del modello di voce personalizzata](media/custom-voice/custom-voice-model-details.png)

La tabella seguente rappresenta i vari `docker run` parametri e le relative descrizioni corrispondenti:

| Parametro | Descrizione |
|---------|---------|
| `{VOLUME_MOUNT}` | Montaggio del [volume del computer host,](https://docs.docker.com/storage/volumes/)che docker usa per rendere persistente il modello personalizzato. Ad esempio, *C:\CustomSpeech* in cui si trova *l'unità C* nel computer host. |
| `{MODEL_ID}` | ID Riconoscimento vocale personalizzato **modello** dalla pagina **Training** del portale vocale personalizzato. |
| `{ENDPOINT_URI}` | L'endpoint è necessario per la misurazione e la fatturazione. Per altre informazioni, vedere [Raccolta dei parametri obbligatori](#gathering-required-parameters). |
| `{API_KEY}` | La chiave API è obbligatoria. Per altre informazioni, vedere [Raccolta dei parametri obbligatori](#gathering-required-parameters). |

Per eseguire il *contenitore di sintesi vocale* personalizzato, eseguire il comando `docker run` seguente:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *contenitore di sintesi vocale* personalizzato dall'immagine del contenitore.
* Alloca 1 core CPU e 2 gigabyte (GB) di memoria.
* Carica il *modello di sintesi vocale personalizzato* dal montaggio di input del volume, ad esempio *C:\CustomVoice*.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Scarica il modello in base a `ModelId` (se non viene trovato nel montaggio del volume).
* Se il modello personalizzato è stato scaricato in precedenza, `ModelId` viene ignorato.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="speech-language-detection"></a>[Riconoscimento vocale Rilevamento lingua](#tab/lid)

Per eseguire il *contenitore Speech Rilevamento lingua,* eseguire il comando `docker run` seguente.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando: 

* Esegue un contenitore di rilevamento della lingua vocale dall'immagine del contenitore. Attualmente non verrà addebitato alcun costo per l'esecuzione di questa immagine.
* Alloca 1 core CPU e 1 gigabyte (GB) di memoria.
* Espone la porta TCP 5003 e alloca una pseudo-TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

Se si inviano solo richieste Rilevamento lingua voce, è necessario impostare il valore del client voce `phraseDetection` su `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Se si vuole eseguire questo contenitore con il contenitore speech-to-text, è possibile usare questa [immagine Docker](https://hub.docker.com/r/antsu/on-prem-client). Dopo aver avviato entrambi i contenitori, usare questo comando Docker Run per eseguire `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> L'aumento del numero di chiamate simultanee può influire sull'affidabilità e sulla latenza. Per il rilevamento della lingua, è consigliabile usare un massimo di 4 chiamate simultanee usando 1 CPU con e 1 GB di memoria. Per gli host con 2 CPU e 2 GB di memoria, è consigliabile un massimo di 6 chiamate simultanee.

***

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire una query sull'endpoint di stima del contenitore

> [!NOTE]
> Usare un numero di porta univoco se si eseguono più contenitori.

| Contenitori | SDK Host URL | Protocollo |
|--|--|--|
| Riconoscimento vocale standard e Riconoscimento vocale personalizzato da testo a testo | `ws://localhost:5000` | WS |
| Sintesi vocale (inclusi standard, personalizzati e neurali), rilevamento della lingua vocale | `http://localhost:5000` | HTTP |

Per altre informazioni sull'uso dei protocolli WSS e HTTPS, vedere Sicurezza [dei contenitori](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Riconoscimento vocale (standard e personalizzato)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analizzare la valutazione

Se sono stati forniti Analisi del testo credenziali dell'API di riconoscimento vocale al contenitore [,](#analyze-sentiment-on-the-speech-to-text-output)è possibile usare Speech SDK per inviare richieste di riconoscimento vocale con l'analisi del sentiment. È possibile configurare le risposte dell'API per l'uso di un *formato* semplice *o* dettagliato.
> [!NOTE]
> La versione 1.13 di Speech Service Python SDK presenta un problema identificato con l'analisi del sentiment. Usare la versione 1.12.x o precedente se si usa l'analisi del sentiment in Speech Service Python SDK.

# <a name="simple-format"></a>[Formato semplice](#tab/simple-format)

Per configurare il client voce per l'uso di un formato semplice, aggiungere `"Sentiment"` come valore per `Simple.Extensions` . Se si vuole scegliere una versione specifica Analisi del testo modello, sostituire `'latest'` nella configurazione `speechcontext-phraseDetection.sentimentAnalysis.modelversion` delle proprietà.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` restituirà il risultato del sentiment nel livello radice della risposta.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Formato dettagliato](#tab/detailed-format)

Per configurare il client voce per l'uso di un formato dettagliato, aggiungere `"Sentiment"` come valore per , o `Detailed.Extensions` `Detailed.Options` entrambi. Se si vuole scegliere una versione specifica Analisi del testo modello, sostituire `'latest'` nella configurazione `speechcontext-phraseDetection.sentimentAnalysis.modelversion` delle proprietà.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` fornisce il risultato del sentiment nel livello radice della risposta. `Detailed.Options` fornisce il risultato nel `NBest` livello della risposta. Possono essere usati separatamente o insieme.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Se si vuole disabilitare completamente l'analisi del sentiment, aggiungere un `false` valore a `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Sintesi vocale (standard, neurale e personalizzata)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Eseguire più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta esposta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Questo contenitore e un contenitore di Servizi cognitivi diverso sono in esecuzione contemporaneamente sull'HOST. Sono inoltre in esecuzione più contenitori dello stesso contenitore di Servizi cognitivi.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si avvia o si esegue il contenitore, è possibile che si verifichino problemi. Usare un [montaggio di](speech-container-configuration.md#mount-settings) output e abilitare la registrazione. In questo modo il contenitore sarà in grado di generare file di log utili per la risoluzione dei problemi.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori Voce inviano le informazioni di fatturazione ad Azure usando una *risorsa Voce* nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](speech-container-configuration.md).

## <a name="summary"></a>Riepilogo

In questo articolo sono stati illustrati i concetti e il flusso di lavoro per il download, l'installazione e l'esecuzione di contenitori voce. In sintesi:

* Speech offre quattro contenitori Linux per Docker, incapsulando varie funzionalità:
  * *Sintesi vocale*
  * *Riconoscimento vocale personalizzato*
  * *Sintesi vocale*
  * *Sintesi vocale personalizzata*
  * *Sintesi vocale neurale*
  * *Riconoscimento vocale Rilevamento lingua*
* Le immagini dei contenitori vengono scaricate dal registro contenitori in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* Sia che si utilizzi l'API REST (solo sintesi vocale) o l'SDK (sintesi vocale o sintesi vocale), si specifica l'URI host del contenitore. 
* Quando si crea un'istanza di un contenitore, è necessario fornire informazioni di fatturazione.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [configurare i contenitori per](speech-container-configuration.md) le impostazioni di configurazione
* Informazioni su come [usare i contenitori del servizio Voce con Kubernetes e Helm](speech-container-howto-on-premises.md)
* Usare più [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
