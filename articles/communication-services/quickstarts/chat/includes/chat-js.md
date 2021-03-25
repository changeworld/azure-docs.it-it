---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 5af681b6edf8abc8145caf599fc0fb3c4daf5c15
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107040"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Node.js](https://nodejs.org/en/download/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../../create-communication-resource.md). Per questa Guida introduttiva è necessario **registrare l'endpoint della risorsa** .
- Creare *tre* utenti ACS ed emettere un token di [accesso utente](../../access-tokens.md)per il token di accesso utente. Assicurarsi di impostare l'ambito su **chat** e **Annotare la stringa del token, nonché la stringa UserID**. La demo completa crea un thread con due partecipanti iniziali e quindi aggiunge un terzo partecipante al thread.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-web-application"></a>Creare una nuova applicazione Web

Aprire prima di tutto il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

### <a name="install-the-packages"></a>Installare i pacchetti

Usare il `npm install` comando per installare gli SDK di comunicazione di servizi di comunicazione per JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Questa guida di avvio rapido usa webpack per aggregare gli asset dell'applicazione. Eseguire il comando seguente per installare i pacchetti npm webpack, webpack-cli e webpack-dev-server ed elencarli come dipendenze di sviluppo in **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Creare un `webpack.config.js` file nella directory radice. Copiare la configurazione seguente in questo file:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Aggiungere uno `start` script al `package.json` , che verrà usato per l'esecuzione dell'app. All'interno della `scripts` sezione di `package.json` aggiungere quanto segue:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Creare un file **index.html** nella directory radice del progetto. Questo file verrà usato come modello per aggiungere la funzionalità chat usando Azure Communication Chat SDK per JavaScript.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Creare un file nella directory radice del progetto denominato **client.js** per contenere la logica dell'applicazione per questa guida di avvio rapido.

### <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat nell'app Web, si userà l' **endpoint** del servizio di comunicazione e il **token di accesso** generato come parte dei passaggi prerequisiti.

I token di accesso utente consentono di creare applicazioni client che eseguono l'autenticazione direttamente in Servizi di comunicazione di Azure. Questa Guida introduttiva non illustra la creazione di un livello di servizio per gestire i token per l'applicazione di chat. Per altre informazioni sui token di accesso, vedere Concetti relativi alla [chat](../../../concepts/chat/concepts.md) per altre informazioni sull'architettura della chat e i [token di accesso dell'utente](../../access-tokens.md) .

All'interno **client.js** usare l'endpoint e il token di accesso nel codice riportato di seguito per aggiungere la funzionalità chat usando Azure Communication Chat SDK per JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Sostituire **endpointUrl** con l'endpoint della risorsa servizi di comunicazione. se non è già stato fatto, vedere [creare una risorsa di comunicazione di Azure](../../create-communication-resource.md) .
- Sostituire **userAccessToken** con il token emesso.


### <a name="run-the-code"></a>Eseguire il codice

Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:
```console
npm run start
```
Aprire il browser e passare a http://localhost:8080/.
Nella console degli strumenti di sviluppo nel browser dovrebbe essere visualizzato quanto segue:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Modello a oggetti
Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services Chat SDK per JavaScript.

| Nome                                   | Descrizione                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura, sottoscrivere eventi di chat. |


## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `createThread` per creare un thread di chat.

`createThreadRequest` viene usato per descrivere la richiesta di thread:

- Usare `topic` per fornire un argomento a questa chat. Gli argomenti possono essere aggiornati dopo la creazione del thread di chat mediante la `UpdateThread` funzione.
- Consente `participants` di elencare i partecipanti da aggiungere al thread di chat.

Quando viene risolto, `createChatThread` il metodo restituisce `CreateChatThreadResult` . Questo modello contiene una `chatThread` Proprietà in cui è possibile accedere al `id` del thread appena creato. È quindi possibile usare `id` per ottenere un'istanza di un oggetto `ChatThreadClient` . `ChatThreadClient`Può quindi essere usato per eseguire l'operazione all'interno del thread, ad esempio l'invio di messaggi o l'elenco dei partecipanti.

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    id: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    id: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createChatThreadResult = await chatClient.createChatThread(createThreadRequest);
    let threadId = createChatThreadResult.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

Sostituire **USER_ID_FOR_JACK** e **USER_ID_FOR_GEETA** con gli ID utente ottenuti dalla creazione di utenti e token ([token di accesso utente](../../access-tokens.md))

Quando si aggiorna la scheda del browser, nella console dovrebbe essere visualizzato quanto segue:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Il metodo `getChatThreadClient` restituisce un `chatThreadClient` per un thread già esistente. Può essere usato per eseguire operazioni nel thread creato: aggiungere partecipanti, inviare messaggi e così via. threadId è l'ID univoco del thread di chat esistente.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Aggiungere questo codice al posto del commento `<CREATE CHAT THREAD CLIENT>` in **client.js**, aggiornare la scheda del browser e controllare la console. Verrà visualizzato quanto segue:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il `sendMessage` metodo per inviare un messaggio a un thread identificato da ThreadId.

