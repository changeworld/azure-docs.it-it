---
title: Usare transazioni con più documenti in Azure Cosmos DB API per MongoDB
description: Informazioni su come creare un'app Mongo shell di esempio in grado di eseguire una transazione a più documenti (semantica "All-or-Nothing") in una raccolta fissa nell'API Azure Cosmos DB per MongoDB 4,0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692471"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Usare transazioni con più documenti in Azure Cosmos DB API per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In questo articolo verrà creata un'app Mongo Shell che esegue una transazione a più documenti in una raccolta fissa nell'API Azure Cosmos DB per MongoDB con Server versione 4,0.

## <a name="what-are-multi-document-transactions"></a>Che cosa sono le transazioni su più documenti?

In Azure Cosmos DB API per MongoDB, le operazioni su un singolo documento sono atomiche. Le transazioni a documenti multipli consentono alle applicazioni di eseguire operazioni atomiche su più documenti. Offre la semantica "All-or-Nothing" per le operazioni. Al commit, le modifiche apportate all'interno delle transazioni vengono rese persistenti e, in caso di errore della transazione, tutte le modifiche all'interno della transazione vengono ignorate.

Le transazioni a più documenti seguono la semantica **acid** :

* Atomicità: tutte le operazioni vengono considerate come una sola
* Coerenza: dati di cui è stato eseguito il commit sono validi
* Isolamento: isolato dalle altre operazioni
* Durabilità: i dati delle transazioni vengono salvati in modo permanente quando il client viene segnalato

## <a name="requirements"></a>Requisiti

Le transazioni a più documenti sono supportate all'interno di una raccolta non partizionata nella versione API 4,0. Le transazioni a più documenti non sono supportate in raccolte o in raccolte partizionate in 4,0. Il timeout per le transazioni è di 5 secondi fissi.

Tutti i driver che supportano il protocollo wire versione 4,0 o successiva supporteranno Azure Cosmos DB API per le transazioni in più documenti di MongoDB.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Eseguire transazioni a più documenti nella shell di MongoDB
> [!Note]
> Questo esempio non funziona nella MongoSH beta (Shell) incorporata nella bussola MongoDB.

1. Aprire un prompt dei comandi, passare alla directory in cui è installato Mongo shell versione 4,0 e versioni successive:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Creare uno script di Mongo shell *connect_friends.js* e aggiungere il contenuto seguente

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Eseguire il comando seguente per eseguire la transazione a più documenti. L'host, la porta, l'utente e la chiave sono disponibili nella portale di Azure.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Passaggi successivi

Scopri le novità dell' [API Azure Cosmos DB per MongoDB 4,0](mongodb-feature-support-40.md)
