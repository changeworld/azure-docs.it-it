---
title: Evitare gli errori di limitazione della frequenza per le operazioni di Azure Cosmos DB API per MongoDB.
description: Informazioni su come impedire che l'API Azure Cosmos DB per le operazioni di MongoDB raggiunga gli errori di limitazione della frequenza con la funzionalità SSR (Server Side retry).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: e1ccf55d38a9a3a5a1d0a3622c90dd7b51e5e477
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258491"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Impedisci errori di limitazione della frequenza per le operazioni di Azure Cosmos DB API per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API per le operazioni MongoDB possono avere esito negativo con errori di limitazione della frequenza (16500/429) se superano il limite di velocità effettiva della raccolta (UR). 

È possibile abilitare la funzionalità di ripetizione del lato server (SSR) e consentire al server di ritentare automaticamente queste operazioni. Le richieste vengono ritentate dopo un breve ritardo per tutte le raccolte nell'account. Questa funzionalità rappresenta una comoda alternativa alla gestione degli errori di limitazione della frequenza nell'applicazione client.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare all'API Azure Cosmos DB per l'account MongoDB.

1. Passare al riquadro **funzionalità** sotto la sezione **Impostazioni** .

1. Selezionare **Riprova sul lato server**.

1. Fare clic su **Abilita** per abilitare questa funzionalità per tutte le raccolte nell'account.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Screenshot della funzionalità di ripetizione dei tentativi sul lato server per l'API Azure Cosmos DB per MongoDB":::

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

1. Controllare se SSR è già abilitato per l'account:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Abilita** SSR per tutte le raccolte nell'account del database. Per rendere effettive le modifiche potrebbero essere necessario fino a 15 minuti.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Il comando che segue **Disabilita** la funzione SSR per tutte le raccolte nell'account del database. Per rendere effettive le modifiche potrebbero essere necessario fino a 15 minuti.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```

## <a name="frequently-asked-questions"></a>Domande frequenti
* In che modo vengono ritentate le richieste?
    * Le richieste vengono ritentate continuamente (più o meno) finché non viene raggiunto il timeout di 60 secondi. Se viene raggiunto il timeout, il client riceverà un' [eccezione ExceededTimeLimit (50)](mongodb-troubleshoot.md).
*  Come è possibile monitorare gli effetti di SSR?
    *  È possibile visualizzare gli errori di limitazione della frequenza (429s) che vengono ritentati sul lato server nel riquadro Cosmos DB metrica. Tenere presente che questi errori non vengono inviati al client quando SSR è abilitato, dal momento che vengono gestiti e ritentati sul lato server. 
    *  È possibile cercare le voci di log contenenti "estimatedDelayFromRateLimitingInMilliseconds" nei [log delle risorse Cosmos DB](cosmosdb-monitor-resource-logs.md).
*  SSR influirà sul livello di coerenza?
    *  Il SSR non influisce sulla coerenza di una richiesta. Le richieste vengono ritentate sul lato server se hanno una frequenza limitata (con un errore 429). 
*  SSR influisce su qualsiasi tipo di errore che potrebbe ricevere il client?
    *  No, SSR influiscono solo sugli errori di limitazione della frequenza (429s) ritentando gli errori sul lato server. Questa funzionalità impedisce di dover gestire gli errori di limitazione della frequenza nell'applicazione client. Tutti gli [altri errori](mongodb-troubleshoot.md) verranno indirizzati al client. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione degli errori comuni, vedere questo articolo:

* [Risolvere i problemi comuni nell'API Azure Cosmos DB per MongoDB](mongodb-troubleshoot.md)
