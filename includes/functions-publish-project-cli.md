---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424875"
---
## <a name="deploy-the-function-project-to-azure"></a>Distribuire il progetto di funzione in Azure

Dopo aver creato l'app per le funzioni in Azure, è ora possibile distribuire il progetto di funzioni locale usando il comando [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment).  

Nell'esempio seguente sostituire `<APP_NAME>` con il nome dell'app.

```console
func azure functionapp publish <APP_NAME>
```

Il comando publish mostra risultati simili a quelli riportati nell'output seguente (troncato per semplicità):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
