---
title: Monitorare lo stato e i risultati dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Monitorare lo stato, lo stato di avanzamento e i risultati degli indicizzatori di Azure ricerca cognitiva nel portale di Azure, usando l'API REST o .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99063268"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Come monitorare lo stato e i risultati dell'indicizzatore ricerca cognitiva di Azure

È possibile monitorare l'elaborazione dell'indicizzatore nell'portale di Azure o a livello di codice tramite chiamate REST o Azure SDK. Oltre allo stato dell'indicizzatore, è possibile esaminare gli orari di inizio e di fine, nonché gli errori e gli avvisi dettagliati di una determinata esecuzione.

## <a name="monitor-using-azure-portal"></a>Monitoraggio con portale di Azure

È possibile visualizzare lo stato corrente di tutti gli indicizzatori nella pagina di panoramica del servizio di ricerca. Le pagine del portale vengono aggiornate ogni pochi minuti, quindi non sarà possibile visualizzare immediatamente l'evidenza di un nuovo indicizzatore.

   ![Elenco di indicizzatori](media/search-monitor-indexers/indexers-list.png "Elenco di indicizzatori")

| Stato | Descrizione |
|--------|-------------|
| **In corso** | Indica l'esecuzione attiva. Il portale segnalerà informazioni parziali. Man mano che l'indicizzazione avanza, è possibile osservare che la crescita del valore docs è stata **completata** in risposta. Gli indicizzatori che elaborano volumi elevati di dati possono richiedere molto tempo per l'esecuzione. Ad esempio, gli indicizzatori che gestiscono milioni di documenti di origine possono essere eseguiti per 24 ore e quindi riavviati quasi immediatamente. Lo stato per gli indicizzatori con volumi elevati può sempre indicare **in corso** nel portale. Anche quando un indicizzatore è in esecuzione, sono disponibili dettagli sullo stato di avanzamento e sulle esecuzioni precedenti. |
| **Success** | Indica che l'esecuzione è stata completata. Un'esecuzione dell'indicizzatore può avere esito positivo anche se i singoli documenti contengono errori, se il numero di errori è inferiore all'impostazione **massima degli elementi non riusciti** dell'indicizzatore. |
| **Operazione non riuscita** | Il numero di errori ha superato il numero **massimo di elementi non riusciti** e l'indicizzazione è stata arrestata. |
| **Reimpostazione** | Lo stato di rilevamento delle modifiche interno dell'indicizzatore è stato reimpostato. L'indicizzatore viene eseguito in modo completo, aggiornando tutti i documenti e non solo quelli con timestamp più recenti. |

È possibile fare clic su un indicizzatore nell'elenco per visualizzare altri dettagli sulle esecuzioni correnti e recenti dell'indicizzatore.

   ![Riepilogo e cronologia di esecuzione dell'indicizzatore](media/search-monitor-indexers/indexer-summary.png "Riepilogo e cronologia di esecuzione dell'indicizzatore")

Nel grafico di **Riepilogo dell'indicizzatore** viene visualizzato un grafico del numero di documenti elaborati nelle esecuzioni più recenti.

L'elenco **Dettagli esecuzione** Mostra fino a 50 dei risultati dell'esecuzione più recenti. Fare clic su un risultato dell'esecuzione nell'elenco per visualizzare le specifiche relative all'esecuzione. Sono incluse le ore di inizio e di fine e gli eventuali errori e avvisi che si sono verificati.

   ![Dettagli esecuzione indicizzatore](media/search-monitor-indexers/indexer-execution.png "Dettagli esecuzione indicizzatore")

Se durante l'esecuzione si sono verificati problemi specifici del documento, questi verranno elencati nei campi errori e avvisi.

   ![Dettagli indicizzatore con errori](media/search-monitor-indexers/indexer-execution-error.png "Dettagli indicizzatore con errori")

Gli avvisi sono comuni ad alcuni tipi di indicizzatori e non sempre indicano un problema. Ad esempio, gli indicizzatori che usano servizi cognitivi possono segnalare avvisi quando i file di immagine o PDF non contengono testo da elaborare. 

Per ulteriori informazioni sull'analisi degli errori e degli avvisi dell'indicizzatore, vedere [risoluzione dei problemi comuni dell'indicizzatore in ricerca cognitiva di Azure](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Monitorare con lo stato di Get Indexer (API REST)

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore usando il [comando Get Indexer status](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

La risposta contiene lo stato globale dell'indicizzatore, la chiamata all'indicizzatore ultimo (o in corso) e la cronologia delle chiamate recenti.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

La cronologia di esecuzione contiene fino alle 50 esecuzioni più recenti, che sono ordinate in ordine cronologico inverso (prima più recenti).

Si noti che sono presenti due valori di stato diversi. Lo stato di primo livello è per l'indicizzatore stesso. Lo stato dell'indicizzatore **in esecuzione** indica che l'indicizzatore è configurato correttamente e disponibile per l'esecuzione, ma non è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato che indica se l'esecuzione specifica è in corso (**in esecuzione**) o è già stata completata con uno stato di **esito positivo**, **transientFailure** o **persistentFailure** . 

Quando un indicizzatore viene reimpostato per aggiornare lo stato di rilevamento delle modifiche, viene aggiunta una voce della cronologia di esecuzione separata con uno stato di **reimpostazione** .

Per altri dettagli sui codici di stato e sui dati di monitoraggio dell'indicizzatore, vedere [ottenere lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Monitorare con .NET

Con Azure ricerca cognitiva .NET SDK, l'esempio C# seguente scrive informazioni sullo stato di un indicizzatore e i risultati dell'esecuzione più recente (o in corso) nella console.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

L'output nella console sarà simile al seguente:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Si noti che sono presenti due valori di stato diversi. Lo stato di primo livello è lo stato dell'indicizzatore stesso. Lo stato dell'indicizzatore **in esecuzione** indica che l'indicizzatore è configurato correttamente e disponibile per l'esecuzione, ma non è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato per determinare se l'esecuzione specifica è in corso (**in esecuzione**) o se è già stata completata con uno stato di **esito positivo** o **TransientError** . 

Quando un indicizzatore viene reimpostato per aggiornare lo stato di rilevamento delle modifiche, viene aggiunta una voce di cronologia separata con uno stato di **reimpostazione** .

## <a name="next-steps"></a>Passaggi successivi

Per altri dettagli sui codici di stato e le informazioni di monitoraggio dell'indicizzatore, vedere le informazioni di riferimento sulle API seguenti:

* [GetIndexerStatus (API REST)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)