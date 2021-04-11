---
title: Creare la prima funzione nel portale di Azure
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite il portale di Azure.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: ea5b6a9e51b6982a33dc748f72557ed539b8e2e0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385990"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creare la prima funzione nel portale di Azure

Funzioni di Azure consente di eseguire il codice in un ambiente senza server senza dover prima creare una macchina virtuale (VM) o pubblicare un'applicazione Web. Questo articolo illustra come usare funzioni di Azure per creare una funzione trigger HTTP "Hello World" nel portale di Azure.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Si consiglia invece di [sviluppare le funzioni in locale](functions-develop-local.md) e di pubblicarle in un'app per le funzioni in Azure.  
Usare uno dei collegamenti seguenti per iniziare a usare l'ambiente di sviluppo locale e la lingua scelti:

| Visual Studio Code | Terminale/prompt dei comandi | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Introduzione a C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Introduzione a Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Introduzione a JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Introduzione a PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Introduzione a Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Introduzione a C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Introduzione a Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Introduzione a JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Introduzione a PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Introduzione a Python](./create-first-function-cli-python.md) | [Introduzione a C#](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Successivamente, creare una funzione nella nuova app per le funzioni.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creare una funzione Trigger HTTP

1. Selezionare **Funzioni** nel menu a sinistra della finestra **Funzioni** e quindi **Aggiungi** nel menu in alto. 
 
1. Dalla finestra **Aggiungi funzione** selezionare il modello di **trigger http** .

    ![Scegliere la funzione Trigger HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. In **Dettagli modello** usare `HttpExample` per **nuova funzione** scegliere **Anonimo** dall'elenco a discesa **[livello di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys)** e quindi selezionare **Aggiungi**.

    A questo punto Azure crea la funzione Trigger HTTP. Ora è possibile eseguire la nuova funzione inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione

1. Nella nuova funzione Trigger HTTP, selezionare **Codice + test** dal menu a sinistra, quindi selezionare **Ottieni URL funzione** dal menu in alto.

    ![Selezionare Ottieni URL funzione](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Nella finestra di dialogo **Ottieni URL funzione**, selezionare **Predefinito** dall'elenco a discesa, quindi l'icona **Copia negli Appunti**. 

    ![Creare l'URL della funzione dal portale di Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Incollare l'URL della funzione nella barra degli indirizzi del browser. Aggiungere il valore della stringa di query `?name=<your_name>` alla fine dell'URL e premere INVIO per eseguire la richiesta. Nel browser dovrebbe essere visualizzato un messaggio di risposta che restituisce il valore della stringa di query. 

    Se l'URL della richiesta include un [tasto](functions-bindings-http-webhook-trigger.md#authorization-keys) di `?code=...` scelta (), si sceglie la **funzione** anziché il livello di accesso **Anonimo** durante la creazione della funzione. In questo caso, è necessario aggiungere `&name=<your_name>` .

1. Quando viene eseguita la funzione, vengono scritte nei log informazioni di traccia. Per visualizzare l'output di traccia, tornare alla pagina **Codice + test** nel portale ed espandere la freccia **Log** nella parte inferiore della pagina.

   ![Visualizzatore log di Funzioni nel portale di Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
