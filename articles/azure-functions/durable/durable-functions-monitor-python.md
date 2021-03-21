---
title: Monitoraggi in Durable Functions (Python)-Azure
description: Informazioni su come implementare un monitoraggio dello stato usando l'estensione Durable Functions per funzioni di Azure (Python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434764"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Scenario di monitoraggio in Durable Functions-esempio di monitoraggio del problema di GitHub

Il modello di monitoraggio fa riferimento a un processo ricorrente flessibile in un flusso di lavoro, ad esempio il polling finché vengono soddisfatte determinate condizioni. Questo articolo illustra un esempio che usa Funzioni durevoli per implementare il monitoraggio.

## <a name="prerequisites"></a>Prerequisiti

* [Completare l'articolo introduttivo](quickstart-python-vscode.md)
* [Clonare o scaricare il progetto di esempio da GitHub](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>Panoramica dello scenario

Questo esempio monitora il numero di problemi in un repository GitHub e avvisa l'utente se sono presenti più di 3 problemi aperti. È possibile utilizzare una normale funzione attivata tramite timer per richiedere il numero di numeri aperti a intervalli regolari. Un problema di questo approccio è però la **gestione della durata**. Se deve essere inviato un solo avviso, il monitoraggio deve essere disabilitato dopo 3 o più problemi. Il modello di monitoraggio può terminare la propria esecuzione, tra gli altri vantaggi:

* I monitoraggi vengono eseguiti a intervalli, non in base a pianificazioni: un trigger timer *viene eseguito* ogni ora. Un monitoraggio *attende* un'ora tra un'azione e l'altra. Se non specificato, le azioni di un monitoraggio non si sovrapporranno e questo può essere importante per le attività con esecuzione prolungata.
* I monitoraggi possono avere intervalli dinamici: il tempo di attesa può variare in base ad alcune condizioni.
* I monitoraggi possono terminare quando viene soddisfatta una condizione o essere terminati da un altro processo.
* Monitoraggi possono accettare parametri. L'esempio illustra come applicare lo stesso processo di monitoraggio del repository a qualsiasi repository GitHub pubblico e numero di telefono richiesti.
* Monitoraggi sono scalabili. Poiché ogni monitoraggio è un'istanza di orchestrazione, si possono creare più monitoraggi senza dover creare nuove funzioni o definire altro codice.
* I monitoraggi si integrano facilmente in flussi di lavoro più grandi. Un monitoraggio può essere una sezione di una funzione di orchestrazione più complessa o un'[orchestrazione secondaria](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Configurazione

### <a name="configuring-twilio-integration"></a>Configurazione dell'integrazione di Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funzioni

Questo articolo descrive le funzioni seguenti nell'app di esempio:

* `E3_Monitor`: Funzione dell'agente di [orchestrazione](durable-functions-bindings.md#orchestration-trigger) che chiama `E3_TooManyOpenIssues` periodicamente. Chiama `E3_SendAlert` se il valore restituito di `E3_TooManyOpenIssues` è `True` .
* `E3_TooManyOpenIssues`: [Funzione di attività](durable-functions-bindings.md#activity-trigger) che controlla se un repository ha troppi problemi aperti. A scopo dimostrativo, è consigliabile avere più di 3 problemi aperti per essere troppo numerosi.
* `E3_SendAlert`: funzione dell'attività che invia un SMS tramite Twilio.

### <a name="e3_monitor-orchestrator-function"></a>Funzione dell'agente di orchestrazione E3_Monitor


La funzione **E3_Monitor** usa il codice *function.json* standard per le funzioni dell'agente di orchestrazione.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Di seguito è riportato il codice che implementa la funzione:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Le azioni di questa funzione dell'agente di orchestrazione sono le seguenti:

1. Ottiene il *repository* da monitorare e il *numero di telefono* a cui verrà inviata una notifica SMS.
2. Determina la scadenza del monitoraggio. L'esempio usa un valore hardcoded per ragioni di brevità.
3. Chiama **E3_TooManyOpenIssues** per determinare se sono presenti troppi problemi aperti nel repository richiesto.
4. Se sono presenti troppi problemi, chiama **E3_SendAlert** per inviare una notifica tramite SMS al numero di telefono richiesto.
5. Crea un timer durevole per riprendere l'orchestrazione all'intervallo di polling successivo. L'esempio usa un valore hardcoded per ragioni di brevità.
6. Continua l'esecuzione fino all'ora UTC corrente che supera l'ora di scadenza del monitoraggio oppure viene inviato un avviso SMS.

È possibile eseguire contemporaneamente più istanze dell'agente di orchestrazione chiamando la funzione dell'agente di orchestrazione più volte. È possibile specificare il repository da monitorare e il numero di telefono a cui inviare un avviso SMS. Si noti infine che la funzione dell'agente di orchestrazione *non* è in esecuzione in attesa del timer, quindi non viene addebitato alcun costo.


### <a name="e3_toomanyopenissues-activity-function"></a>Funzione E3_TooManyOpenIssues Activity

In modo analogo agli altri esempi, le funzioni di attività helper sono normali funzioni che usano l'associazione di trigger `activityTrigger`. La funzione **E3_TooManyOpenIssues** ottiene un elenco di problemi attualmente aperti nel repository e determina se sono presenti "troppi" di essi: più di 3 come per l'esempio.


Il codice *function.json* viene definito come segue:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Di seguito ne viene riportata l'implementazione.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>Funzione E3_SendAlert Activity

La funzione **E3_SendAlert** usa l'associazione Twilio per inviare un messaggio SMS che informa l'utente finale che sono presenti almeno 3 problemi aperti in attesa di una risoluzione.


Il codice *function.json* è semplice:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Di seguito è disponibile il codice che invia l'SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>Eseguire l'esempio

Sarà necessario un account [GitHub](https://github.com/) . Con esso, creare un repository pubblico temporaneo a cui è possibile accedere.

Con le funzioni attivate da HTTP incluse nell'esempio, è possibile avviare l'orchestrazione inviando la richiesta HTTP POST seguente:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Ad esempio, se il nome utente di GitHub è `foo` e il repository è, `bar` il valore di `"repo"` deve essere `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Viene avviata l'istanza **E3_Monitor** e viene eseguita una query sul repository fornito per individuare problemi aperti. Se vengono rilevati almeno 3 problemi aperti, viene chiamata una funzione di attività per l'invio di un avviso. in caso contrario, viene impostato un timer. Quando il timer scade, l'orchestrazione viene ripresa.

È possibile visualizzare l'attività dell'orchestrazione esaminando i log della funzione nel portale Funzioni di Azure.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

L'orchestrazione verrà completata dopo il raggiungimento del timeout o verranno rilevati più di 3 problemi aperti. È anche possibile usare l' `terminate` API all'interno di un'altra funzione o richiamare il webhook http post **terminatePostUri** a cui si fa riferimento nella risposta 202 precedente. Per usare il webhook, sostituire `{text}` con il motivo della terminazione anticipata. L'URL HTTP POST sarà approssimativamente simile al seguente:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Passaggi successivi

Questo esempio ha illustrato come usare Funzioni durevoli per monitorare lo stato di un'origine esterna usando [timer durevoli](durable-functions-timers.md) e la logica condizionale. Nell'esempio successivo viene illustrato come usare eventi esterni e [timer durevoli](durable-functions-timers.md) per gestire l'interazione umana.

> [!div class="nextstepaction"]
> [Eseguire l'esempio di interazione umana](durable-functions-phone-verification.md)
