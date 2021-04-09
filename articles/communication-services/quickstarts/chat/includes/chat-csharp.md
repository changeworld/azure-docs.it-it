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
ms.openlocfilehash: 4c8bd66dde54ff90ea2191fba58f10c87c45cf68
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958020"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:
- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di comunicazione di Azure](../../create-communication-resource.md). Per questa guida di avvio rapido sarà necessario registrare l'**endpoint** della risorsa.
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `ChatQuickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Impostare la directory sulla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installare il pacchetto

Installare Azure Communication Chat SDK per .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services Chat SDK per C#.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite il ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere i partecipanti, inviare notifiche di digitazione e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat, è necessario usare l'endpoint di servizi di comunicazione e il token di accesso generato nell'ambito dei passaggi dei prerequisiti. È necessario usare la `CommunicationIdentityClient` classe di Identity SDK per creare un utente ed emettere un token da passare al client di chat.

Vedere altre informazioni sui [token di accesso utente](../../access-tokens.md).

Questa Guida introduttiva non illustra la creazione di un livello di servizio per gestire i token per l'applicazione di chat, sebbene sia consigliabile. Altre informazioni sull' [architettura della chat](../../../concepts/chat/concepts.md)

Copiare i frammenti di codice seguenti e incollarli nel file di origine: **Program. cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il `createChatThread` metodo in chatClient per creare un thread di chat
- Usare `topic` per specificare un argomento per questa chat. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `UpdateTopic`.
- Usare la proprietà `participants` per passare un elenco di oggetti `ChatParticipant` da aggiungere al thread di chat. L'oggetto `ChatParticipant` viene inizializzato con un oggetto `CommunicationIdentifier`. `CommunicationIdentifier` può essere di tipo `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` o `PhoneNumberIdentifier` . Per ottenere un `CommunicationIdentifier` oggetto, ad esempio, è necessario passare un ID di accesso creato seguendo le istruzioni per [creare un utente](../../access-tokens.md#create-an-identity)

L'oggetto Response del `createChatThread` metodo contiene i `chatThread` Dettagli. Per interagire con le operazioni del thread di chat, ad esempio l'aggiunta di partecipanti, l'invio di un messaggio, l'eliminazione di un messaggio e così via, è `chatThreadClient` necessario creare un'istanza del client utilizzando il `GetChatThreadClient` metodo nel `ChatClient` client.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat
Il metodo `GetChatThreadClient` restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiungere membri, inviare messaggi e così via. threadId è l'ID univoco del thread di chat esistente.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Elenca tutti i thread di chat
Utilizzare `GetChatThreads` per recuperare tutti i thread di chat di cui l'utente fa parte.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Utilizzare `SendMessage` per inviare un messaggio a un thread.

- Utilizzare `content` per fornire il contenuto del messaggio, è obbligatorio.
- Utilizzare `type` per il tipo di contenuto del messaggio, ad esempio ' text ' o ' html '. Se non è specificato, verrà impostato ' text '.
- Usare `senderDisplayName` per specificare il nome visualizzato del mittente. Se non è specificato, verrà impostata una stringa vuota.

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `GetMessages` sul client del thread di chat a intervalli specificati.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` accetta il parametro facoltativo `DateTimeOffset`. Se si specifica tale offset, si riceveranno i messaggi ricevuti, aggiornati o eliminati dopo di esso. Si noti che verranno restituiti anche i messaggi ricevuti prima della data/ora di offset ma modificati o rimossi dopo tale data/ora.

`GetMessages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `UpdateMessage` e `DeleteMessage`. Per i messaggi eliminati, `chatMessage.DeletedOn` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `chatMessage.EditedOn` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `chatMessage.CreatedOn` e tale ora può essere usata per ordinare i messaggi.

`GetMessages` restituisce tipi diversi di messaggi che possono essere identificati da `chatMessage.Type`. Questi tipi sono:

- `Text`: messaggio di chat normale inviato da un membro del thread.

- `Html`: Un messaggio di testo formattato. Tenere presente che attualmente gli utenti di Servizi di comunicazione non possono inviare messaggi in formato RTF. Questo tipo di messaggio è supportato per i messaggi inviati da utenti di Teams a utenti di Servizi di comunicazione in scenari di interoperabilità di Teams.

- `TopicUpdated`: messaggio di sistema che indica che l'argomento è stato aggiornato. ReadOnly

- `ParticipantAdded`: Messaggio di sistema che indica che uno o più partecipanti sono stati aggiunti al thread di chat. ReadOnly

- `ParticipantRemoved`: Messaggio di sistema che indica che un partecipante è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Dopo aver creato un thread, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per poter inviare messaggi al thread e aggiungere/rimuovere altri partecipanti. Prima di chiamare `AddParticipants`, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

Usare `AddParticipants` per aggiungere uno o più partecipanti al thread di chat. Di seguito sono riportati gli attributi supportati per ogni partecipante di thread:
- `communicationUser`, required, è l'identità del partecipante del thread.
- `displayName`, facoltativo, è il nome visualizzato per il partecipante del thread.
- `shareHistoryTime`, facoltativo, ora da cui la cronologia delle chat viene condivisa con il partecipante.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>Ottenere i partecipanti ai thread

Utilizzare `GetParticipants` per recuperare i partecipanti del thread di chat.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Invia ricevuta di lettura

Utilizzare `SendReadReceipt` per notificare agli altri partecipanti che il messaggio viene letto dall'utente.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```console
dotnet run
```
