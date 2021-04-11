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
ms.openlocfilehash: 24a5c92164e0eace41224edfd2153c6142f7ea49
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251645"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installare [Xcode](https://developer.apple.com/xcode/) e [CocoaPods](https://cocoapods.org/). Usare Xcode per creare un'applicazione iOS per la Guida introduttiva e CocoaPods per installare le dipendenze.
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Guida introduttiva: creare e gestire le risorse di servizi di comunicazione](../../create-communication-resource.md). Per questa Guida introduttiva è necessario registrare l'endpoint della risorsa.
- Creare due utenti nei servizi di comunicazione di Azure ed emettere un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su `chat` e prendere nota della stringa, nonché `token` della `userId` stringa. In questa Guida introduttiva viene creato un thread con un partecipante iniziale, quindi viene aggiunto un secondo partecipante al thread.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-ios-application"></a>Creare una nuova applicazione iOS

Aprire Xcode e selezionare **Create a new Xcode project**. Selezionare quindi **iOS** come piattaforma e **app** per il modello.

Per il nome del progetto, immettere **ChatQuickstart**. Quindi selezionare **storyboard** come interfaccia, **delegare l'app UIKit** come ciclo di vita e **Swift** come linguaggio.

Selezionare **Avanti** e scegliere la directory in cui si vuole creare il progetto.

### <a name="install-the-libraries"></a>Installare le librerie

Usare CocoaPods per installare le dipendenze dei servizi di comunicazione necessarie.

Dalla riga di comando, passare all'interno della directory radice del `ChatQuickstart` progetto iOS. Creare un Podfile con il comando seguente: `pod init` .

Aprire Podfile e aggiungere le dipendenze seguenti alla `ChatQuickstart` destinazione:

```
pod 'AzureCommunication', '~> 1.0.0-beta.11'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
```

Installare le dipendenze con il comando seguente: `pod install` . Si noti che in questo modo viene creata anche un'area di lavoro Xcode.

Dopo l'esecuzione `pod install` , aprire nuovamente il progetto in Xcode selezionando il appena creato `.xcworkspace` .

### <a name="set-up-the-placeholders"></a>Configurare i segnaposto

Aprire l'area `ChatQuickstart.xcworkspace` di lavoro in Xcode, quindi aprire `ViewController.swift` .

In questa Guida introduttiva si aggiunge il codice a `viewController` e si visualizza l'output nella console di Xcode. Questa Guida introduttiva non risolve la compilazione di un'interfaccia utente in iOS. 

Nella parte superiore di `viewController.swift` importare le `AzureCommunication` librerie e `AzureCommunicatonChat` :

```
import AzureCommunication
import AzureCommunicationChat
```

Copiare il codice seguente nel `viewDidLoad()` metodo di `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

A scopo dimostrativo, verrà usato un semaforo per sincronizzare il codice. Nei passaggi seguenti si sostituiscono i segnaposto con il codice di esempio usando la libreria di chat di servizi di comunicazione Azure.


### <a name="create-a-chat-client"></a>Creare un client di chat

Sostituire il commento `<CREATE A CHAT CLIENT>` con il codice seguente:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

Sostituire `<ACS_RESOURCE_ENDPOINT>` con l'endpoint della risorsa di servizi di comunicazione di Azure. Sostituire `<ACCESS_TOKEN>` con un token di accesso valido per i servizi di comunicazione.

Questa Guida introduttiva non illustra la creazione di un livello di servizio per gestire i token per l'applicazione di chat, ma questa operazione è consigliata. Per ulteriori informazioni, vedere la sezione "architettura della chat" di [concetti relativi a chat](../../../concepts/chat/concepts.md).

Per altre informazioni sui token di accesso utente, vedere [Guida introduttiva: creare e gestire i token di accesso](../../access-tokens.md).

## <a name="object-model"></a>Modello a oggetti 

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services Chat SDK per JavaScript.

| Nome                                   | Descrizione                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni sulla sottoscrizione e usarlo per creare, ottenere ed eliminare thread. |
| `ChatThreadClient` | Questa classe è necessaria per la funzionalità del thread di chat. È possibile ottenere un'istanza tramite `ChatClient` e usarla per inviare, ricevere, aggiornare ed eliminare messaggi. È anche possibile usarlo per aggiungere, rimuovere e ottenere gli utenti, inviare notifiche di digitazione e conferme di lettura e sottoscrivere gli eventi di chat. |

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

È ora possibile usare il `ChatClient` per creare un nuovo thread con un utente iniziale.

Sostituire il commento `<CREATE A CHAT THREAD>` con il codice seguente:

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Sostituire `<USER_ID>` con un ID utente di servizi di comunicazione valido.

Si sta usando un semaforo per attendere il gestore di completamento prima di continuare. Nei passaggi successivi si userà la `threadId` dalla risposta restituita al gestore di completamento.

## <a name="list-all-chat-threads"></a>Elenca tutti i thread di chat

Dopo aver creato un thread di chat è possibile elencare tutti i thread di chat chiamando il `listChatThreads` metodo su `ChatClient` . Sostituire il commento `<LIST ALL CHAT THREADS>` con il codice seguente:

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(chatThreadItems):
        var iterator = chatThreadItems.syncIterator
            while let chatThreadItem = iterator.next() {
                print("Thread id: \(chatThreadItem.id)")
            }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Ora che è stato creato un thread di chat, è possibile ottenere un oggetto `ChatThreadClient` per eseguire operazioni all'interno del thread.

Sostituire il commento `<GET A CHAT THREAD CLIENT>` con il codice seguente:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Sostituire il commento `<SEND A MESSAGE>` con il codice seguente:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Innanzitutto, si costruisce l'oggetto `SendChatMessageRequest` , che contiene il contenuto e il nome visualizzato del mittente. Questa richiesta può inoltre contenere l'ora della cronologia di condivisione, se si desidera includerla. La risposta restituita al gestore di completamento contiene l'ID del messaggio inviato.


## <a name="send-a-read-receipt"></a>Invia una ricevuta di lettura

È possibile inviare una ricevuta di lettura per un determinato messaggio chiamando il `ChatThreadClients` `sendReadReceipt` metodo. Sostituire il commento `<SEND A READ RECEIPT>` con il codice seguente:

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile ricevere messaggi da un thread di chat chiamando il `listMessages()` metodo da `ChatThreadClient` . I messaggi di elenco includono messaggi di sistema e messaggi inviati dall'utente. Per ulteriori informazioni sui tipi di messaggi che è possibile ricevere, vedere [tipi di messaggio](https://docs.microsoft.com/azure/communication-services/concepts/chat/concepts#message-types)

Sostituire il commento `<RECEIVE MESSAGES>` con il codice seguente:

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messages):
        var iterator = messages.syncIterator
        while let message = iterator.next() {
            print("Received message of type \(message.type)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Sostituire il commento `<ADD A USER>` con il codice seguente:

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

Sostituire `<USER_ID>` con l'ID utente di servizi di comunicazione dell'utente da aggiungere.

Quando si aggiunge un partecipante a un thread, la risposta restituita potrebbe contenere errori. Questi errori rappresentano un errore durante l'aggiunta di particolari partecipanti.

## <a name="list-users-in-a-thread"></a>Elencare gli utenti in un thread

Sostituire il commento `<LIST USERS>` con il codice seguente:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>Eseguire il codice

In Xcode premere il pulsante Run (Esegui) per compilare ed eseguire il progetto. Nella console è possibile visualizzare l'output del codice e l'output del logger dal ChatClient.

