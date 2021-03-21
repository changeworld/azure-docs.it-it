---
title: Creare una funzione in Python con Visual Studio Code - Funzioni di Azure
description: Informazioni su come creare una funzione in Python e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure usando l'estensione Funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031736"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Avvio rapido: Creare una funzione in Azure con Python usando Visual Studio Code

> [!div class="op_single_selector" title1="Selezionare il linguaggio della funzione: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Altro (Go/Rust)](create-first-function-vs-code-other.md)

In questo articolo si userà Visual Studio Code per creare una funzione Python che risponde alle richieste HTTP. Dopo aver testato il codice localmente, è necessario distribuirlo nel <abbr title="Ambiente di elaborazione del runtime in cui tutti i dettagli del server sono trasparenti per gli sviluppatori di applicazioni, semplificando il processo di distribuzione e gestione del codice.">senza server</abbr> ambiente di <abbr title="Un servizio di Azure che fornisce un ambiente di elaborazione senza server a basso costo per le applicazioni.">Funzioni di Azure</abbr>.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione di questo articolo basata sull'interfaccia della riga di comando](create-first-function-cli-python.md).

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

+ Azure <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">account</abbr> con un oggetto attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 3.x.

+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/) e [Python 3.6](https://www.python.org/downloads/release/python-368/) sono supportati da Funzioni di Azure (x64).

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per Visual Studio Code.  

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> creare il progetto locale

1. Scegliere l'icona di Azure nella <abbr title="Il gruppo verticale di icone sul lato sinistro della finestra di Visual Studio Code.">Barra attività</abbr>, quindi nell'area **Azure: Functions** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `Python`.

    + **Selezionare un alias Python per creare un ambiente virtuale**: Scegliere la posizione dell'interprete Python. Se la posizione non viene visualizzata, digitare il percorso completo del file binario di Python.  

    + **Selezionare un modello per la prima funzione del progetto**: Scegliere `HTTP trigger`.

    + **Specificare un nome di funzione**: Digitare `HttpExample`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione. Per informazioni sui livelli di autorizzazione, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.

<br/>
<details>
<summary><strong>Non è possibile creare un progetto di funzione?</strong></summary>

I problemi più comuni da risolvere quando si crea un progetto di funzioni locali sono:
* Non è stata installata l'estensione funzioni di Azure. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Premere <kbd>F5</kbd> per avviare il progetto di app per le funzioni.

1. Nel pannello **terminale** , vedere l'endpoint URL della funzione in esecuzione in locale.

    ![Funzione locale - Output di VS Code](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Con gli strumenti di base in esecuzione, passare all'area **funzioni di Azure:** . In **funzioni** espandere funzioni **del progetto locale**  >  . Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Esegui ora la funzione da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere INVIO per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita localmente e restituisce una risposta, viene generata una notifica in Visual Studio Code. Informazioni sull'esecuzione della funzione vengono visualizzate nel pannello del **terminale** .

1. Premere <kbd>CTRL+C</kbd> per arrestare Core Tools e disconnettere il debugger.

<br/>
<details>
<summary><strong>Non è possibile eseguire la funzione localmente?</strong></summary>

I problemi più comuni da risolvere quando si esegue un progetto di funzioni locali sono:
* Non sono installati gli strumenti di base. 
*  In caso di problemi di esecuzione in Windows, assicurarsi che la shell di Terminal predefinita per Visual Studio Code non sia impostata su **WSL bash**. 
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

    + **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia <abbr title="Il nome deve essere univoco in tutti i clienti di Azure a livello globale. Ad esempio, è possibile usare una combinazione del nome personale o dell'organizzazione, il nome dell'applicazione e un identificatore numerico, come in Contoso-bizapp-Func-20.">univoco in Azure</abbr>. 

    + **Selezionare un runtime**: scegliere la versione di Python in esecuzione in locale. Per verificare la versione in uso, eseguire il comando `python --version`.

    + **Selezionare un percorso per le nuove risorse**: per ottenere prestazioni migliori, scegliere un' [area](https://azure.microsoft.com/regions/) nelle vicinanze.

    L'estensione Mostra lo stato delle singole risorse man mano che vengono create in Azure nell'area di notifica.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notifica della creazione di risorse di Azure":::

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

1. Tornare alla barra laterale **Azure: Functions** , espandere la nuova app per le funzioni.
1. Espandere **funzioni**, fare clic con il pulsante destro del mouse su (Windows <kbd>) o fare</kbd> clic con il pulsante destro del mouse sulla `HttpExample` funzione e scegliere **Esegui ora funzione**....

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Esegui la funzione ora in Azure da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` .

    Premere INVIO per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita in Azure e restituisce una risposta, viene generata una notifica in Visual Studio Code.

## <a name="7-clean-up-resources"></a>7. Pulire le risorse

Quando si continua con il [passaggio successivo](#next-steps) e si aggiunge un <abbr title="Un metodo per associare una funzione a una coda di archiviazione, in modo che possa creare messaggi nella coda."> Associazione di output della coda di Archiviazione di Azure</abbr> per la funzione, è necessario che tutte le risorse siano configurate per la compilazione in base a quanto già fatto.

In caso contrario, seguire questa procedura per eliminare l'app per le funzioni e le risorse correlate per evitare l'addebito di costi aggiuntivi.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Per altre informazioni sui costi di Funzioni di Azure, vedere [Stima dei costi del piano a consumo](functions-consumption-costs.md).

## <a name="next-steps"></a>Passaggi successivi

Espandi la funzione aggiungendo un output <abbr title="Connessione dichiarativa tra una funzione e altre risorse. Un'associazione di input fornisce dati alla funzione. un'associazione di output fornisce dati dalla funzione ad altre risorse.">binding</abbr>. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure. 

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Problemi? Segnalarli](https://aka.ms/python-functions-qs-survey).

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
