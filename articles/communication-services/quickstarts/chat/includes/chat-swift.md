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
ms.openlocfilehash: dedea2a622cb0eece92bb8b57871c76daa05fb68
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495422"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installare [Xcode](https://developer.apple.com/xcode/) e [Cocoapods](https://cocoapods.org/). verrà usato Xcode per creare un'applicazione iOS per la Guida introduttiva e Cocoapods per installare le dipendenze.
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../../create-communication-resource.md). Per questa Guida introduttiva è necessario **registrare l'endpoint della risorsa** .
- Creare **due** utenti di ACS ed emettere un token di [accesso utente](../../access-tokens.md)per il token di accesso utente. Assicurarsi di impostare l'ambito su **chat** e **Annotare la stringa del token, nonché la stringa UserID**. In questa Guida introduttiva si creerà un thread con un partecipante iniziale e quindi si aggiungerà un secondo partecipante al thread.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-ios-application"></a>Creare una nuova applicazione iOS

Aprire Xcode e selezionare `Create a new Xcode project` .

Nella finestra successiva selezionare `iOS` come piattaforma e `App` per il modello.

Quando si scelgono `ChatQuickstart` le opzioni immettere come nome del progetto. Selezionare `Storyboard` come interfaccia, `UIKit App Delegate` come ciclo di vita e `Swift` come linguaggio.

Fare clic su Avanti e scegliere la directory in cui si desidera creare il progetto.

### <a name="install-the-libraries"></a>Installare le librerie

Cocoapods verrà usato per installare le dipendenze dei servizi di comunicazione necessarie.

Dalla riga di comando spostarsi all'interno della directory radice del `ChatQuickstart` progetto iOS.

Creare un Podfile: `pod init`

Aprire Podfile e aggiungere le dipendenze seguenti alla `ChatQuickstart` destinazione:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Installare le dipendenze. verrà inoltre creata un'area di lavoro Xcode: `pod install`

**Dopo l'esecuzione di Pod install, riaprire il progetto in Xcode selezionando il appena creato `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>Configurare i segnaposto

Aprire l'area `ChatQuickstart.xcworkspace` di lavoro in Xcode e quindi aprire `ViewController.swift` .

In questa Guida introduttiva si aggiungerà il codice a `viewController` e si visualizza l'output nella console di Xcode. Questa Guida introduttiva non risolve la compilazione di un'interfaccia utente in iOS. 

Nella parte superiore di `viewController.swift` importare le `AzureCommunication` `AzureCommunicatonChat` librerie e:

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
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Verrà usato un semaforo per sincronizzare il codice a scopo dimostrativo. Nei passaggi seguenti verranno sostituiti i segnaposto con il codice di esempio usando la libreria di chat dei servizi di comunicazione di Azure.


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

Sostituire `<ACS_RESOURCE_ENDPOINT>` con l'endpoint della risorsa ACS.
Sostituire `<ACCESS_TOKEN>` con un token di accesso ACS valido.

Questa Guida introduttiva non illustra la creazione di un livello di servizio per gestire i token per l'applicazione di chat, sebbene sia consigliabile. Per ulteriori informazioni sull' [architettura di chat](../../../concepts/chat/concepts.md) , vedere la documentazione seguente

Vedere altre informazioni sui [token di accesso utente](../../access-tokens.md).

## <a name="object-model"></a>Modello a oggetti 
Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per JavaScript.

| Nome                                   | Descrizione                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura, sottoscrivere eventi di chat. |

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Ora verrà usato `ChatClient` per creare un nuovo thread con un utente iniziale.

Sostituire il commento `<CREATE A CHAT THREAD>` con il codice seguente:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Sostituire `<USER_ID>` con un ID utente di servizi di comunicazione valido.

Si sta usando un semaforo per attendere il gestore di completamento prima di continuare. Si userà `threadId` dalla risposta restituita al gestore di completamento nei passaggi successivi.

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Ora che è stato creato un thread di chat, otterremo un `ChatThreadClient` per eseguire operazioni all'interno del thread.

Sostituire il commento `<CREATE A CHAT THREAD CLIENT>` con il codice seguente:

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

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Prima di tutto viene costruito l'oggetto `SendChatMessageRequest` che contiene il nome visualizzato del contenuto e dei mittenti (facoltativamente può contenere il tempo della cronologia della condivisione). La risposta restituita al gestore di completamento contiene l'ID del messaggio inviato.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Sostituire il commento `<ADD A USER>` con il codice seguente:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Sostituire `<USER_ID>` con l'ID utente ACS dell'utente da aggiungere.

Quando si aggiunge un partecipante a un thread, la risposta che restituisce il completamento può contenere errori. Questi errori rappresentano un errore durante l'aggiunta di particolari partecipanti.

## <a name="list-users-in-a-thread"></a>Elencare gli utenti in un thread

Sostituire il commento `<LIST USERS>` con il codice seguente:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Sostituire il commento `<REMOVE A USER>` con il codice seguente:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Sostituire `<USER ID>` con l'ID utente di servizi di comunicazione del partecipante da rimuovere.

## <a name="run-the-code"></a>Eseguire il codice

In Xcode premere il pulsante Run (Esegui) per compilare ed eseguire il progetto. Nella console è possibile visualizzare l'output del codice e l'output del logger dal ChatClient.


