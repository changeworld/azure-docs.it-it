---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748264"
---
## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

1. Nell'area **Azure: Funzioni** nella barra laterale espandere la nuova app per le funzioni nella sottoscrizione. Espandere **Funzioni**, fare clic con il pulsante destro del mouse su (Windows) o premere <kbd>CTRL +</kbd> clic (macOS) su **HttpExample** e quindi scegliere **Copy function URL** (Copia URL funzione).

    ![Copiare l'URL della funzione per il nuovo trigger HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Incollare questo URL per la richiesta HTTP nella barra degli indirizzi del browser, aggiungere alla fine la stringa di query `name` come `?name=Functions`, quindi eseguire la richiesta. Il formato dell'URL che chiama la funzione attivata tramite HTTP sarà simile al seguente:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    L'esempio seguente mostra la risposta nel browser alla richiesta GET remota restituita dalla funzione:

    ![Risposta della funzione nel browser](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
