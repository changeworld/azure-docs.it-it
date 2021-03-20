---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78191044"
---
Aggiornare *HttpExample \\ \_ \_ init \_ \_ . py* in modo che corrisponda al codice seguente, aggiungendo il `msg` parametro alla definizione della funzione e `msg.set(name)` nell' `if name:` istruzione.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Il `msg` parametro è un'istanza di [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . Il relativo metodo `set` scrive un messaggio stringa nella coda, in questo caso il nome passato alla funzione nella stringa di query dell'URL.
