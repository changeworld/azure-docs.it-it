---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 5bc1d2ddb5b9ddb185a8349fb42d5f5db79c1498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201362"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Visual Studio Code si integra con [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) per consentire l'esecuzione di questo progetto nel computer di sviluppo locale prima della pubblicazione in Azure.

1. Per chiamare la funzione, premere <kbd>F5</kbd> per avviare il progetto di app per le funzioni. L'output dagli strumenti di base viene visualizzato nel pannello **Terminale**. L'app viene avviata nel pannello del **terminale** . È possibile visualizzare l'endpoint dell'URL della funzione attivata da HTTP eseguita in locale.

    ![Funzione locale - Output di VS Code](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    In caso di problemi con l'esecuzione in Windows, assicurarsi che il terminale predefinito per Visual Studio Code non sia impostato su **WSL Bash**.

1. Con gli strumenti di base in esecuzione, passare all'area **funzioni di Azure:** . In **funzioni** espandere funzioni **del progetto locale**  >  . Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Esegui ora la funzione da Visual Studio Code":::
    
1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere INVIO per inviare il messaggio di richiesta alla funzione. 

   È possibile che sia stata inviata una richiesta HTTP GET all' <http://localhost:7071/api/HttpExample> indirizzo in un Web browser.

1. Quando la funzione viene eseguita localmente e restituisce una risposta, viene generata una notifica in Visual Studio Code. Informazioni sull'esecuzione della funzione vengono visualizzate nel pannello del **terminale** .

1. Premere <kbd>CTRL+C</kbd> per arrestare Core Tools e disconnettere il debugger.
