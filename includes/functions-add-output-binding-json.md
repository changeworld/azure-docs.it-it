---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94341002"
---
Gli attributi di binding vengono definiti direttamente nel file function.json. A seconda del tipo di binding, potrebbero essere necessarie altre proprietà. La tabella di [configurazione dell'output della coda](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) indica i campi necessari per un binding della coda di archiviazione di Azure. L'estensione semplifica l'aggiunta di binding nel file function.json. 

Per creare un'associazione, fare clic con il pulsante destro del mouse (Ctrl + clic su macOS) `function.json` nel file nella cartella HttpTrigger e scegliere **Aggiungi binding.** Seguire le istruzioni per definire le seguenti proprietà di binding per la nuova associazione:

| Prompt | Valore | Descrizione |
| -------- | ----- | ----------- |
| **Selezionare la direzione di binding** | `out` | Il binding è un binding di output. |
| **Selezionare il binding con direzione** | `Azure Queue Storage` | Il binding è un binding della coda di archiviazione di Azure. |
| **Nome usato per identificare questa associazione nel codice** | `msg` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **La coda a cui verrà inviato il messaggio** | `outqueue` | Il nome della coda in cui scrive il binding. Se *queueName* non esiste, il binding lo crea al primo utilizzo. |
| **Selezionare l'impostazione da "local.setting.json"** | `AzureWebJobsStorage` | Il nome dell'impostazione dell'applicazione che contiene la stringa di connessione per l'account di archiviazione. L'impostazione `AzureWebJobsStorage` contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

Viene aggiunta un'associazione alla `bindings` matrice nella function.jssu, che dovrebbe essere simile alla seguente:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
