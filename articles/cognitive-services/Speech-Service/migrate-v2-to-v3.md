---
title: Eseguire la migrazione da V2 a V3 API REST-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Rispetto alla versione V2, la nuova versione dell'API V3 contiene un set di modifiche di rilievo più piccole. Questo documento consente di eseguire la migrazione alla nuova versione principale in pochissimo tempo.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737993"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migrazione dalla versione 2.0 alla versione 3.0 dell'API REST di riconoscimento vocale a testo

La versione V3 dell'API REST vocale migliora rispetto alla versione precedente dell'API rispetto all'affidabilità e alla semplicità d'uso. Il layout dell'API è allineato in modo più accurato con altri Azure o API Servizi cognitivi. Questo consente di applicare le competenze esistenti quando si usa l'API per la sintesi vocale.

La panoramica delle API è disponibile come [documento spavaldo](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Questa è la soluzione ideale per fornire una panoramica dell'API e testare la nuova API.

Sono stati forniti esempi per C# e Python. Per le trascrizioni batch, gli esempi sono disponibili nel [repository di esempio GitHub](https://aka.ms/csspeech/samples) all'interno della `samples/batch` sottodirectory.

## <a name="forward-compatibility"></a>Compatibilità con le versioni successive

Per garantire una migrazione senza problemi a V3, tutte le entità dalla versione V2 possono essere trovate anche nell'API V3 con la stessa identità. Se è presente una modifica dello schema dei risultati, ad esempio trascrizioni, le risposte per una versione di GET in V3 dell'API saranno nello schema V3. Se si esegue il GET nella versione V2 dell'API, lo schema dei risultati sarà nel formato v2. Le entità appena create in V3 **non sono** disponibili nella versione V2.

## <a name="breaking-changes"></a>Modifiche di rilievo

L'elenco delle modifiche di rilievo è stato ordinato in base alla grandezza delle modifiche necessarie per adattarsi. Esistono solo alcune modifiche che richiedono una modifica non semplice nel codice chiamante. Per la maggior parte delle modifiche è necessario rinominare semplici. Il tempo impiegato dai team per eseguire la migrazione da V2 a V3 varia da un paio di ore a un paio di giorni. Tuttavia, i vantaggi di una maggiore stabilità, codice più semplice, risposte più veloci comportano un rapido offset dell'investimento. 

### <a name="host-name-changes"></a>Modifiche al nome host

I nomi host sono stati modificati da {Region}. Cris. ai in {Region}. API. cognitive. Microsoft. com. In questa modifica, i percorsi non contengono più "API/" perché fanno parte del nome host. Per una descrizione completa di aree e percorsi, vedere il [documento di spavalderia](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) .

### <a name="identity-of-an-entity"></a>Identità di un'entità

La proprietà `id` è stata sostituita con `self` . Nella versione V2, un utente dell'API doveva capire come vengono creati i percorsi nell'API. Questa operazione non era estensibile e richiedeva un lavoro non necessario da parte dell'utente. La proprietà `id` (UUID) viene sostituita da `self` (String), che corrisponde alla posizione dell'entità (URL). Il valore è ancora univoco tra tutte le entità. Se `id` viene archiviato come stringa nel codice, una semplice ridenominazione è sufficiente per supportare il nuovo schema. È ora possibile usare il `self` contenuto come URL per tutte le chiamate REST per l'entità (Get, patch, Delete).

Se l'entità dispone di funzionalità aggiuntive disponibili in altri percorsi, sono elencate in `links` . Un esempio valido è una trascrizione, che ha un metodo separato per `GET` il contenuto della trascrizione.

trascrizione V2:

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

trascrizione V3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

A seconda dell'implementazione del client, potrebbe non essere sufficiente rinominare la proprietà. Si consiglia di sfruttare i vantaggi dell'uso dei valori restituiti di `self` e `links` come URL di destinazione delle chiamate REST e non di generare i percorsi nel client. Utilizzando gli URL restituiti, è possibile assicurarsi che le future modifiche nei percorsi non interrompano il codice client.

### <a name="working-with-collections-of-entities"></a>Utilizzo di raccolte di entità

In precedenza l'API v2 restituiva tutte le entità disponibili in una risposta. Per consentire un controllo più granulare sulle dimensioni della risposta prevista, nella V3 vengono impaginate tutte le risposte delle raccolte. Si ha il controllo sul numero di entità restituite e sull'offset della pagina. Questo comportamento rende più semplice la stima del runtime del processore di risposta ed è coerente con altre API di Azure.

La forma di base della risposta è la stessa per tutte le raccolte:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

La proprietà `values` contiene un subset delle entità della raccolta disponibili. Il conteggio e l'offset possono essere controllati usando i parametri della query `skip` e `top` . Quando `@nextLink` non è null, sono disponibili più dati e il batch di dati successivo può essere recuperato eseguendo un'operazione Get on `$.@nextLink` .

Questa modifica richiede la chiamata `GET` di per la raccolta in un ciclo fino a quando non vengono restituiti tutti gli elementi.

### <a name="creating-transcriptions"></a>Creazione di trascrizioni

Per una descrizione dettagliata della procedura di creazione della trascrizione, vedere [procedure per la trascrizione batch](./batch-transcription.md).

La creazione di trascrizioni è stata modificata in V3 per consentire l'impostazione esplicita di opzioni di trascrizione specifiche. Tutte le proprietà di configurazione (facoltative) possono ora essere impostate nella `properties` Proprietà.
Anche la versione V3 supporta ora più file di input e quindi richiede un elenco di URL e non un URL singolo come richiesto da V2. Il nome della proprietà è stato rinominato da `recordingsUrl` a `contentUrls` . La funzionalità di analisi del sentimento nelle trascrizioni è stata rimossa in V3. Si consiglia invece di usare l'analisi del [testo](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) di servizi cognitivi Microsoft.

La nuova proprietà `timeToLive` in `properties` può semplificare l'eliminazione delle entità completate esistenti. `timeToLive`Specifica un periodo di tempo dopo il quale un'entità completata verrà eliminata automaticamente. Impostarla su un valore elevato, ad esempio, `PT12H` quando le entità vengono costantemente rilevate, utilizzate ed eliminate e pertanto vengono elaborate di solito lungo prima che vengano passate 12 ore.

corpo della richiesta POST trascrizione V2:

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

corpo della richiesta POST trascrizione V3:

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>Formato dei risultati della trascrizione V3

Lo schema dei risultati della trascrizione è stato leggermente modificato per allinearsi alle trascrizioni create dagli endpoint in tempo reale. Una descrizione approfondita del nuovo formato è disponibile nelle [procedure per la trascrizione batch](./batch-transcription.md). Lo schema del risultato viene pubblicato nel repository di [esempio GitHub](https://aka.ms/csspeech/samples) in `samples/batch/transcriptionresult_v3.schema.json` .

I nomi delle proprietà sono ora con la combinazione di maiuscole e minuscole e i valori per Channel e speaker usano tipi Integer. Per allineare il formato delle durate con altre API di Azure, ora è formattato come descritto in ISO 8601.

Esempio di risultato della trascrizione V3. Le differenze sono descritte nei commenti.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>Ottenere il contenuto delle entità e i risultati

Nella versione V2 i collegamenti ai file di input o di risultati sono stati inline con il resto dei metadati dell'entità. Come miglioramento in V3, esiste una netta separazione tra i metadati dell'entità, che viene restituita da un'operazione GET on `$.self` e i dettagli e le credenziali per accedere ai file dei risultati. Questa separazione consente di proteggere i dati dei clienti e consente un controllo accurato sulla durata della validità delle credenziali.

In V3 è presente una proprietà denominata in `files` collegamenti nel caso in cui l'entità esponga dati (set di dati, trascrizioni, endpoint, valutazioni). Una GET on restituirà `$.links.files` un elenco di file e URL di firma di accesso condiviso per accedere al contenuto di ogni file. Per controllare la durata di validità degli URL di firma di accesso condiviso, `sasValidityInSeconds` è possibile usare il parametro di query per specificare la durata.

trascrizione V2:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

trascrizione V3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Una GET on `$.links.files` comporterebbe:

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Indica il formato del contenuto del file. Per le trascrizioni, i file di tipo `TranscriptionReport` sono il riepilogo del processo e i file del tipo `Transcription` sono il risultato del processo stesso.

### <a name="customizing-models"></a>Personalizzazione di modelli

Prima della versione V3, era presente una distinzione tra un "modello acustico" e un "modello di linguaggio" durante il training di un modello. Questa distinzione ha comportato la necessità di specificare più modelli durante la creazione di endpoint o trascrizioni. Per semplificare questo processo per un chiamante, le differenze sono state rimosse e tutte dipendono dal contenuto dei set di dati utilizzati per il training del modello. Con questa modifica, la creazione del modello supporta ora set di dati misti (dati del linguaggio e dati acustici). Gli endpoint e le trascrizioni richiedono ora solo un modello.

Con questa modifica, la necessità di un oggetto `kind` nel post è stata rimossa e `datasets[]` ora può contenere più set di impostazioni di tipi misti o uguali.

Per migliorare i risultati di un modello sottoposto a training, i dati acustici vengono automaticamente usati internamente per la formazione della lingua. In generale, i modelli creati tramite l'API V3 forniscono risultati più accurati rispetto ai modelli creati con l'API v2.

### <a name="retrieving-base-and-custom-models"></a>Recupero di modelli di base e personalizzati

Per semplificare il recupero dei modelli disponibili, V3 ha separato le raccolte di "modelli di base" dai "modelli personalizzati" di proprietà del cliente. Le due route sono ora `GET /speechtotext/v3.0/models/base` e `GET /speechtotext/v3.0/models/` .

In precedenza tutti i modelli sono stati restituiti insieme in un'unica risposta.

### <a name="name-of-an-entity"></a>Nome di un'entità

Il nome della proprietà `name` è stato rinominato in `displayName` . Questo è allineato ad altre API di Azure in modo da non indicare le proprietà di identità. Il valore di questa proprietà non deve essere univoco e può essere modificato dopo la creazione dell'entità con un oggetto `PATCH` .

trascrizione V2:

```json
{
    "name": "Transcription using locale en-US"
}
```

trascrizione V3:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Accesso alle entità a cui si fa riferimento

In V2 le entità a cui si fa riferimento sono sempre state inline, ad esempio i modelli utilizzati di un endpoint. L'annidamento delle entità ha prodotto risposte di grandi dimensioni e i consumer hanno raramente utilizzato il contenuto annidato. Per ridurre le dimensioni della risposta e migliorare le prestazioni per tutti gli utenti dell'API, le entità a cui si fa riferimento non vengono più inline nella risposta. Viene invece utilizzato un riferimento all'altra entità, che può essere utilizzata direttamente. Questo riferimento può essere usato per un successivo `GET` (è anche un URL), seguendo lo stesso modello del `self` collegamento.

trascrizione V2:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

trascrizione V3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Se è necessario utilizzare i dettagli di un modello a cui si fa riferimento, come illustrato nell'esempio precedente, semplificare il problema a GET on `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Recupero dei log degli endpoint

La versione V2 del servizio supporta la registrazione delle risposte degli endpoint. Per recuperare i risultati di un endpoint con V2, è necessario creare una "esportazione dei dati", che rappresenta uno snapshot dei risultati definiti da un intervallo di tempo. Il processo di esportazione di batch di dati è diventato non flessibile. L'API v3 consente l'accesso a ogni singolo file e consente l'iterazione attraverso di essi.

Un endpoint V3 eseguito correttamente:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Risposta di GET `$.links.logs` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

La paginazione dei log degli endpoint funziona in modo simile a tutte le altre raccolte, con la differenza che non è possibile specificare alcun offset A causa della grande quantità di dati disponibili, è stata implementata l'impaginazione basata su server.

In V3, ogni log dell'endpoint può essere eliminato singolarmente eseguendo un'eliminazione sul `self` di un file o utilizzando delete on `$.links.logs` . Per specificare i dati finali, `endDate` è possibile aggiungere il parametro di query alla richiesta.

### <a name="using-custom-properties"></a>Uso delle proprietà "personalizzate"

Per separare le proprietà personalizzate dalle proprietà di configurazione facoltative, tutte le proprietà denominate in modo esplicito si trovano nella `properties` proprietà e tutte le proprietà definite dai chiamanti sono ora situate nella `customProperties` Proprietà.

entità trascrizione V2

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

entità trascrizione V3

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Questa modifica ha consentito anche l'uso di tipi corretti su tutte le proprietà denominate in modo esplicito in, `properties` ad esempio bool anziché String.

### <a name="response-headers"></a>Intestazioni di risposta

V3 non restituisce più l'intestazione oltre all' `Operation-Location` intestazione nelle `Location` richieste post. Il valore di entrambe le intestazioni usato come identico. Ora `Location` viene restituito solo.

Poiché la nuova versione dell'API è ora gestita da gestione API di Azure (gestione API), le intestazioni correlate alla limitazione `X-RateLimit-Limit` , `X-RateLimit-Remaining` e `X-RateLimit-Reset` non sono contenute nelle intestazioni della risposta.

### <a name="accuracy-tests"></a>Test di accuratezza

I test di accuratezza sono stati rinominati in valutazioni perché il nuovo nome descrive meglio gli elementi che rappresentano. I percorsi delle notizie sono ad esempio "https:///{Region}. API. cognitive. Microsoft. com/speechtotext/v 3.0/evaluations".
