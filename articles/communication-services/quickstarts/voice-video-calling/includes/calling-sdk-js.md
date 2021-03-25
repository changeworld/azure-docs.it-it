---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 7fee393b694bf761cf052702a975239d6dff9a9c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104329"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un token di accesso utente per abilitare il client chiamante. Per altre informazioni, vedere [creare e gestire i token di accesso](../../access-tokens.md).
- Facoltativo: completare la Guida introduttiva per [aggiungere la chiamata vocale all'applicazione](../getting-started-with-calling.md).

## <a name="install-the-client-library"></a>Installare la libreria client

> [!NOTE]
> Questo documento usa la versione 1.0.0-beta. 6 della libreria client chiamante.

Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano e le librerie client comuni per JavaScript.
Questo documento fa riferimento ai tipi nella versione 1.0.0-beta. 5 della libreria chiamante.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dei servizi di comunicazione di Azure che chiamano la libreria client:

| Nome                             | Descrizione                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Il punto di ingresso principale della libreria client chiamante.                                                                       |
| `CallAgent`                        | Utilizzato per avviare e gestire le chiamate.                                                                                            |
| `DeviceManager`                    | Usato per gestire i dispositivi multimediali.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementa l' `CommunicationTokenCredential` interfaccia utilizzata per creare un'istanza di `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inizializzare un'istanza di CallClient, creare un'istanza di CallAgent e accedere a deviceManager

Creare una nuova `CallClient` istanza. È possibile configurarla con opzioni personalizzate come un'istanza del logger.

Quando si dispone `CallClient` di un'istanza di, è possibile creare un' `CallAgent` istanza di chiamando il `createCallAgent` metodo sull' `CallClient` istanza. Restituisce in modo asincrono un `CallAgent` oggetto istanza.

Il `createCallAgent` metodo usa `CommunicationTokenCredential` come argomento. Accetta un [token di accesso utente](../../access-tokens.md).

Dopo aver creato un' `callAgent` istanza di, è possibile utilizzare il `getDeviceManager` metodo nell' `CallClient` istanza di per accedere a `deviceManager` .

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Effettuare una chiamata

Per creare e avviare una chiamata, usare una delle API in `callAgent` e fornire un utente creato tramite la libreria client di identità dei servizi di comunicazione.

La creazione e l'avvio di una chiamata sono sincrone. L'istanza di chiamata consente di sottoscrivere gli eventi di chiamata.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Inserire una chiamata 1: n a un utente o a una rete PSTN

Per chiamare un altro utente di servizi di comunicazione, utilizzare il `startCall` metodo su `callAgent` e passare l'oggetto del destinatario `CommunicationUserIdentifier` [creato con la libreria di amministrazione di servizi di comunicazione](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Per effettuare una chiamata a una rete telefonica a commutazione pubblica (PSTN), usare il `startCall` metodo su `callAgent` e passare l'oggetto del destinatario `PhoneNumberIdentifier` . La risorsa di servizi di comunicazione deve essere configurata in modo da consentire la chiamata PSTN.

Quando si chiama un numero PSTN, specificare l'ID chiamante alternativo. Un ID chiamante alternativo è un numero di telefono (basato sullo standard E. 164) che identifica il chiamante in una chiamata PSTN. Si tratta del numero di telefono che il destinatario della chiamata vede per una chiamata in ingresso.

> [!NOTE]
> La chiamata PSTN è attualmente in anteprima privata. Per l'accesso, [applicare al programma Early Adopter](https://aka.ms/ACS-EarlyAdopter).

Per una chiamata 1:1, usare il codice seguente:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Per una chiamata 1: n, usare il codice seguente:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Inserire una chiamata 1:1 con la videocamera video

> [!IMPORTANT]
> Attualmente non è possibile avere più di un flusso video locale in uscita.

Per effettuare una chiamata video, è necessario specificare le fotocamere usando il `getCameras()` metodo in `deviceManager` .

Dopo aver selezionato una fotocamera, usarla per creare un' `LocalVideoStream` istanza. Passarlo all'interno di `videoOptions` come elemento all'interno della `localVideoStream` matrice al `startCall` metodo.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Quando la chiamata si connette, avvia automaticamente l'invio di un flusso video dalla fotocamera selezionata all'altro partecipante. Questo vale anche per le `Call.Accept()` Opzioni video e `CallAgent.join()` video.

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo

Per avviare una nuova chiamata di gruppo o partecipare a una chiamata di gruppo in corso, usare il `join` metodo e passare un oggetto con una `groupId` Proprietà. Il `groupId` valore deve essere un GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Unisciti a un team meeting

Per partecipare a una riunione teams, usare il `join` metodo e passare un collegamento alla riunione o le coordinate.

Join tramite un collegamento alla riunione:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Join usando le coordinate della riunione:

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

L' `callAgent` istanza genera un `incomingCall` evento quando l'identità di accesso riceve una chiamata in ingresso. Per restare in ascolto di questo evento, effettuare la sottoscrizione usando una di queste opzioni:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo

    //Accept the call
    var call = await incomingCall.accept();

    //Reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

L' `incomingCall` evento include un' `incomingCall` istanza che è possibile accettare o rifiutare.

## <a name="manage-calls"></a>Gestisci chiamate

Durante una chiamata, è possibile accedere alle proprietà delle chiamate e gestire le impostazioni audio e video.

### <a name="check-call-properties"></a>Controlla proprietà della chiamata

Ottenere l'ID univoco (stringa) per una chiamata:

   ```js
    const callId: string = call.id;
   ```

Per informazioni sugli altri partecipanti alla chiamata, esaminare la `remoteParticipant` raccolta:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identificare il chiamante di una chiamata in ingresso:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` è uno dei `CommunicationIdentifier` tipi.

Ottenere lo stato di una chiamata:

   ```js
   const callState = call.state;
   ```

   Restituisce una stringa che rappresenta lo stato corrente di una chiamata:

  - `None`: Stato di chiamata iniziale.
  - `Incoming`: Indica che una chiamata è in arrivo. Deve essere accettato o rifiutato.
  - `Connecting`: Stato di transizione iniziale quando viene inserita o accettata una chiamata.
  - `Ringing`: Per una chiamata in uscita, indica che una chiamata è squillante per i partecipanti remoti. Si trova `Incoming` sul lato.
  - `EarlyMedia`: Indica uno stato in cui viene riprodotto un annuncio prima della connessione della chiamata.
  - `Connected`: Indica che la chiamata è connessa.
  - `LocalHold`: Indica che la chiamata viene messa in attesa da un partecipante locale. Nessun supporto viene propagato tra l'endpoint locale e i partecipanti remoti.
  - `RemoteHold`: Indica che la chiamata è stata messa in attesa da parte del partecipante remoto. Nessun supporto viene propagato tra l'endpoint locale e i partecipanti remoti.
  - `Disconnecting`: Stato di transizione prima che la chiamata passi a uno `Disconnected` stato.
  - `Disconnected`: Stato finale della chiamata. Se la connessione di rete viene persa, lo stato diventa `Disconnected` dopo due minuti.

Individuare il motivo per cui è stata terminata una chiamata controllando la `callEndReason` proprietà:

   ```js
   const callEndReason = call.callEndReason;
   // callEndReason.code (number) code associated with the reason
   // callEndReason.subCode (number) subCode associated with the reason
   ```

Viene illustrato se la chiamata corrente è in entrata o in uscita controllando la `direction` Proprietà. Restituisce `CallDirection`.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Controllare se il microfono corrente è disattivato. Restituisce `Boolean`.

   ```js
   const muted = call.isMicrophoneMuted;
   ```

Determinare se il flusso di condivisione dello schermo viene inviato da un endpoint specificato controllando la `isScreenSharingOn` Proprietà. Restituisce `Boolean`.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Controllare i flussi video attivi controllando la `localVideoStreams` raccolta. Restituisce `LocalVideoStream` oggetti.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Controllare un evento callEnded

L' `call` istanza genera un `callEnded` evento al termine della chiamata. Per restare in ascolto di questo evento, effettuare la sottoscrizione usando il codice seguente:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Mute e Unmute

Per disattivare o disattivare l'endpoint locale, è possibile usare le `mute` `unmute` API asincrone e:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio del video locale

Per avviare un video, è necessario specificare le fotocamere usando il `getCameras` metodo nell' `deviceManager` oggetto. Creare quindi una nuova istanza di `LocalVideoStream` passando la fotocamera desiderata al `startVideo` metodo come argomento:

```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);
```

Una volta avviato correttamente l'invio del video, `LocalVideoStream` viene aggiunta un'istanza alla `localVideoStreams` raccolta in un'istanza di chiamata.

```js
call.localVideoStreams[0] === localVideoStream;
```

Per arrestare il video locale, passare l' `localVideoStream` istanza disponibile nella `localVideoStreams` raccolta:

```js
await call.stopVideo(localVideoStream);
```

È possibile passare a un dispositivo fotocamera diverso durante l'invio di un video richiamando `switchSource` in un' `localVideoStream` istanza:

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);
```

## <a name="manage-remote-participants"></a>Gestisci partecipanti remoti

Tutti i partecipanti remoti sono rappresentati da `remoteParticipant` e sono disponibili tramite la `remoteParticipants` raccolta in un'istanza di chiamata.

### <a name="list-the-participants-in-a-call"></a>Elenca i partecipanti a una chiamata

La `remoteParticipants` raccolta restituisce un elenco di partecipanti remoti in una chiamata:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Accedere alle proprietà del partecipante remoto

I partecipanti remoti dispongono di un set di proprietà e raccolte associate:

- `CommunicationIdentifier`: Ottiene l'identificatore per un partecipante remoto. Identity è uno dei `CommunicationIdentifier` tipi:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Può essere uno dei `CommunicationIdentifier` tipi seguenti:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Oggetto che rappresenta l'utente ACS.
  - `{ phoneNumber: '<E.164>' }`: Oggetto che rappresenta il numero di telefono in formato E. 164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Oggetto che rappresenta l'utente teams.

- `state`: Ottiene lo stato di un partecipante remoto.

  ```js
  const state = remoteParticipant.state;
  ```

  Lo stato può essere:

  - `Idle`: Stato iniziale.
  - `Connecting`: Stato di transizione mentre un partecipante si connette alla chiamata.
  - `Ringing`: Il partecipante sta squillando.
  - `Connected`: Il partecipante è connesso alla chiamata.
  - `Hold`: Il partecipante è in attesa.
  - `EarlyMedia`: Annuncio che gioca prima che un partecipante si connetta alla chiamata.
  - `Disconnected`: Stato finale. Il partecipante è disconnesso dalla chiamata. Se il partecipante remoto perde la connettività di rete, il relativo stato viene modificato in `Disconnected` dopo due minuti.

- `callEndReason`: Per informazioni sul motivo per cui un partecipante ha lasciato la chiamata, controllare la `callEndReason` proprietà:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  // callEndReason.code (number) code associated with the reason
  // callEndReason.subCode (number) subCode associated with the reason
  ```

