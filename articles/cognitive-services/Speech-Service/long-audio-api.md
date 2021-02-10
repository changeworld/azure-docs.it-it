---
title: API audio lunga-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Scopri in che modo l'API Long audio è progettata per la sintesi asincrona del testo in formato esteso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: e28bd5b5caca259201758f0c633b2120a411f422
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007449"
---
# <a name="long-audio-api"></a>API Long audio

L'API Long audio è progettata per la sintesi asincrona del testo a lungo termine (ad esempio, libri audio, articoli di notizie e documenti). Questa API non restituisce audio sintetizzato in tempo reale, ma l'aspettativa è che si esegua il polling delle risposte e si utilizzino gli output non appena vengono resi disponibili dal servizio. A differenza dell'API sintesi vocale usata dall'SDK vocale, l'API Long audio può creare un audio sintetizzato più lungo di 10 minuti, rendendolo ideale per gli editori e le piattaforme di contenuti audio per creare contenuti audio lunghi, ad esempio libri audio in un batch.

Vantaggi aggiuntivi dell'API Long audio:

* Il riconoscimento vocale sintetizzato restituito dal servizio usa le migliori voci neurali.
* Non è necessario distribuire un endpoint vocale perché sintetizza le voci in modalità batch None in tempo reale.

> [!NOTE]
> L'API Long audio supporta ora sia le [voci neurali pubbliche](./language-support.md#neural-voices) che le [voci neurali personalizzate](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Flusso di lavoro

In genere, quando si usa l'API Long audio, si invierà un file di testo o file da sintetizzare, eseguire il polling dello stato, quindi, se lo stato ha esito positivo, è possibile scaricare l'output audio.

Questo diagramma fornisce una panoramica di alto livello del flusso di lavoro.

