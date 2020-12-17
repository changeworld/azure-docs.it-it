---
title: Binding di output RabbitMQ per funzioni di Azure
description: Informazioni su come inviare messaggi RabbitMQ da funzioni di Azure.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/16/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: febcb3d2b6990d36a686dc4fab57a6bcbc96b080
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616661"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Panoramica dell'associazione di output RabbitMQ per funzioni di Azure

> [!NOTE]
> Le associazioni RabbitMQ sono completamente supportate solo nei piani **Premium di Windows** . Il consumo e Linux non sono attualmente supportati.

Usare l'associazione di output RabbitMQ per inviare messaggi a una coda di RabbitMQ.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che invia un messaggio RabbitMQ quando viene attivato da un TimerTrigger ogni 5 minuti usando il valore restituito del metodo come output:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ("outputQueue", ConnectionStringSetting = "ConnectionStringSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Nell'esempio seguente viene illustrato come utilizzare l'interfaccia IAsyncCollector per inviare messaggi.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    // processing:
    var myProcessedEvent = DoSomething(rabbitMQEvent);
    
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
}
```

Nell'esempio seguente viene illustrato come inviare i messaggi come POCO.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] TestClass rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<TestClass> outputPocObj,
ILogger log)
{
    // send the message
    await outputPocObj.Add(rabbitMQEvent);
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Nell'esempio seguente viene illustrata un'associazione di output RabbitMQ in un *function.jssu* file e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione legge il messaggio da un trigger HTTP e lo restituisce alla coda RabbitMQ.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

Ecco il codice script C#:

```csx
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nell'esempio seguente viene illustrata un'associazione di output RabbitMQ in un *function.jssu* file e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione legge il messaggio da un trigger HTTP e lo restituisce alla coda RabbitMQ.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di output di RabbitMQ in un *function.jssu* file e una funzione Python che usa l'associazione. La funzione legge il messaggio da un trigger HTTP e lo restituisce alla coda RabbitMQ.

Ecco i dati di associazione nel file *function.json*:

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    msg.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata una funzione Java che invia un messaggio alla coda RabbitMQ quando viene attivato da un TimerTrigger ogni 5 minuti.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQ", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md)usare [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Di seguito è riportato un `RabbitMQAttribute` attributo in una firma del metodo:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [esempio](#example)in C#.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L' `RabbitMQOutput` annotazione consente di creare una funzione che viene eseguita quando si invia un messaggio RabbitMQ. Le opzioni di configurazione disponibili includono il nome della coda e il nome della stringa di connessione. Per ulteriori dettagli sui parametri, vedere le [annotazioni Java RabbitMQOutput](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Per altri dettagli, vedere l' [esempio](#example) di associazione di output.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `RabbitMQ`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Deve essere impostato su "RabbitMQ".|
|**direction** | n/d | Deve essere impostato su "out". |
|**nome** | n/d | Nome della variabile che rappresenta la coda nel codice della funzione. |
|**queueName**|**QueueName**| Nome della coda a cui inviare i messaggi. |
|**Nome host**|**HostName**|(ignorato se si usa ConnectStringSetting) <br>Nome host della coda (ad esempio: 10.26.45.210)|
|**userName**|**UserName**|(ignorato se si usa ConnectionStringSetting) <br>Nome dell'impostazione dell'app che contiene il nome utente per accedere alla coda. Esempio: UserNameSetting: "< UserNameFromSettings >"|
|**password**|**Password**|(ignorato se si usa ConnectionStringSetting) <br>Nome dell'impostazione dell'app che contiene la password per accedere alla coda. Esempio: UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|Nome dell'impostazione dell'app che contiene la stringa di connessione della coda di messaggi RabbitMQ. Si noti che se si specifica la stringa di connessione direttamente e non tramite un'impostazione dell'app in local.settings.json, il trigger non funzionerà. (Ad esempio, in *function.json*: connectionStringSetting: "rabbitMQConnection" <br> In *local.settings.json*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**port**|**Porta**|(ignorato se si usa ConnectionStringSetting) Ottiene o imposta la porta utilizzata. Il valore predefinito è 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Usare i tipi di parametro seguenti per l'associazione di output:

* `byte[]`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `string`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `POCO` -Se il valore del parametro non è formattato come oggetto C#, verrà ricevuto un errore. Per un esempio completo, vedere [esempio](#example)in C#.

Quando si lavora con le funzioni C#:

* Per le funzioni asincrone è necessario un valore restituito o `IAsyncCollector` anziché un `out` parametro.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Usare i tipi di parametro seguenti per l'associazione di output:

* `byte[]`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `string`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `POCO` -Se il valore del parametro non è formattato come oggetto C#, verrà ricevuto un errore. Per un esempio completo, vedere [esempio](#example)di script C#.

Quando si lavora con le funzioni script C#:

* Per le funzioni asincrone è necessario un valore restituito o `IAsyncCollector` anziché un `out` parametro.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il messaggio della coda è disponibile tramite Context. Bindings.<NAME> dove <NAME> corrisponde al nome definito in function.json. Se il payload è JSON, il valore viene deserializzato in un oggetto.

# <a name="python"></a>[Python](#tab/python)

Vedere l' [esempio](#example)di Python.

# <a name="java"></a>[Java](#tab/java)

Usare i tipi di parametro seguenti per l'associazione di output:

* `byte[]`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `string`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `POJO` -Se il valore del parametro non è formattato come oggetto Java, verrà ricevuto un errore.

---

## <a name="next-steps"></a>Passaggi successivi

- [Esegui una funzione quando viene creato un messaggio RabbitMQ (trigger)](./functions-bindings-rabbitmq-trigger.md)
