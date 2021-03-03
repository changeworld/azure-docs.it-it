---
title: "Guida introduttiva: creare un'app Web di Node.js Linux"
description: Distribuire la prima app Node.js Hello World nel servizio app di Azure in pochi minuti.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a6c580f7d6bc03f298621b1a33fcb9f3f461e802
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748610"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. eseguire la distribuzione nel servizio app Azure da Visual Studio Code

1. Aprire la cartella dell'applicazione in Visual Studio Code.

    ```bash
    code .
    ```

1. In Esplora **Servizi app di Azure** selezionare **Accedi ad Azure...** e seguire le istruzioni. Una volta effettuato l'accesso, la finestra di esplorazione dovrebbe visualizzare il nome della sottoscrizione di Azure.

    ![Accedi ad Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
    <details>
    <summary>Risoluzione dei problemi di accesso ad Azure</summary>
    
    Se viene visualizzato l'errore **"La sottoscrizione con il nome [ID sottoscrizione] non è stata trovata"** quando si esegue l'accesso ad Azure, è possibile che un proxy impedisca di raggiungere l'API di Azure. Configurare le variabili di ambiente `HTTP_PROXY` e `HTTPS_PROXY` con le informazioni del proxy nel terminale usando `export`.
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Segnalare un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. Nell'area **SERVIZIO APP DI AZURE** selezionare l'icona della freccia verso l'alto blu per distribuire l'app in Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Screenshot del servizio app di Azure in VS Code con l'icona della freccia blu selezionata.":::

1. Scegliere la directory attualmente aperta, `nodejs-docs-hello-world`.

1. Scegliere **Crea una nuova app Web**, che esegue la distribuzione in Servizio app di Azure in Linux per impostazione predefinita.

1. Digitare un univoco globale <abbr title="I caratteri validi per il nome dell'app sono "a-z", "0-9" e "-".">name</abbr> per l'app Web e premere **invio**. 

1. Scegliere un'opzione per **Versione di Node.js**. Il valore consigliato è LTS.

    Il canale di notifica mostra le risorse di Azure che vengono create per l'app.

1. Selezionare **Sì** quando viene chiesto di aggiornare la configurazione per eseguire `npm install` nel server di destinazione. L'app viene quindi distribuita.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Screenshot della richiesta di aggiornare la configurazione nel server di destinazione con il pulsante Sì selezionato.":::

1. Scegliere **Sì** quando viene richiesto di aggiornare l'area di lavoro in modo che le distribuzioni successive siano destinate automaticamente alla stessa app Web del servizio app. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Screenshot della richiesta di aggiornare l'area di lavoro con il pulsante Sì selezionato.":::




1. Una volta completata la distribuzione, selezionare **Esplora sito Web** nel messaggio per visualizzare l'app Web appena distribuita.

<br>
<details>
<summary>Risoluzione dei problemi</summary>
* Assicurarsi che l'applicazione sia in ascolto sulla porta fornita dalla variabile di ambiente PORT: `process.env.PORT`.
* Se viene visualizzato l'errore **"Non si dispone delle autorizzazioni necessarie per visualizzare la directory o la pagina"** , è probabile che l'applicazione non sia stata avviata correttamente. Esaminare l'output del log per individuare e correggere l'errore. 

</details>

<br>

[Segnalare un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>


