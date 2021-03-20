---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77474199"
---
### <a name="query-the-storage-queue"></a>Eseguire una query sulla coda di archiviazione

È possibile usare il [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) comando per visualizzare le code di archiviazione nell'account, come nell'esempio seguente:

```azurecli-interactive
az storage queue list --output tsv
```

L'output di questo comando include una coda denominata `outqueue`, che è la coda creata quando è stata eseguita la funzione.

Usare quindi il [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) comando per visualizzare i messaggi in questa coda, come nell'esempio seguente:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

La stringa restituita dovrebbe essere la stessa del messaggio inviato per testare la funzione.

> [!NOTE]  
> L'esempio precedente decodifica la stringa restituita da base64. Il motivo è che i binding di archiviazione della coda scrivono e leggono i dati da Archiviazione di Azure sotto forma di [stringhe base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).