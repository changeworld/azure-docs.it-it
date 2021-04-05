---
title: 'Esercitazione: Eseguire la migrazione di dati di eventi ad Azure Synapse Analytics - Hub eventi di Azure'
description: Informazioni su come usare Griglia di eventi e Funzioni di Azure per eseguire la migrazione dei dati di Hub eventi acquisiti ad Azure Synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854243"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Esercitazione: Eseguire la migrazione dei dati di Hub eventi acquisiti ad Azure Synapse Analytics usando Griglia di eventi e Funzioni di Azure
La funzionalità di [acquisizione](./event-hubs-capture-overview.md) di Hub eventi di Azure consente di acquisire automaticamente i dati di streaming in Hub eventi in un'archiviazione BLOB di Azure o in Azure Data Lake Storage. Questa esercitazione illustra come eseguire la migrazione dei dati di Hub eventi acquisiti da Archiviazione in Azure Synapse Analytics usando una funzione di Azure attivata da una [griglia di eventi](../event-grid/overview.md).

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Passaggi successivi 
È possibile usare strumenti di visualizzazione avanzata dei dati con il data warehouse per ottenere informazioni operative dettagliate.

Questo articolo illustra come usare [Power BI con Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)