---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: dbb32aa0cb61024c3d59879775fe73801d2b9668
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214709"
---
### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.|
|eventProcessorOptions/maxBatchSize|10|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|eventProcessorOptions/prefetchCount|300|Il conteggio predefinito di pre-recupero usato dall'elemento `EventProcessorHost` sottostante. Il valore minimo consentito è 10.|
|initialOffsetOptions/type|fromStart|La posizione nel flusso di eventi da cui iniziare l'elaborazione quando nell'archiviazione non esiste un checkpoint. Le opzioni disponibili sono `fromStart`, `fromEnd` o `fromEnqueuedTime`. `fromEnd` elabora nuovi eventi accodati dopo l'avvio dell'esecuzione dell'app per le funzioni. Si applica a tutte le partizioni.  Per altre informazioni, vedere la [documentazione di EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
|initialOffsetOptions/enqueuedTimeUtc|N/D| Specifica il tempo di accodamento dell'evento nel flusso da cui iniziare l'elaborazione. Se `initialOffsetOptions/type` viene configurato come `fromEnqueuedTime`, questa impostazione è obbligatoria. Supporta l'ora in qualsiasi formato supportato da [DateTime.Parse ()](/dotnet/standard/base-types/parsing-datetime), ad esempio `2020-10-26T20:31Z`. Per maggiore chiarezza, è consigliabile specificare anche un fuso orario. Se il fuso orario non viene specificato, Funzioni presuppone il fuso orario locale del computer che esegue l'app per le funzioni, che è espresso in UTC in Azure. Per altre informazioni, vedere la [documentazione di EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni di Azure 2.x e versioni successive, vedere [Informazioni di riferimento su host.json per Funzioni di Azure](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|maxBatchSize|64|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|prefetchCount|n/d|Il conteggio predefinito di pre-recupero che verrà usato dall'elemento `EventProcessorHost` sottostante.| 
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.| 

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni di Azure 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).
