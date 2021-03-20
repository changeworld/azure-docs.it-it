---
title: Pianificare l'esecuzione dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Pianificare gli indicizzatori di Azure ricerca cognitiva per indicizzare il contenuto periodicamente o in momenti specifici.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097977"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Come pianificare gli indicizzatori in Azure ricerca cognitiva

Un indicizzatore viene in genere eseguito una volta, subito dopo la creazione. Successivamente, è possibile eseguirlo di nuovo su richiesta usando portale di Azure, [Esegui indicizzatore (REST)](/rest/api/searchservice/run-indexer)o Azure SDK. In alternativa, è anche possibile configurare un indicizzatore per l'esecuzione in base a una pianificazione. Di seguito sono riportate alcune situazioni in cui la pianificazione dell'indicizzatore è utile:

* I dati di origine cambieranno nel tempo e si vuole che l'indicizzatore di ricerca elabori automaticamente la differenza.

* I dati di origine sono molto grandi e si vuole distribuire l'elaborazione dell'indicizzatore nel tempo. I processi dell'indicizzatore sono soggetti a un tempo di esecuzione massimo di 24 ore per le origini dati normali e 2 ore per gli indicizzatori con skillsets. Se non è possibile completare l'indicizzazione entro l'intervallo massimo, è possibile configurare una pianificazione che viene eseguita ogni 2 ore. Gli indicizzatori possono riprendersi automaticamente dal punto in cui sono stati lasciati, come evidenziato da un limite massimo interno che contrassegna la fine dell'ultima indicizzazione. L'esecuzione di un indicizzatore in una pianificazione ricorrente di 2 ore consente all'IT di elaborare un set di dati di grandi dimensioni (molti milioni di documenti) oltre l'intervallo consentito per un singolo processo. Per altre informazioni sull'indicizzazione di volumi di dati di grandi dimensioni, vedere [come indicizzare set di dati di grandi dimensioni in ricerca cognitiva di Azure](search-howto-large-index.md).

* Un indice di ricerca verrà popolato da più origini dati e si desidera che gli indicizzatori vengano eseguiti in momenti diversi per ridurre i conflitti.

Visivamente, una pianificazione potrebbe essere simile alla seguente: a partire dal 1 ° gennaio ed eseguito ogni 50 minuti.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> L'utilità di pianificazione è una funzionalità predefinita di Azure ricerca cognitiva. Non è disponibile alcun supporto per le utilità di pianificazione esterne.

## <a name="schedule-property"></a>Proprietà Schedule

Una pianificazione fa parte della definizione dell'indicizzatore. Se la proprietà **Schedule** viene omessa, l'indicizzatore verrà eseguito una sola volta immediatamente dopo la creazione. Se si aggiunge una proprietà **Schedule** , si specificheranno due parti.

| Proprietà | Descrizione |
|----------|-------------|
|**Interval** | necessaria La quantità di tempo che intercorre tra l'inizio di due esecuzioni consecutive dell'indicizzatore. L'intervallo minimo consentito è 5 minuti e il più lungo è 1440 minuti (24 ore). Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `P(nD)(T(nH)(nM))`. <br/><br/>Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore.|
| **Ora di inizio (UTC)** | opzionale Indica quando devono iniziare le esecuzioni pianificate. Se omesso, viene utilizzata l'ora UTC corrente. Questa volta può essere nel passato, nel qual caso la prima esecuzione è pianificata come se l'indicizzatore venisse eseguito in modo continuativo rispetto al **StartTime** originale.<br/><br/>Esempi: a `2021-01-01T00:00:00Z` partire dalla mezzanotte del 1 ° gennaio, a `2021-01-05T22:28:00Z` partire dalle ore 10:28 il 5 gennaio.|

## <a name="scheduling-behavior"></a>Comportamento di pianificazione

È possibile effettuare solo l'esecuzione di un indicizzatore specificato per volta. Se un indicizzatore è già in esecuzione quando viene pianificata l'esecuzione successiva, l'esecuzione viene posticipata fino alla successiva ora pianificata.

Ecco un esempio per rendere il discorso più concreto. Si supponga di configurare una pianificazione dell'indicizzatore con un **intervallo** di ore e l' **ora di inizio** del 1 ° giugno 2019 alle 8:00:00 AM UTC. Ecco cosa può accadere quando l'esecuzione di un indicizzatore richiede più di un'ora:

* La prima esecuzione dell'indicizzatore inizia a partire dal 1 ° giugno 2019 alle 8:00 AM UTC. Si supponga che l'esecuzione richieda 20 minuti (o un tempo qualsiasi inferiore a 1 ora).
* La seconda esecuzione inizia a partire dal 1 ° giugno 2019 9:00 AM UTC. Si supponga che questa esecuzione riprenda 70 minuti (più di un'ora) e che non venga completata fino all'ora UTC 10:10.
* La terza esecuzione è pianificata per iniziare alle 10:00 AM UTC, ma in quel momento l'esecuzione precedente è ancora in esecuzione. L'esecuzione pianificata viene quindi ignorata. L'esecuzione successiva dell'indicizzatore non verrà avviata fino al 11:00 UTC.

> [!NOTE]
> Se un indicizzatore è impostato su una determinata pianificazione ma ha ripetutamente esito negativo nello stesso documento ogni volta, l'indicizzatore inizierà a essere eseguito in un intervallo meno frequente, fino a un intervallo massimo di almeno una volta ogni 24 ore, fino a quando non ripeterà correttamente lo stato di avanzamento. Se si ritiene di aver risolto il problema sottostante, è possibile eseguire l'indicizzatore manualmente. se l'indicizzazione ha esito positivo, l'indicizzatore tornerà alla pianificazione regolare.

## <a name="schedule-using-rest"></a>Pianificare con REST

Specificare la proprietà **Schedule** durante la creazione o l'aggiornamento dell'indicizzatore.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Pianificare con .NET

L'esempio C# seguente crea un indicizzatore del database SQL di Azure, usando un'origine dati e un indice predefiniti e ne imposta la pianificazione in modo che venga eseguito una volta al giorno, a partire da ora:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

La pianificazione viene definita usando la classe [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) , quando si crea o si aggiorna un indicizzatore usando [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Il costruttore **IndexingSchedule** richiede un parametro **Interval** specificato utilizzando un oggetto **TimeSpan** . Come indicato in precedenza, il valore minimo dell'intervallo consentito è 5 minuti e il massimo è 24 ore. Il secondo parametro **StartTime** , specificato come oggetto **DateTimeOffset** , è facoltativo.

## <a name="next-steps"></a>Passaggi successivi

Per gli indicizzatori in esecuzione in base a una pianificazione, è possibile monitorare le operazioni recuperando lo stato dal servizio di ricerca o ottenere informazioni dettagliate abilitando la registrazione diagnostica.

* [Monitorare lo stato dell'indicizzatore di ricerca](search-howto-monitor-indexers.md)
* [Raccogliere e analizzare i dati di log](search-monitor-logs.md)