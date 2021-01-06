---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936864"
---
L'[endpoint privato di Azure](../articles/private-link/private-endpoint-overview.md) è un'interfaccia di rete che si connette in modo privato e sicuro a un servizio basato sul collegamento privato di Azure.  L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando il servizio nella rete virtuale in modo efficace.

È possibile usare l'endpoint privato per le funzioni ospitate nei piani [Premium](../articles/azure-functions/functions-premium-plan.md) e di [servizio app](../articles/azure-functions/dedicated-plan.md) .

Quando si crea una connessione all'endpoint privato in ingresso per le funzioni, sarà necessario anche un record DNS per risolvere l'indirizzo privato.  Per impostazione predefinita, viene creato un record DNS privato quando si crea un endpoint privato usando il portale di Azure.

Per altre informazioni, vedere [uso di endpoint privati per le app Web](../articles/app-service/networking/private-endpoint.md).