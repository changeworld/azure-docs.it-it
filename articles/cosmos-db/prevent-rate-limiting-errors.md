---
title: Evitare gli errori di limitazione della frequenza per le operazioni di Azure Cosmos DB API per MongoDB.
description: Informazioni su come impedire che l'API Azure Cosmos DB per le operazioni di MongoDB raggiunga gli errori di limitazione della frequenza con la funzionalità SSR (Server Side retry).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540607"
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


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione degli errori comuni, vedere questo articolo:

* [Risolvere i problemi comuni nell'API Azure Cosmos DB per MongoDB](mongodb-troubleshoot.md)
