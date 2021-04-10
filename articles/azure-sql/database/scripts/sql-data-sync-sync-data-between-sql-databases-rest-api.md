---
title: 'API REST: sincronizzazione tra più database'
description: Usare uno script di esempio dell'API REST per sincronizzare più database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565907"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Usare l'API REST per sincronizzare i dati tra più database 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di API REST Configura sincronizzazione dati SQL per sincronizzare i dati tra più database.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL in Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Al momento, la sincronizzazione dati SQL non supporta Istanza gestita di SQL di Azure.

## <a name="create-sync-group"></a>Creare un gruppo di sincronizzazione

Usare il modello [Crea o aggiorna](/rest/api/sql/syncgroups/createorupdate) per creare un gruppo di sincronizzazione.
 
Quando si crea un gruppo di sincronizzazione, non passare lo schema di sincronizzazione (table\column) e non passare masterSyncMemberName, perché in questo momento il gruppo di sincronizzazione non dispone ancora di informazioni table\column.

Richiesta di esempio per la creazione di un gruppo di sincronizzazione: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Risposta di esempio per la creazione di un gruppo di sincronizzazione:

Codice di stato: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Codice di stato: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Crea membro di sincronizzazione

Usare il modello [Crea o aggiorna](/rest/api/sql/syncmembers/createorupdate) per creare un membro di sincronizzazione.

Richiesta di esempio per la creazione di un membro di sincronizzazione:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Risposta di esempio per la creazione di un membro di sincronizzazione:

Codice di stato: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Codice di stato: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Aggiorna schema

Una volta creato correttamente il gruppo di sincronizzazione, aggiornare lo schema usando i modelli seguenti.

Usare il modello di [schema dell'hub di aggiornamento](/rest/api/sql/syncgroups/refreshhubschema)  per aggiornare lo schema per il database hub. 

Richiesta di esempio per aggiornare uno schema di database hub: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Risposta di esempio per l'aggiornamento di uno schema del database hub: 

Codice di stato: 200

Codice di stato: 202

Usare il modello [list hub schemas](/rest/api/sql/syncgroups/listhubschemas) per elencare lo schema del database hub. 

Utilizzare il modello di [schema membro Refresh](/rest/api/sql/syncmembers/refreshmemberschema) per aggiornare lo schema del database membro. 

Utilizzare il modello di [schema membro elenca](/rest/api/sql/syncmembers/listmemberschemas) per elencare lo schema del database membro. 

Quando lo schema viene aggiornato correttamente, procedere al passaggio successivo. 

## <a name="update-sync-group"></a>Aggiornare il gruppo di sincronizzazione 

Usare il modello di [creazione o aggiornamento](/rest/api/sql/syncgroups/createorupdate) per aggiornare il gruppo di sincronizzazione.

Aggiornare il gruppo di sincronizzazione specificando lo schema di sincronizzazione. Includere lo schema e masterSyncMemberName, ovvero il nome che contiene lo schema che si desidera utilizzare. 

Richiesta di esempio per l'aggiornamento del gruppo di sincronizzazione: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Risposta di esempio per l'aggiornamento del gruppo di sincronizzazione: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Aggiorna membro di sincronizzazione

Usare il modello di [creazione o aggiornamento](/rest/api/sql/syncmembers/createorupdate) per aggiornare il membro di sincronizzazione.

Richiesta di esempio per l'aggiornamento di un membro di sincronizzazione: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Risposta di esempio per l'aggiornamento di un membro di sincronizzazione: 

Codice di stato: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Codice di stato: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Attivazione sincronizzazione

Usare il modello [trigger Sync](/rest/api/sql/syncgroups/triggersync) per attivare un'operazione di sincronizzazione.

Richiesta di esempio per l'attivazione dell'operazione di sincronizzazione: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Risposta di esempio per l'attivazione dell'operazione di sincronizzazione: 

Codice di stato: 200

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../powershell-script-content-guide.md).

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

- Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL in Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Configurare la sincronizzazione dati
    - Usare il portale di Azure: [Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server](../sql-data-sync-sql-server-configure.md)
    - Usare PowerShell: [Usare PowerShell per sincronizzare i dati tra un database nel database SQL di Azure e SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent: [Data Sync Agent per la sincronizzazione dati SQL in Azure](../sql-data-sync-agent-overview.md)
- Procedure consigliate: [Procedure consigliate per la sincronizzazione dati SQL in Azure](../sql-data-sync-best-practices.md)
- Monitoraggio: [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](../monitor-tune-overview.md)
- Risoluzione dei problemi: [Risolvere i problemi relativi alla sincronizzazione dati SQL in Azure](../sql-data-sync-troubleshoot.md)
- Aggiornare lo schema di sincronizzazione
    - Usare Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL in Azure](../sql-data-sync-update-sync-schema.md)
    - Usare PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](update-sync-schema-in-sync-group.md)

Per altre informazioni sul database SQL, vedere:

- [Panoramica del database SQL](../sql-database-paas-overview.md)
- [Gestione del ciclo di vita del database](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))