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
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386826"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Python](https://www.python.org/downloads/)
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di comunicazione di Azure](../../create-communication-resource.md). Per questa guida di avvio rapido sarà necessario registrare l'**endpoint** della risorsa.
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Aprire il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Usare un editor di testo per creare un file denominato **start-chat.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Installare l'SDK

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Servizi di comunicazione di Azure Chat SDK per Python.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat, si useranno l''endpoint di Servizi di comunicazione e il `Access Token` generato nell'ambito dei passaggi preliminari. Vedere altre informazioni sui [token di accesso utente](../../access-tokens.md).

Questa guida introduttiva non illustra la creazione di un livello di servizio per gestire i token per l'applicazione di chat, anche se è consigliabile. Per informazioni più dettagliate sull'architettura di [Chat,](../../../concepts/chat/concepts.md) vedere la documentazione seguente

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `create_chat_thread` per creare un thread di chat.

- Usare `topic` per specificare un argomento per il thread. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `update_thread`.
- Usare `thread_participants` per elencare il `ChatParticipant` da aggiungere al thread di chat. `ChatParticipant` accetta il tipo `CommunicationUserIdentifier` come `user`, che corrisponde a quello ottenuto in [Creare un utente](../../access-tokens.md#create-an-identity).

`CreateChatThreadResult` è il risultato restituito dalla creazione di un thread ed è possibile usarlo per recuperare `id` l'oggetto del thread di chat creato. Può `id` quindi essere usato per recuperare un oggetto usando il metodo `ChatThreadClient` `get_chat_thread_client` . `ChatThreadClient` può essere usato per eseguire altre operazioni di chat in questo thread di chat.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat
Il metodo `get_chat_thread_client` restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiungere partecipanti, inviare messaggi e thread_id è l'ID univoco del thread di chat esistente.

`ChatThreadClient` può essere usato per eseguire altre operazioni di chat in questo thread di chat.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Elencare tutti i thread di chat
Il `list_chat_threads` metodo restituisce un iteratore di tipo `ChatThreadItem` . Può essere usato per elencare tutti i thread di chat.

- Usare `start_time` per specificare il primo momento in cui ottenere i thread di chat.
- Usare `results_per_page` per specificare il numero massimo di thread di chat restituiti per pagina.

Un iteratore di `[ChatThreadItem]` è la risposta restituita dall'elenco dei thread

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare `send_message` il metodo per inviare un messaggio a un thread di chat appena creato, identificato da thread_id.

- Usare `content` per fornire il contenuto del messaggio di chat.
- Usare `chat_message_type` per specificare il tipo di contenuto del messaggio. I valori possibili sono 'text' e 'html'. se non viene specificato il valore predefinito 'text'.
- Usare `sender_display_name` per specificare il nome visualizzato del mittente.

`SendChatMessageResult` è la risposta restituita dall'invio di un messaggio, che contiene un ID, ovvero l'ID univoco del messaggio.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `list_messages` a intervalli specificati.

- Utilizzare `results_per_page` per specificare il numero massimo di messaggi da restituire per ogni pagina.
- Usare `start_time` per specificare il primo momento in cui ottenere i messaggi.

Un iteratore di `[ChatMessage]` è la risposta restituita dall'elenco dei messaggi

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `update_message` e `delete_message`. Per i messaggi eliminati, `ChatMessage.deleted_on` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `ChatMessage.edited_on` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `ChatMessage.created_on` e tale ora può essere usata per ordinare i messaggi.

`list_messages` restituisce tipi diversi di messaggi che possono essere identificati da `ChatMessage.type`. 

Altre informazioni sui tipi di messaggio sono disponibili qui: [Tipi di messaggio.](../../../concepts/chat/concepts.md#message-types)

## <a name="send-read-receipt"></a>Inviare la conferma di lettura
Il `send_read_receipt` metodo può essere usato per pubblicare un evento di conferma di lettura in un thread, per conto di un utente.

- Consente `message_id` di specificare l'ID del messaggio più recente letto dall'utente corrente.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo gli utenti, è possibile concedere loro l'accesso per poter inviare messaggi al thread di chat e aggiungere/rimuovere altri partecipanti. Prima di chiamare il metodo `add_participants`, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

È possibile aggiungere uno o più utenti al thread di chat usando il metodo , purché il nuovo token di accesso e l'identificazione `add_participants` siano disponibili per tutti gli utenti.

Viene restituito un `list(tuple(ChatParticipant, CommunicationError))`. Quando il partecipante viene aggiunto correttamente, è previsto un elenco vuoto. In caso di errore durante l'aggiunta del partecipante, l'elenco viene popolato con i partecipanti non riusciti insieme all'errore rilevato.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Elencare i partecipanti a un thread di chat

Analogamente all'aggiunta di un partecipante, è anche possibile elencare i partecipanti da un thread.

Usare `list_participants` per recuperare i partecipanti del thread.
- Usare `results_per_page` , facoltativo, Numero massimo di partecipanti da restituire per pagina.
- Usare `skip` , facoltativo, per ignorare i partecipanti fino a una posizione specificata nella risposta.

Un iteratore di `[ChatParticipant]` è la risposta restituita dall'elenco dei partecipanti

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `python`.

```console
python start-chat.py
```
