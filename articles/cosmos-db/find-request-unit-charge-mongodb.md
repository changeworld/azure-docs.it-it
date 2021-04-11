---
title: Trova l'addebito delle unità richiesta per le operazioni di Azure Cosmos DB API per MongoDB
description: Informazioni su come trovare l'addebito delle unità richiesta (UR) per le query MongoDB eseguite su un contenitore di Azure Cosmos. È possibile usare i driver portale di Azure, MongoDB .NET, Java Node.js.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b2944c1d29849ea44b5afd878d5b0e030358cc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801826"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Trovare l'addebito delle unità richiesta per le operazioni eseguite nell'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni di database viene normalizzato da Azure Cosmos DB ed è espresso in termini di unità richiesta (o in breve UR). Il costo della richiesta è l'unità di richiesta utilizzata da tutte le operazioni di database. È possibile considerare le UR come una valuta delle prestazioni astraendo le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire le operazioni del database supportate da Azure Cosmos DB. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur. Per altre informazioni, vedere l'articolo relativo alle [unità richiesta e alle relative considerazioni](request-units.md) .

Questo articolo illustra i diversi modi in cui è possibile trovare il consumo di [unità richiesta](request-units.md) (UR) per qualsiasi operazione eseguita su un contenitore nell'API Azure Cosmos DB per MongoDB. Se si usa un'API diversa, vedere l'articolo relativo all'API [SQL](find-request-unit-charge.md), [API Cassandra](find-request-unit-charge-cassandra.md), l' [api Gremlin](find-request-unit-charge-gremlin.md)e gli articoli [API tabella](find-request-unit-charge-table.md) per trovare il costo di Ur/s.

L'addebito delle UR è esposto da un [comando di database](https://docs.mongodb.com/manual/reference/command/) personalizzato denominato `getLastRequestStatistics`. Il comando restituisce un documento contenente il nome dell'ultima operazione eseguita, il relativo addebito della richiesta e la durata. Se si usa l'API di Azure Cosmos DB per MongoDB, sono disponibili diverse opzioni per il recupero dell'addebito delle UR.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e inserirvi dati oppure selezionare un account esistente che contiene già dati.

1. Passare al riquadro **Esplora dati** e quindi selezionare il contenitore da usare.

1. Selezionare il **...** accanto al nome del contenitore e selezionare **nuova query**.

1. Immettere una query valida e quindi fare clic su **Esegui query**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta eseguita. Questo editor di query consente di eseguire e visualizzare gli addebiti per le unità richiesta solo per i predicati di query. Non è possibile usare questo editor per i comandi di manipolazione dei dati, ad esempio istruzioni INSERT.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Screenshot dell'addebito per la richiesta relativa a una query MongoDB nel portale di Azure":::

1. Per ottenere gli addebiti per le richieste di manipolazione dei dati, eseguire il `getLastRequestStatistics` comando da un'interfaccia utente basata su Shell, ad esempio Mongo Shell, [Robo 3T](mongodb-robomongo.md), [MongoDB Compass](mongodb-compass.md)o un'estensione vs code con script della shell.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Usare il driver .NET di MongoDB

Quando si usa il [driver .NET MongoDB ufficiale](https://docs.mongodb.com/ecosystem/drivers/csharp/), è possibile eseguire i comandi chiamando il metodo `RunCommand` per un oggetto `IMongoDatabase`. Questo metodo richiede un'implementazione della classe astratta `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Per altre informazioni, vedere [Guida introduttiva: creare un'app Web .NET usando un'API Azure Cosmos DB per MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Usare il driver Java di MongoDB


Quando si usa il [driver Java MongoDB ufficiale](https://mongodb.github.io/mongo-java-driver/), è possibile eseguire i comandi chiamando il metodo `runCommand` per un oggetto `MongoDatabase`:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Per altre informazioni, vedere [Guida introduttiva: creare un'app Web usando l'API Azure Cosmos DB per MongoDB e Java SDK](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Usare il driver Node.js di MongoDB

Quando si usa il [driver Node.js MongoDB ufficiale](https://mongodb.github.io/node-mongodb-native/), è possibile eseguire i comandi chiamando il metodo `command` per un oggetto `db`:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Per altre informazioni, vedere [Guida introduttiva: eseguire la migrazione di un'app Web MongoDB Node.js esistente a Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di UR, vedere questi articoli:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](./optimize-cost-reads-writes.md)