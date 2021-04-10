---
title: "Guida introduttiva: aggiungere il video chiamante all'app (JavaScript)"
titleSuffix: An Azure Communication Services quickstart
description: Questa Guida introduttiva illustra come aggiungere funzionalità di chiamata di video all'app usando i servizi di comunicazione di Azure.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b7fd8e8cd5bd3ab0f15115365ed057fc67f1204
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604431"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Guida introduttiva: aggiungere la chiamata a 1:1 video all'app (JavaScript)

## <a name="download-code"></a>Scarica codice

Trovare il codice finalizzato per questa Guida introduttiva su [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Prerequisiti
- Ottenere un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/) Versioni LTS attive e manutenzione LTS (8.11.1 e 10.14.1)
- Creare una risorsa Active Communication Services. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Creare un token di accesso utente per creare un'istanza del client di chiamata. [Informazioni su come creare e gestire i token di accesso utente](../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Configurazione
### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js
Aprire la finestra del terminale o di comando, creare una nuova directory per l'app e passare a tale directory.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Installare il pacchetto
Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano SDK per JavaScript.

> [!IMPORTANT]
> Questa Guida introduttiva usa i servizi di comunicazione di Azure che chiamano la versione dell'SDK `1.0.0.beta-10` . 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Questa guida di avvio rapido usa webpack per aggregare gli asset dell'applicazione. Eseguire il comando seguente per installare i `webpack` `webpack-cli` `webpack-dev-server` pacchetti NPM e ed elencarli come dipendenze di sviluppo nel `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Creare un `index.html` file nella directory radice del progetto. Questo file verrà usato per configurare un layout di base che consentirà all'utente di inserire una chiamata a 1:1 video.

Ecco il codice:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Creare un file nella directory radice del progetto denominato `client.js` per contenere la logica dell'applicazione per questa Guida introduttiva. Aggiungere il codice seguente per importare il client chiamante e ottenere riferimenti agli elementi DOM.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services che chiamano SDK:

| Nome      | Descrizione | 
| :---        |    :----   |
| CallClient  | CallClient è il punto di ingresso principale dell'SDK chiamante.      |
| CallAgent  | CallAgent si usa per avviare e gestire le chiamate.        |
| DeviceManager | DeviceManager viene usato per gestire i dispositivi multimediali.    |
| AzureCommunicationTokenCredential | La classe AzureCommunicationTokenCredential implementa l'interfaccia CommunicationTokenCredential utilizzata per creare un'istanza di CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Autenticare il client e accedere a DeviceManager

È necessario sostituire <USER_ACCESS_TOKEN> con un token di accesso utente valido per la risorsa. Se non è disponibile un token, vedere la documentazione dei token di accesso utente. Usando `CallClient`, inizializzare un'istanza di `CallAgent` con `CommunicationUserCredential` per consentire l'effettuazione e la ricezione di chiamate. Per accedere a `DeviceManager` un'istanza di callAgent, è necessario creare prima di tutto. È quindi possibile usare il `getDeviceManager` metodo nell' `CallClient` istanza per ottenere `DeviceManager` .

Aggiungere il codice seguente a `client.js`:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Inserire una chiamata del video in uscita 1:1 per un utente

Aggiungere un listener di eventi per avviare una chiamata quando `callButton` si fa clic su:

Per prima cosa è necessario enumerare le telecamere locali usando l' `getCameraList` API deviceManager. In questa Guida introduttiva viene usata la prima fotocamera della raccolta. Una volta selezionata la fotocamera desiderata, un'istanza di LocalVideoStream verrà creata e passata `videoOptions` come elemento all'interno della matrice LocalVideoStream al metodo di chiamata. Una volta che la chiamata si connette, inizierà automaticamente a inviare un flusso video all'altro partecipante. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Per eseguire il rendering di un `LocalVideoStream` , è necessario creare una nuova istanza di `VideoStreamRenderer` e quindi creare una nuova `VideoStreamRendererView` istanza di utilizzando il `createView` metodo asincrono. È quindi possibile connettersi `view.target` a qualsiasi elemento dell'interfaccia utente. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Tutti i partecipanti remoti sono disponibili tramite la `remoteParticipants` raccolta in un'istanza di chiamata. È necessario ascoltare l'evento per ricevere `remoteParticipantsUpdated` una notifica quando un nuovo partecipante remoto viene aggiunto alla chiamata. È anche necessario eseguire l'iterazione della `remoteParticipants` raccolta per sottoscrivere ognuno di essi per sottoscrivere i flussi video. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
È necessario sottoscrivere l' `videoStreamsUpdated` evento per gestire i flussi video aggiuntivi dei partecipanti remoti. È possibile esaminare le `videoStreams` raccolte per elencare i flussi di ogni partecipante durante l'analisi `remoteParticipants` della raccolta della chiamata corrente.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
È necessario sottoscrivere un `isAvailableChanged` evento per eseguire il rendering dell'oggetto `remoteVideoStream` . Se la `isAvailable` proprietà viene modificata in `true` , un partecipante remoto sta inviando un flusso. Ogni volta che la disponibilità di un flusso remoto cambia è possibile scegliere di eliminare definitivamente l'intero oggetto `Renderer` , uno specifico `RendererView` o mantenerli, ma ciò comporterà la visualizzazione di un frame video vuoto.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Per eseguire il rendering di un `RemoteVideoStream` , è necessario creare una nuova istanza di `VideoStreamRenderer` e quindi creare una nuova `VideoStreamRendererView` istanza di utilizzando il `createView` metodo asincrono. È quindi possibile connettersi `view.target` a qualsiasi elemento dell'interfaccia utente. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Ricevere una chiamata in ingresso
Per gestire le chiamate in ingresso è necessario ascoltare l' `incomingCall` evento di `callAgent` . Una volta che è presente una chiamata in ingresso, è necessario enumerare le telecamere locali e costruire un' `LocalVideoStream` istanza per inviare un flusso video all'altro partecipante. È anche necessario sottoscrivere `remoteParticipants` per gestire i flussi video remoti. È possibile accettare o rifiutare la chiamata attraverso l' `incomingCall` istanza. 

Inserire l'implementazione in `init()` per gestire le chiamate in ingresso. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);  
});
```
## <a name="end-the-current-call"></a>Termina la chiamata corrente
Aggiungere un listener di eventi per terminare la chiamata corrente quando si fa clic su `hangUpButton`:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Sottoscrivere gli aggiornamenti delle chiamate
È necessario sottoscrivere l'evento quando il partecipante remoto termina la chiamata per eliminare i renderer video e gli Stati dei pulsanti. 

Inserire l'implementazione in init () per sottoscrivere l' `callsUpdated` evento. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Video iniziale e finale durante la chiamata
È possibile arrestare il video durante la chiamata corrente aggiungendo un listener di eventi al pulsante Arresta video per eliminare il renderer di `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
È possibile aggiungere un listener di eventi al pulsante Avvia video per riattivare il video quando è stato interrotto durante la chiamata corrente. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Eseguire il codice
Usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Aprire il browser e passare a http://localhost:8080/. Dovrebbe essere visualizzata la seguente schermata:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 in 1 pagina chiamante":::

È possibile effettuare una chiamata del video in uscita 1:1 fornendo un ID utente nel campo di testo e facendo clic sul pulsante Avvia chiamata. 

## <a name="sample-code"></a>Codice di esempio
È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Pulire le risorse
Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere gli articoli seguenti:

- Vedere l' [esempio di chiamata Web](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Informazioni sulla [chiamata di funzionalità SDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Leggere altre informazioni sul [funzionamento delle chiamate](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)

