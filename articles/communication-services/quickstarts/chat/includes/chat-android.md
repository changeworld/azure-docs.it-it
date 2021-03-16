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
ms.openlocfilehash: 02b47d7797eaebf6049099729ea51554a61a765e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495419"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Android Studio](https://developer.android.com/studio), si utilizzerà Android Studio per creare un'applicazione Android per la Guida introduttiva per l'installazione delle dipendenze.
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../../create-communication-resource.md). Per questa Guida introduttiva è necessario **registrare l'endpoint della risorsa** .
- Creare **due** utenti di servizi di comunicazione ed emettere un token di [accesso utente](../../access-tokens.md)del token di accesso utente. Assicurarsi di impostare l'ambito su **chat** e **prendere nota della stringa del token e della stringa UserID**. In questa Guida introduttiva si creerà un thread con un partecipante iniziale e quindi si aggiungerà un secondo partecipante al thread.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-android-application"></a>Creare una nuova applicazione Android

1. Aprire Android Studio e selezionare `Create a new project` . 
2. Nella finestra successiva selezionare `Empty Activity` come modello di progetto.
3. Quando si scelgono le opzioni, immettere `ChatQuickstart` come nome del progetto.
4. Fare clic su Avanti e scegliere la directory in cui si desidera creare il progetto.

### <a name="install-the-libraries"></a>Installare le librerie

Gradle verrà usato per installare le dipendenze dei servizi di comunicazione necessarie. Dalla riga di comando spostarsi all'interno della directory radice del `ChatQuickstart` progetto. Aprire il file Build. Gradle dell'app e aggiungere le dipendenze seguenti alla `ChatQuickstart` destinazione:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.7'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.7'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Escludere i metadati nelle opzioni di creazione pacchetti nella compilazione radice. Gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="add-a-maven-resource-in-root-buildgradle"></a>Aggiungere una risorsa Maven nella build radice. Gradle
```
allprojects {
    repositories {
        ...
        maven {
            url 'https://trouterpublicpackages.z13.web.core.windows.net'
        }
    }
```

Fare clic su' Sincronizza ora ' in Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>Alternativa Per installare le librerie tramite Maven
Per importare la libreria nel progetto usando il sistema di compilazione [Maven](https://maven.apache.org/) , aggiungerla alla `dependencies` sezione del file dell'app `pom.xml` , specificando l'ID dell'artefatto e la versione che si vuole usare:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.7</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configurare i segnaposto

Aprire e modificare il file `MainActivity.java` . In questa Guida introduttiva si aggiungerà il codice a `MainActivity` e si visualizza l'output nella console. Questa Guida introduttiva non risolve la compilazione di un'interfaccia utente. Nella parte superiore del file importare le `Communication common` librerie e `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Copiare il codice seguente nel file `MainActivity` :

```java
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.7";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
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
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
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

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. Utilizzare `ChatAsyncClient.Builder` per configurare e creare un'istanza di `ChatAsyncClient` .
2. Sostituire `<resource>` con la risorsa servizi di comunicazione.
3. Sostituire `<user_access_token>` con un token di accesso valido per i servizi di comunicazione.

## <a name="object-model"></a>Modello a oggetti
Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per JavaScript.

| Nome                                   | Descrizione                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni sulla sottoscrizione e usarlo per creare, ottenere ed eliminare thread. |
| ChatThreadClient/ChatThreadAsyncClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura, sottoscrivere eventi di chat. |

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Verrà usato `ChatClient` per creare un nuovo thread con un utente iniziale.

Sostituire il commento `<CREATE A CHAT THREAD>` con il codice seguente:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setCommunicationIdentifier(new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(id)))
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

Sostituire `<user_id>` con un ID utente di servizi di comunicazione valido. Si userà la `threadId` dalla risposta restituita al gestore di completamento nei passaggi successivi, quindi sostituire `<thread_id>` nella classe con l'ottenimento `threadId` da questa richiesta ed eseguire di nuovo l'app.

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Ora che è stato creato un thread di chat, otterremo un `ChatThreadClient` per eseguire operazioni all'interno del thread. Sostituire il commento `<CREATE A CHAT THREAD CLIENT>` con il codice seguente:

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Assicurarsi che sia stato sostituito `<thread_id>` con un ID di thread valido. verrà inviato un messaggio a tale thread.

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

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Al termine `chatMessageId` dell'operazione, è possibile sostituire `<chat_message_id>` con `chatMessageId` per un utilizzo successivo del metodo nella Guida introduttiva ed eseguire nuovamente l'app.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Sostituire il commento `<ADD A USER>` con il codice seguente:

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setCommunicationIdentifier(
          new CommunicationIdentifierModel().setCommunicationUser(
              new CommunicationUserIdentifierModel().setId(second_user_id)
          )).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Sostituire `<second_user_id>` nella classe con l'ID utente di servizi di comunicazione dell'utente da aggiungere. 

## <a name="list-users-in-a-thread"></a>Elencare gli utenti in un thread

Sostituire il commento `<LIST USERS>` con il codice seguente:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Inserire il seguente metodo helper nella classe:

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Assicurarsi di sostituire `<second_user_id>` con un ID utente valido. verrà rimosso il secondo utente dal thread.

Sostituire il commento `<REMOVE A USER>` con il codice seguente:

```java
CommunicationIdentifierModel communicationIdentifierModel = new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(second_user_id));
threadClient.removeChatParticipant(threadId, communicationIdentifierModel, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>Invia una notifica di tipizzazione

Sostituire il commento `<SEND A TYPING NOTIFICATION>` con il codice seguente:

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>Invia una ricevuta di lettura

Assicurarsi di sostituire `<chat_message_id>` con un ID messaggio di chat valido. verrà inviata una ricevuta di lettura per questo messaggio.

Sostituire il commento `<SEND A READ RECEIPT>` con il codice seguente:

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>Elencare le conferme di lettura

Sostituire il commento `<READ RECEIPTS>` con il codice seguente:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

Inserire il seguente metodo helper nella classe:
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>Eseguire il codice

In Android Studio, fare clic sul pulsante Run (Esegui) per compilare ed eseguire il progetto. Nella console di è possibile visualizzare l'output del codice e l'output del logger da ChatClient.
