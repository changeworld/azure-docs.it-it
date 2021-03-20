---
title: 'Esercitazione: Inviare i dati di Hub eventi di Azure al data warehouse: Griglia di eventi'
description: Informazioni su come archiviare i dati di Hub eventi acquisiti in Azure Synapse Analytics usando i trigger di Funzioni di Azure e Griglia di eventi.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854717"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Esercitazione: Trasmettere Big Data a un data warehouse
[Griglia di eventi](overview.md) di Azure è un servizio intelligente di routing di eventi che consente di rispondere alle notifiche o agli eventi inviati da applicazioni e servizi. Ad esempio, può attivare una funzione di Azure per elaborare i dati di Hub eventi che sono stati acquisiti in un'archiviazione BLOB o Data Lake Storage. Questo [esempio](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra come usare Griglia di eventi e Funzioni di Azure per eseguire la migrazione dei dati di Hub eventi acquisiti dall'archiviazione BLOB ad Azure Synapse Analytics, in modo specifico un pool SQL dedicato.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle differenze tra i servizi di messaggistica di Azure, vedere [Scegliere tra i servizi di Azure che recapitano messaggi](compare-messaging-services.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per informazioni introduttive alla funzionalità di acquisizione di Hub eventi, vedere [Abilitare Acquisizione di Hub eventi usando il portale di Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Per altre informazioni sulla configurazione e l'esecuzione dell'esempio, vedere l'[esempio della funzionalità di acquisizione di Hub eventi e della Griglia di eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
