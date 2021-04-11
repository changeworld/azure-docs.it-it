---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498777"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di servizi di comunicazione di Azure distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un token di accesso utente per abilitare il client di chiamata. [Ottenere un token di accesso utente](../../access-tokens.md).
- Facoltativo: completare l' [aggiunta della chiamata vocale alla](../getting-started-with-calling.md) Guida introduttiva dell'app.

## <a name="set-up-your-system"></a>Configurare il sistema

### <a name="create-the-xcode-project"></a>Creare il progetto Xcode

In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application**. Questa Guida introduttiva usa il [Framework SwiftUI](https://developer.apple.com/xcode/swiftui/), quindi è necessario impostare la **lingua** su **Swift** e l' **interfaccia utente** su **SwiftUI**. 

Non verranno creati unit test o test dell'interfaccia utente durante questa Guida introduttiva. È possibile cancellare le caselle di testo **Includi unit test** e **Includi test dell'interfaccia utente** .

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot che mostra la finestra per la creazione di un progetto in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installare il pacchetto e le dipendenze con CocoaPods

1. Creare un file Podfile per l'applicazione, come questo:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Eseguire `pod install`.
3. Aprire `.xcworkspace` con Xcode.

### <a name="request-access-to-the-microphone"></a>Richiedere l'accesso al microfono

Per accedere al microfono del dispositivo, è necessario aggiornare l'elenco delle proprietà delle informazioni dell'app con `NSMicrophoneUsageDescription` . Il valore associato viene impostato su un oggetto `string` che verrà incluso nella finestra di dialogo utilizzata dal sistema per richiedere l'accesso all'utente.

Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e scegliere **Open As** > **Source Code** (Apri come > Codice sorgente). Aggiungere le righe seguenti nella sezione di primo livello `<dict>` , quindi salvare il file.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aprire il file *contentView. Swift* del progetto e aggiungere una `import` dichiarazione all'inizio del file per importare la `AzureCommunicationCalling` libreria. Importare inoltre `AVFoundation` . Sarà necessario per le richieste di autorizzazione audio nel codice.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>Informazioni sul modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dei servizi di comunicazione di Azure che chiamano SDK per iOS.

> [!NOTE]
> Questa Guida introduttiva usa la versione 1.0.0-beta. 8 dell'SDK chiamante.


| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` è il punto di ingresso principale dell'SDK chiamante.|
| `CallAgent` | `CallAgent` viene usato per avviare e gestire le chiamate. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` viene usato come credenziale del token per creare un'istanza `CallAgent` .| 
| `CommunicationIdentifier` | `CommunicationIdentifier` viene utilizzato per rappresentare l'identità dell'utente. L'identità può essere `CommunicationUserIdentifier` , `PhoneNumberIdentifier` o `CallingApplication` . |

> [!NOTE]
> Quando l'applicazione implementa i delegati di eventi, deve conservare un riferimento sicuro agli oggetti che richiedono sottoscrizioni di eventi. Ad esempio, quando un `RemoteParticipant` oggetto viene restituito quando si richiama il `call.addParticipant` metodo e l'applicazione imposta il delegato per l'ascolto `RemoteParticipantDelegate` , l'applicazione deve mantenere un riferimento sicuro all' `RemoteParticipant` oggetto. In caso contrario, se l'oggetto viene raccolto, il delegato genererà un'eccezione irreversibile quando l'SDK chiamante tenterà di richiamare l'oggetto.

## <a name="initialize-callagent"></a>Inizializzare CallAgent

Per creare un' `CallAgent` istanza da `CallClient` , è necessario usare un `callClient.createCallAgent` metodo che restituisca in modo asincrono un `CallAgent` oggetto dopo che è stato inizializzato.

Per creare un client di chiamata, è necessario passare un `CommunicationTokenCredential` oggetto.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passare l' `CommunicationTokenCredential` oggetto creato `CallClient` e impostare il nome visualizzato.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Inserire una chiamata in uscita

Per creare e avviare una chiamata, è necessario chiamare una delle API in `CallAgent` e fornire l'identità dei servizi di comunicazione di un utente di cui è stato effettuato il provisioning tramite Communication Services Management SDK.

La creazione e l'avvio di una chiamata sono sincrone. Si riceverà un'istanza di chiamata che consente di sottoscrivere tutti gli eventi della chiamata.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1:1 a un utente o una chiamata 1: n con gli utenti e la rete PSTN

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Inserire una chiamata 1: n con gli utenti e la rete PSTN
Per effettuare la chiamata a PSTN, è necessario specificare un numero di telefono acquisito con servizi di comunicazione.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>Inserire una chiamata 1:1 con video
Per ottenere un'istanza di gestione dispositivi, vedere la sezione relativa alla [gestione dei dispositivi](#manage-devices).

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Partecipare a una chiamata di gruppo
Per partecipare a una chiamata, è necessario chiamare una delle API in `CallAgent` .

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>Sottoscrivere una chiamata in ingresso
Sottoscrivere un evento di chiamata in ingresso.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Accetta una chiamata in ingresso
Per accettare una chiamata, chiamare il `accept` metodo su un oggetto call. Impostare un delegato su `CallAgent` .

```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Configurare le notifiche push

Una notifica push Mobile è la notifica popup che si ottiene nel dispositivo mobile. Per chiamare, ci concentreremo sulle notifiche push VoIP (Voice over Internet Protocol). 

Le sezioni seguenti descrivono come registrare, gestire e annullare la registrazione di notifiche push. Prima di iniziare queste attività, completare i prerequisiti seguenti:

1. In Xcode passare a **firma & funzionalità**. Aggiungere una funzionalità selezionando **+ funzionalità**, quindi selezionare **notifiche push**.
2. Aggiungere un'altra funzionalità selezionando **+ funzionalità**, quindi selezionare **modalità in background**.
3. In **modalità in background** selezionare le caselle di **controllo Voice over IP** and **Remote Notifications** .

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Screenshot che illustra come aggiungere funzionalità in Xcode." lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registrarsi per le notifiche push

Per eseguire la registrazione per le notifiche push, chiamare `registerPushNotification()` su un' `CallAgent` istanza con un token di registrazione del dispositivo.

La registrazione per le notifiche push deve essere eseguita dopo l'inizializzazione. Quando l' `callAgent` oggetto viene eliminato definitivamente, `logout` viene chiamato, che consente di annullare automaticamente la registrazione delle notifiche push.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Gestisci notifiche push
Per ricevere notifiche push per le chiamate in ingresso, chiamare `handlePushNotification()` su un' `CallAgent` istanza con un payload del dizionario.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>Annulla registrazione notifiche push

Le applicazioni possono annullare la registrazione della notifica push in qualsiasi momento. È sufficiente chiamare il `unregisterPushNotification` metodo su `CallAgent` .

> [!NOTE]
> Non viene annullata automaticamente la registrazione delle applicazioni dalle notifiche push durante la disconnessione.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>Eseguire operazioni a metà chiamata

È possibile eseguire diverse operazioni durante una chiamata per gestire le impostazioni relative a video e audio.

### <a name="mute-and-unmute"></a>Mute e Unmute

Per disattivare o disattivare l'endpoint locale, è possibile utilizzare le `mute` `unmute` API asincrone e.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Usare il codice seguente per riattivare in modo asincrono l'endpoint locale.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Avviare e arrestare l'invio del video locale

Per iniziare a inviare il video locale ad altri partecipanti in una chiamata, usare l' `startVideo` API e passare `localVideoStream` con `camera` .

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Dopo l'avvio dell'invio del video, l' `LocalVideoStream` istanza di viene aggiunta alla `localVideoStreams` raccolta in un'istanza di chiamata.

```swift

call.localVideoStreams[0]

```

Per arrestare il video locale, passare l' `localVideoStream` istanza restituita dalla chiamata di `call.startVideo` . Si tratta di un'azione asincrona.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Gestisci partecipanti remoti

Tutti i partecipanti remoti sono rappresentati dal `RemoteParticipant` tipo e sono disponibili tramite la `remoteParticipants` raccolta in un'istanza di chiamata.

### <a name="list-participants-in-a-call"></a>Elenca i partecipanti a una chiamata

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Ottenere le proprietà del partecipante remoto

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Aggiungere un partecipante a una chiamata

Per aggiungere un partecipante a una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare `addParticipant` . Questo comando restituisce in modo sincrono un'istanza del partecipante remoto.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Rimuovere un partecipante da una chiamata
Per rimuovere un partecipante da una chiamata, ovvero un utente o un numero di telefono, è possibile richiamare l' `removeParticipant` API. Questa operazione verrà risolta in modo asincrono.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Rendering dei flussi video del partecipante remoto

I partecipanti remoti possono avviare la condivisione video o schermo durante una chiamata.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Gestire i flussi di condivisione video o condivisione schermo di partecipanti remoti

Per elencare i flussi di partecipanti remoti, ispezionare le `videoStreams` raccolte.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>Ottenere le proprietà del flusso video remoto

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>Eseguire il rendering di flussi del partecipante remoto

Per avviare il rendering dei flussi del partecipante remoto, usare il codice seguente.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Ottenere metodi e proprietà del renderer video remoto

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>Gestire dispositivi

`DeviceManager` consente di enumerare i dispositivi locali che possono essere usati in una chiamata per trasmettere flussi audio o video. Consente inoltre di richiedere l'autorizzazione da un utente per accedere a un microfono o a una fotocamera. È possibile accedere all' `deviceManager` `callClient` oggetto.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Enumerare i dispositivi locali

Per accedere ai dispositivi locali, è possibile usare i metodi di enumerazione in gestione dispositivi. L'enumerazione è un'azione sincrona.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>Imposta il microfono o l'altoparlante predefinito

È possibile utilizzare Gestione dispositivi per impostare un dispositivo predefinito che verrà utilizzato all'avvio di una chiamata. Se le impostazioni predefinite dello stack non sono impostate, i servizi di comunicazione eseguiranno il fallback alle impostazioni predefinite del sistema operativo.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>Ottenere un'anteprima della fotocamera locale

È possibile usare `Renderer` per avviare il rendering di un flusso dalla fotocamera locale. Questo flusso non verrà inviato ad altri partecipanti; si tratta di un feed di anteprima locale. Si tratta di un'azione asincrona.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>Ottenere le proprietà di anteprima della fotocamera locale

Il renderer dispone di un set di proprietà e metodi che consentono di controllare il rendering.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>Sottoscrivere le notifiche

È possibile sottoscrivere la maggior parte delle proprietà e delle raccolte per ricevere una notifica quando cambiano i valori.

### <a name="properties"></a>Proprietà
Per sottoscrivere `property changed` gli eventi, usare il codice seguente.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Raccolte
Per sottoscrivere `collection updated` gli eventi, usare il codice seguente.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
