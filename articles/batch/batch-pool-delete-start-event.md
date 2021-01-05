---
title: Evento di avvio eliminazione del pool di Azure Batch
description: Riferimento per l’evento di avvio eliminazione del pool di batch. Questo evento viene generato quando un'operazione di eliminazione pool è stata avviata.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803749"
---
# <a name="pool-delete-start-event"></a>Evento di avvio eliminazione pool

 Questo evento viene generato quando un'operazione di eliminazione pool è stata avviata. Poiché l'eliminazione pool è un evento asincrono, è possibile prevedere l'emissione di un evento di completamento eliminazione pool una volta completata l'operazione di eliminazione.

 L'esempio seguente illustra il corpo di un evento di avvio eliminazione pool.

```
{
   "id": "myPool1"
}
```

|Elemento|Type|Note|
|-------------|----------|-----------|
|`id`|string|ID del pool.|
