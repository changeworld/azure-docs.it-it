---
title: Trigger e associazioni di archiviazione code di Azure per la Panoramica di funzioni di Azure
description: Informazioni su come usare il trigger di archiviazione code di Azure e l'associazione di output in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381478"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Trigger e associazioni di archiviazione code di Azure per la Panoramica di funzioni di Azure

Funzioni di Azure può essere eseguito quando vengono creati nuovi messaggi di archiviazione code di Azure e possono scrivere messaggi di Accodamento all'interno di una funzione.

| Azione | Tipo |
|---------|---------|
| Eseguire una funzione come modifiche ai dati di archiviazione di Accodamento | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Scrivi messaggi di archiviazione code |[Binding di output](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Commenti 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

#### <a name="storage-extension-5x-and-higher"></a>Estensione di archiviazione 5. x e versioni successive

Una nuova versione dell'estensione delle associazioni di archiviazione è disponibile come [pacchetto NuGet di anteprima](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Questa versione di anteprima introduce la possibilità di [connettersi usando un'identità invece di un segreto](./functions-reference.md#configure-an-identity-based-connection). Per le applicazioni .NET, vengono modificati anche i tipi a cui è possibile eseguire l'associazione, sostituendo i tipi da `WindowsAzure.Storage` e `Microsoft.Azure.Storage` con i tipi più recenti da [Azure. storage. Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> Il pacchetto di anteprima non è incluso in un bundle di estensione e deve essere installato manualmente. Per le applicazioni .NET, aggiungere un riferimento al pacchetto. Per tutti gli altri tipi di app, vedere [aggiornare le estensioni].

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aggiornare le estensioni]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Il *host.jsdi esempio nel* file seguente contiene solo le impostazioni versione 2. x + per questa associazione. Per altre informazioni sulle impostazioni di configurazione globali nelle versioni 2. x e successive, vedere [host.jsdi riferimento per funzioni di Azure](functions-host-json.md).

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|maxPollingInterval|00:00:01|L'intervallo massimo tra i polling di coda. Il valore minimo è 00:00:00.100 (100 ms) e incrementa fino a 00:01:00 (1 min).  In functions 2. x e versioni successive il tipo di dati è `TimeSpan` , mentre nella versione 1. x è in millisecondi.|
|visibilityTimeout|00:00:00|L'intervallo di tempo tra i tentativi se l'elaborazione di un messaggio ha esito negativo. |
|batchSize|16|Il numero di messaggi in coda che il runtime di Funzioni recupera simultaneamente e di processi in parallelo. Quando il numero elaborato viene ridotto a `newBatchThreshold`, il runtime ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è `batchSize` più `newBatchThreshold`. Questo limite si applica separatamente a ogni funzione attivata dalla coda. <br><br>Se si vuole evitare l'esecuzione in parallelo per i messaggi ricevuti su una coda, è possibile impostare `batchSize` su 1. Questa impostazione, tuttavia, Elimina la concorrenza finché l'app per le funzioni viene eseguita solo su una singola macchina virtuale (VM). Se l'app per le funzioni scala orizzontalmente più macchine virtuali, ogni macchina virtuale potrebbe eseguire un'istanza di ogni funzione attivata dalla coda.<br><br>Il valore massimo per `batchSize` è 32. |
|maxDequeueCount|5|Il numero di volte per provare l'elaborazione di un messaggio prima di essere spostato nella coda non elaborabile.|
|newBatchThreshold|batchSize/2|Ogni volta che il numero di messaggi elaborati simultaneamente viene ridotto a questo numero, il runtime recupera un altro batch.|
|messageEncoding|base64| Questa impostazione è disponibile solo nella [versione dell'estensione 5.0.0 e versioni successive](#storage-extension-5x-and-higher). Rappresenta il formato di codifica per i messaggi. I valori validi sono `base64` e `none`.|

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione come modifiche ai dati di archiviazione di Accodamento (trigger)](./functions-bindings-storage-queue-trigger.md)
- [Scrivi messaggi di archiviazione code (associazione di output)](./functions-bindings-storage-queue-output.md)