- `isMuted` stato: per sapere se un partecipante remoto è disattivato, controllare la `isMuted` Proprietà. Restituisce `Boolean`.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` stato: per sapere se un partecipante remoto sta parlando, controllare la `isSpeaking` Proprietà. Restituisce `Boolean`.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Per controllare tutti i flussi video inviati da un determinato partecipante in questa chiamata, controllare la `videoStreams` raccolta. Contiene `RemoteVideoStream` oggetti.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```

### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante (un utente o un numero di telefono) a una chiamata, è possibile usare `addParticipant` . Fornire uno dei `Identifier` tipi. Restituisce l' `remoteParticipant` istanza di.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Rimuovere un partecipante da una chiamata

Per rimuovere un partecipante, ovvero un utente o un numero di telefono, da una chiamata, è possibile richiamare `removeParticipant` . È necessario passare uno dei `Identifier` tipi. Questo problema viene risolto in modo asincrono dopo la rimozione del partecipante dalla chiamata. Il partecipante viene rimosso anche dalla `remoteParticipants` raccolta.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Rendering dei flussi video del partecipante remoto

Per elencare i flussi video e i flussi di condivisione dello schermo dei partecipanti remoti, esaminare le `videoStreams` Raccolte:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Per eseguire il rendering `RemoteVideoStream` , è necessario sottoscrivere un `isAvailableChanged` evento. Se la `isAvailable` proprietà viene modificata in `true` , un partecipante remoto sta inviando un flusso. Successivamente, creare una nuova istanza di `Renderer` e quindi creare una nuova `RendererView` istanza di utilizzando il `createView` metodo asincrono.  È quindi possibile connettersi `view.target` a qualsiasi elemento dell'interfaccia utente.

