---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701413"
---
1. Per eseguire la funzione, premere <kbd>F5</kbd> in Visual Studio. Potrebbe essere necessario abilitare un'eccezione del firewall in modo che gli strumenti possano gestire le richieste HTTP. I livelli di autorizzazione non vengono mai applicati quando si esegue una funzione localmente.

2. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Runtime locale di Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<YOUR_NAME>` a questo URL ed eseguire la richiesta. L'immagine seguente visualizza la risposta restituita dalla funzione nel browser alla richiesta GET locale: 

    ![Risposta localhost della funzione nel browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Per arrestare il debug, premere <kbd>MAIUSC</kbd> + <kbd>F5</kbd> in Visual Studio.
