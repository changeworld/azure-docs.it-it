---
title: Eseguire la migrazione da V2 a V3 API REST-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Questo documento consente agli sviluppatori di eseguire la migrazione del codice dalla versione V2 alla versione V3 di nell'API REST per la sintesi vocale dei servizi vocali.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: e9e5db87f983c5db59715eb8b6a9561acf5fad14
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630616"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Eseguire la migrazione del codice dalla versione 2.0 alla versione 3.0 dell'API REST

Rispetto alla versione V2, la versione V3 dell'API REST di servizi vocali per la sintesi vocale è più affidabile, più semplice da usare e più coerente con le API per servizi simili. La maggior parte dei team può eseguire la migrazione da V2 a V3 in un giorno o due.

## <a name="forward-compatibility"></a>Compatibilità con le versioni successive

Tutte le entità dalla versione V2 possono essere trovate anche nell'API V3 con la stessa identità. Quando lo schema di un risultato è stato modificato (ad esempio, trascrizioni), il risultato di un GET nella versione V3 dell'API usa lo schema V3. Il risultato di un GET nella versione V2 dell'API usa lo stesso schema V2. Le entità appena create in V3 **non** sono disponibili nei risultati delle API v2.

## <a name="breaking-changes"></a>Modifiche di rilievo

L'elenco delle modifiche di rilievo è stato ordinato in base alla grandezza delle modifiche necessarie per adattarsi. Solo alcune modifiche richiedono modifiche non semplici nel codice chiamante. La maggior parte delle modifiche richiede solo una modifica ai nomi degli elementi.

### <a name="host-name-changes"></a>Modifiche al nome host

I nomi host dell'endpoint sono stati modificati da `{region}.cris.ai` a `{region}.api.cognitive.microsoft.com` . I percorsi dei nuovi endpoint non contengono più `api/` perché fa parte del nome host. Il [documento spavalderia](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) elenca le aree e i percorsi validi.
>[!IMPORTANT]
>Modificare il nome host da `{region}.cris.ai` a `{region}.api.cognitive.microsoft.com` dove Region è l'area della sottoscrizione vocale. Rimuovere anche `api/` da qualsiasi percorso nel codice client.

### <a name="identity-of-an-entity"></a>Identità di un'entità

La proprietà `id` è ora `self` . Nella versione V2, un utente dell'API doveva capire come vengono creati i percorsi nell'API. Questa operazione non era estensibile e richiedeva un lavoro non necessario da parte dell'utente. La proprietà `id` (UUID) viene sostituita da `self` (String), che corrisponde alla posizione dell'entità (URL). Il valore è ancora univoco tra tutte le entità. Se `id` viene archiviato come stringa nel codice, una ridenominazione è sufficiente per supportare il nuovo schema. È ora possibile usare il `self` contenuto come URL per le `GET` `PATCH` chiamate REST, e `DELETE` per l'entità.

Se l'entità dispone di funzionalità aggiuntive disponibili tramite altri percorsi, sono elencate in `links` . L'esempio seguente per la trascrizione Mostra un metodo separato per `GET` il contenuto della trascrizione:
>[!IMPORTANT]
>Rinominare la proprietà `id` `self` in nel codice client. Modificare il tipo da `uuid` a `string` se necessario. 

**trascrizione V2:**

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

**trascrizione V3:**

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

A seconda dell'implementazione del codice, potrebbe non essere sufficiente rinominare la proprietà. È consigliabile usare i `self` valori restituiti e `links` come URL di destinazione delle chiamate REST, anziché generare percorsi nel client. Utilizzando gli URL restituiti, è possibile assicurarsi che le future modifiche nei percorsi non interrompano il codice client.

### <a name="working-with-collections-of-entities"></a>Utilizzo di raccolte di entità

In precedenza l'API v2 restituiva tutte le entità disponibili in un risultato. Per consentire un controllo più granulare sulle dimensioni di risposta previste in V3, tutti i risultati della raccolta sono impaginati. Si ha il controllo sul numero di entità restituite e sull'offset iniziale della pagina. Questo comportamento rende più semplice la stima del runtime del processore di risposta.

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

