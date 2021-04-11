---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283253"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Eseguire la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionProj*:

    ```
    func start
    ```

    Verso la fine dell'output, verranno visualizzate le righe seguenti: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Se HttpExample non viene visualizzato come illustrato sopra, è probabile che l'host non sia stato avviato dalla cartella radice del progetto. In tal caso, premere **CTRL**+**C** per arrestare l'host, passare alla cartella radice del progetto ed eseguire di nuovo il comando precedente.

1. Copiare l'URL della funzione `HttpExample` da questo output in un browser e aggiungere la stringa di query `?name=<YOUR_NAME>`, rendendo l'URL completo come `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser dovrebbe essere visualizzato un messaggio di risposta che restituisce il valore della stringa di query. Il terminale in cui è stato avviato il progetto mostra anche l'output del log quando si effettuano le richieste.

1. Al termine, premere **CTRL**+**C** e scegliere `y` per arrestare l'host di Funzioni.
