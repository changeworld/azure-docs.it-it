---
title: 'Avvio rapido: Partecipare a una riunione di Teams'
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd183e9088f24aa8b94955bc8ed2a68b4a7eb27c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496177"
---
## <a name="joining-the-meeting-chat"></a>Partecipare alla chat meeting 

Una volta abilitata l'interoperabilità dei team, un utente di servizi di comunicazione può partecipare alla chiamata del team come utente esterno utilizzando la libreria client chiamante. Partecipando alla chiamata, l'utente viene aggiunto anche come partecipante alla chat, dove può inviare e ricevere messaggi con altri utenti che partecipano alla chiamata. L'utente non ha però accesso ai messaggi della chat inviati prima che venisse aggiunto alla chiamata. Per partecipare alla riunione e iniziare la chat, è possibile seguire i passaggi successivi.

## <a name="install-the-chat-packages"></a>Installare i pacchetti di chat

Usare il `npm install` comando per installare le librerie client di servizi di comunicazione necessarie per JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

## <a name="add-the-teams-ui-controls"></a>Aggiungere i controlli dell'interfaccia utente di Teams

Sostituire il codice in index.html con il frammento di codice seguente.
Le caselle di testo nella parte superiore della pagina verranno utilizzate per immettere il contesto della riunione dei team e l'ID del thread di riunione. Il pulsante ' join teams meeting ' verrà usato per partecipare alla riunione specificata.
Verrà visualizzata una finestra popup della chat nella parte inferiore della pagina. Può essere usato per inviare messaggi al thread della riunione e verrà visualizzato in tempo reale per tutti i messaggi inviati sul thread mentre l'utente ACS è membro.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Abilitare i controlli dell'interfaccia utente di Teams

Sostituire il contenuto del file di client.js con il frammento di codice seguente.

All'interno del frammento, sostituire 
- `SECRET CONNECTION STRING` con la stringa di connessione del servizio di comunicazione 
- `ENDPOINT URL` con l'URL dell'endpoint del servizio di comunicazione

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Ottenere un thread di chat della riunione di Teams per un utente di Servizi di comunicazione

Il collegamento alla riunione e la chat per i team possono essere recuperate tramite le API Graph, descritte in dettaglio nella [documentazione Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). L'SDK per le chiamate di Servizi di comunicazione accetta un collegamento alla riunione di Teams completo. Questo collegamento viene restituito come parte della `onlineMeeting` risorsa, accessibile sotto la [ `joinWebUrl` Proprietà](/graph/api/resources/onlinemeeting?view=graph-rest-beta) con le [API Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta), è anche possibile ottenere `threadId` . La risposta avrà un `chatInfo` oggetto che contiene `threadID` . 

È anche possibile ottenere le informazioni sulla riunione e l'ID del thread necessari dall'URL della **riunione di join** nell'invito della riunione dei team.
Un collegamento a una riunione di Teams ha un aspetto simile al seguente: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Il `threadId` sarà dove `meeting_chat_thread_id` si trova nel collegamento. Verificare che l'oggetto non sia preceduto da un carattere di `meeting_chat_thread_id` escape prima dell'utilizzo. Il formato deve essere il seguente: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Eseguire il codice

Gli utenti di Webpack possono usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Aprire il browser e passare a http://localhost:8080/. Dovrebbe essere visualizzata la seguente schermata:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Screenshot dell'applicazione JavaScript completata.":::

Inserire il collegamento riunione team e l'ID thread nelle caselle di testo. Premere *join teams meeting* per partecipare alla riunione dei team. Una volta che l'utente ACS è stato ammesso alla riunione, è possibile chattare dall'applicazione Servizi di comunicazione. Passare alla casella nella parte inferiore della pagina per avviare la chat.

> [!NOTE] 
> Attualmente, solo gli scenari di interoperabilità con i team supportano solo l'invio, la ricezione e la modifica dei messaggi. Altre funzionalità, come gli indicatori di digitazione e la possibilità per gli utenti di Servizi di comunicazione di aggiungere o rimuovere altri utenti dalla riunione di Teams, non sono ancora supportate.  