La `values` proprietà contiene un subset delle entità della raccolta disponibili. Il conteggio e l'offset possono essere controllati usando `skip` i `top` parametri di query e. Quando `@nextLink` non è `null` , sono disponibili più dati e il batch di dati successivo può essere recuperato eseguendo un'operazione Get on `$.@nextLink` .

Questa modifica richiede la chiamata `GET` di per la raccolta in un ciclo fino a quando non vengono restituiti tutti gli elementi.

>[!IMPORTANT]
>Quando la risposta di un oggetto GET a `speechtotext/v3.0/{collection}` contiene un valore in, continuare l'esecuzione di fino a quando `$.@nextLink` `GETs` `$.@nextLink` `$.@nextLink` non è impostato per recuperare tutti gli elementi della raccolta.

### <a name="creating-transcriptions"></a>Creazione di trascrizioni

Una descrizione dettagliata di come creare batch di trascrizioni si trova in [procedura per la trascrizione batch](./batch-transcription.md).

L'API di trascrizione v3 consente di impostare in modo esplicito specifiche opzioni di trascrizione. Tutte le proprietà di configurazione (facoltative) possono ora essere impostate nella `properties` Proprietà.
La versione V3 supporta inoltre più file di input, pertanto è necessario un elenco di URL anziché un singolo URL come V2. Il nome della proprietà v2 `recordingsUrl` è ora `contentUrls` in V3. La funzionalità di analisi del sentimento nelle trascrizioni è stata rimossa in V3. Vedere [analisi del testo](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) del servizio cognitivo Microsoft per le opzioni di analisi dei sentimenti.

La nuova proprietà `timeToLive` in `properties` può semplificare l'eliminazione delle entità completate esistenti. `timeToLive`Specifica un periodo di tempo dopo il quale un'entità completata verrà eliminata automaticamente. Impostarla su un valore elevato, ad esempio, `PT12H` quando le entità vengono continuamente rilevate, utilizzate ed eliminate e pertanto di solito elaborate lunghe prima che siano trascorse 12 ore.

**corpo della richiesta POST trascrizione V2:**

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

**corpo della richiesta POST trascrizione V3:**

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
>[!IMPORTANT]
>Rinominare la proprietà `recordingsUrl` in `contentUrls` e passare una matrice di URL anziché un singolo URL. Passare le impostazioni per `diarizationEnabled` o `wordLevelTimestampsEnabled` come `bool` anziché `string` .

### <a name="format-of-v3-transcription-results"></a>Formato dei risultati della trascrizione V3

