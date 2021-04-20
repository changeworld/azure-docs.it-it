---
title: Servizi di comunicazione di Azure panoramica della registrazione delle chiamate
titleSuffix: An Azure Communication Services concept document
description: Viene fornita una panoramica della funzionalità Di registrazione delle chiamate e delle API.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730609"
---
# <a name="calling-recording-overview"></a>Panoramica della registrazione delle chiamate

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Molti paesi e stati hanno leggi e normative che si applicano alla registrazione di chiamate PSTN, vocali e video, che spesso richiedono il consenso degli utenti per la registrazione delle comunicazioni. È responsabilità dell'utente usare le funzionalità di registrazione delle chiamate in conformità con la legge. È necessario ottenere il consenso dalle parti delle comunicazioni registrate in modo conforme alle leggi applicabili a ogni partecipante.

> [!NOTE]
> Le normative sulla manutenzione dei dati personali richiedono la possibilità di esportare i dati utente. Per supportare questi requisiti, la registrazione dei file di metadati include il valore participantId per ogni partecipante della chiamata nella `participants` matrice. È possibile fare riferimento incrociato alle mri nella matrice con le identità utente interne `participants` per identificare i partecipanti a una chiamata. Di seguito è riportato un esempio di file di metadati di registrazione come riferimento.

Call Recording (Registrazione chiamate) fornisce un set di API per avviare, arrestare, sospendere e riprendere la registrazione. È possibile accedere a queste API dalla logica di business lato server o tramite eventi attivati dalle azioni dell'utente. L'output multimediale registrato è in formato , che `MP4 Audio+Video` è lo stesso formato utilizzato da Teams per registrare i file multimediali. Le notifiche relative ai file multimediali e ai metadati vengono generate tramite Griglia di eventi. Le registrazioni vengono archiviate per 48 ore nell'archiviazione temporanea incorporata per il recupero e lo spostamento in una soluzione di archiviazione a lungo termine preferita. 

## <a name="run-time-control-apis"></a>API di controllo di run-time
Le API di controllo in fase di esecuzione possono essere usate per gestire la registrazione tramite trigger di logica di business interni, ad esempio un'applicazione che crea una chiamata di gruppo e registra la conversazione o da un'azione attivata dall'utente che indica all'applicazione server di avviare la registrazione. In entrambi gli scenari è `<conversation-id>` necessario registrare una riunione o una chiamata specifica. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Recupero dell'ID conversazione da una chiamata avviata dal server

Un `ConversationId` oggetto viene restituito tramite l'evento `Microsoft.Communication.CallLegStateChanged` . Questa notifica dell'evento viene generata dopo che è stata stabilita una chiamata. È disponibile nel `data.ConversationId` campo . Questo valore può essere usato direttamente come parametro nelle API di `{conversationId}` controllo di run-time:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>Recupero dell'ID conversazione da un evento attivato dall'utente nel client

Dalla libreria JavaScript, dopo aver stabilito una chiamata invoke per ottenere `@azure/communication-calling` `let result = call.info.getConversationUrl()` , quindi `conversationUrl` **Base64Url `conversationUrl` `{conversationId}`** codifica l'oggetto per ottenere l'oggetto da usare nelle API del controllo di runtime. La codifica può essere eseguita sul client prima di inviare l'evento al server o sul lato server.

Si noti che deve essere codificato Base64Url, da non confondere con la semplice codifica `conversationUrl`  Base64 (ad esempio btoa).                                                            

### <a name="start-recording"></a>Avvia registrazione

#### <a name="request"></a>Richiesta

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**SDK per C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Risposta

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Ottenere lo stato di registrazione delle chiamate

#### <a name="request"></a>Richiesta

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**SDK per C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Risposta

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Arrestare registrazione
#### <a name="request"></a>Richiesta
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**SDK per C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Risposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Sospendere la registrazione
La sospensione e la ripresa della registrazione delle chiamate consentono di ignorare la registrazione di una parte di una chiamata o di una riunione e di riprendere la registrazione in un singolo file. 
#### <a name="request"></a>Richiesta
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**SDK per C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Risposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Riprendere la registrazione
#### <a name="request"></a>Richiesta
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**SDK per C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Risposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Tipi di output multimediali
La registrazione chiamate supporta attualmente il formato di output MP4 audio e video misto. Il supporto di output corrisponde alle registrazioni delle riunioni prodotte tramite la registrazione di Microsoft Teams.

| Tipo di canale | Formato contenuto | Video | Audio |
| :----------- | :------------- | :---- | :--------------------------- |
| Audiovideo | mp4 | 1920x1080 8 video FPS di tutti i partecipanti nella disposizione del riquadro predefinita | Audio misto mp4a a 16 kHz di tutti i partecipanti |

## <a name="event-grid-notifications"></a>Notifiche di Griglia di eventi
Una notifica di Griglia di eventi viene pubblicata quando una registrazione è pronta per il recupero, in genere 1-2 minuti dopo il completamento del processo di registrazione (ad esempio, la riunione è terminata, la registrazione è `Microsoft.Communication.RecordingFileStatusUpdated` stata interrotta). Le notifiche degli eventi di registrazione includono un ID documento, che può essere usato per recuperare sia i file multimediali registrati che un file di metadati di registrazione:

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Il codice di esempio per la gestione delle notifiche di Griglia di eventi e il download di file di registrazione e metadati sono disponibili [qui.](../../quickstarts/voice-video-calling/download-recording-file-sample.md) 

### <a name="notification-schema"></a>Schema di notifica
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>File Download

> Servizi di comunicazione di Azure l'archiviazione multimediale a breve termine per le registrazioni. **Esportare il contenuto registrato che si vuole conservare entro 48 ore.** Dopo 48 ore, le registrazioni non saranno più disponibili.

### <a name="download-recording"></a>Download della registrazione
#### <a name="request"></a>Richiesta
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Risposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Scaricare i metadati di registrazione
#### <a name="request"></a>Richiesta
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Risposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
