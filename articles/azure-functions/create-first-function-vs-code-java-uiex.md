---
title: Creare una funzione in Java con Visual Studio Code - Funzioni di Azure
description: Informazioni su come creare una funzione in Java e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure usando l'estensione Funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031719"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Avvio rapido: Creare una funzione in Java in Azure con Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Usare Visual Studio Code per creare una funzione Java che risponde alle richieste HTTP. Testare il codice localmente, quindi distribuirlo nell'ambiente senza server di funzioni di Azure.

Il completamento di questa Guida introduttiva comporta un costo ridotto di pochi centesimi USD o meno nella <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">Account Azure</abbr>.

Se si preferisce non usare Visual Studio Code come strumento di sviluppo, vedere le esercitazioni simili per sviluppatori Java che usano [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

+ Un account Azure con un <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione 8 o 11.

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> creare il progetto di funzioni locali

1. Scegliere l'icona di Azure sulla **barra delle attività**, quindi nell'area **Azure: funzioni** selezionare l'icona **Crea nuovo progetto.**

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Scegliere un percorso della directory** per l'area di lavoro del progetto, quindi scegliere **Seleziona**.

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `Java`.

    + **Selezionare una versione di Java**: Scegliere `Java 8` o `Java 11`, la versione di Java in cui eseguire le funzioni in Azure. Scegliere una versione di Java che è stata verificata in locale.

    + **Specificare un ID gruppo**: Scegliere `com.function`.

    + **Specificare un ID artefatto**: Scegliere `myFunction`.

    + **Specificare una versione**: Scegliere `1.0-SNAPSHOT`.

    + **Specificare un nome di pacchetto**: Scegliere `com.function`.

    + **Specificare un nome di app**: Scegliere `myFunction-12345`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione.

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.

<br/>

<details>
<summary><strong>Non è possibile creare un progetto di funzione?</strong></summary>

I problemi più comuni da risolvere quando si crea un progetto di funzioni locali sono:
* Non è stata installata l'estensione funzioni di Azure. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. eseguire la funzione localmente

1. Premere <kbd>F5</kbd> per avviare il progetto di app per le funzioni.

1. Nel **terminale**, vedere l'endpoint URL della funzione in esecuzione in locale.

    ![Funzione locale - Output di VS Code](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Con gli strumenti di base in esecuzione, passare all'area **funzioni di Azure:** . In **funzioni** espandere funzioni **del progetto locale**  >  . Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Esegui ora la funzione da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere <kbd>invio</kbd> per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita localmente e restituisce una risposta, viene generata una notifica in Visual Studio Code. Informazioni sull'esecuzione della funzione vengono visualizzate nel pannello del **terminale** .

1. Premere <kbd>CTRL+C</kbd> per arrestare Core Tools e disconnettere il debugger.

<br/>

<details>
<summary><strong>Non è possibile eseguire la funzione localmente?</strong></summary>

I problemi più comuni da risolvere quando si esegue un progetto di funzioni locali sono:
* Non sono installati gli strumenti di base. 
*  In caso di problemi di esecuzione in Windows, assicurarsi che la shell di Terminal predefinita per Visual Studio Code non sia impostata su WSL bash. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. accedere ad Azure

Per pubblicare l'app, accedere ad Azure. Se è già stato effettuato l'accesso, passare alla sezione successiva.

1. Scegliere l'icona di Azure sulla barra delle attività, quindi nell'area **Azure: Functions** scegliere **Accedi ad Azure.**

    ![Accedere ad Azure in VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Quando richiesto nel browser, **scegliere il proprio account Azure** e **accedere** con le credenziali dell'account Azure.

1. Dopo aver eseguito l'accesso, chiudere la nuova finestra del browser e tornare a Visual Studio Code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. pubblicare il progetto in Azure

La prima distribuzione del codice include la creazione di una risorsa di funzione nella sottoscrizione di Azure.

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    + **Seleziona cartella**: scegliere la cartella che contiene l'app per le funzioni. 

    + **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    + **Selezionare l'app per le funzioni in Azure**: Scegliere `Create new Function App`.

    + **Immettere un nome univoco globale per l'app per le funzioni**: digitare un nome univoco in Azure in un percorso URL. Il nome digitato viene convalidato per garantire l'univocità globale.

    - **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina.

1. Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** per visualizzare i risultati della creazione e della distribuzione.

    ![Notifica di creazione completata](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. eseguire la funzione in Azure

1. Tornare all'area **Azure: Functions** sulla barra laterale, espandere la sottoscrizione, la nuova app per le funzioni e le **funzioni**. Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Esegui la funzione ora in Azure da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere INVIO per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita in Azure e restituisce una risposta, viene generata una notifica in Visual Studio Code.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. Pulire le risorse

Se non si prevede di continuare con il [passaggio successivo](#next-steps), eliminare l'app per le funzioni e le relative risorse per evitare di incorrere in costi aggiuntivi.

1. In Visual Studio Code selezionare l'icona di Azure sulla barra delle attività, quindi selezionare l'area funzioni nella barra laterale.
1. Selezionare l'app per le funzioni, quindi fare clic con il pulsante destro del mouse e scegliere **Elimina app per le funzioni...**.

<br/>
<hr/>

## <a name="next-steps"></a>Passaggi successivi

Espandere la funzione aggiungendo un <abbr title="In archiviazione di Azure, un mezzo per associare una funzione a una coda di archiviazione, in modo che possa creare messaggi nella coda.">binding di output</abbr>. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure.

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
