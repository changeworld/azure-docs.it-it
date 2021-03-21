---
title: "Avvio rapido: Creare un'app Web Node.js"
description: Distribuire la prima app Node.js Hello World nel servizio app di Azure in pochi minuti.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748607"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Creare un'app Web Node.js in Azure

Configurazione iniziale di <abbr title="Un servizio basato su HTTP per l'hosting di applicazioni Web, API REST e applicazioni back-end per dispositivi mobili.">Servizio app di Azure</abbr> creando una Node.jsapp/Express localmente usando Visual Studio Code e quindi distribuendo l'app nel cloud di Azure. Poiché si usa un <abbr title="Nel servizio app Azure, un livello di base in cui l'app viene eseguita nelle stesse VM di altre app, incluse le app di altri clienti. Questo livello è destinato a sviluppo e test.">livello gratuito</abbr>non vengono addebitati costi per completare questa Guida introduttiva.

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

- Un account Azure con un <abbr title="Una sottoscrizione di Azure è un contenitore logico usato per il provisioning delle risorse in Azure. Contiene i dettagli relativi a tutte le risorse, ad esempio macchine virtuali, database e così via.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Installare Git</a>
- [Node.js e npm](https://nodejs.org). Eseguire il comando `node --version` per verificare che Node.js sia installato.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Estensione Servizio app di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) per Visual Studio Code.

[Segnalare un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. clonare ed eseguire un'applicazione Node.js locale

1. Nel computer locale aprire un terminale e clonare il repository di esempio:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Passare alla cartella della nuova app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Installare le dipendenze:

    ```bash
    npm install
    ```

1. Avviare l'app per testarla in locale:

    ```bash
    npm start
    ```
    
1. Aprire il browser e passare a `http://localhost:1337`. Nel browser verrà visualizzato il testo "Hello World!".

1. Premere <kbd>CTRL</kbd> + <kbd>C</kbd> nel terminale per arrestare il server.

[Segnalare un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. visualizzazione dei log da Visual Studio Code

Visualizza il <abbr title="Tutte le chiamate a `console.log` nell'app vengono visualizzate nella finestra di output in Visual Studio Code.">log</abbr> dell'app del servizio app in esecuzione.

1. Trovare l'app in Esplora **Servizi app di Azure** , fare clic con il pulsante destro del mouse sul nome dell'app e scegliere **Avvia log in streaming**.

1. Viene visualizzata la finestra di output Visual Studio Code.

    ![Comando Visualizza log in streaming](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot della richiesta di VS Code di abilitare la registrazione dei file e riavviare l'app Web, con il pulsante Sì selezionato.":::

1. Dopo alcuni secondi verrà visualizzato un messaggio che indica che si è connessi al servizio di streaming di log. 
1. Aggiornare la pagina alcune volte per visualizzare altre attività.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Segnalare un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. pulire le risorse

Trovare l'app in Esplora **Servizi app di Azure** , fare clic con il pulsante destro del mouse sul nome dell'app e scegliere **Elimina**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Trovare l'app nell'esploratore ' * *' del servizio APP di AZURE ' * *', fare clic con il pulsante destro del mouse sul nome dell'app e scegliere ' Elimina '":::

## <a name="next-steps"></a>Passaggi successivi

L'argomento avvio rapido è stato completato. È possibile distribuire le modifiche all'app usando lo stesso processo e scegliendo l'app esistente invece di crearne una nuova.

Vedere ora le altre estensioni di Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Strumenti di Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Strumenti dell'interfaccia della riga di comando di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

In alternativa, è possibile ottenere tutte queste soluzioni installando il pacchetto di estensioni [Node per Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).