---
title: Evento di completamento eliminazione del pool di Azure Batch
description: Riferimento per l’evento di completamento eliminazione del pool di batch. Questo evento viene generato quando un'operazione di eliminazione pool è stata completata.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803732"
---
# <a name="pool-delete-complete-event"></a>Evento di completamento eliminazione pool

 Questo evento viene generato quando un'operazione di eliminazione pool è stata completata.

 L'esempio seguente illustra il corpo di un evento di completamento eliminazione pool.

```
{
   "id": "myPool1",
   "startTime": "2016-09-09T22:13:48.579Z",
   "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Type|Note|
|-------------|----------|-----------|
|`id`|string|ID del pool.|
|`startTime`|Datetime|Data e ora in cui è stata avviata l'eliminazione del pool.|
|`endTime`|Datetime|Data e ora in cui è stata completata l'eliminazione del pool.|

## <a name="remarks"></a>Osservazioni

Per altre informazioni sugli stati e sui codici di errore per l'operazione di ridimensionamento pool, vedere [Delete a pool from an account](/rest/api/batchservice/delete-a-pool-from-an-account) (Eliminare un pool da un account).
