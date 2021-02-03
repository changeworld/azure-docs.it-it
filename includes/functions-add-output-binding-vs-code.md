---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493947"
---
Visual Studio Code consente di aggiungere binding al function.jsnel file attenendosi a un set di istruzioni appropriato. 

Per aggiungere un'associazione, aprire il comando pallet (F1) e digitare **funzioni di Azure: Aggiungi binding...**, scegliere la funzione per il nuovo binding, quindi seguire le istruzioni, che variano a seconda del tipo di binding aggiunto alla funzione. 

Di seguito sono riportate alcune richieste di esempio per definire una nuova associazione di output di archiviazione:

| Prompt | Valore | Descrizione |
| -------- | ----- | ----------- |
| **Selezionare la direzione di binding** | `out` | Il binding è un binding di output. |
| **Seleziona binding con direzione** | `Azure Queue Storage` | Il binding è un binding della coda di archiviazione di Azure. |
| **Nome usato per identificare questa associazione nel codice** | `msg` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **La coda a cui verrà inviato il messaggio** | `outqueue` | Il nome della coda in cui scrive il binding. Se *queueName* non esiste, il binding lo crea al primo utilizzo. |
| **Selezionare l'impostazione da "local.settings.json"** | `MyStorageConnection` | Nome di un'impostazione dell'applicazione che contiene la stringa di connessione per l'account di archiviazione. L' `AzureWebJobsStorage` impostazione contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

È anche possibile fare clic con il pulsante destro del mouse (Ctrl + clic su macOS) direttamente nel **function.js** file nella cartella della funzione, selezionare **Aggiungi binding** e seguire le stesse richieste.

In questo esempio, l'associazione seguente viene aggiunta alla `bindings` matrice nel function.jsnel file:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```