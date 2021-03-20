---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93424716"
---
Eseguire il comando seguente per visualizzare i [log in streaming](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) quasi in tempo reale:

```console
func azure functionapp logstream <APP_NAME> 
```

In una finestra del terminale separata o nel browser chiamare di nuovo la funzione remota. Nel terminale viene visualizzato un log dettagliato dell'esecuzione della funzione in Azure. 