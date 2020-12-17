---
title: Trigger RabbitMQ per funzioni di Azure
description: Informazioni su come eseguire una funzione di Azure quando viene creato un messaggio RabbitMQ.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/15/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 26dee5200a60f4900ed20c2fd49a874552272776
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617222"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Panoramica del trigger RabbitMQ per funzioni di Azure

> [!NOTE]
> Le associazioni RabbitMQ sono completamente supportate solo nei piani **Premium di Windows** . Il consumo e Linux non sono attualmente supportati.

Usare il trigger RabbitMQ per rispondere ai messaggi provenienti da una coda di RabbitMQ.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-rabbitmq.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

Nell'esempio seguente viene illustrata una [funzione C#](functions-dotnet-class-library.md) che legge e registra il messaggio RabbitMQ come [evento RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

Nell'esempio seguente viene illustrato come leggere il messaggio come POCO.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

Analogamente agli oggetti JSON, si verificherà un errore se il messaggio non è formattato correttamente come oggetto C#. In caso contrario, viene associato alla variabile pocObj, che può essere usata per qualsiasi elemento necessario per.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Nell'esempio seguente viene illustrata un'associazione di trigger RabbitMQ in un *function.jssu* file e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione legge e registra il messaggio RabbitMQ.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Ecco il codice script C#:

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nell'esempio seguente viene illustrata un'associazione di trigger RabbitMQ in un *function.jssu* file e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione legge e registra un messaggio RabbitMQ.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Ecco il codice script JavaScript:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come leggere un messaggio della coda RabbitMQ tramite un trigger.

Un binding RabbitMQ viene definito in *function.jssu* dove *Type* è impostato su `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

La funzione Java seguente usa l' `@RabbitMQTrigger` annotazione dei [tipi Java RabbitMQ](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) per descrivere la configurazione per un trigger della coda RabbitMQ. La funzione acquisisce il messaggio inserito nella coda e lo aggiunge ai log.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnection", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md)usare l'attributo [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) .

Di seguito è riportato un `RabbitMQTrigger` attributo in una firma del metodo:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere l'esempio in C#.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L' `RabbitMQTrigger` annotazione consente di creare una funzione che viene eseguita quando viene creato un messaggio RabbitMQ. Le opzioni di configurazione disponibili includono il nome della coda e il nome della stringa di connessione. Per ulteriori dettagli sui parametri, vedere le [annotazioni Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Per altri dettagli, vedere l' [esempio](#example) di trigger.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `RabbitMQTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Deve essere impostato su "RabbitMQTrigger".|
|**direction** | n/d | Il valore deve essere impostato su "in".|
|**nome** | n/d | Nome della variabile che rappresenta la coda nel codice della funzione. |
|**queueName**|**QueueName**| Nome della coda da cui ricevere i messaggi. |
|**Nome host**|**HostName**|(ignorato se si usa ConnectStringSetting) <br>Nome host della coda (ad esempio: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ignorato se si usa ConnectionStringSetting) <br>Nome dell'impostazione dell'app che contiene il nome utente per accedere alla coda. Esempio: UserNameSetting: "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ignorato se si usa ConnectionStringSetting) <br>Nome dell'impostazione dell'app che contiene la password per accedere alla coda. Esempio: PasswordSetting: "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|Nome dell'impostazione dell'app che contiene la stringa di connessione della coda di messaggi RabbitMQ. Si noti che se si specifica la stringa di connessione direttamente e non tramite un'impostazione dell'app in local.settings.json, il trigger non funzionerà. (Ad esempio, in *function.json*: connectionStringSetting: "rabbitMQConnection" <br> In *local.settings.json*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**port**|**Porta**|(ignorato se si usa ConnectionStringSetting) Ottiene o imposta la porta utilizzata. Il valore predefinito è 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Il tipo di messaggio predefinito è l' [evento RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)e la `Body` proprietà dell'evento RabbitMQ può essere letta come i tipi elencati di seguito:

* `An object serializable as JSON` -Il messaggio viene recapitato come stringa JSON valida.
* `string`
* `byte[]`
* `POCO` -Il messaggio viene formattato come oggetto C#. Per un esempio completo, vedere [esempio](#example)in C#.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Il tipo di messaggio predefinito è l' [evento RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html)e la `Body` proprietà dell'evento RabbitMQ può essere letta come i tipi elencati di seguito:

* `An object serializable as JSON` -Il messaggio viene recapitato come stringa JSON valida.
* `string`
* `byte[]`
* `POCO` -Il messaggio viene formattato come oggetto C#. Per un esempio completo, vedere [esempio](#example)di script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il messaggio della coda è disponibile tramite Context. Bindings.<NAME> dove <NAME> corrisponde al nome definito in function.json. Se il payload è JSON, il valore viene deserializzato in un oggetto.

# <a name="python"></a>[Python](#tab/python)

Vedere l' [esempio](#example)di Python.

# <a name="java"></a>[Java](#tab/java)

Vedere [gli attributi e le annotazioni](#attributes-and-annotations)Java.

---

## <a name="dead-letter-queues"></a>Code dei messaggi non recapitabili
Le code e gli scambi di messaggi non recapitabili non possono essere controllati o configurati dal trigger RabbitMQ.  Per usare le code dei messaggi non recapitabili, pre-configurare la coda usata dal trigger in RabbitMQ. Consultare la documentazione di [RabbitMQ](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Ilhost.jsdi esempio *nel* file seguente contiene solo le impostazioni per questa associazione. Per altre informazioni sulle impostazioni di configurazione globali, vedere [host.jsdi riferimento per la versione di funzioni di Azure](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|prefetchCount|30|Ottiene o imposta il numero di messaggi che il ricevitore del messaggio può richiedere simultaneamente e viene memorizzato nella cache.|
|queueName|n/d| Nome della coda da cui ricevere i messaggi. |
|connectionString|n/d|Nome dell'impostazione dell'app che contiene la stringa di connessione della coda di messaggi RabbitMQ. Si noti che se si specifica la stringa di connessione direttamente e non tramite un'impostazione dell'app in local.settings.json, il trigger non funzionerà.|
|port|0|(ignorato se si usa connectionString) Numero massimo di sessioni che possono essere gestite simultaneamente per istanza ridimensionata.|

## <a name="local-testing"></a>Test locale

> [!NOTE]
> ConnectionString ha la precedenza su "hostName", "userName" e "password". Se tutte le impostazioni sono impostate, connectionString sostituirà gli altri due.

Se si esegue il test in locale senza una stringa di connessione, è necessario impostare l'impostazione "hostName" e "userName" e "password", se applicabile nella sezione "rabbitMQ" di *host.jsin*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|hostName|n/d|(ignorato se si usa ConnectStringSetting) <br>Nome host della coda (ad esempio: 10.26.45.210)|
|userName|n/d|(ignorato se si usa ConnectionStringSetting) <br>Nome per accedere alla coda |
|password|n/d|(ignorato se si usa ConnectionStringSetting) <br>Password per accedere alla coda|

## <a name="monitoring-rabbitmq-endpoint"></a>Monitoraggio dell'endpoint RabbitMQ
Per monitorare le code e gli scambi per un determinato endpoint RabbitMQ:

* Abilitare il [plug](https://www.rabbitmq.com/management.html) -in di gestione di RabbitMQ
* Passare a http://{node-hostname}: 15672 e accedere con il nome utente e la password.

## <a name="next-steps"></a>Passaggi successivi

- [Inviare messaggi RabbitMQ da funzioni di Azure (associazione di output)](./functions-bindings-rabbitmq-output.md)
