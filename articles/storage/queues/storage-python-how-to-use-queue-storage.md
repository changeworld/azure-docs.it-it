---
title: Come usare l'archiviazione di Accodamento di Azure da Python
description: Informazioni su come usare l'archiviazione code di Azure da Python per creare ed eliminare code e per inserire, ottenere ed eliminare messaggi.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 8c30d05cf49162155f84961e1fd8a32361444ba5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653262"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Come usare l'archiviazione di Accodamento di Azure da Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Panoramica

Questo articolo illustra scenari comuni con il servizio di archiviazione di Accodamento di Azure. Gli scenari descritti includono inserimento, visualizzazione, recupero ed eliminazione dei messaggi in coda. Viene inoltre analizzato il codice per la creazione e l'eliminazione di code.

Gli esempi in questo articolo sono scritti in Python e usano la [libreria client di archiviazione code di Azure per Python](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue). Per altre informazioni sulle code, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per Python

[Azure Storage SDK per Python](https://github.com/azure/azure-storage-python) richiede Python v 2.7, v 3.3 o versione successiva.

### <a name="install-via-pypi"></a>Installare tramite PyPI

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python V2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python v 0.36 o versione precedente, disinstallare l'SDK precedente usando `pip uninstall azure-storage` prima di installare il pacchetto più recente.

Per i metodi di installazione alternativi, vedere [Azure SDK per Python](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Configurazione dell'applicazione per l’accesso ad Archiviazione di accodamento

# <a name="python-v12"></a>[Python V12](#tab/python)

L' [`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) oggetto consente di utilizzare una coda. Aggiungere il codice seguente nella parte superiore del file Python in cui si vuole accedere a una coda di Azure a livello di codice:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

L' [`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true) oggetto consente di usare le code. Il codice seguente crea un `QueueService` oggetto. Aggiungere il codice seguente nella parte superiore del file Python in cui si vuole accedere ad archiviazione di Azure a livello di codice:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

Il `os` pacchetto fornisce supporto per recuperare una variabile di ambiente. Il `uuid` pacchetto fornisce supporto per la generazione di un identificatore univoco per un nome di coda.

## <a name="create-a-queue"></a>Creare una coda

La stringa di connessione viene recuperata dalla `AZURE_STORAGE_CONNECTION_STRING` variabile di ambiente impostata in precedenza.

# <a name="python-v12"></a>[Python V12](#tab/python)

Il codice seguente crea un `QueueClient` oggetto usando la stringa di connessione di archiviazione.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Il codice seguente crea un `QueueService` oggetto usando la stringa di connessione di archiviazione.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

I messaggi della coda di Azure vengono archiviati come testo. Se si desidera archiviare i dati binari, configurare le funzioni di codifica e decodifica Base64 prima di inserire un messaggio nella coda.

# <a name="python-v12"></a>[Python V12](#tab/python)

Configurare le funzioni di codifica e decodifica Base64 quando si crea l'oggetto client.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Configurare le funzioni di codifica e decodifica Base64 nell'oggetto di archiviazione di Accodamento.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>Inserire un messaggio in una coda

# <a name="python-v12"></a>[Python V12](#tab/python)

Per inserire un messaggio in una coda, usare il [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metodo.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Per inserire un messaggio in una coda, usare il [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) metodo per creare un nuovo messaggio e aggiungerlo alla coda.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>Visualizzare in anteprima i messaggi

# <a name="python-v12"></a>[Python V12](#tab/python)

È possibile leggere i messaggi senza rimuoverli dalla coda chiamando il [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metodo. Per impostazione predefinita, questo metodo Visualizza un singolo messaggio.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

È possibile leggere i messaggi senza rimuoverli dalla coda chiamando il [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) metodo. Per impostazione predefinita, questo metodo Visualizza un singolo messaggio.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività, è possibile utilizzare questa funzionalità per aggiornare lo stato dell'attività.

# <a name="python-v12"></a>[Python V12](#tab/python)

Il codice seguente usa il [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metodo per aggiornare un messaggio. Il timeout di visibilità è impostato su 0. Ciò significa che il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Il codice seguente usa il [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metodo per aggiornare un messaggio. Il timeout di visibilità è impostato su 0. Ciò significa che il messaggio viene visualizzato immediatamente e il contenuto viene aggiornato.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda.

# <a name="python-v12"></a>[Python V12](#tab/python)

Il metodo [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) restituisce le proprietà della coda `approximate_message_count` , tra cui.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Il [`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) metodo restituisce le proprietà della coda, tra cui `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Il risultato è solo approssimativo perché i messaggi possono essere aggiunti o rimossi dopo che il servizio risponde alla richiesta.

## <a name="dequeue-messages"></a>Rimuovere dalla coda i messaggi

Rimuovere un messaggio da una coda in due passaggi. Se il codice non riesce a elaborare un messaggio, questo processo in due passaggi garantisce che sia possibile ottenere lo stesso messaggio e riprovare. Chiamare `delete_message` dopo che il messaggio è stato elaborato correttamente.

# <a name="python-v12"></a>[Python V12](#tab/python)

Quando si chiama [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-), per impostazione predefinita si ottiene il messaggio successivo nella coda. Un messaggio restituito da `receive_messages` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Quando si chiama [get_Messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), per impostazione predefinita si ottiene il messaggio successivo nella coda. Un messaggio restituito da `get_messages` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-).

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio.

# <a name="python-v12"></a>[Python V12](#tab/python)

Nell'esempio di codice seguente viene usato il [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metodo per ottenere i messaggi in batch. Elabora quindi ogni messaggio all'interno di ogni batch usando un ciclo annidato `for` . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Nell'esempio di codice seguente viene usato il [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) metodo per ottenere 16 messaggi in una sola chiamata. Ogni messaggio viene poi elaborato con un ciclo `for`. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Eliminare una coda

# <a name="python-v12"></a>[Python V12](#tab/python)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metodo.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python V2](#tab/python2)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metodo.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le nozioni di base dell'archiviazione code, seguire questi collegamenti per altre informazioni.

- [Informazioni di riferimento sull'API Python di archiviazione code di Azure](/python/api/azure-storage-queue)
- [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/)
- [Riferimento all'API REST di archiviazione di Azure](/rest/api/storageservices/)
