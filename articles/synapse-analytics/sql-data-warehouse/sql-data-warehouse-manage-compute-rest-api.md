---
title: Sospendere, riprendere e ridimensionare con le API REST per il pool SQL dedicato (in precedenza SQL DW)
description: Gestire la potenza di calcolo per il pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics tramite le API REST.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375902"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>API REST per il pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

API REST per la gestione di risorse di calcolo per pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics.

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

Per modificare le unità Data Warehouse, usare l'API REST per [creare o aggiornare il database](/rest/api/sql/databases/createorupdate). L'esempio seguente imposta le unità Data Warehouse su DW1000 per il database MySQLDW, ospitato nel server MyServer. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Sospendere le risorse di calcolo

Per sospendere un database, usare l'API REST per [sospendere i database](/rest/api/sql/databases/pause). L'esempio seguente sospende il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Riavviare le risorse di calcolo

Per avviare un database, usare l'API REST per [riprendere i database](/rest/api/sql/databases/resume). L'esempio seguente avvia il database Database02 ospitato sul server Server01. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Controllare lo stato del database

> [!NOTE]
> Attualmente il controllo dello stato del database può restituire ONLINE mentre il database completa il flusso di lavoro online, generando errori di connessione. Se si usa questa chiamata API per attivare i tentativi di connessione, potrebbe essere necessario aggiungere un ritardo di 2-3 minuti nel codice dell'applicazione.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Ottenere la pianificazione della manutenzione

Controllare la pianificazione di manutenzione impostata per un pool SQL dedicato (in precedenza SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Impostare la pianificazione della manutenzione

Per impostare e aggiornare una pianificazione di manutenzione in un pool SQL dedicato esistente (in precedenza SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Gestire le risorse di calcolo](sql-data-warehouse-manage-compute-overview.md).