Quando la disponibilità di un flusso remoto cambia, è possibile eliminare definitivamente `Renderer` , eliminare un' `RendererView` istanza specifica o mantenerla. I renderer collegati a un flusso non disponibile determineranno un frame video vuoto.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
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

- `Stream.size`: Altezza e larghezza di un flusso video remoto.

  ```js
  const size: {width: number; height: number} = remoteVideoStream.size;
  ```

- `mediaStreamType`: Può essere `Video` o `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Indica se un endpoint del partecipante remoto sta inviando attivamente un flusso.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="renderer-methods-and-properties"></a>Metodi e proprietà del renderer

Creare un' `rendererView` istanza che può essere collegata nell'interfaccia utente dell'applicazione per eseguire il rendering del flusso video remoto:

  ```js
  renderer.createView()
  ```

Eliminare `renderer` e tutte le `rendererView` istanze associate:

  ```js
  renderer.dispose()
  ```

### <a name="rendererview-methods-and-properties"></a>Metodi e proprietà di RendererView

Quando si crea `rendererView` , è possibile specificare le `scalingMode` `isMirrored` proprietà e. `scalingMode` può essere `Stretch` , `Crop` o `Fit` . Se `isMirrored` viene specificato, il flusso di cui è stato eseguito il rendering viene capovolto verticalmente.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```

