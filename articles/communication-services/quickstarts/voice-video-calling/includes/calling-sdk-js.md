---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d391998e7f20cff0f77f6aab7938bc375f75c9e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616403"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un `User Access Token` per abilitare il client di chiamata. Per altre informazioni, vedere [come ottenere un `User Access Token`](../../access-tokens.md)
- Facoltativo: completare la Guida introduttiva per [iniziare ad aggiungere una chiamata all'applicazione](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano e le librerie client comuni per JavaScript.

```console
npm install @azure/communication-calling --save
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client Chiamate di Servizi di comunicazione di Azure:

| Nome                             | Descrizione                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient è il principale punto di ingresso alla libreria client Chiamate.                                                                       |
| CallAgent                        | CallAgent si usa per avviare e gestire le chiamate.                                                                                            |
| DeviceManager                    | DeviceManager viene usato per gestire i dispositivi multimediali                                                                                           |
| AzureCommunicationTokenCredential | La classe AzureCommunicationTokenCredential implementa l'interfaccia CommunicationTokenCredential utilizzata per creare un'istanza di CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inizializzare CallClient, creare CallAgent e accedere a DeviceManager

Creare un'istanza di una nuova `CallClient` istanza. È possibile configurarla con opzioni personalizzate come un'istanza del logger.
Una volta `CallClient` creata un'istanza di, è possibile creare un' `CallAgent` istanza chiamando il `createCallAgent` metodo sull' `CallClient` istanza. Restituisce in modo asincrono un `CallAgent` oggetto istanza.
Il `createCallAgent` metodo accetta `CommunicationTokenCredential` come argomento, che accetta un token di [accesso utente](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
Per accedere a `DeviceManager` un'istanza di callAgent, è necessario creare prima di tutto. È quindi possibile usare il `getDeviceManager` metodo nell' `CallClient` istanza per ottenere devicemanager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Inserire una chiamata in uscita

Per creare e avviare una chiamata, è necessario usare una delle API in CallAgent e fornire un utente creato tramite la libreria client di amministrazione dei servizi di comunicazione.

La creazione e l'avvio di una chiamata sono sincrone. L'istanza di chiamata consente di sottoscrivere gli eventi di chiamata.

## <a name="place-a-call"></a>Effettuare una chiamata

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Inserire una chiamata 1:1 a un utente o a una rete PSTN
Per effettuare una chiamata a un altro utente di servizi di comunicazione, richiamare il `call` metodo su `callAgent` e passare il CommunicationUserIdentifier del chiamato:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.call([userCallee]);
```

Per effettuare una chiamata a una rete PSTN, richiamare il `call` metodo su `callAgent` e passare il PhoneNumberIdentifier del chiamato.
La risorsa di servizi di comunicazione deve essere configurata in modo da consentire la chiamata PSTN.
Quando si chiama un numero PSTN, è necessario specificare l'ID chiamante alternativo.
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.call([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1: n con gli utenti e la rete PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.call([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="place-a-11-call-with-video-camera"></a>Inserire una chiamata 1:1 con la videocamera video
> [!WARNING]
> Attualmente non è possibile avere più di un flusso video locale in uscita.
Per effettuare una chiamata video, è necessario enumerare le telecamere locali usando l' `getCameraList` API deviceManager.
Dopo aver selezionato la fotocamera desiderata, usarla per creare un' `LocalVideoStream` istanza e passarla `videoOptions` come elemento all'interno della `localVideoStream` matrice al `call` metodo.
Una volta che la chiamata si connette, inizierà automaticamente a inviare un flusso video dalla fotocamera selezionata agli altri partecipanti. Questo vale anche per le opzioni video call. Accept () e CallAgent. Join ().
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo
Per avviare una nuova chiamata di gruppo o partecipare a una chiamata di gruppo in corso, usare il metodo ' Join ' e passare un oggetto con una `groupId` Proprietà. Il valore deve essere un GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Unisciti a un team meeting
Per partecipare a una riunione teams, usare il metodo ' Join ' e passare un collegamento per la riunione o le coordinate di una riunione
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Ricezione di una chiamata in ingresso

L' `CallAgent` istanza genera un `incomingCall` evento quando l'identità registrata riceve una chiamata in ingresso. Per restare in ascolto di questo evento, effettuare la sottoscrizione nel modo seguente:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

L' `incomingCall` evento fornirà un'istanza di `IncomingCall` in cui è possibile accettare o rifiutare una chiamata.


## <a name="call-management"></a>Gestione chiamate

È possibile accedere alle proprietà delle chiamate ed eseguire varie operazioni durante una chiamata per gestire le impostazioni relative a video e audio.

### <a name="call-properties"></a>Proprietà della chiamata
* Ottiene l'ID univoco (stringa) per questa chiamata.
```js

const callId: string = call.id;

```

* Per informazioni sugli altri partecipanti alla chiamata, controllare la `remoteParticipant` raccolta nell'istanza di `call` . Matrice contenente `RemoteParticipant` oggetti elenco
```js
const remoteParticipants = call.remoteParticipants;
```

* Identità del chiamante se la chiamata è in arrivo. Identity è uno dei `CommunicationIdentifier` tipi
```js

const callerIdentity = call.callerInfo.identity;

```

* Ottiene lo stato della chiamata.
```js

const callState = call.state;

```
Restituisce una stringa che rappresenta lo stato corrente di una chiamata:
* ' None '-stato di chiamata iniziale
* ' In entrata ': indica che una chiamata è in ingresso, deve essere accettata o rifiutata
* ' Connecting '-stato di transizione iniziale dopo aver inserito o accettato la chiamata
* ' Squillo '-per una chiamata in uscita: indica che la chiamata è squillante per i partecipanti remoti, ma è "in ingresso" sul lato
* ' EarlyMedia ': indica uno stato in cui viene riprodotto un annuncio prima della connessione della chiamata
* ' Connected ': la chiamata è connessa
* ' Trattieni ': la chiamata viene messa in attesa, nessun contenuto multimediale viene propagato tra l'endpoint locale e i partecipanti remoti
* ' Disconnecting '-stato di transizione prima della chiamata allo stato ' disconnected '
* ' Disconnected '-stato finale della chiamata
  * Se la connessione di rete viene persa, lo stato passa a "Disconnected" dopo circa 2 minuti.

* Per individuare il motivo per cui una determinata chiamata è terminata, controllare la `callEndReason` Proprietà.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Per sapere se la chiamata corrente è una chiamata in ingresso o in uscita, controllare la `direction` proprietà, che restituisce `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Per verificare se il microfono corrente è disattivato, controllare la `muted` proprietà, che restituisce `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Per verificare se il flusso di condivisione dello schermo viene inviato da un determinato endpoint, controllare la `isScreenSharingOn` proprietà, restituisce `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Per esaminare i flussi video attivi, controllare la `localVideoStreams` raccolta, che contiene `LocalVideoStream` gli oggetti
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Evento di chiamata terminata

L' `Call` istanza genera un `callEnded` evento al termine della chiamata. Per ascoltare questo evento, effettuare la sottoscrizione nel modo seguente:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Mute e Unmute

Per disattivare o disattivare l'endpoint locale è possibile usare le `mute` `unmute` API asincrone e:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio del video locale


Per avviare un video, è necessario enumerare le fotocamere usando il `getCameraList` metodo nell' `deviceManager` oggetto. Creare quindi una nuova istanza di `LocalVideoStream` passando la fotocamera desiderata al `startVideo` metodo come argomento:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Una volta avviato correttamente l'invio del video, un' `LocalVideoStream` istanza di verrà aggiunta alla `localVideoStreams` raccolta in un'istanza di chiamata.

```js

call.localVideoStreams[0] === localVideoStream;

```

Per arrestare il video locale, passare l' `localVideoStream` istanza disponibile nella `localVideoStreams` raccolta:

```js

await call.stopVideo(localVideoStream);

```

È possibile passare a un dispositivo della fotocamera diverso mentre è in corso l'invio del video richiamando `switchSource` in un' `localVideoStream` istanza:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>Gestione dei partecipanti remoti

Tutti i partecipanti remoti sono rappresentati dal `RemoteParticipant` tipo e disponibili tramite `remoteParticipants` la raccolta in un'istanza di chiamata.

### <a name="list-participants-in-a-call"></a>Elenca i partecipanti a una chiamata
La `remoteParticipants` raccolta restituisce un elenco di partecipanti remoti nella chiamata specificata:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Proprietà del partecipante remoto
Al partecipante remoto è associato un set di proprietà e raccolte
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Ottiene l'identificatore per questo partecipante remoto.
```js
const identifier = remoteParticipant.identifier;
```
Può essere uno dei tipi ' CommunicationIdentifier ':
  * {communicationUserId:' <ACS_USER_ID ' >}-oggetto che rappresenta l'utente ACS
  * {phoneNumber:' <E. 164>'}-oggetto che rappresenta il numero di telefono in formato E. 164
  * {microsoftTeamsUserId:' <TEAMS_USER_ID>', Anonymous?: Boolean; cloud?: "public" | "DOD" | "gcch"}-oggetto che rappresenta l'utente Teams

#### <a name="state"></a>State
Ottiene lo stato del partecipante remoto.
```js

const state = remoteParticipant.state;
```
Lo stato può essere uno dei
* ' Idle '-stato iniziale
* ' Connecting '-stato di transizione durante la connessione del partecipante alla chiamata
* ' Connected ': il partecipante è connesso alla chiamata
* ' Trattieni ': il partecipante è in attesa
* ' EarlyMedia '-l'annuncio viene riprodotto prima della connessione del partecipante alla chiamata
* ' Disconnected '-stato finale-partecipante disconnesso dalla chiamata
  * Se il partecipante remoto perde la connettività di rete, lo stato del partecipante remoto passa a "Disconnected" dopo circa 2 minuti.

#### <a name="call-end-reason"></a>Motivo della chiamata
Per informazioni sul motivo per cui il partecipante ha lasciato la chiamata, controllare la `callEndReason` proprietà:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>È disattivato
Per verificare se il partecipante remoto è disattivato o meno, controllare la `isMuted` proprietà, restituisce `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Sta parlando
Per verificare se il partecipante remoto sta parlando o meno, controllare la `isSpeaking` proprietà restituita `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Flussi video
Per controllare tutti i flussi video che un determinato partecipante sta inviando in questa chiamata, controllare la `videoStreams` raccolta, contiene `RemoteVideoStream` oggetti
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante a una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `addParticipant` .
Specificare uno dei tipi "Identifier".
In questo modo verrà restituita in modo sincrono l'istanza del partecipante remoto.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Rimuovi partecipante da una chiamata

Per rimuovere un partecipante da una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `removeParticipant` .
È necessario passare uno dei tipi ' Identifier ', che verrà risolto in modo asincrono dopo la rimozione del partecipante dalla chiamata.
Il partecipante verrà rimosso anche dalla `remoteParticipants` raccolta.

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
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Per eseguire il rendering di un `RemoteVideoStream` , è necessario sottoscrivere un `isAvailableChanged` evento.
Se la `isAvailable` proprietà viene modificata in `true` , un partecipante remoto sta inviando un flusso.
Una volta eseguita questa operazione, creare una nuova istanza di `Renderer` e quindi creare una nuova `RendererView` istanza di utilizzando il `createView` metodo asincrono.  È quindi possibile connettersi `view.target` a qualsiasi elemento dell'interfaccia utente.
Ogni volta che la disponibilità di un flusso remoto cambia è possibile scegliere di eliminare definitivamente l'intero renderer, un oggetto specifico `RendererView` o mantenerlo, ma ciò comporterà la visualizzazione di un frame video vuoto.

```js
let renderer: Renderer = new Renderer(remoteParticipantStream);
const displayVideo = () => {
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Proprietà del flusso video remoto
I flussi video remoti hanno le proprietà seguenti:

* `Id` -ID di un flusso video remoto
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -dimensione (larghezza/altezza) di un flusso video remoto
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -può essere ' video ' o ' ScreenSharing '
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -Indica se l'endpoint del partecipante remoto sta inviando un flusso in modo attivo
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Metodi e proprietà del renderer

* Creare un' `RendererView` istanza che può essere collegata successivamente nell'interfaccia utente dell'applicazione per eseguire il rendering del flusso video remoto.
```js
renderer.createView()
```

* Eliminare il renderer e tutte le `RendererView` istanze associate.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Metodi e proprietà di RendererView
Quando si crea un oggetto `RendererView` , è possibile specificare `scalingMode` le `mirrored` proprietà e.
La modalità di ridimensionamento può essere ' Stretch ',' crop ' o ' Fit ' se `Mirrored` è specificato, il flusso di cui è stato eseguito il rendering viene capovolto verticalmente.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Una determinata `RendererView` istanza ha una `target` proprietà che rappresenta la superficie di rendering. Questo deve essere collegato nell'interfaccia utente dell'applicazione:
```js
document.body.appendChild(rendererView.target);
```

In un secondo momento è possibile aggiornare la modalità di ridimensionamento richiamando il `updateScalingMode` metodo.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestione dei dispositivi

`DeviceManager` consente di enumerare i dispositivi locali che possono essere usati in una chiamata per trasmettere i flussi audio/video. Consente inoltre di richiedere all'utente l'autorizzazione per l'accesso al microfono e alla fotocamera mediante l'API del browser nativo.

È possibile accedere al chiamando il `deviceManager` `callClient.getDeviceManager()` metodo.
> [!WARNING]
> Attualmente `callAgent` è necessario creare un'istanza di un oggetto per ottenere l'accesso a devicemanager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Enumerare i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione sul Gestione dispositivi. L'enumerazione è un'azione sincrona.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Imposta microfono/altoparlante predefinito

Gestione dispositivi consente di impostare un dispositivo predefinito che verrà usato all'avvio di una chiamata.
Se le impostazioni predefinite del client non sono impostate, i servizi di comunicazione eseguiranno il fallback alle impostazioni predefinite del sistema operativo.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceManager.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Anteprima della fotocamera locale

È possibile usare `DeviceManager` e `Renderer` per avviare il rendering dei flussi dalla fotocamera locale. Questo flusso non verrà inviato ad altri partecipanti; si tratta di un feed di anteprima locale. Si tratta di un'azione asincrona.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Richiedi autorizzazione a fotocamera/microfono

Chiedere all'utente di concedere le autorizzazioni per la fotocamera/microfono con quanto segue:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Questa operazione verrà risolta in modo asincrono con un oggetto che indica se `audio` `video` sono state concesse le autorizzazioni e:
```js
console.log(result.audio);
console.log(result.video);
```

È possibile controllare lo stato di autorizzazione corrente per un tipo specificato chiamando `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="call-recording-management"></a>Gestione della registrazione delle chiamate

La registrazione delle chiamate è una funzionalità estesa dell' `Call` API di base. Per prima cosa è necessario ottenere l'oggetto API della funzionalità di registrazione:

```js
const callRecordingApi = call.api(Features.Recording);
```

Quindi, per verificare se la chiamata viene registrata, controllare la `isRecordingActive` proprietà di `callRecordingApi` , restituisce `Boolean` .

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

## <a name="call-transfer-management"></a>Gestione chiamate Transfer

Il trasferimento delle chiamate è una funzionalità estesa dell' `Call` API di base. Per prima cosa è necessario ottenere l'oggetto API della funzionalità di trasferimento:

```js
const callTransferApi = call.api(Features.Transfer);
```

Il trasferimento delle chiamate prevede il *trasferimento* di tre parti, il *trasferimento* e la *destinazione* del trasferimento. Il flusso di trasferimento funziona come segue:

1. Esiste già una chiamata *connessa tra il* trasportatore e il *trasferimento*
2. il *cedente* decide di trasferire la chiamata (destinazione di trasferimento del *trasferimento*  ->  )
3. *API chiamata del* trasportatore `transfer`
4. *Transferee* decide se `accept` o `reject` la richiesta di trasferimento per *trasferire la destinazione* tramite un `transferRequested` evento.
5. la *destinazione di trasferimento* riceverà una chiamata in ingresso solo se il *trasferimento* ha fatto `accept` la richiesta di trasferimento

### <a name="transfer-terminology"></a>Terminologia di trasferimento

- Transferr: l'utente che avvia la richiesta di trasferimento
- Transferee: quello trasferito dal cedente alla destinazione di trasferimento
- Destinazione trasferimento: quello che è la destinazione a cui viene trasferito

Per trasferire la chiamata corrente, è possibile usare l' `transfer` API sincrona. `transfer` accetta facoltativo `TransferCallOptions` che consente di impostare il `disableForwardingAndUnanswered` flag:

- `disableForwardingAndUnanswered` = false-se la *destinazione del trasferimento* non risponde alla chiamata di trasferimento, seguirà le impostazioni di invio e senza risposta della *destinazione* del trasferimento
- `disableForwardingAndUnanswered` = true-se la *destinazione del trasferimento* non risponde alla chiamata di trasferimento, il tentativo di trasferimento verrà terminato

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Il trasferimento consente di sottoscrivere `transferStateChanged` `transferRequested` gli eventi e. `transferRequsted` l'evento deriva da un'istanza, da un `call` `transferStateChanged` evento e da un trasferimento `state` e `error` deriva dall' `transfer` istanza

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

Il Trasferitore può accettare o rifiutare la richiesta di trasferimento avviata dal trasportatore nell' `transferRequested` evento tramite `accept()` o `reject()` in `transferRequestedEventArgs` . È possibile accedere alle `targetParticipant` informazioni, `accept` , `reject` metodi in `transferRequestedEventArgs` .

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

## <a name="eventing-model"></a>Modello di eventi

È possibile sottoscrivere la maggior parte delle proprietà e delle raccolte per ricevere una notifica quando cambiano i valori.

### <a name="properties"></a>Proprietà
Per sottoscrivere `property changed` gli eventi:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Raccolte
Per sottoscrivere `collection updated` gli eventi:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