![Diagramma del flusso di lavoro API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparare il contenuto per la sintesi

Quando si prepara il file di testo, verificare che:

* Testo normale (con estensione txt) o testo SSML (. txt)
* Codificata come [UTF-8 con BOM (Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* È un singolo file, non un file zip
* Contiene più di 400 caratteri per testo normale o 400 [caratteri fatturabili](./text-to-speech.md#pricing-note) per il testo SSML e meno di 10.000 paragrafi
  * Per testo normale, ogni paragrafo viene **separato premendo l'** esempio di [input di testo normale](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Per il testo SSML, ogni elemento SSML è considerato un paragrafo. Le parti SSML devono essere separate da paragrafi diversi, [ad esempio visualizzare input di testo SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)

## <a name="python-example"></a>Esempio in Python

Questa sezione contiene esempi Python che mostrano l'uso di base dell'API Long audio. Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice in un file denominato `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Queste librerie vengono usate per costruire la richiesta HTTP e chiamano l'API REST di sintesi audio lunga da sintesi vocale.

### <a name="get-a-list-of-supported-voices"></a>Ottenere un elenco di voci supportate

Per ottenere un elenco di voci supportate, inviare una richiesta GET a `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Questo codice consente di ottenere un elenco completo delle voci per un'area o un endpoint specifico che è possibile usare.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Sostituire i valori seguenti:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).

Verrà visualizzato un output simile al seguente:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Se **Properties. publicAvailable** è **true**, la voce è una voce neurale pubblica. In caso contrario, si tratta di una voce neurale personalizzata.

### <a name="convert-text-to-speech"></a>Sintesi vocale

Preparare un file di testo di input, in formato testo normale o SSML, quindi aggiungere il codice seguente a `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` è un parametro facoltativo. Se questo parametro non è impostato, verranno generati gli output audio per paragrafo. È anche possibile concatenare gli audio in 1 output impostando il parametro. 
> `outputFormat` è anche facoltativo. Per impostazione predefinita, l'output audio è impostato su riff-16kHz-16 bit-mono-PCM. Per altre informazioni sui formati di output audio supportati, vedere [formati di output audio](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Sostituire i valori seguenti:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<input_file_path>` con il percorso del file di testo preparato per la sintesi vocale.
* Sostituire `<locale>` con le impostazioni locali di output desiderate. Per ulteriori informazioni, vedere [supporto](language-support.md#neural-voices)per le lingue.

Usare una delle voci restituite dalla chiamata precedente all' `/voices` endpoint.

* Se si usa una voce neurale pubblica, sostituire `<voice_name>` con la voce di output desiderata.
* Per utilizzare una voce neurale personalizzata, sostituire `voice_identities` la variabile con la seguente e sostituirla `<voice_id>` con la `id` della voce neurale personalizzata.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Verrà visualizzato un output simile al seguente:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Se si dispone di più di 1 file di input, sarà necessario inviare più richieste. È necessario tenere presenti alcune limitazioni.
> * Il client può inviare fino a **5** richieste al server al secondo per ogni account della sottoscrizione di Azure. Se supera la limitazione, il client otterrà un codice di errore 429 (troppe richieste). Ridurre il numero di richieste al secondo.
> * Il server può eseguire e accodare fino a **120** richieste per ogni account della sottoscrizione di Azure. Se supera la limitazione, il server restituirà un codice di errore 429 (troppe richieste). Attendere ed evitare di inviare una nuova richiesta fino a quando alcune richieste non vengono completate.

L'URL nell'output può essere usato per ottenere lo stato della richiesta.

### <a name="get-information-of-a-submitted-request"></a>Ottenere informazioni su una richiesta inviata

Per ottenere lo stato di una richiesta di sintesi inviata, è sufficiente inviare una richiesta GET all'URL restituito dal passaggio precedente.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
L'output sarà simile al seguente:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Da `status` Property è possibile leggere lo stato della richiesta. La richiesta viene avviata dallo `NotStarted` stato, quindi passa a `Running` e infine diventa `Succeeded` o `Failed` . È possibile usare un ciclo per eseguire il polling dell'API fino a quando lo stato non diventa `Succeeded` .

### <a name="download-audio-result"></a>Scarica risultato audio

Quando una richiesta di sintesi ha esito positivo, è possibile scaricare il risultato audio chiamando GET `/files` API.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Sostituire `<request_id>` con l'ID della richiesta per cui si vuole scaricare il risultato. Si trova nella risposta del passaggio precedente.

L'output sarà simile al seguente:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
L'output contiene informazioni di 2 file. Quello con `"kind": "LongAudioSynthesisScript"` è lo script di input inviato. L'altra con `"kind": "LongAudioSynthesisResult"` è il risultato della richiesta.
Il risultato è zip che contiene i file di output audio generati, insieme a una copia del testo di input.

Entrambi i file possono essere scaricati dall'URL nella relativa `links.contentUrl` Proprietà.

### <a name="get-all-synthesis-requests"></a>Ottenere tutte le richieste di sintesi

È possibile ottenere un elenco di tutte le richieste inviate con il codice seguente:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

L'output sarà simile al seguente:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` la proprietà contiene un elenco di richieste di sintesi. L'elenco è impaginato, con una dimensione massima di pagina di 100. Se sono presenti più di 100 richieste, `"@nextLink"` viene fornita una proprietà per ottenere la pagina successiva dell'elenco impaginato.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

È anche possibile personalizzare le dimensioni della pagina e ignorare il numero fornendo il `skip` `top` parametro URL.

### <a name="remove-previous-requests"></a>Rimuovi richieste precedenti

Il servizio manterrà fino a **20.000** richieste per ogni account della sottoscrizione di Azure. Se la quantità di richieste supera questa limitazione, rimuovere le richieste precedenti prima di renderne nuove. Se non si rimuovono le richieste esistenti, si riceverà una notifica di errore.

Nel codice seguente viene illustrato come rimuovere una specifica richiesta di sintesi.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Se la richiesta viene rimossa correttamente, il codice di stato della risposta sarà HTTP 204 (nessun contenuto).

```console
response.status_code: 204
```

> [!NOTE]
> Le richieste con stato `NotStarted` o `Running` non possono essere rimosse o eliminate.

Il completamento `long_audio_synthesis_client.py` è disponibile su [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Codici di stato HTTP

La tabella seguente illustra in dettaglio i messaggi e i codici di risposta HTTP dell'API REST.

| API | Codice di stato HTTP | Descrizione | Soluzione |
|-----|------------------|-------------|----------|
| Crea | 400 | La sintesi vocale non è abilitata in questa area. | Modificare la chiave di sottoscrizione vocale con un'area supportata. |
|        | 400 | Per questa area è valida solo la sottoscrizione vocale **standard** . | Modificare la chiave di sottoscrizione vocale nel piano tariffario "standard". |
|        | 400 | Superare il limite di richieste 20.000 per l'account Azure. Rimuovere alcune richieste prima di inviarne di nuove. | Il server manterrà fino a 20.000 richieste per ogni account Azure. Eliminare alcune richieste prima di inviarne di nuove. |
|        | 400 | Non è possibile usare questo modello nella sintesi vocale: {modelID}. | Verificare che lo stato di {modelID} sia corretto. |
|        | 400 | L'area per la richiesta non corrisponde all'area del modello: {modelID}. | Verificare che l'area di {modelID} corrisponda all'area della richiesta. |
|        | 400 | La sintesi vocale supporta solo il file di testo nella codifica UTF-8 con il marcatore dell'ordine dei byte. | Verificare che i file di input siano nella codifica UTF-8 con il marcatore dell'ordine dei byte. |
|        | 400 | Nella richiesta di sintesi vocale sono consentiti solo input SSML validi. | Verificare che le espressioni SSML di input siano corrette. |
|        | 400 | Il nome vocale {voicename} non è stato trovato nel file di input. | Il nome della voce SSML di input non è allineato con l'ID modello. |
|        | 400 | Il numero di paragrafi nel file di input deve essere minore di 10.000. | Verificare che il numero di paragrafi nel file sia inferiore a 10.000. |
|        | 400 | Il file di input deve contenere più di 400 caratteri. | Verificare che il file di input superi 400 caratteri. |
|        | 404 | Il modello dichiarato nella definizione di sintesi vocale non è stato trovato: {modelID}. | Assicurarsi che {modelID} sia corretto. |
|        | 429 | Superare il limite di sintesi vocale attiva. Attendere il completamento di alcune richieste. | Il server può eseguire e accodare fino a 120 richieste per ogni account Azure. Attendere ed evitare di inviare nuove richieste finché alcune richieste non vengono completate. |
| Tutti       | 429 | Troppe richieste. | Il client può inviare fino a 5 richieste al server al secondo per ogni account Azure. Ridurre il numero di richieste al secondo. |
| Elimina    | 400 | L'attività di sintesi vocale è ancora in uso. | È possibile eliminare solo le richieste **completate** o **non riuscite**. |
| GetByID   | 404 | Impossibile trovare l'entità specificata. | Verificare che l'ID di sintesi sia corretto. |

## <a name="regions-and-endpoints"></a>Aree ed endpoint

L'API Long audio è disponibile in più aree con endpoint univoci.

| Region | Endpoint |
|--------|----------|
| Stati Uniti orientali | `https://eastus.customvoice.api.speech.microsoft.com` |
| India centrale | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Asia sud-orientale | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Regno Unito meridionale | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa occidentale | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formati di output audio

Sono supportati formati di output audio flessibili. È possibile generare output audio per paragrafo o concatenare gli output audio in un singolo output impostando il parametro ' concatenateResult '. I formati di output audio seguenti sono supportati da Long audio API:

> [!NOTE]
> Il formato audio predefinito è riff-16kHz-16 bit-mono-PCM.

* riff-8kHz-16 bit-mono-PCM
* riff-16kHz-16 bit-mono-PCM
* riff-24kHz-16 bit-mono-PCM
* riff-kHz-16 bit-mono-PCM
* audio-16kHz-32kbitrate-mono-MP3
* audio-16kHz-64kbitrate-mono-MP3
* audio-16kHz-128kbitrate-mono-MP3
* audio-24kHz-48kbitrate-mono-MP3
* audio-24kHz-96kbitrate-mono-MP3
* audio-24kHz-160kbitrate-mono-MP3

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per l'API Long audio è disponibile su GitHub.

* [Codice di esempio: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Codice di esempio: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Codice di esempio: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
