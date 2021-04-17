---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590001"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Token di accesso utente per abilitare il client chiamante. Per altre informazioni, vedere [Creare e gestire i token di accesso](../../access-tokens.md).
- Facoltativo: completare la guida introduttiva per [aggiungere chiamate vocali all'applicazione.](../getting-started-with-calling.md)

## <a name="install-the-sdk"></a>Installare l'SDK

> [!NOTE]
> Questo documento usa ACS Calling Web SDK.

Usare il `npm install` comando per installare il Servizi di comunicazione di Azure e gli SDK comuni per JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Servizi di comunicazione di Azure Calling SDK:

| Nome                             | Descrizione                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Punto di ingresso principale per l'SDK chiamante.                                                                       |
| `CallAgent`                        | Usato per avviare e gestire le chiamate.                                                                                            |
| `DeviceManager`                    | Usato per gestire i dispositivi multimediali.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementa `CommunicationTokenCredential` l'interfaccia , utilizzata per creare un'istanza di `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inizializzare un'istanza callClient, creare un'istanza di CallAgent e accedere a deviceManager

Creare una nuova `CallClient` istanza. È possibile configurarlo con opzioni personalizzate, ad esempio un'istanza di Logger.

Quando si dispone di `CallClient` un'istanza di , è possibile creare `CallAgent` un'istanza chiamando il `createCallAgent` metodo `CallClient` sull'istanza . Restituisce in modo asincrono un `CallAgent` oggetto istanza.

Il `createCallAgent` metodo utilizza come `CommunicationTokenCredential` argomento. Accetta un [token di accesso utente](../../access-tokens.md).

È possibile usare il `getDeviceManager` metodo `CallClient` nell'istanza di per accedere a `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Effettuare una chiamata

Per creare e avviare una chiamata, usare una delle API in e fornire un utente creato tramite `callAgent` Communication Services Identity SDK.

La creazione e l'avvio delle chiamate sono sincroni. L'istanza di chiamata consente di sottoscrivere gli eventi di chiamata.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Eseguire una chiamata 1:n a un utente o pstn

Per chiamare un altro utente di Servizi di comunicazione, usare il metodo su e passare l'oggetto del destinatario creato con `startCall` la libreria di amministrazione di Servizi di `callAgent` `CommunicationUserIdentifier` [comunicazione](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Per eseguire una chiamata a una rete PSTN (Public Switched Telephone Network), usare il metodo su e `startCall` `callAgent` passare l'oggetto del `PhoneNumberIdentifier` destinatario. La risorsa di Servizi di comunicazione deve essere configurata per consentire le chiamate PSTN.

Quando si chiama un numero PSTN, specificare l'ID chiamante alternativo. Un ID chiamante alternativo è un numero di telefono (basato sullo standard E.164) che identifica il chiamante in una chiamata PSTN. È il numero di telefono visualizzato dal destinatario della chiamata per una chiamata in ingresso.

> [!NOTE]
> Le chiamate PSTN sono attualmente in anteprima privata. Per [l'accesso, applicare al early adopter .](https://aka.ms/ACS-EarlyAdopter)

Per una chiamata 1:1, usare il codice seguente:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Per una chiamata 1:n, usare il codice seguente:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Inserire una chiamata 1:1 con la videocamera

> [!IMPORTANT]
> Attualmente non può essere presente più di un flusso video locale in uscita.

Per eseguire una chiamata video, è necessario enumerare le fotocamere locali usando il `getCameras()` metodo in `deviceManager` .

Dopo aver selezionato una fotocamera, usarla per costruire `LocalVideoStream` un'istanza di . Passarlo `videoOptions` all'interno di come elemento `localVideoStream` all'interno della matrice al `startCall` metodo .


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Quando la chiamata si connette, inizia automaticamente a inviare un flusso video dalla fotocamera selezionata all'altro partecipante. Questo vale anche per le opzioni `Call.Accept()` video e le opzioni `CallAgent.join()` video.

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo

> [!NOTE]
> Il parametro è considerato metadati di sistema e può essere usato da Microsoft per `groupId` le operazioni necessarie per eseguire il sistema. Non includere i dati personali nel `groupId` valore . Microsoft non considera questo parametro come dati personali e il relativo contenuto può essere visibile ai dipendenti Microsoft o archiviato a lungo termine.
>
> Il `groupId` parametro richiede che i dati siano in formato GUID. È consigliabile usare GUID generati in modo casuale che non sono considerati dati personali nei sistemi.
>

Per avviare una nuova chiamata di gruppo o partecipare a una chiamata di gruppo in corso, usare il metodo e `join` passare un oggetto con una proprietà `groupId` . Il `groupId` valore deve essere un GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Partecipare a una riunione di Teams
> [!NOTE]
> Questa API viene fornita come anteprima per gli sviluppatori e potrebbe essere modificata in base ai commenti ricevuti. Non usare questa API in un ambiente di produzione. Per usare questa API, usare la versione "beta" di ACS Calling Web SDK

Per partecipare a una riunione di Teams, usare il metodo e passare un collegamento a una `join` riunione o le coordinate di una riunione.

Partecipare usando un collegamento alla riunione:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Partecipare usando le coordinate delle riunioni:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Ricevere una chiamata in ingresso

`callAgent`L'istanza genera un evento quando `incomingCall` l'identità registrata riceve una chiamata in ingresso. Per restare in ascolto di questo evento, sottoscrivere usando una delle opzioni seguenti:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`L'evento include `incomingCall` un'istanza che è possibile accettare o rifiutare.

## <a name="manage-calls"></a>Gestire le chiamate

Durante una chiamata, è possibile accedere alle proprietà della chiamata e gestire le impostazioni video e audio.

### <a name="check-call-properties"></a>Controllare le proprietà delle chiamate

Ottenere l'ID univoco (stringa) per una chiamata:

   ```js
    const callId: string = call.id;
   ```
Ottenere informazioni sulla chiamata:
> [!NOTE]
> Questa API viene fornita come anteprima per gli sviluppatori e potrebbe essere modificata in base ai commenti ricevuti. Non usare questa API in un ambiente di produzione. Per usare questa API, usare la versione "beta" di ACS Calling Web SDK
   ```js
   const callInfo = call.info;
   ```

Per informazioni sugli altri partecipanti alla chiamata, esaminare la `remoteParticipants` raccolta nell'istanza di 'call':

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identificare il chiamante di una chiamata in ingresso:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` è uno dei `CommunicationIdentifier` tipi .

Ottenere lo stato di una chiamata:

   ```js
   const callState = call.state;
   ```

   Viene restituita una stringa che rappresenta lo stato corrente di una chiamata:

  - `None`: stato della chiamata iniziale.
  - `Connecting`: stato di transizione iniziale quando viene inserita o accettata una chiamata.
  - `Ringing`: per una chiamata in uscita, indica che una chiamata sta per essere chiamata per i partecipanti remoti. È `Incoming` sul loro lato.
  - `EarlyMedia`: indica uno stato in cui viene riprodotto un annuncio prima della connessione della chiamata.
  - `Connected`: indica che la chiamata è connessa.
  - `LocalHold`: indica che la chiamata viene messa in attesa da un partecipante locale. Nessun supporto scorre tra l'endpoint locale e i partecipanti remoti.
  - `RemoteHold`: indica che la chiamata è stata messa in attesa dal partecipante remoto. Nessun supporto scorre tra l'endpoint locale e i partecipanti remoti.
  - `InLobby`: indica che l'utente è in stato di attesa.
  - `Disconnecting`: stato di transizione prima che la chiamata venga passata a uno `Disconnected` stato .
  - `Disconnected`: stato della chiamata finale. Se la connessione di rete viene persa, lo stato viene modificato in `Disconnected` dopo due minuti.

Scoprire perché una chiamata è terminata controllando la `callEndReason` proprietà :

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Informazioni su se la chiamata corrente è in ingresso o in uscita controllando la `direction` proprietà . Restituisce `CallDirection`.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Controllare se il microfono corrente è disattivato. Restituisce `Boolean`.

   ```js
   const muted = call.isMuted;
   ```

Verificare se il flusso di condivisione dello schermo viene inviato da un determinato endpoint controllando la `isScreenSharingOn` proprietà . Restituisce `Boolean`.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Esaminare i flussi video attivi controllando la `localVideoStreams` raccolta. Restituisce `LocalVideoStream` oggetti .

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Disattivare e riattivare l'audio

Per disattivare o riattivare l'endpoint locale, è possibile usare le `mute` API asincrone e `unmute` :

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio di video locali

Per avviare un video, è necessario enumerare le fotocamere usando `getCameras` il metodo `deviceManager` sull'oggetto . Creare quindi una nuova istanza di `LocalVideoStream` con la fotocamera desiderata e quindi passare l'oggetto al metodo `LocalVideoStream` `startVideo` :

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Dopo aver avviato correttamente l'invio di video, viene `LocalVideoStream` aggiunta un'istanza alla `localVideoStreams` raccolta in un'istanza di chiamata.

```js
call.localVideoStreams[0] === localVideoStream;
```

Per arrestare il video locale, `localVideoStream` passare l'istanza disponibile nella `localVideoStreams` raccolta:

```js
await call.stopVideo(localVideoStream);
```

È possibile passare a un dispositivo fotocamera diverso durante l'invio di un video richiamando `switchSource` su `localVideoStream` un'istanza:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Gestire i partecipanti remoti

Tutti i partecipanti remoti sono rappresentati dal `RemoteParticipant` tipo e disponibili tramite raccolta in `remoteParticipants` un'istanza di chiamata.

### <a name="list-the-participants-in-a-call"></a>Elencare i partecipanti a una chiamata

La `remoteParticipants` raccolta restituisce un elenco di partecipanti remoti in una chiamata:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Accedere alle proprietà dei partecipanti remoti

I partecipanti remoti hanno un set di proprietà e raccolte associate:

- `CommunicationIdentifier`: ottiene l'identificatore per un partecipante remoto. L'identità è uno dei `CommunicationIdentifier` tipi seguenti:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Può essere uno dei tipi `CommunicationIdentifier` seguenti:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: oggetto che rappresenta l'utente ACS.
  - `{ phoneNumber: '<E.164>' }`: oggetto che rappresenta il numero di telefono in formato E.164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: oggetto che rappresenta l'utente di Teams.
  - `{ id: string }`: identificatore di nuovo rientro dell'oggetto che non rientra in nessuno degli altri tipi di identificatore

- `state`: ottiene lo stato di un partecipante remoto.

  ```js
  const state = remoteParticipant.state;
  ```

  Lo stato può essere:

  - `Idle`: stato iniziale.
  - `Connecting`: stato della transizione mentre un partecipante si connette alla chiamata.
  - `Ringing`: il partecipante sta ringing.
  - `Connected`: il partecipante è connesso alla chiamata.
  - `Hold`: il partecipante è in attesa.
  - `EarlyMedia`: annuncio che viene riprodotto prima che un partecipante si connetta alla chiamata.
  - `InLobby`: indica che il partecipante remoto è in camera.
  - `Disconnected`: stato finale. Il partecipante è disconnesso dalla chiamata. Se il partecipante remoto perde la connettività di rete, lo stato cambia in `Disconnected` dopo due minuti.

- `callEndReason`: per informazioni sul motivo per cui un partecipante ha lasciato la chiamata, controllare la `callEndReason` proprietà :

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` status: per determinare se un partecipante remoto è disattivato, controllare la `isMuted` proprietà . Restituisce `Boolean`.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: per determinare se un partecipante remoto parla, controllare la `isSpeaking` proprietà . Restituisce `Boolean`.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: per esaminare tutti i flussi video inviati da un determinato partecipante in questa chiamata, controllare la `videoStreams` raccolta. Contiene `RemoteVideoStream` oggetti .

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: per ottenere il nome visualizzato per questo partecipante remoto, controllare `displayName` la proprietà che restituisce stringa. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante (un utente o un numero di telefono) a una chiamata, è possibile usare `addParticipant` . Specificare uno dei `Identifier` tipi. Restituisce in modo sincrono `remoteParticipant` l'istanza . `remoteParticipantsUpdated`L'evento da Call viene generato quando un partecipante viene aggiunto correttamente alla chiamata.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Rimuovere un partecipante da una chiamata

Per rimuovere un partecipante (utente o numero di telefono) da una chiamata, è possibile richiamare `removeParticipant` . È necessario passare uno dei `Identifier` tipi. Questa operazione viene risolta in modo asincrono dopo la rimozione del partecipante dalla chiamata. Anche il partecipante viene rimosso dalla `remoteParticipants` raccolta.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Eseguire il rendering dei flussi video dei partecipanti remoti

Per elencare i flussi video e i flussi di condivisione dello schermo dei partecipanti remoti, esaminare le `videoStreams` raccolte:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Per eseguire `RemoteVideoStream` il rendering di , è necessario sottoscrivere l'evento `isAvailableChanged` . Se la `isAvailable` proprietà cambia in , un `true` partecipante remoto invia un flusso. Successivamente, creare una nuova istanza di e quindi crearne una `VideoStreamRenderer` nuova usando il metodo `VideoStreamRendererView` `createView` asincrono.  È quindi possibile connettersi `view.target` a qualsiasi elemento dell'interfaccia utente.

Ogni volta che la disponibilità di un flusso remoto cambia, è possibile scegliere di eliminare l'intero , un oggetto specifico o mantenerlo, ma questo comporta la visualizzazione `VideoStreamRenderer` `VideoStreamRendererView` di un fotogramma video vuoto.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Proprietà del flusso video remoto

I flussi video remoti hanno le proprietà seguenti:

- `id`: ID di un flusso video remoto.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: può essere `Video` o `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: indica se un endpoint del partecipante remoto sta inviando attivamente un flusso.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Metodi e proprietà di VideoStreamRenderer
Creare un'istanza di che può essere collegata nell'interfaccia utente dell'applicazione per eseguire il rendering del flusso video remoto, usare il metodo asincrono, si risolve quando il flusso è pronto per il rendering e restituisce un oggetto con la proprietà che rappresenta l'elemento che può essere aggiunto in qualsiasi punto dell'albero `VideoStreamRendererView` `createView()` `target` `video` DOM

  ```js
  videoStreamRenderer.createView()
  ```

Eliminare e `videoStreamRenderer` tutte le istanze `VideoStreamRendererView` associate:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Metodi e proprietà di VideoStreamRendererView

Quando si crea un `VideoStreamRendererView` oggetto , è possibile specificare le proprietà e `scalingMode` `isMirrored` . `scalingMode` può essere `Stretch` `Crop` , o `Fit` . Se `isMirrored` viene specificato , il flusso sottoposto a rendering viene capovolto verticalmente.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Ogni `VideoStreamRendererView` istanza ha una proprietà che rappresenta la superficie di `target` rendering. Associare questa proprietà nell'interfaccia utente dell'applicazione:

```js
htmlElement.appendChild(view.target);
```

È possibile eseguire `scalingMode` l'aggiornamento richiamando il `updateScalingMode` metodo :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestione dei dispositivi

In `deviceManager` è possibile enumerare i dispositivi locali in grado di trasmettere i flussi audio e video in una chiamata . È anche possibile usarlo per richiedere l'autorizzazione per accedere ai microfoni e alle fotocamere del dispositivo locale.

È possibile accedere `deviceManager` chiamando il `callClient.getDeviceManager()` metodo :

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Ottenere i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione in `deviceManager` . L'enumerazione è un'azione asincrona

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Impostare il microfono e l'altoparlante predefiniti

In `deviceManager` è possibile impostare un dispositivo predefinito che verrà utilizzato per avviare una chiamata. Se le impostazioni predefinite del client non sono impostate, Servizi di comunicazione usa le impostazioni predefinite del sistema operativo.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Anteprima della fotocamera locale

È possibile usare `deviceManager` e per iniziare a eseguire il rendering dei flussi dalla fotocamera `VideoStreamRenderer` locale. Questo flusso non verrà inviato ad altri partecipanti. è un feed di anteprima locale.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Richiedere l'autorizzazione per la fotocamera e il microfono

Richiedere a un utente di concedere le autorizzazioni per fotocamera e microfono:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Questa operazione viene risolta con un oggetto che indica se `audio` e le autorizzazioni sono state `video` concesse:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Registrare le chiamate
> [!NOTE]
> Questa API viene fornita come anteprima per gli sviluppatori e potrebbe essere modificata in base ai commenti ricevuti. Non usare questa API in un ambiente di produzione. Per usare questa API, usare la versione "beta" di ACS Calling Web SDK

La registrazione delle chiamate è una funzionalità estesa `Call` dell'API di base. È prima necessario ottenere l'oggetto API della funzionalità di registrazione:

```js
const callRecordingApi = call.api(Features.Recording);
```

Quindi, per verificare se la chiamata viene registrata, esaminare la `isRecordingActive` proprietà di `callRecordingApi` . Restituisce `Boolean`.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

È anche possibile sottoscrivere la registrazione delle modifiche:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>Trasferire le chiamate
> [!NOTE]
> Questa API viene fornita come anteprima per gli sviluppatori e potrebbe essere modificata in base ai commenti ricevuti. Non usare questa API in un ambiente di produzione. Per usare questa API, usare la versione "beta" di ACS Calling Web SDK

Il trasferimento delle chiamate è una funzionalità estesa `Call` dell'API principale. È prima necessario ottenere l'oggetto API della funzionalità di trasferimento:

```js
const callTransferApi = call.api(Features.Transfer);
```

I trasferimenti di chiamata coinvolgono tre parti:

- *Transferor*: la persona che avvia la richiesta di trasferimento.
- *Trasferito:* la persona che viene trasferita.
- *Destinazione di* trasferimento: la persona a cui viene trasferito.

I trasferimenti seguono questa procedura:

1. Esiste già una chiamata connessa tra il *trasfertore* e il *trasferitore*. Il *trasferimento decide* di trasferire la  chiamata dal trasferitore all'oggetto di destinazione *del trasferimento.*
1. Il *trasferimento chiama* l'API. `transfer`
1. Il *destinatario del trasferimento* decide se determinare se la richiesta di trasferimento alla destinazione del trasferimento `accept` viene inviata tramite un evento `reject`  `transferRequested` .
1. La *destinazione di* trasferimento riceve una chiamata in ingresso solo se il *destinatario accetta* la richiesta di trasferimento.

Per trasferire una chiamata corrente, è possibile usare `transfer` l'API . `transfer` accetta `transferCallOptions` l'oggetto facoltativo , che consente di impostare un `disableForwardingAndUnanswered` flag :

- `disableForwardingAndUnanswered = false`: se la *destinazione del trasferimento* non risponde alla  chiamata di trasferimento, il trasferimento segue le impostazioni di inoltro e di inoltro della destinazione di trasferimento senza risposta.
- `disableForwardingAndUnanswered = true`: se la *destinazione del trasferimento* non risponde alla chiamata di trasferimento, il tentativo di trasferimento termina.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer`L'API consente di sottoscrivere `transferStateChanged` gli eventi e `transferRequested` . Un `transferRequested` evento proviene da un'istanza di , un evento e lo `call` `transferStateChanged` `state` `error` trasferisce e proviene da un'istanza `transfer` di .

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Il *trasferitore può* accettare o rifiutare la richiesta di trasferimento avviata dal *trasferimentotore* nell'evento `transferRequested` usando o in `accept()` `reject()` `transferRequestedEventArgs` . È possibile accedere `targetParticipant` alle informazioni e ai metodi o in `accept` `reject` `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="learn-about-eventing-models"></a>Informazioni sui modelli di gestione degli eventi

Esaminare i valori correnti e sottoscrivere gli eventi di aggiornamento per i valori futuri.

### <a name="properties"></a>Proprietà

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Raccolte

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