`sendMessageRequest` viene utilizzato per descrivere la richiesta di messaggio:

- Usare `content` per fornire il contenuto del messaggio di chat.

`sendMessageOptions` viene usato per descrivere i parametri facoltativi dell'operazione:

- Usare `senderDisplayName` per specificare il nome visualizzato del mittente.
- Utilizzare `type` per specificare il tipo di messaggio, ad esempio ' text ' o ' html ';

`SendChatMessageResult` indica che la risposta restituita dall'invio di un messaggio contiene un ID, ovvero l'ID univoco del messaggio.

```JavaScript
let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    senderDisplayName : 'Jack',
    type: 'text'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
```

Aggiungere questo codice al posto del commento `<SEND MESSAGE TO A CHAT THREAD>` in **client.js**, aggiornare la scheda del browser e controllare la console.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

Con la segnalazione in tempo reale, è possibile sottoscrivere l'ascolto dei nuovi messaggi in arrivo e aggiornare i messaggi correnti in memoria di conseguenza. Servizi di comunicazione Azure supporta un [elenco di eventi che è possibile sottoscrivere](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Aggiungere questo codice al posto del commento `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` in **client.js**.
Aggiornare la scheda del browser. Nella console verrà visualizzato un messaggio `Notification chatMessageReceived`.

In alternativa, è possibile recuperare i messaggi di chat eseguendo il polling del metodo `listMessages` a intervalli specificati.

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
    while (!nextMessage.done) {
        let chatMessage = nextMessage.value;
        console.log(`Message :${chatMessage.content}`);
        // your code here
        nextMessage = await pagedAsyncIterableIterator.next();
    }

```
Aggiungere questo codice al posto del commento `<LIST MESSAGES IN A CHAT THREAD>` in **client.js**.
Aggiornare la scheda nella console è necessario trovare l'elenco dei messaggi inviati in questo thread di chat.


`listMessages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `updateMessage` e `deleteMessage`.
Per i messaggi eliminati, `chatMessage.deletedOn` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `chatMessage.editedOn` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `chatMessage.createdOn` e tale ora può essere usata per ordinare i messaggi.

`listMessages` restituisce tipi diversi di messaggi che possono essere identificati da `chatMessage.type`. Questi tipi sono:

- `Text`: Messaggio di chat normale inviato da un partecipante del thread.

- `ThreadActivity/TopicUpdate`: messaggio di sistema che indica che l'argomento è stato aggiornato.

- `ThreadActivity/AddParticipant`: Messaggio di sistema che indica che uno o più partecipanti sono stati aggiunti al thread di chat.

- `ThreadActivity/RemoveParticipant`: Messaggio di sistema che indica che un partecipante è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per inviare messaggi al thread di chat e aggiungere o rimuovere altri partecipanti.

Prima di chiamare il `addParticipants` metodo, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

`addParticipantsRequest` descrive l'oggetto richiesta in cui `participants` Elenca i partecipanti da aggiungere al thread di chat.
- `id`, required, è l'identificatore di comunicazione da aggiungere al thread di chat.
- `displayName`, facoltativo, è il nome visualizzato per il partecipante del thread.
- `shareHistoryTime`, facoltativo, è l'ora da cui la cronologia delle chat viene condivisa con il partecipante. Per condividere la cronologia dall'inizio del thread di chat, impostare questa proprietà su qualsiasi data uguale o precedente all'ora di creazione del thread. Per non condividere alcuna cronologia precedente a quando il partecipante è stato aggiunto, impostarlo sulla data corrente. Per condividere una cronologia parziale, impostarla su una data a scelta.

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Sostituire **NEW_PARTICIPANT_USER_ID** con un [nuovo ID utente](../../access-tokens.md) aggiungere questo codice al posto del `<ADD NEW PARTICIPANT TO THREAD>` commento in **client.js**

## <a name="list-users-in-a-chat-thread"></a>Elencare gli utenti in un thread di chat
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
Aggiungere questo codice al posto del commento `<LIST PARTICIPANTS IN A THREAD>` in **client.js**, aggiornare la scheda del browser e controllare la console. Verranno visualizzate informazioni sugli utenti in un thread.

## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Analogamente all'aggiunta di un partecipante, è possibile rimuovere i partecipanti da un thread di chat. Per rimuovere, è necessario tenere traccia degli ID dei partecipanti aggiunti.

Usare il metodo `removeParticipant` in cui `participant` è l'utente di comunicazione da rimuovere dal thread.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Sostituire **PARTICIPANT_ID** con un ID utente usato nel passaggio precedente (<NEW_PARTICIPANT_USER_ID>).
Aggiungere questo codice al posto del commento `<REMOVE PARTICIPANT FROM THREAD>` in **client.js**.
