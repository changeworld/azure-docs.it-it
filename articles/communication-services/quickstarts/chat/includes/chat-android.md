---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7e62bbc5929eaf23a9b7be12de222105bc2529cd
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653547"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Android Studio](https://developer.android.com/studio), si utilizzerà Android Studio per creare un'applicazione Android per la Guida introduttiva per l'installazione delle dipendenze.
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../../create-communication-resource.md). Per questa Guida introduttiva è necessario **registrare l'endpoint della risorsa** .
- Creare **due** utenti di servizi di comunicazione ed emettere un token di [accesso utente](../../access-tokens.md)del token di accesso utente. Assicurarsi di impostare l'ambito su **chat** e **Annotare la stringa del token, nonché la stringa UserID**. In questa Guida introduttiva si creerà un thread con un partecipante iniziale e quindi si aggiungerà un secondo partecipante al thread.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-android-application"></a>Creare una nuova applicazione Android

1. Aprire Android Studio e selezionare `Create a new project` . 
2. Nella finestra successiva selezionare `Empty Activity` come modello di progetto.
3. Quando si scelgono `ChatQuickstart` le opzioni immettere come nome del progetto.
4. Fare clic su Avanti e scegliere la directory in cui si desidera creare il progetto.

### <a name="install-the-libraries"></a>Installare le librerie

Gradle verrà usato per installare le dipendenze dei servizi di comunicazione necessarie. Dalla riga di comando spostarsi all'interno della directory radice del `ChatQuickstart` progetto. Aprire il file Build. Gradle dell'app e aggiungere le dipendenze seguenti alla `ChatQuickstart` destinazione:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.5'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.5'
```

Fare clic su' Sincronizza ora ' in Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>Alternativa Per installare le librerie tramite Maven
Per importare la libreria nel progetto usando il sistema di compilazione [Maven](https://maven.apache.org/) , aggiungerla alla `dependencies` sezione del file dell'app `pom.xml` , specificando l'ID dell'artefatto e la versione che si vuole usare:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.5</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configurare i segnaposto

Aprire e modificare il file `MainActivity.java` . In questa Guida introduttiva si aggiungerà il codice a `MainActivity` e si visualizza l'output nella console. Questa Guida introduttiva non risolve la compilazione di un'interfaccia utente. Nella parte superiore del file importare le `Communication common` librerie e `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Copiare il codice seguente nel file `MainActivity` :

```
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

Nei passaggi seguenti verranno sostituiti i segnaposto con il codice di esempio usando la libreria di chat dei servizi di comunicazione di Azure.


### <a name="create-a-chat-client"></a>Creare un client di chat

Sostituire il commento `<CREATE A CHAT CLIENT>` con il codice seguente (inserire le istruzioni Import all'inizio del file):

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatClient client = new ChatClient.Builder()
    .endpoint(endpoint)
    .credentialInterceptor(chain -> chain.proceed(chain.request()
        .newBuilder()
        .header(HttpHeader.AUTHORIZATION, userAccessToken)
        .build());
```

1. Utilizzare `AzureCommunicationChatServiceAsyncClient.Builder` per configurare e creare un'istanza di `AzureCommunicationChatClient` .
2. Sostituire `<resource>` con la risorsa servizi di comunicazione.
3. Sostituire `<user_access_token>` con un token di accesso valido per i servizi di comunicazione.

## <a name="object-model"></a>Modello a oggetti
Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per JavaScript.

| Nome                                   | Descrizione                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura, sottoscrivere eventi di chat. |

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Verrà usato `ChatClient` per creare un nuovo thread con un utente iniziale.

Sostituire il commento `<CREATE A CHAT THREAD>` con il codice seguente:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setId(id)
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
    .setTopic(topic)
    .setParticipants(participants);

// optional, set a repeat request ID 
final String repeatabilityRequestID = '123';

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
    public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
        // MultiStatusResponse is the result returned from creating a thread.
        // It has a 'multipleStatus' property which represents a list of IndividualStatusResponse.
        String threadId;
        List<IndividualStatusResponse> statusList = result.getMultipleStatus();
        for (IndividualStatusResponse status : statusList) {
            if (status.getId().endsWith("@thread.v2")
                && status.getType().contentEquals("Thread")) {
                threadId = status.getId();
                break;
            }
        }
        // Take further action.
    }

    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
    }
});
```

Sostituire `<user_id>` con un ID utente di servizi di comunicazione valido. Si userà `threadId` dalla risposta restituita al gestore di completamento nei passaggi successivi.

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Ora che è stato creato un thread di chat, otterremo un `ChatThreadClient` per eseguire operazioni all'interno del thread. Sostituire il commento `<CREATE A CHAT THREAD CLIENT>` con il codice seguente:

```
ChatThreadClient threadClient =
        new ChatThreadClient.Builder()
            .endpoint(<endpoint>))
            .build();
```

Sostituire `<endpoint>` con l'endpoint di servizi di comunicazione.

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Sostituire il commento `<SEND A MESSAGE>` con il codice seguente:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.sendChatMessage(threadId, message, new Callback<String>() {
    @Override
    public void onSuccess(String messageId, Response response) {
        // A string is the response returned from sending a message, it is an id, 
        // which is the unique ID of the message.
        final String chatMessageId = messageId;
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

Sostituire `<thread_id>` con l'ID del thread a cui è stato inviato il messaggio.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Sostituire il commento `<ADD A USER>` con il codice seguente:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The CommunicationUser.identifier you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "a new participant";
participants.add(new ChatParticipant().setId(id).setDisplayName(displayName));
// The model to pass to the add method.
AddChatParticipantsRequest participants = new AddChatParticipantsRequest()
    .setParticipants(participants);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.addChatParticipants(threadId, participants, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Sostituire `<user_id>` con l'ID utente di servizi di comunicazione dell'utente da aggiungere. 
2. Sostituire `<thread_id>` con l'ID del thread a cui l'utente sta aggiungendo.

## <a name="list-users-in-a-thread"></a>Elencare gli utenti in un thread

Sostituire il commento `<LIST USERS>` con il codice seguente:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";

// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
    @Override
    public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> firstPage,
        Response response) {
        // pageCollection enables enumerating list of chat participants.
        pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> firstPage, Response response) {
                for (ChatParticipant participant : firstPage.getItems()) {
                    // Take further action.
                }
                retrieveNextParticipantsPages(firstPage.getPageId(), pageCollection);
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                // Handle error.
            }
         }
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});

void listChatParticipantsNext(String nextLink,
    AsyncPagedDataCollection<Page<ChatParticipant>> pageCollection) {
        @Override
        public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
            for (ChatParticipant participant : nextPage.getItems()) {
                // Take further action.
            }
            if (nextPage.getPageId() != null) {
                retrieveNextParticipantsPages(nextPage.getPageId(), pageCollection);
            }
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            // Handle error.
        }
}
```

Sostituire `<thread_id>` con l'ID del thread di cui si stanno elencando gli utenti.

## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Sostituire il commento `<REMOVE A USER>` con il codice seguente:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";
// The unique ID of the participant.
final String participantId = "<participant_id>";
threadClient.removeChatParticipant(threadId, participantId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Sostituire `<thread_id>` con l'ID del thread che rimuove l'utente da.
1. Sostituire `<participant_id>` con l'ID utente di servizi di comunicazione del partecipante da rimuovere.

## <a name="run-the-code"></a>Eseguire il codice

In Android Studio premere il pulsante Esegui per compilare ed eseguire il progetto. Nella console è possibile visualizzare l'output del codice e l'output del logger dal ChatClient.
