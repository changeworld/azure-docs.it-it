---
title: Esempi di Node.js per la gestione dei dati in un database Azure Cosmos
description: In GitHub sono disponibili esempi di Node.js per attività comuni in Azure Cosmos DB, tra cui operazioni CRUD.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: e2d8316e0b44593e220a0c9a9358b465ad39c9eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216837"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Esempi di Node.js per la gestione dei dati in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Esempi di .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Esempi di .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Esempi di Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Esempi di Spring Data V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Esempi di Node.js](sql-api-nodejs-samples.md)
> * [Esempi di Python](sql-api-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Alcune soluzioni di esempio che eseguono operazioni CRUD e altre operazioni comuni in risorse di Azure Cosmos DB sono inclusi nel repository [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) in GitHub. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto Node.js di esempio.
* Collegamenti al contenuto di riferimento sulle API correlato.

**Prerequisiti**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- È possibile [attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Con l'abbonamento Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

È necessario anche [JavaScript SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Ogni esempio è indipendente e le operazioni di installazione e pulizia sono eseguite automaticamente. Di conseguenza, gli esempi effettuano più chiamate a [Containers.create](/javascript/api/%40azure/cosmos/containers). A ogni chiamata, alla sottoscrizione viene addebitata 1 ora di utilizzo per ogni livello di prestazioni del contenitore creato.
   > 
   > 

## <a name="database-examples"></a>Esempi di database

Il file [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) illustra come eseguire operazioni CRUD sul database. Per saperne di più sui database di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Se non esiste, creare un database](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases#createifnotexists-databaserequest--requestoptions-) |
| [Elencare database per un account](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases#readall-feedoptions-) |
| [Leggere un database in base all'ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database#read-requestoptions-) |
| [Eliminare un database](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database#delete-requestoptions-) |

## <a name="container-examples"></a>Esempi di contenitore

Il file [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) illustra come eseguire operazioni CRUD sul contenitore. Per saperne di più sulle raccolte di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Se non esiste, creare un contenitore](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers#createifnotexists-containerrequest--requestoptions-) |
| [Elencare i contenitori per un account](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers#readall-feedoptions-) |
| [Leggere una definizione di contenitore](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container#read-requestoptions-) |
| [Eliminare un contenitore](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container#delete-requestoptions-) |

## <a name="item-examples"></a>Esempi di elementi

Il file [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) illustra come eseguire operazioni CRUD sull'elemento. Per saperne di più sui documenti di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](account-databases-containers-items.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare elementi](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items#create-t--requestoptions-) |
| [Leggere tutti gli elementi in un contenitore](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items#readall-feedoptions-) |
| [Leggere un elemento in base all'ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item#read-requestoptions-) |
| [Leggere un elemento solo se è stato modificato](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Eseguire query per documenti](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items) |
| [Sostituire un elemento](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item) |
| [Sostituire documenti con il controllo condizionale degli ETag](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Eliminare un elemento](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item) |

## <a name="indexing-examples"></a>Esempi di indicizzazione

Il file [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) illustra come gestire l'indicizzazione. Per saperne di più sull'indicizzazione in Azure Cosmos DB prima di passare agli esempi seguenti, vedere gli articoli concettuali su [criteri di indicizzazione](index-policy.md), [tipi di indicizzazione](index-overview.md#index-types) e [percorsi di indicizzazione](index-policy.md#include-exclude-paths). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Indicizzare manualmente un elemento specifico](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Escludere manualmente un elemento specifico dall'indice](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Escludere un percorso dall'indice](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy#excludedpaths) |
| [Creare un indice di intervallo su un percorso di stringa](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy), [Items.query](/javascript/api/%40azure/cosmos/items) |
| [Creare un contenitore con indexPolicy predefinito e quindi aggiornarlo online](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers)

## <a name="server-side-programming-examples"></a>Esempi di programmazione lato server

Il file [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) del progetto [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) illustra come eseguire le attività seguenti. Per informazioni sulla programmazione lato server in Azure Cosmos DB prima di passare agli esempi seguenti, vedere l'articolo concettuale [Stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una stored procedure](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures) |
| [Eseguire una stored procedure](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure) |

Per altre informazioni sulla programmazione lato server, vedere [Programmazione lato server per Azure Cosmos DB: stored procedure, trigger del database e funzioni definite dall'utente](stored-procedures-triggers-udfs.md).
