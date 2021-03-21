---
title: Binding di output di archiviazione code di Azure per funzioni di Azure
description: Informazioni su come creare messaggi di archiviazione code di Azure in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455806"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Associazioni di output di archiviazione code di Azure per funzioni di Azure

Funzioni di Azure può creare nuovi messaggi di archiviazione code di Azure impostando un'associazione di output.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-storage-queue.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che crea un messaggio nella coda per ogni richiesta HTTP ricevuta.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente illustra un'associazione di trigger HTTP in un file *function.json* e il codice [script C# (file con estensione csx)](functions-reference-csharp.md) che usa l'associazione. La funzione crea un elemento della coda con un payload dell'oggetto **CustomQueueMessage** per ogni richiesta HTTP ricevuta.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C# che crea un singolo messaggio nella coda:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

È possibile inviare più messaggi contemporaneamente usando `ICollector` o il parametro `IAsyncCollector`. Ecco il codice script C# che invia più messaggi, uno con i dati della richiesta HTTP e uno con i valori hardcoded:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 Nell'esempio seguente viene illustrata una funzione Java che consente di creare un messaggio in coda per quando viene attivato da una richiesta HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare l'annotazione `@QueueOutput` per i parametri il cui valore viene scritto nell'archiviazione code.  Il tipo di parametro deve essere `OutputBinding<T>` , dove `T` è qualsiasi tipo Java nativo di un POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente illustra un'associazione di trigger HTTP in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione crea un elemento della coda per ogni richiesta HTTP ricevuta.

Ecco il file *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

È possibile inviare più messaggi contemporaneamente definendo un array di messaggio per l'associazione di output `myQueueItem`. Il codice JavaScript seguente invia due messaggi della coda con valori hardcoded per ogni richiesta HTTP ricevuta.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Negli esempi di codice seguenti viene illustrato come generare un messaggio della coda da una funzione attivata tramite HTTP. La sezione di configurazione con `type` di `queue` definisce l'associazione di output.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Utilizzando questa configurazione dell'associazione, una funzione di PowerShell può creare un messaggio della coda utilizzando `Push-OutputBinding` . In questo esempio viene creato un messaggio da una stringa di query o da un parametro del corpo.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Per inviare più messaggi contemporaneamente, definire una matrice di messaggi e utilizzare `Push-OutputBinding` per inviare messaggi all'associazione di output della coda.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come restituire valori singoli e multipli nelle code di archiviazione. La configurazione necessaria per *function.json* è identica in entrambi i casi.

Un binding della coda di archiviazione è definito infunction.jsin cui il *tipo* è impostato *su* `queue` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Per impostare un singolo messaggio nella coda, passare un singolo valore al `set` metodo.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Per creare più messaggi nella coda, dichiarare un parametro come tipo di elenco appropriato e passare una matrice di valori (che corrispondono al tipo di elenco) al `set` metodo.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

L'attributo si applica a un parametro `out` o al valore restituito della funzione. Il costruttore dell'attributo accetta il nome della coda, come illustrato nell'esempio seguente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

È possibile impostare la proprietà `Connection` per specificare l'account di archiviazione da usare, come illustrato nell'esempio seguente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [esempio di output](#example).

È possibile usare l'attributo `StorageAccount` per specificare l'account di archiviazione a livello di classe, metodo o parametro. Per altre informazioni, vedere Trigger - attributi.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="java"></a>[Java](#tab/java)

L' `QueueOutput` annotazione consente di scrivere un messaggio come output di una funzione. Nell'esempio seguente viene illustrata una funzione attivata tramite HTTP che consente di creare un messaggio in coda.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Proprietà    | Descrizione |
|-------------|-----------------------------|
|`name`       | Dichiara il nome del parametro nella firma della funzione. Quando la funzione viene attivata, il valore di questo parametro presenta il contenuto del messaggio della coda. |
|`queueName`  | Dichiara il nome della coda nell'account di archiviazione. |
|`connection` | Punta alla stringa di connessione dell'account di archiviazione. |

Il parametro associato all' `QueueOutput` annotazione viene tipizzato come istanza di [output \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Gli attributi non sono supportati da PowerShell.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `Queue`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `queue`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su `out`. Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta la coda nel codice della funzione. Impostare su `$return` per fare riferimento al valore restituito della funzione.|
|**queueName** |**QueueName** | Nome della coda. |
|**connection** | **Connection** |Nome di un'impostazione dell'app che contiene la stringa di connessione di archiviazione da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo il resto del nome.<br><br>Se ad esempio si imposta `connection` su "Storage", il runtime di funzioni Cerca un'impostazione dell'app denominata "" Storage ". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione di archiviazione predefinita nell'impostazione dell'app denominata `AzureWebJobsStorage`.<br><br>Se si usa [la versione 5. x o una versione successiva dell'estensione](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher), anziché una stringa di connessione, è possibile fornire un riferimento a una sezione di configurazione che definisce la connessione. Vedere [connessioni](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Predefinito

Scrivere un singolo messaggio della coda usando un parametro di metodo, ad esempio `out T paramName` . È possibile usare il tipo restituito del metodo anziché un parametro `out` e `T` può essere uno dei seguenti tipi:

* Un oggetto serializzabile come JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

In C# e negli script C# scrivere più messaggi nella coda usando uno dei seguenti tipi: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipi aggiuntivi

Anche le app che usano la [versione 5.0.0 o successiva dell'estensione di archiviazione](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) possono usare i tipi di [Azure SDK per .NET](/dotnet/api/overview/azure/storage.queues-readme). Questa versione Elimina il supporto per i `CloudQueue` tipi legacy e `CloudQueueMessage` a favore dei tipi seguenti:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) per la scrittura di più messaggi in coda

Per esempi relativi all'uso di questi tipi, vedere [il repository GitHub per l'estensione](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

### <a name="default"></a>Predefinito

Scrivere un singolo messaggio della coda usando un parametro di metodo, ad esempio `out T paramName` . `paramName`È il valore specificato nella `name` proprietà di *function.jssu*. È possibile usare il tipo restituito del metodo anziché un parametro `out` e `T` può essere uno dei seguenti tipi:

* Un oggetto serializzabile come JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se si prova a eseguire l'associazione a `CloudQueueMessage` e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

In C# e negli script C# scrivere più messaggi nella coda usando uno dei seguenti tipi: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipi aggiuntivi

Anche le app che usano la [versione 5.0.0 o successiva dell'estensione di archiviazione](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) possono usare i tipi di [Azure SDK per .NET](/dotnet/api/overview/azure/storage.queues-readme). Questa versione Elimina il supporto per i `CloudQueue` tipi legacy e `CloudQueueMessage` a favore dei tipi seguenti:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) per la scrittura di più messaggi in coda

Per esempi relativi all'uso di questi tipi, vedere [il repository GitHub per l'estensione](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

Sono disponibili due opzioni per l'output di un messaggio della coda da una funzione tramite l'annotazione [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Valore restituito**: applicando l'annotazione alla funzione stessa, il valore restituito della funzione viene reso persistente come messaggio in coda.

- **Imperativo**: per impostare in modo esplicito il valore del messaggio, applicare l'annotazione a un parametro specifico di tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), dove `T` è un POJO o qualsiasi tipo Java nativo. Con questa configurazione, il passaggio di un valore al `setValue` metodo rende permanente il valore come messaggio in coda.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'elemento della coda di output è disponibile tramite `context.bindings.<NAME>` dove `<NAME>` corrisponde al nome definito in *function.js*. È possibile usare una stringa o un oggetto serializzabile in JSON per il payload dell'elemento della coda.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

L'output per il messaggio della coda è disponibile tramite `Push-OutputBinding` la posizione in cui si passano gli argomenti che corrispondono al nome designato dal parametro del binding `name` nel *function.jssu* file.

# <a name="python"></a>[Python](#tab/python)

Sono disponibili due opzioni per l'output di un messaggio della coda da una funzione:

- **Valore restituito**: impostare la proprietà `name` in *function.json* su `$return`. Con questa configurazione, il valore restituito della funzione viene reso permanente come messaggio di archiviazione di Accodamento.

- **Imperativo**: passare un valore al metodo [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) del parametro dichiarato come tipo [Out](/python/api/azure-functions/azure.functions.out). Il valore passato a `set` viene reso permanente come messaggio di archiviazione di Accodamento.

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Binding |  Informazioni di riferimento |
|---|---|
| Coda | [Codici di errore della coda](/rest/api/storageservices/queue-service-error-codes) |
| Blob, Table, Queue | [Codici di errore di archiviazione](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Risoluzione dei problemi](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione come modifiche ai dati di archiviazione di Accodamento (trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
