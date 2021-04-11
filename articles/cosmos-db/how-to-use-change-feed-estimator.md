---
title: Usare lo strumento di stima dei feed di modifiche - Azure Cosmos DB
description: Informazioni su come usare lo strumento di stima dei feed di modifiche per analizzare lo stato di avanzamento del processore di feed di modifiche
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d4e461b25a25ecdf0d4d89ee7f1c82b9d4a0737
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220165"
---
# <a name="use-the-change-feed-estimator"></a>Usare lo strumento di stima dei feed di modifiche
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo descrive come monitorare lo stato di avanzamento delle istanze del [processore di feed di modifiche](./change-feed-processor.md) durante la lettura del feed di modifiche.

## <a name="why-is-monitoring-progress-important"></a>Perché è importante monitorare lo stato di avanzamento?

Il processore di feed di modifiche funge da puntatore che avanza nel [feed di modifiche](./change-feed.md) e distribuisce le modifiche a un'implementazione delegata.

La distribuzione del processore di feed di modifiche può elaborare le modifiche a una determinata frequenza in base alle risorse disponibili, ad esempio CPU, memoria, rete e così via.

Se questa frequenza è più bassa di quella con cui si verificano le modifiche nel contenitore Azure Cosmos, il processore inizierà a ritardare.

L'identificazione di questo scenario consente di capire se è necessario ridimensionare la distribuzione del processore di feed di modifiche.

## <a name="implement-the-change-feed-estimator"></a>Implementare lo strumento di stima dei feed di modifiche

### <a name="as-a-push-model-for-automatic-notifications"></a>Come modello push per le notifiche automatiche

Come il [processore del feed](./change-feed-processor.md)delle modifiche, lo strumento di stima del feed delle modifiche può funzionare come modello push. Lo strumento misura la differenza tra l'ultimo elemento elaborato (definito dallo stato del contenitore di lease) e la modifica più recente nel contenitore ed effettua il push di questo valore a un delegato. L'intervallo in cui viene eseguita la misurazione può anche essere personalizzato con un valore predefinito di 5 secondi.

Ad esempio, se il processore di feed di modifiche è definito come segue:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Il modo corretto per inizializzare uno strumento di stima per misurare il processore consiste nell'uso di `GetChangeFeedEstimatorBuilder`, come indicato di seguito:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

In cui sia il processore che lo strumento di stima condividono lo stesso `leaseContainer` e lo stesso nome.

Gli altri due parametri corrispondono al delegato, che riceverà un numero che rappresenta la **quantità di modifiche in sospeso per la lettura** da parte del processore e l'intervallo di tempo in cui si vuole che venga eseguita questa misurazione.

Di seguito è riportato un esempio di un delegato che riceve la stima:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

È possibile inviare questa stima alla soluzione di monitoraggio e usarla per comprendere lo stato di avanzamento nel corso del tempo.

### <a name="as-an-on-demand-detailed-estimation"></a>Come stima dettagliata su richiesta

A differenza del modello push, esiste un'alternativa che consente di ottenere la stima su richiesta. Questo modello fornisce anche informazioni più dettagliate:

* Ritardo stimato per lease.
* L'istanza che possiede ed elabora ogni lease, quindi è possibile identificare se si verifica un problema in un'istanza.

Se il processore del feed delle modifiche è definito come segue:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

È possibile creare lo strumento di stima con la stessa configurazione del lease:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

E ogni volta che si vuole, con la frequenza necessaria, è possibile ottenere la stima dettagliata:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

Ogni oggetto conterrà `ChangeFeedProcessorState` le informazioni relative a lease e lag, oltre a chi è l'istanza corrente che lo possiede. 

> [!NOTE]
> Non è necessario distribuire lo strumento di stima dei feed di modifiche come parte del processore di feed di modifiche né come parte dello stesso progetto. Può essere indipendente ed essere eseguita in un'istanza completamente diversa, che è consigliata. È sufficiente usare lo stesso nome e la stessa configurazione di lease.

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del processore di feed di modifiche](change-feed-processor.md)
* [Ora di avvio del processore di feed di modifiche](./change-feed-processor.md#starting-time)