Ogni `RendererView` istanza ha una `target` proprietà che rappresenta la superficie di rendering. Alleghi questa proprietà nell'interfaccia utente dell'applicazione:

```js
document.body.appendChild(rendererView.target);
```

È possibile aggiornare `scalingMode` richiamando il `updateScalingMode` Metodo:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestione dei dispositivi

In `deviceManager` è possibile specificare i dispositivi locali che possono trasmettere i flussi audio e video in una chiamata. Consente inoltre di richiedere l'autorizzazione per accedere al microfono e alla fotocamera di un altro utente tramite l'API del browser nativo.

È possibile accedere chiamando `deviceManager` il `callClient.getDeviceManager()` Metodo:

> [!IMPORTANT]
> È necessario disporre di un `callAgent` oggetto prima di poter accedere a `deviceManager` .

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Ottenere i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione in `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Imposta il microfono e l'altoparlante predefiniti

In `deviceManager` è possibile impostare un dispositivo predefinito che verrà usato per avviare una chiamata. Se le impostazioni predefinite del client non sono impostate, servizi di comunicazione usa le impostazioni predefinite del sistema operativo.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);
```

### <a name="local-camera-preview"></a>Anteprima della fotocamera locale

È possibile usare `deviceManager` e `Renderer` per avviare il rendering dei flussi dalla fotocamera locale. Questo flusso non verrà inviato ad altri partecipanti; si tratta di un feed di anteprima locale.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Richiedi autorizzazione alla fotocamera e al microfono

Chiedere all'utente di concedere le autorizzazioni per la fotocamera e il microfono:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Questo problema si risolve con un oggetto che indica se `audio` `video` sono state concesse le autorizzazioni e:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Chiamate di record

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

La registrazione delle chiamate è una funzionalità estesa dell' `Call` API di base. Per prima cosa è necessario ottenere l'oggetto API della funzionalità di registrazione:

```js
const callRecordingApi = call.api(Features.Recording);
```

Quindi, per verificare se la chiamata viene registrata, controllare la `isRecordingActive` proprietà di `callRecordingApi` . Restituisce `Boolean`.

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

## <a name="transfer-calls"></a>Chiamate di trasferimento

Il trasferimento delle chiamate è una funzionalità estesa dell' `Call` API di base. Per prima cosa è necessario ottenere l'oggetto API della funzionalità di trasferimento:

```js
const callTransferApi = call.api(Features.Transfer);
```

I trasferimenti di chiamate coinvolgono tre parti:

- *Transferr*: persona che avvia la richiesta di trasferimento.
- *Transferee*: la persona che viene trasferita.
- *Destinazione trasferimento*: la persona a cui viene trasferito.

I trasferimenti seguono questi passaggi:

1. Esiste già una chiamata connessa tra il trasportatore *e* il *trasferimento*. Il *trasferiscitore* decide di trasferire la chiamata dal *trasferimento* alla destinazione di *trasferimento*.
1. Il *trasferiscitore* chiama l' `transfer` API.
1. Il *trasferitore* decide se `accept` o `reject` la richiesta di trasferimento alla *destinazione di trasferimento* tramite un `transferRequested` evento.
1. La *destinazione di trasferimento* riceve una chiamata in ingresso solo se il *trasferimento* accetta la richiesta di trasferimento.

Per trasferire una chiamata corrente, è possibile usare l' `transfer` API. `transfer` accetta l'oggetto facoltativo `transferCallOptions` , che consente di impostare un `disableForwardingAndUnanswered` flag:

- `disableForwardingAndUnanswered = false`: Se la *destinazione del trasferimento* non risponde alla chiamata di trasferimento, il trasferimento segue le impostazioni di invio e senza risposta della *destinazione* di trasferimento.
- `disableForwardingAndUnanswered = true`: Se la *destinazione del trasferimento* non risponde alla chiamata di trasferimento, il tentativo di trasferimento termina.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

L' `transfer` API consente di sottoscrivere `transferStateChanged` `transferRequested` gli eventi e. Un `transferRequested` evento proviene da un' `call` istanza di, un `transferStateChanged` evento e un trasferimento `state` e `error` provengono da un' `transfer` istanza di.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Il *trasferitore* può accettare o rifiutare la *richiesta di trasferimento* avviata dal trasportatore nell' `transferRequested` evento tramite `accept()` o `reject()` in `transferRequestedEventArgs` . È possibile accedere `targetParticipant` a informazioni `accept` e `reject` metodi in `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Informazioni sui modelli di eventi

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