Lo schema dei risultati della trascrizione è stato modificato leggermente per allinearsi con le trascrizioni create dagli endpoint in tempo reale. Trovare una descrizione dettagliata del nuovo formato nella [procedura per la trascrizione batch](./batch-transcription.md). Lo schema del risultato viene pubblicato nel repository di [esempio GitHub](https://aka.ms/csspeech/samples) in `samples/batch/transcriptionresult_v3.schema.json` .

I nomi delle proprietà sono ora con la combinazione di maiuscole e minuscole e i valori per `channel` e `speaker` ora usano tipi Integer. Il formato per le durate ora usa la struttura descritta in ISO 8601, che corrisponde alla formattazione della durata usata in altre API di Azure.

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
>[!IMPORTANT]
>Deserializzare il risultato della trascrizione nel nuovo tipo, come illustrato in precedenza. Anziché un singolo file per canale audio, distinguere i canali controllando il valore della proprietà di `channel` per ogni elemento in `recognizedPhrases` . È ora disponibile un unico file di risultati per ogni file di input.


### <a name="getting-the-content-of-entities-and-the-results"></a>Ottenere il contenuto delle entità e i risultati

Nella versione V2 i collegamenti ai file di input o di risultati sono stati inline con il resto dei metadati dell'entità. Come miglioramento in V3, esiste una netta separazione tra i metadati dell'entità (restituiti da GET on `$.self` ) e i dettagli e le credenziali per accedere ai file di risultati. Questa separazione consente di proteggere i dati dei clienti e consente un controllo accurato sulla durata della validità delle credenziali.

In V3 `links` includere una sottoproprietà denominata `files` nel caso in cui l'entità esponga dati (set di dati, trascrizioni, endpoint o valutazioni). Un GET on restituirà `$.links.files` un elenco di file e un URL di firma di accesso condiviso per accedere al contenuto di ogni file. Per controllare la durata di validità degli URL di firma di accesso condiviso, `sasValidityInSeconds` è possibile usare il parametro di query per specificare la durata.

**trascrizione V2:**

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

**trascrizione V3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Un GET on `$.links.files` comporterebbe:**

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

La `kind` proprietà indica il formato del contenuto del file. Per le trascrizioni, i file di tipo `TranscriptionReport` sono il riepilogo del processo e i file del tipo `Transcription` sono il risultato del processo stesso.

>[!IMPORTANT]
>Per ottenere i risultati delle operazioni, usare un `GET` in `/speechtotext/v3.0/{collection}/{id}/files` , non sono più contenuti nelle risposte di `GET` in `/speechtotext/v3.0/{collection}/{id}` o `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Personalizzazione di modelli

Prima di V3 era presente una distinzione tra un modello _acustico_ e un _modello di linguaggio_ durante il training di un modello. Questa distinzione ha comportato la necessità di specificare più modelli durante la creazione di endpoint o trascrizioni. Per semplificare questo processo per un chiamante, sono state rimosse le differenze e tutti gli elementi dipendono dal contenuto dei set di dati utilizzati per il training del modello. Con questa modifica, la creazione del modello supporta ora set di dati misti (dati del linguaggio e dati acustici). Gli endpoint e le trascrizioni richiedono ora un solo modello.

Con questa modifica, la necessità di un oggetto `kind` nell' `POST` operazione è stata rimossa e la `datasets[]` matrice può ora contenere più set di impostazioni dello stesso tipo o di tipi misti.

Per migliorare i risultati di un modello sottoposto a training, i dati acustici vengono automaticamente usati internamente durante il training della lingua. In generale, i modelli creati tramite l'API V3 forniscono risultati più accurati rispetto ai modelli creati con l'API v2.

>[!IMPORTANT]
>Per personalizzare la parte del modello acustico e della lingua, passare tutti i set di impostazioni di lingua e acustici richiesti in `datasets[]` del post a `/speechtotext/v3.0/models` . Verrà creato un singolo modello con entrambe le parti personalizzate.

### <a name="retrieving-base-and-custom-models"></a>Recupero di modelli di base e personalizzati

Per semplificare il recupero dei modelli disponibili, V3 ha separato le raccolte di "modelli di base" dai "modelli personalizzati" di proprietà del cliente. Le due route sono ora `GET /speechtotext/v3.0/models/base` e `GET /speechtotext/v3.0/models/` .

Nella versione V2 tutti i modelli sono stati restituiti insieme in un'unica risposta.

>[!IMPORTANT]
>Per ottenere un elenco dei modelli di base forniti per la personalizzazione, usare `GET` on `/speechtotext/v3.0/models/base` . È possibile trovare modelli personalizzati con un `GET` `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Nome di un'entità

La `name` proprietà è ora `displayName` . Questo è coerente con altre API di Azure in modo da non indicare le proprietà di identità. Il valore di questa proprietà non deve essere univoco e può essere modificato dopo la creazione dell'entità con un' `PATCH` operazione.

**trascrizione V2:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**trascrizione V3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Rinominare la proprietà `name` `displayName` in nel codice client.

### <a name="accessing-referenced-entities"></a>Accesso alle entità a cui si fa riferimento

Nella versione V2, le entità a cui si fa riferimento sono sempre inline, ad esempio i modelli utilizzati di un endpoint. L'annidamento delle entità ha prodotto risposte di grandi dimensioni e i consumer hanno raramente utilizzato il contenuto annidato. Per ridurre le dimensioni della risposta e migliorare le prestazioni, le entità a cui si fa riferimento non vengono più inline nella risposta. Viene invece visualizzato un riferimento all'altra entità e può essere usato direttamente per un successivo `GET` (si tratta di un URL), seguendo lo stesso modello del `self` collegamento.

**trascrizione V2:**

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

**trascrizione V3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Se è necessario utilizzare i dettagli di un modello a cui si fa riferimento, come illustrato nell'esempio precedente, è sufficiente emettere un GET on `$.model.self` .

>[!IMPORTANT]
>Per recuperare i metadati delle entità a cui si fa riferimento, eseguire un'operazione GET on `$.{referencedEntity}.self` , ad esempio per recuperare il modello di una trascrizione `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Recupero dei log degli endpoint

Versione V2 del servizio che supporta i risultati dell'endpoint di registrazione. Per recuperare i risultati di un endpoint con V2, è necessario creare una "esportazione dei dati", che rappresenta uno snapshot dei risultati definiti da un intervallo di tempo. Il processo di esportazione di batch di dati non era flessibile. L'API v3 consente l'accesso a ogni singolo file e consente l'iterazione attraverso di essi.

**Un endpoint V3 eseguito correttamente:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Risposta di GET `$.links.logs` :**

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

La paginazione dei log degli endpoint funziona in modo simile a tutte le altre raccolte, con la differenza che non è possibile specificare alcun offset A causa della grande quantità di dati disponibili, l'impaginazione è determinata dal server.

In V3, ogni log dell'endpoint può essere eliminato singolarmente eseguendo un' `DELETE` operazione su `self` di un file o utilizzando `DELETE` on `$.links.logs` . Per specificare una data di fine, `endDate` è possibile aggiungere il parametro di query alla richiesta.

>[!IMPORTANT]
>Anziché creare esportazioni di log da `/api/speechtotext/v2.0/endpoints/{id}/data` utilizzare `/v3.0/endpoints/{id}/files/logs/` per accedere singolarmente ai file di log. 

### <a name="using-custom-properties"></a>Utilizzo di proprietà personalizzate

Per separare le proprietà personalizzate dalle proprietà di configurazione facoltative, tutte le proprietà denominate in modo esplicito si trovano nella `properties` proprietà e tutte le proprietà definite dai chiamanti si trovano ora nella `customProperties` Proprietà.

**entità trascrizione V2:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**entità trascrizione V3:**

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

Questa modifica consente inoltre di utilizzare i tipi corretti su tutte le proprietà denominate in modo esplicito in `properties` (ad esempio, booleano anziché String).

>[!IMPORTANT]
>Passare tutte le proprietà personalizzate come `customProperties` anziché `properties` nelle `POST` richieste.

### <a name="response-headers"></a>Intestazioni di risposta

V3 non restituisce più l' `Operation-Location` intestazione in aggiunta all' `Location` intestazione sulle `POST` richieste. Il valore di entrambe le intestazioni nella versione V2 era lo stesso. Viene ora `Location` restituito solo.

Poiché la nuova versione dell'API è ora gestita da gestione API di Azure (gestione API), le intestazioni correlate alla limitazione `X-RateLimit-Limit` , `X-RateLimit-Remaining` e `X-RateLimit-Reset` non sono contenute nelle intestazioni della risposta.

>[!IMPORTANT]
>Leggere il percorso dall'intestazione della risposta `Location` anziché da `Operation-Location` . Nel caso di un codice di risposta 429, leggere il `Retry-After` valore dell'intestazione anziché `X-RateLimit-Limit` , `X-RateLimit-Remaining` o `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Test di accuratezza

I test di accuratezza sono stati rinominati in valutazioni perché il nuovo nome descrive meglio gli elementi che rappresentano. I nuovi percorsi sono: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Rinominare il segmento `accuracytests` di percorso `evaluations` in nel codice client.


## <a name="next-steps"></a>Passaggi successivi

Esaminare tutte le funzionalità di queste API REST comunemente usate fornite da servizi vocali:

* [API REST di riconoscimento vocale](rest-speech-to-text.md)
* [Documento spavaldo](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) per V3 dell'API REST
* Per il codice di esempio per eseguire le trascrizioni batch, visualizzare il [repository di esempio GitHub](https://aka.ms/csspeech/samples) nella `samples/batch` sottodirectory.
