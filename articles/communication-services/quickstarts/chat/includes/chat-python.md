---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: deec1dabe405d13d6009311c8b2d68a930e7aa29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661656"
---
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
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Installare la libreria client

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per Python.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat, si useranno l''endpoint di Servizi di comunicazione e il `Access Token` generato nell'ambito dei passaggi preliminari. Vedere altre informazioni sui [token di accesso utente](../../access-tokens.md).

Questa Guida introduttiva non illustra la creazione di un livello di servizio per gestire i token per l'applicazione di chat, sebbene sia consigliabile. Per ulteriori informazioni sull' [architettura di chat](../../../concepts/chat/concepts.md) , vedere la documentazione seguente

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `create_chat_thread` per creare un thread di chat.

- Usare `topic` per specificare un argomento per il thread. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `update_thread`.
- Usare `thread_participants` per elencare il `ChatThreadParticipant` da aggiungere al thread di chat. `ChatThreadParticipant` accetta il tipo `CommunicationUserIdentifier` come `user`, che corrisponde a quello ottenuto in [Creare un utente](../../access-tokens.md#create-an-identity).
- Usare `repeatability_request_id` per indicare che la richiesta è ripetibile. Il client può eseguire la richiesta più volte con lo stesso valore REPEATABLE-Request-ID e ottenere una risposta appropriata senza che il server esegua la richiesta più volte.

La risposta `chat_thread_client` viene utilizzata per eseguire operazioni sul thread di chat appena creato, ad esempio l'aggiunta di partecipanti al thread di chat, l'invio di messaggi, l'eliminazione di messaggi e così via. Contiene una `thread_id` proprietà che rappresenta l'ID univoco del thread di chat.

#### <a name="without-repeatability-request-id"></a>Senza ripetibilità-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Con REPEATABLE-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat
Il metodo `get_chat_thread` restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni nel thread creato: aggiungere partecipanti, inviare messaggi e così via. thread_id è l'ID univoco del thread di chat esistente.

```python
thread_id = 'id'
chat_thread = chat_client.get_chat_thread(thread_id)
```

## <a name="list-all-chat-threads"></a>Elenca tutti i thread di chat
Il `list_chat_threads` metodo restituisce un iteratore di tipo `ChatThreadInfo` . Può essere usato per elencare tutti i thread di chat.

- Consente `start_time` di specificare il primo punto nel tempo in cui ottenere i thread della chat fino a.
- Consente `results_per_page` di specificare il numero massimo di thread di chat restituiti per pagina.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for info in chat_thread_infos:
    # Iterate over all chat threads
    print("thread id:", info.id)
```

## <a name="delete-a-chat-thread"></a>Eliminare un thread di chat
`delete_chat_thread`Viene utilizzato per eliminare un thread di chat.

- Usare `thread_id` per specificare il thread_id di un thread di chat esistente che deve essere eliminato

```python
thread_id='id'
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il `send_message` metodo per inviare un messaggio a un thread di chat appena creato, identificato da thread_id.

- Usare `content` per fornire il contenuto del messaggio di chat.
- Utilizzare `chat_message_type` per specificare il tipo di contenuto del messaggio. I valori possibili sono ' text ' è html '; Se non viene specificato, viene assegnato il valore predefinito ' text '.
- Usare `sender_display_name` per specificare il nome visualizzato del mittente.

La risposta è un "ID" di tipo `str` , che è l'ID univoco del messaggio.

#### <a name="message-type-not-specified"></a>Tipo di messaggio non specificato
```python

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Tipo di messaggio specificato
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Ottenere un messaggio di chat specifico da un thread di chat
La `get_message` funzione può essere utilizzata per recuperare un messaggio specifico, identificato da un message_id

- Utilizzare `message_id` per specificare l'ID del messaggio

La risposta di tipo `ChatMessage` contiene tutte le informazioni relative al singolo messaggio.

```python
message_id = 'message_id'
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `list_messages` a intervalli specificati.

- Consente `results_per_page` di specificare il numero massimo di messaggi da restituire per ogni pagina.
- Consente `start_time` di specificare il momento in cui recuperare i messaggi.

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
```

`list_messages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `update_message` e `delete_message`. Per i messaggi eliminati, `ChatMessage.deleted_on` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `ChatMessage.edited_on` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `ChatMessage.created_on` e tale ora può essere usata per ordinare i messaggi.

`list_messages` restituisce tipi diversi di messaggi che possono essere identificati da `ChatMessage.type`. Questi tipi sono:

- `ChatMessageType.TEXT`: Messaggio di chat normale inviato da un partecipante del thread.

- `ChatMessageType.HTML`: Messaggio di chat HTML inviato da un partecipante del thread.

- `ChatMessageType.TOPIC_UPDATED`: messaggio di sistema che indica che l'argomento è stato aggiornato.

- `ChatMessageType.PARTICIPANT_ADDED`: Messaggio di sistema che indica che uno o più partecipanti sono stati aggiunti al thread di chat.

- `ChatMessageType.PARTICIPANT_REMOVED`: Messaggio di sistema che indica che un partecipante è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Aggiornare un argomento di un thread di chat
È possibile aggiornare l'argomento di un thread di chat usando il `update_topic` Metodo

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)
```

## <a name="update-a-message"></a>Aggiornare un messaggio
È possibile aggiornare il contenuto di un messaggio esistente usando il `update_message` metodo, identificato dal message_id

- Usare `message_id` per specificare il message_id
- Utilizzare `content` per impostare il nuovo contenuto del messaggio

```python
message_id='id'
content = 'updated content'
chat_thread_client.update_message(message_id=message_id, content=content)
```

## <a name="send-read-receipt-for-a-message"></a>Invia ricevuta di lettura per un messaggio
Il `send_read_receipt` metodo può essere usato per inserire un evento di conferma di lettura in un thread, per conto di un utente.

- Consente `message_id` di specificare l'ID dell'ultimo messaggio letto dall'utente corrente

```python
message_id='id'
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Elencare le conferme di lettura per un thread di chat
Il `list_read_receipts` metodo può essere usato per ottenere le ricevute di lettura per un thread.

