---
title: Creare una funzione in JavaScript con Visual Studio Code - Funzioni di Azure
description: Informazioni su come creare una funzione in JavaScript e quindi pubblicare il progetto Node.js locale nell'hosting serverless in Funzioni di Azure usando l'estensione Funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748592"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Avvio rapido: Creare una funzione in JavaScript in Azure con Visual Studio Code

> [!div class="op_single_selector" title1="Selezionare il linguaggio della funzione: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Altro (Go/Rust)](create-first-function-vs-code-other.md)

Usare Visual Studio Code per creare una funzione JavaScript che risponde alle richieste HTTP. Testare il codice localmente, quindi distribuirlo nell'ambiente senza server di funzioni di Azure.

Il completamento di questa Guida introduttiva comporta un costo ridotto di pochi centesimi USD o meno nella <abbr title="L'account Azure è un'entità univoca globale che consente di accedere ai servizi di Azure e alle sottoscrizioni di Azure.">Account Azure</abbr>.

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

+ Un account Azure con un <abbr title="Una sottoscrizione di Azure è un contenitore logico usato per il provisioning delle risorse in Azure. Contiene i dettagli relativi a tutte le risorse, ad esempio macchine virtuali, database e così via.">sottoscrizione attiva</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ [Estensione di funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

+ [Strumenti di base di funzioni di Azure](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> creare il progetto di funzioni locali

1. Scegliere l'icona di Azure nella <abbr title="">Barra attività</abbr>, quindi nell'area **Azure: Functions** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Scegliere un percorso della directory** per l'area di lavoro del progetto, quindi scegliere **Seleziona**. 

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `JavaScript`.

    + **Selezionare un modello per la prima funzione del progetto**: Scegliere `HTTP trigger`.

    + **Specificare un nome di funzione**: Digitare `HttpExample`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione.

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.




<br/>
<details>
<summary><strong>Non è possibile creare un progetto di funzione?</strong></summary>

I problemi più comuni da risolvere quando si crea un progetto di funzioni locali sono:
* Non è stata installata l'estensione funzioni di Azure. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. eseguire la funzione localmente


1. Premere <kbd>F5</kbd> per avviare il progetto di app per le funzioni. 

1. Nel **terminale**, vedere l'endpoint URL della funzione in esecuzione in locale.

    ![Funzione locale - Output di VS Code](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Copiare l'URL seguente e incollarlo in un Web browser, quindi premere INVIO.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Visualizzazione della risposta restituita.


    ![Browser - Output di esempio di localhost](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Visualizzare le informazioni nel pannello **terminale** sulla richiesta.

    ![Avvio dell'host attività - Output del terminale di VS Code](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Premere <kbd>CTRL+C</kbd> per arrestare Core Tools e disconnettere il debugger.

<br/>
<details>
<summary><strong>Non è possibile eseguire la funzione localmente?</strong></summary>

I problemi più comuni da risolvere quando si esegue un progetto di funzioni locali sono:
* Non sono installati gli strumenti di base. 
*  In caso di problemi di esecuzione in Windows, assicurarsi che la shell di Terminal predefinita per Visual Studio Code non sia impostata su WSL bash. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. accedere ad Azure

Per pubblicare l'app, accedere ad Azure. Se è già stato effettuato l'accesso, passare alla sezione successiva.

1. Scegliere l'icona di Azure sulla barra delle attività, quindi nell'area **Azure: Functions** scegliere **Accedi ad Azure.**

    ![Accedere ad Azure in VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Quando richiesto nel browser, **scegliere il proprio account Azure** e **accedere** con le credenziali dell'account Azure.

1. Dopo aver eseguito l'accesso, chiudere la nuova finestra del browser e tornare a Visual Studio Code. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. pubblicare il progetto in Azure

La prima distribuzione del codice include la creazione di una risorsa di funzione nella sottoscrizione di Azure. 

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    + **Seleziona cartella**: scegliere la cartella che contiene l'app per le funzioni. 

    + **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    + **Selezionare l'app per le funzioni in Azure**: Scegliere `+ Create new Function App`.

    + **Immettere un nome univoco globale per l'app per le funzioni**: digitare un nome univoco in Azure in un percorso URL. Il nome digitato viene convalidato per garantire l'univocità globale.

    + **Selezionare un runtime**: scegliere la versione di Node.js in esecuzione in locale. Per verificare la versione in uso, eseguire il comando `node --version`.

    + **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina. 

1. Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** per visualizzare i risultati della creazione e della distribuzione. 
    
    ![Notifica di creazione completata](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>Non è possibile pubblicare la funzione?</strong></summary>

Questa sezione ha creato le risorse di Azure e ha distribuito il codice locale nell'app per le funzioni. Se l'operazione non è riuscita:

* Esaminare l'output per ottenere informazioni sull'errore. L'icona a forma di campana nell'angolo inferiore destro è un altro modo per visualizzare l'output. 
* È stato pubblicato in un'app per le funzioni esistente? Questa azione sovrascrive il contenuto dell'app in Azure.
</details>


<br/>
<details>
<summary><strong>Quali risorse sono state create?</strong></summary>

Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti con i nomi basati sul nome dell'app per le funzioni: 
* **Gruppo di risorse**: un gruppo di risorse è un contenitore logico per le risorse correlate nella stessa area.
* **Account di archiviazione di Azure**: una risorsa di archiviazione mantiene lo stato e altre informazioni sul progetto.
* **Piano a consumo**: un piano a consumo definisce l'host sottostante per l'app per le funzioni senza server.
* **App** per le funzioni: un'app per le funzioni fornisce l'ambiente per l'esecuzione del codice della funzione e delle funzioni di gruppo come unità logica.
* **Application Insights**: Application Insights tiene traccia dell'utilizzo della funzione senza server.

</details>





<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. eseguire la funzione in Azure
1. Nella barra laterale di **Azure: funzioni** espandere la nuova app per le funzioni. 
1. Espandere **funzioni**, fare clic con il pulsante destro del mouse su **HttpExample**, quindi scegliere **Esegui funzione adesso...**.

    ![Copiare l'URL della funzione per il nuovo trigger HTTP](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. **Premere invio** per inviare il messaggio di richiesta predefinito alla funzione. 

1. Quando l'esecuzione della funzione viene completata, viene generata una notifica in Visual Studio Code.

<br/>
<details>
<summary><strong>Non è stato possibile eseguire l'app per le funzioni basata sul cloud?</strong></summary>

* Si è ricordato di aggiungere la QueryString alla fine dell'URL?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. Pulire le risorse

Eliminare l'app per le funzioni e le relative risorse per evitare di incorrere in costi aggiuntivi.

1. In Visual Studio Code selezionare l'icona di Azure sulla barra delle attività, quindi selezionare l'area funzioni nella barra laterale. 
1. Selezionare l'app per le funzioni, quindi fare clic con il pulsante destro del mouse e scegliere **Elimina app per le funzioni...**.

<hr/>
<br/>

## <a name="next-steps"></a>Passaggi successivi

Espandere la funzione aggiungendo un <abbr title="Il binding a una funzione è un modo per connettere in modo dichiarativo un'altra risorsa alla funzione.">binding di output</abbr>. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure. 

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
