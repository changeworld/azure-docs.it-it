---
title: Come usare l'archiviazione di Accodamento di Azure da Node.js-archiviazione di Azure
description: Informazioni su come usare l'archiviazione code di Azure per creare ed eliminare code. Informazioni su come inserire, ottenere ed eliminare i messaggi utilizzando Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803681"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Come usare l'archiviazione di Accodamento di Azure da Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Panoramica

Questa guida illustra come eseguire scenari comuni usando l'archiviazione code di Azure. Gli esempi sono scritti usando l'API Node.js. Gli scenari descritti includono inserimento, visualizzazione, recupero ed eliminazione dei messaggi in coda. Viene inoltre illustrato come creare ed eliminare code.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

Per creare un'applicazione Node.js vuota, vedere [creare un'app Web di Node.js nel servizio app Azure](../../app-service/quickstart-nodejs.md), [compilare e distribuire un'applicazione Node.js nei servizi cloud di Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) usando PowerShell o [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione

La [libreria client di archiviazione di Azure per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) include un set di pratici librerie che comunicano con i servizi REST di archiviazione.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare node Package Manager (NPM) per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio PowerShell (Windows), Terminal (Mac) o bash (Unix), per passare alla cartella in cui è stata creata l'applicazione di esempio.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Digitare `npm install @azure/storage-queue` nella finestra di comando.

1. Verificare che `node_modules` sia stata creata una cartella. All'interno di tale cartella troverai il `@azure/storage-queue` pacchetto che contiene la libreria client che ti serve per accedere all'archiviazione.

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

1. Digitare `npm install azure-storage` nella finestra di comando.

1. Verificare che `node_modules` sia stata creata una cartella. All'interno di tale cartella è disponibile il `azure-storage` pacchetto, che contiene le librerie necessarie per accedere all'archiviazione.

---

### <a name="import-the-package"></a>Importare il pacchetto

Usando l'editor di codice, aggiungere quanto segue alla parte superiore del file JavaScript in cui si intende usare le code.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Come creare una coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Il codice seguente consente di ottenere il valore di una variabile di ambiente denominata `AZURE_STORAGE_CONNECTION_STRING` e di utilizzarlo per creare un [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) oggetto. Questo oggetto viene quindi utilizzato per creare un [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) oggetto che consente di utilizzare una coda specifica.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Se la coda esiste già, viene generata un'eccezione.

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Il modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` o le `AZURE_STORAGE_CONNECTION_STRING` informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni sull'account quando si chiama `createQueueService` .

Il codice seguente crea un `QueueService` oggetto che consente di usare le code.

```javascript
var queueSvc = azure.createQueueService();
```

Chiamare il `createQueueIfNotExists` metodo per creare una nuova coda con il nome specificato o restituire la coda, se esiste già.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se la coda viene creata, `result.created` è true. Se la coda esiste già, `result.created` è false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Come inserire un messaggio in una coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per aggiungere un messaggio a una coda, chiamare il [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metodo.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per inserire un messaggio in una coda, chiamare il `createMessage` metodo per creare un nuovo messaggio e aggiungerlo alla coda.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Come leggere il messaggio successivo

È possibile leggere i messaggi nella coda senza rimuoverli dalla coda chiamando il `peekMessages` metodo.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per impostazione predefinita, [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) Visualizza un singolo messaggio. Nell'esempio seguente vengono visualizzati i primi cinque messaggi nella coda. Se sono visibili meno di cinque messaggi, vengono restituiti solo i messaggi visibili.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per impostazione predefinita, `peekMessages` Visualizza un singolo messaggio.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` contiene il messaggio.

---

`peekMessages`Quando non sono presenti messaggi nella coda, non verrà restituito alcun errore. Tuttavia, non viene restituito alcun messaggio.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Come modificare il contenuto di un messaggio in coda

Nell'esempio seguente viene aggiornato il testo di un messaggio.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Modificare il contenuto di un messaggio sul posto nella coda chiamando [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Modificare il contenuto di un messaggio sul posto nella coda chiamando `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Come rimuovere dalla coda un messaggio

La rimozione dalla coda di un messaggio è un processo in due fasi:

1. Ottenere il messaggio.

1. Eliminazione del messaggio.

Nell'esempio seguente viene ottenuto un messaggio, che viene quindi eliminato.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per ottenere un messaggio, chiamare il [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metodo. Questa chiamata rende i messaggi invisibile nella coda, pertanto nessun altro client può elaborarli. Una volta che l'applicazione ha elaborato un messaggio, chiamare [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) per eliminarlo dalla coda.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi. Dopo 30 secondi è visibile agli altri client. È possibile specificare un valore diverso impostando [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) quando si chiama `receiveMessages` .

`receiveMessages`Quando non sono presenti messaggi nella coda, non verrà restituito alcun errore. Tuttavia, non verrà restituito alcun messaggio.

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per ottenere un messaggio, chiamare il `getMessages` metodo. Questa chiamata rende i messaggi invisibile nella coda, pertanto nessun altro client può elaborarli. Una volta che l'applicazione ha elaborato un messaggio, chiamare `deleteMessage` per eliminarlo dalla coda.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Per impostazione predefinita, un messaggio viene nascosto solo per 30 secondi. Dopo 30 secondi è visibile agli altri client. È possibile specificare un valore diverso usando `options.visibilityTimeout` con `getMessages` .

`getMessages`Se non sono presenti messaggi nella coda, l'utilizzo di non restituirà un errore. Tuttavia, non verrà restituito alcun messaggio.

---

## <a name="additional-options-for-dequeuing-messages"></a>Opzioni aggiuntive per rimuovere i messaggi dalla coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

È possibile personalizzare il recupero di messaggi da una coda in due modi:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Recuperare un batch di messaggi (fino a 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il `receiveMessages` metodo per ottenere cinque messaggi in un'unica chiamata. Ogni messaggio viene poi elaborato con un ciclo `for`. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

È possibile personalizzare il recupero di messaggi da una coda in due modi:

- `options.numOfMessages`: Recuperare un batch di messaggi (fino a 32).
- `options.visibilityTimeout`: Impostare un timeout di invisibilità più lungo o più breve.

Nell'esempio seguente viene usato il `getMessages` metodo per ottenere 15 messaggi in una sola chiamata. Ogni messaggio viene poi elaborato con un ciclo `for`. Per tutti i messaggi restituiti dal metodo, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Come ottenere la lunghezza della coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Il [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) metodo restituisce i metadati relativi alla coda, incluso il numero approssimativo di messaggi in attesa nella coda.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Il `getQueueMetadata` metodo restituisce i metadati relativi alla coda, incluso il numero approssimativo di messaggi in attesa nella coda.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Come elencare le code

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per recuperare un elenco di code, chiamare [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Per recuperare un elenco filtrato in base a un prefisso specifico, impostare [options. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) nella chiamata a `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per recuperare un elenco di code, usare `listQueuesSegmented` . Per recuperare un elenco filtrato in base a un prefisso specifico, usare `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se non è possibile restituire tutte le code, passare `result.continuationToken` come primo parametro di `listQueuesSegmented` o il secondo parametro di `listQueuesSegmentedWithPrefix` per recuperare altri risultati.

---

## <a name="how-to-delete-a-queue"></a>Come eliminare una coda

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metodo sull' `QueueClient` oggetto.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Per cancellare tutti i messaggi da una coda senza eliminarli, chiamare [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[JavaScript V2](#tab/javascript2)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il `deleteQueue` metodo sull'oggetto Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Per cancellare tutti i messaggi da una coda senza eliminarli, chiamare `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le nozioni di base dell'archiviazione code, seguire i collegamenti seguenti per informazioni sulle attività di archiviazione più complesse.

- Per informazioni sulle novità, visitare il [Blog del team di archiviazione di Azure](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
- Visitare il repository della [libreria client di archiviazione di Azure per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) su GitHub