- Consente `results_per_page` di specificare il numero massimo di conferme di lettura dei messaggi di chat da restituire per ogni pagina.
- Usare `skip` per specificare le ricevute di lettura del messaggio di chat fino a una posizione specificata in risposta.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for page in read_receipts.by_page():
    for item in page:
        print(item)
```

## <a name="send-typing-notification"></a>Invia notifica di digitazione
Il `send_typing_notification` metodo può essere usato per pubblicare un evento di digitazione in un thread, per conto di un utente.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Eliminare un messaggio
Il `delete_message` metodo può essere utilizzato per eliminare un messaggio identificato da un message_id

- Usare `message_id` per specificare il message_id

```python
message_id='id'
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per poter inviare messaggi al thread di chat e aggiungere o rimuovere altri partecipanti. Prima di chiamare il metodo `add_participant`, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

Usare `add_participant` il metodo per aggiungere partecipanti di thread al thread identificato da thread_id.

- Utilizzare `thread_participant` per specificare il partecipante da aggiungere al thread di chat.
- `user`, obbligatoria, è il `CommunicationUserIdentifier` creato da `CommunicationIdentityClient` in [Creare un utente](../../access-tokens.md#create-an-identity)
- `display_name`, facoltativo, è il nome visualizzato per il partecipante del thread.
- `share_history_time`, facoltativo, è l'ora da cui la cronologia delle chat viene condivisa con il partecipante. Per condividere la cronologia dall'inizio del thread di chat, impostare questa proprietà su qualsiasi data uguale o precedente all'ora di creazione del thread. Per non condividere alcuna cronologia precedente a quando il partecipante è stato aggiunto, impostarlo sulla data corrente. Per condividere una cronologia parziale, impostarla su una data intermedia.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

È anche possibile aggiungere più utenti al thread di chat usando il `add_participants` metodo, purché sia disponibile un nuovo token di accesso e l'identificazione sia disponibile per tutti gli utenti.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Analogamente all'aggiunta di un partecipante, è anche possibile rimuovere i partecipanti da un thread. Per rimuovere, è necessario tenere traccia degli ID dei partecipanti aggiunti.

Usare `remove_participant` il metodo per rimuovere il partecipante del thread dal thread identificato da ThreadId.
- `user` oggetto da `CommunicationUserIdentifier` rimuovere dal thread.

```python
chat_thread_client.remove_participant(user)
```

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `python`.

```console
python start-chat.py
```
