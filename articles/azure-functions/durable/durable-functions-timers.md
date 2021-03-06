---
title: Timer in Funzioni permanenti - Azure
description: Informazioni su come implementare timer permanenti nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91876448"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timer in Funzioni permanenti (Funzioni di Azure)

[Funzioni permanenti](durable-functions-overview.md) fornisce *timer permanenti* da usare nelle funzioni di orchestrazione per implementare ritardi o per impostare timeout nelle azioni asincrone. I timer durevoli devono essere usati nelle funzioni dell'agente di orchestrazione invece di `Thread.Sleep` e `Task.Delay` (C#), or `setTimeout()` e `setInterval()` (JavaScript) o `time.sleep()` (Python).

Si crea un timer durevole chiamando il metodo `CreateTimer` (.NET) o il `createTimer` Metodo (JavaScript) dell'associazione del [trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger). Il metodo restituisce un'attività che viene completata in una data e un'ora specificate.

## <a name="timer-limitations"></a>Limitazioni dei timer

Quando si crea un timer che scade alle 4:30 PM, il Framework di attività permanenti sottostante Accoda un messaggio che diventa visibile solo alle 4:30. Quando si esegue il piano a consumo di funzioni di Azure, il messaggio del timer appena visibile garantisce che l'app per le funzioni venga attivata in una macchina virtuale appropriata.

> [!NOTE]
> * A partire dalla [versione 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) dell'estensione durevole, i timer durevoli sono illimitati. Nelle versioni precedenti dell'estensione, i timer durevoli sono limitati a sette giorni. Quando si usa una versione precedente e si richiede un ritardo superiore a sette giorni, usare le API del timer in un `while` ciclo per simulare questo ritardo.
> * Usare sempre `CurrentUtcDateTime` anziché `DateTime.UtcNow` in .NET o al `currentUtcDateTime` posto di `Date.now` o `Date.UTC` in JavaScript quando si calcola il tempo di attivazione per i timer durevoli. Per ulteriori informazioni, vedere l'articolo sui vincoli del codice della funzione dell'agente di [orchestrazione](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Utilizzo per il ritardo

L'esempio seguente illustra come usare i timer permanenti per ritardare l'esecuzione. L'esempio sta inviando una notifica di fatturazione ogni giorno per 10 giorni.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> L'esempio C# precedente è destinato a Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Evitare i cicli infiniti nelle funzioni di orchestrazione. Per informazioni su come implementare scenari di ciclo infinito in modo sicuro ed efficiente, vedere [Orchestrazioni perenni](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Utilizzo per i timeout

Questo esempio illustra come usare i timer permanenti per implementare timeout.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> L'esempio C# precedente è destinato a Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> Usare `CancellationTokenSource` (.NET) o chiamare `cancel()` sull'oggetto restituito `TimerTask` (JavaScript) per annullare un timer durevole se il codice non ne attende il completamento. Il Framework di attività permanenti non modifica lo stato di un'orchestrazione su "completato" fino a quando tutte le attività in sospeso non vengono completate o annullate.

Questo meccanismo di annullamento non termina le esecuzioni della funzione di attività in corso o dell'orchestrazione secondaria. ma consente semplicemente alla funzione di orchestrazione di ignorare il risultato e continuare. Se l'app per le funzioni usa il piano a consumo, verranno comunque addebitati tutti i tempi e la memoria utilizzati dalla funzione attività abbandonata. Per impostazione predefinita, le funzioni in esecuzione nel piano a consumo hanno un timeout di cinque minuti. Se questo limite viene superato, l'host di Funzioni di Azure viene riciclato per interrompere ogni esecuzione e impedire una fatturazione eccessiva. Il [timeout delle funzioni è configurabile](../functions-host-json.md#functiontimeout).

Per un esempio più dettagliato di come implementare i timeout nelle funzioni dell'agente di orchestrazione, vedere l'articolo relativo all' [interazione umana & timeouts-Verifica telefono](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come generare e gestire eventi esterni](durable-functions-external-events.md)
